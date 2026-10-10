# TPF_MODULE_ENTITIES — Enriched Entity Cards (Entity Tabs)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (Part III), `REV_RAG_MAP_INNOVATIONS.md` (section 9).

---

## 0. Design Reference and Visual Standard

**The standard to reach:** tabs + header with key metrics
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

An entity card is the knowledge centre for a client/doctor/service. The administrator opens it to quickly get the information needed or perform an action.

Three key feelings:
1. **Card header — instant identification** — name, LTV, status, tags — no need to read tabs
2. **Tabs as knowledge sections** — switch without closing the Drawer
3. **Action available from the card** — book, message, credit — without navigation

Frequent actions: view visit history, open chat, navigate to a booking.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Card header: avatar + full name + tags + LTV — no vertical scroll
- [ ] Tags (VIP, Debtor): badge variant="light", colour by meaning
- [ ] Active tab: accent underline, fw={600}
- [ ] Table in tab: Skeleton on load, EmptyState on empty
- [ ] HoverCard on doctor/patient name: phone + next visit + LTV
- [ ] Drawer size="lg" — enough space for tables inside tabs
- [ ] ActionMenu in card header: Print / Copy / Delete

---

## 1. Purpose

Every entity card in a Drawer is built on tabs (Tabs). Goal — data density at the level of Yclients/Bitrix24, without "empty" 3–5-field forms.

---

## 2. Patient (Patient 360°)

- **Header:** Avatar, full name, phone, email, tags (VIP, Debtor, Cancellation-prone), LTV, loyalty/cashback balance.
- **[General] tab:** Date of birth (with "Birthday soon" tag if applicable), gender, client category, personal data consents, source (UTM).
- **[Visits] tab:** Booking history table: date, doctor, service, status, amount, review (NPS).
- **[Finance] tab:** Payment history, refunds, bonus balance charges, memberships and remaining balance.
- **[Medical Record/Notes] tab:** RichText (TipTap or equivalent) for protocols; file attachments (x-rays, before/after).
- **[Communications] tab:** Notification history (SMS/Email/TG), quick link to chat with this client.

**Opening:** Drawer or page `/admin/patients/[id]`. API: patient CRUD, nested bookings, payments, notes, comms.

---

## 3. Booking

- **[Details] tab:** Patient (with quick new patient creation), doctor, room/chair, start/end time, duration, status (Waiting, Arrived, In chair, Completed, No-show).
- **[Services & Receipt] tab:** Multiple services, total, manual discount, bonus deduction, cash register selection for payment.
- **[Consumables] tab:** List per tech card of selected services with manual adjustment option.
- **[Tasks] tab:** Tasks linked to this visit.

**Opening:** Click on calendar slot or from booking list. API: booking CRUD, services, consumables, tasks.

---

## 4. Employee/Doctor

- **[Profile] tab:** Full name, specialisation, contacts, calendar colour, access role.
- **[Schedule] tab:** Work schedule configuration (2/2, even/odd, individual hours).
- **[Salary] tab:** Motivation scheme: shift rate, % of services, % of cross-sales.
- **[Services] tab:** Checkboxes — which services they can perform (Many-to-Many link).

**Opening:** Drawer from doctor list. API: doctor CRUD, working_hours, payroll policy, service_doctor.

---

## 5. Service

- **[Description] tab:** Name, category, base price, duration, calendar colour.
- **[Practitioners] tab:** Which staff members can perform it (Many-to-Many).
- **[Tech Card] tab:** List of products and quantities (consumables when providing the service).
- **[Online Booking] tab:** "Available for online booking" flag, client-facing description, whether prepayment is required.

**Opening:** Drawer from service list. API: service CRUD, service_doctor, consumables, online booking flags.

---

## 6. General rules

- All cards open in `<Drawer position="right" size="lg">`.
- Tab data loaded lazily or via a single request with nested collections; during load — Skeleton inside the tab.
- Each card has an ActionMenu in the header when needed (Print, Copy, Delete, etc.).
- Zero-Click Context: in "Patient"/"Doctor" fields on hover — HoverCard with phone, LTV, next visit (if API returns this data).
