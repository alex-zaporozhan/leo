# TPF_MODULE_SCHEDULE — Schedule (Smart Calendar)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.2), `REV_RAG_MAP_INNOVATIONS.md` (section 10).

---

## 0. Design Reference and Visual Standard

**The standard to reach:** Google Calendar — status colours + drag-drop layer; Yclients — doctor grid
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

The schedule is the heart of the operational day. The administrator spends 60% of their working time here. Every second of inconvenience multiplies over hours.

Three key feelings:
1. **Grid is instantly readable** — who is free, who is busy — without studying
2. **Drag works like a desktop app** — smooth, with ghost card, with instant rollback on error
3. **Statuses speak in colour** — waiting/in chair/completed distinguishable in 0.3 seconds

Frequent actions: create a booking (click empty slot), reschedule a booking (drag), view details (click card).

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Bookings: status colour = leftBorder 4px, NOT the whole card background
- [ ] Skeleton: mimics the grid (time rows + doctor columns), not abstract
- [ ] Drag ghost: semi-transparent ghost card when dragging
- [ ] Drop zone: highlights on hover (gray.1 background)
- [ ] Empty day: EmptyState "No bookings" + "New booking" button
- [ ] Slot height is proportional to service duration
- [ ] Grid does not scroll the whole page — only internal scroll

---

## 1. Purpose

Schedule grid with drag-and-drop of bookings between doctors and slots; when creating a booking — highlighting of "ideal" windows (Ghost Slots); waitlist in a side panel with drag-to-slot.

---

## 2. Screen structure

### 2.1. Main grid

- X axis: doctors (or rooms). Y axis: time (day/week as configured).
- Cells are slots. A booking appears as a card with patient, service, time.
- **Drag-and-Drop:** drag a card to another slot or another doctor. Instant UI update (Optimistic UI), then `PUT/PATCH` booking (doctor_id, slot). On error — rollback and notification.

### 2.2. Creating a booking

- "New booking" button or click on an empty slot opens a Drawer (or modal within the rules — Drawer preferred) with form: patient (with quick new patient creation), doctor, service, date, time.
- **Ghost Slots (optional):** when selecting a date/doctor, the system highlights "ideal" windows in pale green (to avoid 15-minute gaps). The algorithm can be on the backend (`GET schedule/suggest-slots`) or on the frontend based on occupancy data.

### 2.3. Side panel — Waitlist

- List of patients in the waitlist (name, phone, desired service/time).
- Action: drag a patient card from the panel to an empty slot in the grid → booking is created and the waitlist entry is marked as "taken" or deleted. API: create booking linked to waitlist_entry.

### 2.4. Clicking on a booking

- Opens the Booking entity card Drawer per TPF_MASTER (tabs: Details, Services & Receipt, Consumables, Tasks).

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Schedule grid | `GET /api/v1/admin/clinics/{id}/schedule` (date, period) | Doctors, slots, bookings. |
| Update slot/doctor | `PUT /api/v1/admin/bookings/{id}` or reschedule | Fields: doctor_id, appointment_date, appointment_time. |
| Create booking | `POST /api/v1/admin/bookings` | With patient_id or quick patient creation. |
| Slot suggestion | `GET /api/v1/admin/schedule/suggest-slots` (optional) | Params: clinic, doctor, date; returns "ideal" windows. |
| Waitlist | `GET /api/v1/admin/waitlist`, `POST .../bookings` (from waitlist) | List + create booking from waitlist entry. |

---

## 4. UI Rules

- Loading: Skeleton matching the grid shape (time rows, doctor columns).
- EmptyState on empty day: "No bookings for this date" with "New booking" button or "Add from waitlist".
- Drag uses @dnd-kit or equivalent; visual feedback during dragging.
- Escape closes the Booking/creation Drawer.

---

## 5. References

- Page: `/admin/schedule`. Entities: Booking, Doctor, Service, Patient, WaitlistEntry.
