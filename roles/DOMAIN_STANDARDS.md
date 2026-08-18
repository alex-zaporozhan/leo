# DOMAIN_STANDARDS.md

> **[v6.16] THE SINGLE CANON OF BUSINESS MINIMUM by page type** (what a screen must be able to do). Universal UI direction by module type — `roles/TEMPLATE_MODULE_DEV.md §2`. Project business logic — requirements + @BIZ → `docs/artifacts/BUSINESS_LOGIC.md`. TPF_* — a project example, not a universal canon.
# Universal dictionary of minimum business standard for SaaS products
# Source of truth for @ARCH when writing DEV_PROMPTS and for @QA_ARCH when auditing.
# Structure: Infrastructure standards → Module standards → Domain extensions

---

## How to use

**@ARCH:** when creating DEV_PROMPTS_*.md — copy the needed sections as `Domain Checklist`.
Order: first apply the applicable infrastructure standards (§0), then the required module type (§1–§7), then a domain extension if any (§8).

**@QA_ARCH:** when auditing — verify implementation sequentially: §0 → required module → domain.
A missing item = 🟡 or 🔴 depending on criticality.
If the domain is not described in §8 — apply only §0 + the module standard.

**@DEV:** when implementing — this is your minimum acceptance criteria. You cannot deliver below it.

---

## §0. INFRASTRUCTURE STANDARDS (applied to all modules)

These standards are checked **before** the module audit. A violation of any = 🔴 blocker.

---

### §0.1 Authentication and sessions

- [ ] Protected pages are inaccessible without authentication — redirect to login, not a 403 without explanation
- [ ] After session expiry — redirect to login preserving the intended URL
- [ ] Logout invalidates the session on the server (not only deletes the cookie on the client)
- [ ] JWT/token: lifetime is limited; refresh logic is documented
- [ ] Wrong password — error without revealing whether the account exists ("Incorrect email or password")
- [ ] Rate limiting on login: protection against brute force
- [ ] After N failed attempts — lockout or captcha (per product policy)

### §0.2 Role and access separation (RBAC)

- [ ] Every protected action checks the role on the **backend** — not only hides the button on the frontend
- [ ] UI hides inaccessible actions (buttons, menu items) — not only blocks the request
- [ ] On an attempt to perform an inaccessible action — a clear message, not a technical 403
- [ ] Roles are documented: who can do what — in `BUSINESS_ROUTES.md` or spine
- [ ] Destructive actions (delete, cancel, reset) — available only to the required roles
- [ ] Changing a user's role requires confirmation + is logged

### §0.3 Multi-tenancy

- [ ] Every DB query filters data by `tenant_id` / `organization_id` on the **backend**
- [ ] A user cannot obtain another tenant's data by substituting an ID in the URL or request body (IDOR)
- [ ] Test: request with a resource ID belonging to another tenant → 404 or 403, not foreign data
- [ ] Tenant filter is embedded in the DB query, not only in business logic (Row Level Security or equivalent)
- [ ] Creating any entity automatically binds `tenant_id` — do not rely on the client
- [ ] No cross-tenant leakage in aggregates and reports — every aggregate is filtered by tenant
- [ ] On switching between tenants (if applicable) — full state and cache reset

### §0.4 Payment and financial operations

- [ ] All amounts stored and transmitted as **Decimal / integer (minor units)** — not float
- [ ] Idempotency key on every payment operation — a repeated request does not create a second payment
- [ ] Payment status is obtained from the payment system's webhook/callback — not only from the API response
- [ ] Webhook verifies the signature — does not accept any incoming request
- [ ] A failed payment does not change the business status of the entity (order stays unpaid)
- [ ] Refund: a separate flow with confirmation, logging, and balance update
- [ ] Partial payment: documented — supported or explicitly forbidden
- [ ] Balance cannot go negative without explicit confirmation or product policy
- [ ] Financial operations are logged append-only: who, when, what, how much
- [ ] Destructive operations (delete a transaction) — confirm dialog + reason + role

### §0.5 Data and integrity

- [ ] UUIDs are never displayed to the user — always resolved to human-readable names
- [ ] Soft delete: entities are archived, not physically deleted (if product policy)
- [ ] A parent entity cannot be deleted if there are children — block with explanation
- [ ] Duplicates: warning when creating a record with a matching unique field (phone, email, code)
- [ ] `null` instead of `[]` is handled explicitly — no crashes on `.map()` or `.forEach()`
- [ ] Dates: single timezone on the backend (UTC), conversion to locale only on the frontend
- [ ] Numeric deltas ("vs yesterday"): always a number `+12%`, `-3%`, `0%` — a dash is not allowed

### §0.6 Observability and errors

- [ ] All API errors in a unified format: `{"detail": "...", "code": "SNAKE_CASE"}`
- [ ] 500 without structure is not allowed — every unexpected error is logged and returns a structured response
- [ ] Stack trace does not appear in the API response in production
- [ ] Critical operations (payment, deletion, role change) are logged with actor + timestamp
- [ ] Health check endpoint verifies DB and dependent services, not only "process alive"

---

## §1. SCHEDULE / CALENDAR (schedule, time slots, booking)

*Applicable to: clinics, salons, co-working spaces, rentals, classes, consultations — any business with a resource and a time slot.*

### Required actions
- [ ] A free slot is clickable → event creation form with pre-filled resource + time
- [ ] A booked slot is clickable → event card (participant, type, status, contact)
- [ ] Changing event status directly from the schedule without navigating to another page
- [ ] Filter by resource (multi-select)
- [ ] Date navigation: back / today / forward + date picker
- [ ] Colour-coded statuses are consistent across the entire module

### Required states
- [ ] Loading: Skeleton on slots, not a white screen
- [ ] No events on date: EmptyState + CTA "Create"
- [ ] No resources in filter: EmptyState with explanation

### Data
- [ ] Displayed: participant.full_name (or "Anonymous" if not set)
- [ ] Displayed: event_type.name / service.name
- [ ] Displayed: resource.full_name in the column/row header

### Business rules
- [ ] Cannot create an event in the past — or an explicit warning with confirmation
- [ ] Cannot create two events for one resource at the same time — backend validation
- [ ] Event duration blocks slots correctly (no overlap)

---

## §2. FINANCE / CASHBOX (finance, cashier, transactions)

*Applicable to: any business with a cashier, balances, transactions, payouts.*

### Cashiers / accounts
- [ ] List of cashiers/accounts with the balance of each
- [ ] Create button always visible (not only when list is empty)
- [ ] EmptyState if no cashiers: explanation + CTA
- [ ] Manual top-up / deduction directly from the cashier card
- [ ] Transfer between cashiers/accounts
- [ ] Transaction history with filter by type and period

### Transactions
- [ ] Table with filters: period, source, type (income/expense/transfer), category
- [ ] CSV export
- [ ] Summary total for the selected period (visible total)
- [ ] Drill-down: click on transaction → details (linked entity, description, author)

### Salaries / payouts (if applicable)
- [ ] List of recipients with accrued amount for the period
- [ ] Actions: accrue / pay out
- [ ] Payout history with filter

### Business rules
- [ ] Balance does not go negative without explicit confirmation
- [ ] All amounts — Decimal, not float (see §0.4)
- [ ] Destructive operations — confirm dialog + reason (see §0.4)
- [ ] Financial total accounts for cancellations and refunds — not just SUM without a filter

---

## §3. CRM / CLIENTS (clients, contacts, leads)

*Applicable to: any business with a client base — clinics, agencies, services, stores.*

### List
- [ ] Search by name, phone, email — with 300ms debounce
- [ ] Filters: status, date of last contact, tags, segment
- [ ] Quick actions from the row: contact, create event, open card
- [ ] Pagination or infinite scroll (not full load)

### Client card
- [ ] Interaction history with dates, types, amounts
- [ ] Financial balance: prepayment / debt (if applicable)
- [ ] Active subscriptions / packages (if applicable)
- [ ] Button to create a new event directly from the card
- [ ] Communication button (SMS / Email / messenger) directly from the card
- [ ] Tags and notes
- [ ] Date of birth visible (for loyalty programmes)

### Business rules
- [ ] Warning when creating a duplicate by a unique field (phone, email)
- [ ] Soft delete — a client cannot be physically deleted, only archived (see §0.5)

---

## §4. DASHBOARD (main dashboard, operations centre)

*Applicable to: any SaaS with an operations dashboard.*

### KPI cards
- [ ] Key metrics today: total + breakdown by statuses
- [ ] Daily financial metric: amount + delta to previous period (%, not a dash)
- [ ] New entities metric for the day + delta
- [ ] Cancellation / problem metric for the day + delta
- [ ] All deltas — numbers: `+12%`, `-3%`, `0%`. Dash = 🔴

### Attention feed
- [ ] Items requiring action (conflicts, overdue, unpaid)
- [ ] Each item is clickable → leads to the source of the problem
- [ ] EmptyState: "All clear" with explanation of where items come from

### Operations list (today / current period)
- [ ] Entity names — human-readable, not UUIDs
- [ ] Status with colour coding
- [ ] Click → entity card or the corresponding module

### Quick actions
- [ ] Module primary CTA accessible directly from the dashboard (create the main entity)
- [ ] Navigation to the main operational module in one click

---

## §5. ANALYTICS / REPORTS (analytics, reports)

*Applicable to: any SaaS with analytics.*

### Required reports (minimum)
- [ ] Financial report for a period (day / week / month / quarter / year)
- [ ] Resource load/utilisation (% usage)
- [ ] Top by the product's key metric (services, goods, channels)
- [ ] New vs returning clients / sessions
- [ ] Cancellation / loss reasons (if applicable)

### UI standard
- [ ] Filter: period + tenant/location (if multi-tenant)
- [ ] CSV export for every report
- [ ] Drill-down: click on a number → list of source records for that period
- [ ] Loading: Skeleton on charts, not a white screen
- [ ] All numbers are consistent with the operational screen for the same period and tenant

### Business rules
- [ ] Financial aggregates account for cancellations and refunds — not just SUM
- [ ] Time boundaries: `>=` start and `<` end, not inclusive on both sides (off-by-one)
- [ ] Timezone: conversion in one place, not spread between backend and frontend

---

## §6. SETTINGS (product settings, configuration)

*Applicable to: any SaaS with organisation settings.*

### Required sections
- [ ] Organisation: name, contacts, logo, main details
- [ ] Resources (staff / executors / premises): list, add, edit, deactivate
- [ ] Service / product types: list, prices, duration (if applicable), categories
- [ ] Working hours / resource availability (if applicable)
- [ ] Roles and access: who can do what

### Business rules
- [ ] Cannot delete a resource with active events/orders — block with explanation
- [ ] Cannot delete a service/product type if it is in use — block with explanation
- [ ] Deactivation (soft delete) — not physical deletion; deactivated does not appear in new forms
- [ ] Changing critical settings (roles, access) — is logged

---

## §7. LOYALTY / SUBSCRIPTIONS (loyalty, passes, subscriptions)

*Applicable to: clinics, fitness, education, services with packaged offerings.*

### Required elements
- [ ] List of packages/subscriptions with prices and contents
- [ ] Sell a package to a client → bind to client_id
- [ ] Remaining uses / days visible in the client card
- [ ] When providing a service — option to deduct from the package instead of payment
- [ ] Deduction history with dates and description
- [ ] Package expiry date with a visual indicator (green / yellow / red / expired)
- [ ] Client notification on expiry (if notifications exist)

### Business rules
- [ ] Cannot deduct from an expired package — block with explanation
- [ ] Cannot sell a package with zero remaining uses
- [ ] Package refund: a separate flow with balance restoration

---

## §8. DOMAIN EXTENSIONS

Specifics of particular business domains on top of the universal §1–§7 standards.
Add a new domain at the start of a project or after the first audit of a new business type.

---

### §8.1 Medical SaaS (clinic, dental, beauty)

**Schedule:**
- [ ] Resource = doctor/practitioner; participant = patient/client
- [ ] On booking cancellation — request reason (for cancellation analytics)
- [ ] Cannot book a patient with a doctor outside their working hours

**Finance:**
- [ ] Link transaction to a specific booking / visit
- [ ] Drill-down: transaction → patient and visit card

**CRM:**
- [ ] Field: date of birth (for greetings and discounts)
- [ ] Field: referral source (how they heard about us)
- [ ] History: visits, services, doctors, amounts

**Dashboard:**
- [ ] Bookings today: total + breakdown pending/confirmed/completed/cancelled
- [ ] Doctor load for the day (% of occupied slots)

**Analytics:**
- [ ] Doctor load (% of occupied slots for the period)
- [ ] New vs returning patients
- [ ] Cancellation and no-show reasons

---

### §8.2 E-commerce / Marketplace

**CRM:**
- [ ] Field: order history with amounts, statuses, items
- [ ] Field: delivery addresses
- [ ] Segmentation: RFM (recency, frequency, monetary)

**Finance:**
- [ ] Refunds: separate tab linked to order
- [ ] Seller commissions (if marketplace)
- [ ] Reconciliation with the payment processor

**Dashboard:**
- [ ] GMV for day / week + delta
- [ ] Number of orders + cart conversion rate
- [ ] Top products by revenue

**Analytics:**
- [ ] Funnel: view → cart → payment → delivery
- [ ] Refunds: % and reasons
- [ ] Customer LTV

---

### §8.3 B2B SaaS (subscription-based, multi-organisation)

**Settings:**
- [ ] Organisation management: add members, assign roles, revoke access
- [ ] Billing: pricing plan, payment history, next charge date
- [ ] API keys: create, revoke, view last usage

**Dashboard:**
- [ ] Product MAU / DAU (if product is for a team)
- [ ] Usage vs plan limit

**Finance:**
- [ ] Subscription billing: automatic charge, retry on failure, grace period
- [ ] Invoice: download PDF, invoice history

---

## §9. UNIVERSAL UI STANDARDS (applied to all pages)

**Design tokens:** see `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` §7–9 if applicable to the project.

### State Matrix — mandatory for every component with data
```
Loading  → Skeleton (not Spinner, not a white screen)
Empty    → EmptyState: icon + text + CTA button
Error    → Toast/Alert + "Retry" button
Success  → form resets, Drawer closes, list refreshes
```

### Data Integrity
```
UUID in UI            → 🔴 BLOCKER
Dash instead of delta → 🟡 IMPORTANT
null instead of []    → 🔴 (crash on .map())
```

### Mutations
```
Submit button  → disabled={isPending}
POST/PUT/DELETE → onSuccess: invalidateQueries([...])
Destructive    → confirm dialog mandatory
Double-click protection → disabled or optimistic lock
```

### Visual
```
Minimum font size in tables   → 13px
Minimum font size in content  → 15px
Touch target (mobile)         → min 44×44px
Long text                     → truncate + title={fullValue}
Icon without label            → tooltip mandatory
```

### Navigation
```
Drawer/Modal       → closes on Escape + overlay click
Modal opening      → focus on first input
Destructive Drawer → "Cancel" button on the left, "Delete" (red) on the right
Breadcrumbs        → when nesting is 2+ levels
```
