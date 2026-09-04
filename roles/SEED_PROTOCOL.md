# SEED_PROTOCOL.md
# Universal seed data protocol
# Applicable to: any relational DB with entity dependencies
# Stack example: Python + SQLAlchemy 2 async + PostgreSQL
# Adaptable to: Node/Prisma, Java/Hibernate, any ORM

---

## MAIN PRINCIPLE

Seed is not "fill tables". It is **deploying a dependency graph in the correct order**.

Any error in seed data (UUID instead of names, empty days, broken pages) has one of three causes:
1. **Order**: entity created before its dependency exists
2. **Relation**: FK set incorrectly or not set at all
3. **Mode**: dev data mixed with prod structure

---

## THREE SEED MODES (never mix)

| Mode | File | Goal | Data |
|-------|------|------|--------|
| `smoke` | `seeds/smoke.py` | every page opens without errors | minimum: 1 record of each type |
| `demo` | `seeds/demo.py` | show product to client | realistic names, dates a month ahead, nice numbers |
| `prod` | `seeds/prod.py` | clean start on prod | reference data only: roles, categories, default settings |

**Rule:** Alembic migrations contain only schema (`upgrade`/`downgrade`). No INSERT in migrations except system constants (enum values, default roles).

**Launch:**
```bash
python scripts/seeds/smoke.py    # check everything works
python scripts/seeds/demo.py     # demo for client
python scripts/seeds/prod.py     # production initialisation
```

---

## STEP 1: BUILD THE DEPENDENCY GRAPH (mandatory before writing seed)

Before any seed — draw the graph. Arrow means "must exist earlier".

**Algorithm:**
1. List all project entities
2. For each, find FK fields
3. Build directed graph: A → B means "B depends on A"
4. Topologically sort — this is the creation order

**Example for a medical SaaS:**
```
Tenant (clinic)
  └── Doctor              FK: tenant_id
  └── Service             FK: tenant_id
  └── Patient             FK: tenant_id
        └── Schedule      FK: doctor_id, tenant_id
              └── Booking FK: doctor_id, patient_id, service_id, tenant_id
                    └── Transaction  FK: booking_id, tenant_id
                    └── Notification FK: booking_id, patient_id
```

**Creation order from graph:**
```
1. Tenant
2. Doctor, Service, Patient (parallel — no dependencies between them)
3. Schedule (depends on Doctor)
4. Booking (depends on Doctor + Patient + Service + Schedule)
5. Transaction, Notification (depend on Booking)
```

**Order violation rule:**
Creating Booking before Schedule — slot does not exist → FK violation or empty fields.
Creating Schedule without Doctor — no owner → error or NULL.

---

## STEP 2: FACTORY PATTERN

Each entity — a separate factory. Factory receives explicit dependencies, generates realistic data.

```python
# scripts/seeds/factories.py

from faker import Faker
from datetime import datetime, timedelta, date
import random

fake = Faker('en_US')

# ─────────────────────────────────────────
# LEVEL 1: Independent entities
# ─────────────────────────────────────────

def make_tenant(session) -> Tenant:
    """Clinic — root of the whole graph"""
    tenant = Tenant(
        name="Demo Dental Clinic",
        phone=fake.phone_number(),
        address=fake.address(),
    )
    session.add(tenant)
    session.flush()   # ← mandatory: get id before using in FK
    session.refresh(tenant)
    return tenant


def make_doctor(session, tenant: Tenant) -> Doctor:
    """Doctor — depends only on Tenant"""
    doctor = Doctor(
        tenant_id=tenant.id,
        full_name=fake.name(),          # ← NEVER UUID, always full_name
        specialty=random.choice([
            "Therapist", "Surgeon", "Orthodontist", "Periodontist"
        ]),
        phone=fake.phone_number(),
        is_active=True,
    )
    session.add(doctor)
    session.flush()
    session.refresh(doctor)
    return doctor


def make_service(session, tenant: Tenant) -> Service:
    """Service — depends only on Tenant"""
    services_catalog = [
        ("Teeth Cleaning", 3500, 60),
        ("Filling", 5000, 45),
        ("X-Ray", 800, 15),
        ("Implant", 45000, 120),
        ("Whitening", 12000, 90),
        ("Children's Visit", 2500, 30),
    ]
    name, price, duration = random.choice(services_catalog)
    service = Service(
        tenant_id=tenant.id,
        name=name,
        price=price,
        duration_minutes=duration,
    )
    session.add(service)
    session.flush()
    session.refresh(service)
    return service


def make_patient(session, tenant: Tenant) -> Patient:
    """Patient — depends only on Tenant"""
    patient = Patient(
        tenant_id=tenant.id,
        full_name=fake.name(),
        phone=fake.phone_number(),
        email=fake.email(),
        birth_date=fake.date_of_birth(minimum_age=18, maximum_age=75),
    )
    session.add(patient)
    session.flush()
    session.refresh(patient)
    return patient


# ─────────────────────────────────────────
# LEVEL 2: Schedule (depends on Doctor)
# ─────────────────────────────────────────

def make_schedule_for_month(
    session,
    doctor: Doctor,
    month_start: date,
    slots_per_day: int = 8
) -> list[Schedule]:
    """
    Creates schedule for the entire month.
    IMPORTANT: iterate over days, do not create one record per month.
    Each working day = its own slots.
    """
    schedules = []
    current = month_start

    while current.month == month_start.month:
        # Skip weekends
        if current.weekday() < 5:
            for slot_num in range(slots_per_day):
                slot_time = datetime.combine(
                    current,
                    datetime.min.time()
                ).replace(hour=9) + timedelta(minutes=30 * slot_num)

                schedule = Schedule(
                    doctor_id=doctor.id,
                    tenant_id=doctor.tenant_id,
                    slot_datetime=slot_time,
                    is_available=True,
                )
                session.add(schedule)
                schedules.append(schedule)

        current += timedelta(days=1)

    session.flush()
    return schedules


# ─────────────────────────────────────────
# LEVEL 3: Bookings (depend on everything above)
# ─────────────────────────────────────────

BOOKING_STATUSES = ["pending", "confirmed", "completed", "cancelled"]
BOOKING_WEIGHTS  = [0.15, 0.35, 0.40, 0.10]  # realistic distribution

def make_booking(
    session,
    schedule: Schedule,
    patient: Patient,
    service: Service,
) -> Booking:
    """
    Booking — depends on Schedule + Patient + Service.
    Schedule passed explicitly — never pick a random one from DB.
    """
    status = random.choices(BOOKING_STATUSES, weights=BOOKING_WEIGHTS)[0]

    booking = Booking(
        tenant_id=patient.tenant_id,
        doctor_id=schedule.doctor_id,
        patient_id=patient.id,
        service_id=service.id,
        schedule_id=schedule.id,
        scheduled_at=schedule.slot_datetime,
        status=status,
        notes=fake.sentence() if random.random() > 0.7 else None,
    )
    session.add(booking)
    session.flush()
    session.refresh(booking)

    # Mark slot as occupied
    schedule.is_available = False

    return booking


# ─────────────────────────────────────────
# LEVEL 4: Child entities
# ─────────────────────────────────────────

def make_transaction(session, booking: Booking) -> Transaction | None:
    """Transaction only for completed bookings"""
    if booking.status != "completed":
        return None

    tx = Transaction(
        tenant_id=booking.tenant_id,
        booking_id=booking.id,
        amount=booking.service.price,
        type="income",
        description=f"Payment: {booking.service.name}",
        created_at=booking.scheduled_at + timedelta(minutes=30),
    )
    session.add(tx)
    session.flush()
    return tx
```

---

## STEP 3: STUBS FOR EXTERNAL SERVICES

Fields requiring real APIs (SMS, payments, OAuth) — do not skip and do not leave NULL. Use deterministic stubs.

```python
# scripts/seeds/stubs.py
# Stubs for external services in dev/demo mode

STUB_PHONE    = "+1 (999) 000-00-{:02d}"   # .format(index) → unique numbers
STUB_EMAIL    = "demo+{index}@example.com"
STUB_PAYMENT  = "stub_payment_{uuid}"        # visible that it is a stub
STUB_TELEGRAM = 100000000                    # invalid but non-empty chat_id

def stub_phone(index: int) -> str:
    """Unique phone without real registration"""
    return f"+1 (999) 000-{index:02d}-{index:02d}"

def stub_payment_id(prefix: str = "demo") -> str:
    """Visible payment stub — not to be confused with a real one"""
    return f"STUB_{prefix.upper()}_{fake.uuid4()[:8]}"

def stub_sms_sent() -> bool:
    """In dev SMS is not sent — return True so flow continues"""
    return True
```

**Stub rules:**
- Stub must be **visible** — `STUB_` prefix, `demo+` in email
- Stub must be **unique** — not the same phone for all patients
- Stub must **not break flow** — field is filled, validation passes
- In prod-seed there are no stubs — only real data or NULL where allowed

---

## STEP 4: THREE SCRIPTS

### `seeds/smoke.py` — minimum for page verification

```python
"""
SMOKE SEED: minimum set to verify that every page
opens without errors. Not for demo — for CI and quick check.
Creates: 1 tenant, 2 doctors, 3 services, 5 patients,
         schedule for 3 days, 4 bookings of different statuses.
"""
async def run_smoke(session: AsyncSession):
    tenant   = make_tenant(session)
    doctors  = [make_doctor(session, tenant) for _ in range(2)]
    services = [make_service(session, tenant) for _ in range(3)]
    patients = [make_patient(session, tenant) for _ in range(5)]

    # 3 days of schedule — enough so calendar is not empty
    today = date.today()
    for doctor in doctors:
        for day_offset in range(3):
            day = today + timedelta(days=day_offset)
            slots = make_schedule_for_day(session, doctor, day, slots=4)

            # At least 1 booking of each status
            for status in ["pending", "confirmed", "completed", "cancelled"]:
                slot = next((s for s in slots if s.is_available), None)
                if slot and patients:
                    booking = make_booking(
                        session, slot,
                        random.choice(patients),
                        random.choice(services)
                    )
                    booking.status = status
                    if status == "completed":
                        make_transaction(session, booking)

    await session.commit()
    print("Smoke seed complete")
```

### `seeds/demo.py` — realistic data for a month

```python
"""
DEMO SEED: full data for client demonstration.
Creates: 1 tenant, 4 doctors, 8 services, 30 patients,
         schedule for current month, ~120 bookings
         with realistic status distribution.
Rules:
- All names via Faker — no UUIDs
- Dates evenly distributed across the month
- Finance: completed bookings → transactions in cashier
- Today and tomorrow — bookings guaranteed (for dashboard)
"""
async def run_demo(session: AsyncSession):
    tenant   = make_tenant(session)
    doctors  = [make_doctor(session, tenant) for _ in range(4)]
    services = [make_service(session, tenant) for _ in range(8)]
    patients = [make_patient(session, tenant) for _ in range(30)]

    # Schedule for entire month
    month_start = date.today().replace(day=1)
    all_slots = []
    for doctor in doctors:
        slots = make_schedule_for_month(session, doctor, month_start)
        all_slots.extend(slots)

    # IMPORTANT: today and tomorrow — guaranteed non-empty
    today    = date.today()
    tomorrow = today + timedelta(days=1)
    priority_dates = {today, tomorrow}

    priority_slots = [s for s in all_slots if s.slot_datetime.date() in priority_dates]
    other_slots    = [s for s in all_slots if s.slot_datetime.date() not in priority_dates]

    # Fill priority days first
    for slot in random.sample(priority_slots, min(len(priority_slots), 12)):
        if slot.is_available:
            booking = make_booking(session, slot, random.choice(patients), random.choice(services))
            make_transaction(session, booking)

    # Then other days — ~40% fill rate
    bookings_count = int(len(other_slots) * 0.4)
    for slot in random.sample(other_slots, min(len(other_slots), bookings_count)):
        if slot.is_available:
            booking = make_booking(session, slot, random.choice(patients), random.choice(services))
            make_transaction(session, booking)

    await session.commit()
    print(f"Demo seed complete: {len(doctors)} doctors, {len(patients)} patients")
```

### `seeds/prod.py` — clean start on production

```python
"""
PROD SEED: only what is necessary for the system to function.
Does NOT create: patients, bookings, transactions, demo doctors.
Creates: roles, default settings, service categories.
Idempotent: can be run repeatedly without duplication.
"""
async def run_prod(session: AsyncSession):
    # Roles — only if they do not exist
    for role_name in ["owner", "admin", "doctor", "receptionist"]:
        exists = await session.scalar(
            select(Role).where(Role.name == role_name)
        )
        if not exists:
            session.add(Role(name=role_name))

    # Default service categories
    default_categories = [
        "Therapy", "Surgery", "Orthodontics",
        "Prevention", "Prosthetics", "Implantology"
    ]
    for cat_name in default_categories:
        exists = await session.scalar(
            select(ServiceCategory).where(ServiceCategory.name == cat_name)
        )
        if not exists:
            session.add(ServiceCategory(name=cat_name))

    # Default settings
    defaults = {
        "booking_reminder_hours": "24",
        "slot_duration_minutes": "30",
        "working_hours_start": "09:00",
        "working_hours_end": "20:00",
    }
    for key, value in defaults.items():
        exists = await session.scalar(
            select(Setting).where(Setting.key == key)
        )
        if not exists:
            session.add(Setting(key=key, value=value))

    await session.commit()
    print("Prod seed complete: roles, categories, settings")
```

---

## DIAGNOSTICS: WHY DAYS ARE EMPTY

If after seed specific days are empty — the cause is always one of four:

| Symptom | Cause | Fix |
|---------|---------|---------|
| Today/tomorrow empty | No date prioritisation | Explicitly fill `priority_dates` first |
| Entire month empty | Schedule created, Booking not created | Check order in graph |
| Bookings exist but doctor is empty | FK doctor_id = None | `session.flush()` after make_doctor |
| Names = UUID | Field taken from id instead of full_name | Check what is passed to display |

---

## RULES FOR @DEV

```
□ No INSERT in Alembic migrations except system constants
□ Each factory calls session.flush() + session.refresh() after add()
□ Creation order strictly follows dependency graph
□ External API stubs — with STUB_ prefix or demo+
□ Today and tomorrow always filled in demo-seed
□ prod-seed is idempotent: repeated run does not create duplicates
□ Faker for project locale — no generic placeholder names
```

---

## FILE STRUCTURE

```
scripts/
  seeds/
    __init__.py
    factories.py      # entity factories (imported by smoke/demo)
    stubs.py          # external service stubs
    smoke.py          # minimum set
    demo.py           # full demo set
    prod.py           # production init
    run.py            # single entry point: python run.py [smoke|demo|prod]
```

`run.py`:
```python
import sys, asyncio
from seeds.smoke import run_smoke
from seeds.demo  import run_demo
from seeds.prod  import run_prod
from db import get_session  # your session factory

MODE_MAP = {"smoke": run_smoke, "demo": run_demo, "prod": run_prod}

async def main():
    mode = sys.argv[1] if len(sys.argv) > 1 else "smoke"
    if mode not in MODE_MAP:
        print(f"Mode '{mode}' unknown. Use: smoke | demo | prod")
        sys.exit(1)
    async with get_session() as session:
        await MODE_MAP[mode](session)

asyncio.run(main())
```

---

Reference: roles/ROLE_DEV.md · roles/ROLE_ARCH.md · roles/STACK_SELECTION.md
Version: 1.0 | 2026-03
