# TPF_MASTER — Frontend Tech Passport for Business OS (master document)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend: tech passports by functionality and UI.  
> **Purpose:** Single source of UI/UX rules for the Business OS admin panel. Every page and every significant endpoint is accounted for within modules. AI developers must cross-reference this document and the relevant TPF_MODULE_*.

**Sources:** `TECH_PASSPORT_FRONTEND_UI_LOGIC.md`, `REV_RAG_MAP_INNOVATIONS.md`, `REV_CRITERIA_IMPLEMENTATION.md`, `BUSINESS_LOGIC_V2.md`, `ARCH_FRONTEND_BUSINESS_OS_UX.md`.

**Connections:** Module details in `TPF_MODULE_DASHBOARD.md`, `TPF_MODULE_OMNICHAT.md`, `TPF_MODULE_CRM.md`, `TPF_MODULE_SCHEDULE.md`, `TPF_MODULE_TASKS.md`, `TPF_MODULE_FINANCE.md`, `TPF_MODULE_LOYALTY.md`, `TPF_MODULE_FORMS.md`, `TPF_MODULE_ENTITIES.md`, `TPF_MODULE_PWA.md`, `TPF_MODULE_SHELL.md`.

---

## Part I. Universal Laws

### 1.1. Relational Integrity

- **Task (Task entity):** The creation form must include: `assignee_id` (Assignee), `due_date` (Due date), optionally `linked_entity` (Patient, Lead, Booking). Without these, the task is not created.
- **Financial transaction:** The form must include: `cashbox_id`, `amount`, `type` (Income/Expense), `category`. Otherwise the transaction is not created.
- **General rule:** No form creates "dangling" entities without the minimum required relationships. Validation on both frontend and backend.

### 1.2. Drawers instead of Modals

- **Modal** is allowed only for quick confirmations: "Are you sure you want to delete?", "Save changes?".
- **Creating and editing** any entities (Service, Doctor, Task, Booking, Patient, Lead, Cash Register, Form Template) — only in `<Drawer position="right" size="lg">`. This preserves the context of the table/calendar.
- Specific sizes: `size="md"` for simple forms, `size="lg"` for tabbed entity cards.

### 1.3. Data Density

- Tables: compact (`Table` with `verticalSpacing="sm"`), no unnecessary padding.
- Each table row has an `ActionMenu` button (three dots) on the right with quick actions: Edit, Copy, Delete + contextual actions (Send link, Move to rejected, Send form, etc.).

### 1.4. Empty States

- If there is no data (`length === 0`), do not show an empty white screen. Always show the `<EmptyState>` component: icon (e.g. size=64, stroke=1), heading, brief description of the section's value, main CTA button (e.g. "Create first task").

### 1.5. Zero-Click Context

- Use `HoverCard` and `Tooltip` (Mantine). When hovering over a patient's or doctor's name anywhere in the system, show a mini-card: phone, LTV, date of next visit (if the API returns this data).

### 1.6. Keyboard First

- `useHotkeys` (Mantine): `Cmd+K` / `Ctrl+K` — global search (Spotlight); `Cmd+Enter` — send message (in chat); `Escape` — close the open drawer or clear focus.
- In chat: `Cmd+J` / `Ctrl+J` — focus on chat search.

### 1.7. Optimistic UI

- Wherever there are mutations via React Query (drag in calendar, changing lead status, marking a task as done), use optimistic updates: UI changes instantly before the API response; on error — rollback and notification.

### 1.8. Loading without spinners

- Full-screen `<Loader />` is forbidden. Instead — `<Skeleton>`, repeating the outline of the future content (table, cards, text block). This creates an illusion of speed and reduces cognitive load.

---

## Part II. App Shell

*Details: `TPF_MODULE_SHELL.md`.*

- **Architecture:** Bipartite structure: contrasting Sidebar (navigation) + light Main (workspace). No global Header.
- **Sidebar:** Dark background (`dark.8`), light text/icons. Collapse button: 260px ↔ 80px width (icons only). Groups: OPERATIONS (Dashboard, Schedule, Chat), BUSINESS (CRM, Finance, Loyalty), SYSTEM (Settings, Team).
- **Workspace:** Background `gray.0`. Content wrapped in `Paper` with a thin border and `xl` radius.
- **Context Bar:** At the top of each page — page title and main action buttons (e.g. "New booking", "Create task"). Breadcrumbs when needed.
- **Spotlight (Cmd+K):** Global search: navigate to sections, search for patients and bookings. Optional: "Ask AI" tab (AI Command Line) — text query to an AI agent.

---

## Part III. Enriched Entity Card Matrix (Entity Tabs)

*Details: `TPF_MODULE_ENTITIES.md`.*

Every entity card in a Drawer is built on tabs (`Tabs`). Three fields in one form — unacceptable for Business OS level.

### 3.1. Patient Card (Patient 360°)

- **Header:** Avatar, full name, phone, email, tags (VIP, Cancellation-prone), LTV, loyalty/cashback balance.
- **Tabs:** [General] (DOB, gender, category, consents, UTM); [Bookings] (visit table: date, doctor, service, status, amount, NPS); [Finance] (payments, refunds, memberships); [Memberships] (purchased packages, Family Sharing — "+ Add family member"); [Medical Record/Notes] (protocols, files); [Communications] (notification history, link to chat).

### 3.2. Booking Card

- **Tabs:** [Details] (patient, doctor, room, time, status); [Services & Receipt] (multiple services, discount, bonuses, cash register); [Consumables] (per service tech-card + manual adjustment); [Tasks] (tasks linked to the visit).

### 3.3. Employee/Doctor Card

- **Tabs:** [Profile] (full name, specialisation, contacts, colour in calendar, role); [Schedule] (2/2, even/odd, individual hours); [Salary] (flat rate, % of services, % of cross-sales); [Services] (checkboxes — which services they can perform).

### 3.4. Service Card

- **Tabs:** [Description] (name, category, price, duration, colour); [Practitioners] (Many-to-Many with doctors); [Tech Card] (products and quantities); [Online Booking] (available or not, description, whether prepayment is required).

---

## Part IV. Modules (brief overview by pages and endpoints)

Each module is detailed in a separate TPF_MODULE_*. Here — only a summary and mapping to routes/API.

### 4.1. Dashboard (Action Center)

**Page:** `/admin` (or home after login).  
**Document:** `TPF_MODULE_DASHBOARD.md`.

- Top row: 4 metrics (Bookings today, Revenue, New leads, NPS/Cancellations) with dynamics (% vs yesterday/week) and Sparkline when needed.
- Left column (~60%): Attention Feed — overdue tasks, unread chats, cancellations, stock/AI alerts; each card has action buttons ("Offer to waitlist", "Open checkout", etc.).
- Right column (~40%): today's schedule timeline (booking cards).

**Key endpoints:** aggregates for metrics, `GET attention-feed` (or equivalent), `GET schedule` for today.

### 4.2. Schedule (Smart Calendar)

**Page:** `/admin/schedule`.  
**Document:** `TPF_MODULE_SCHEDULE.md`.

- Grid by doctors and days. Drag-and-Drop of bookings between slots/doctors (optimistic update).
- When creating a booking: optionally "Ghost Slots" (AI-highlighted ideal windows).
- Side panel: waitlist; dragging from the waitlist to a slot creates a booking.

**Key endpoints:** `GET admin/clinics/{id}/schedule`, `PUT/POST admin/bookings`, waitlist list + create booking from waitlist.

### 4.3. OmniChat (Smart Hub)

**Page:** `/admin/chat` (or equivalent).  
**Document:** `TPF_MODULE_OMNICHAT.md`.

- Three columns: chat list (Smart Inbox with badges and filters), conversation window (AI Suggestions above input field, Rich Bubbles for payments, Action Bar: Book, Invoice, Questionnaire, AI on/off), right — client context (profile, funnel stage, next visit, internal notes). "Create booking" button opens a Drawer with pre-filled phone number.

**Key endpoints:** chats, messages, suggest-replies (AI), payments (status), patient, lead stage, next booking.

### 4.4. CRM & Sales (Kanban)

**Page:** `/admin/crm`.  
**Document:** `TPF_MODULE_CRM.md`.

- Horizontal scroll with columns (LeadStage). Lead cards: name, budget, source, contact date, "AI Active" tag. Drag-and-Drop between columns. Column header — sum (e.g. "Thinking (5) — £15,000"). Lead Rotting: card highlights if stuck in one stage too long. Click on a card — Drawer with chat and "Generate prepayment link" button.

**Key endpoints:** lead_stages, lead_cards (CRUD + PATCH stage), aggregates per stage.

### 4.5. Tasks (Task Manager)

**Page:** `/admin/tasks`.  
**Document:** `TPF_MODULE_TASKS.md`.

- Modes: Table and Kanban. Card: title, priority (badge), assignee avatar, date (red if overdue). "AI Tasks" block with "Take into work" button. In card — micro-actions (call, send link). Mission Control screen: Attention Feed + My Focus (personal tasks by SLA, Time-Bomb when waiting too long).

**Key endpoints:** tasks (list, CRUD, filter by source=ai, assignee), claim from feed.

### 4.6. Finance & Inventory (ERP)

**Page:** `/admin/finance` (and subsections).  
**Document:** `TPF_MODULE_FINANCE.md`.

- Cash registers: table (name, balance), Deposit/Withdraw/Transfer buttons. Transaction form — mandatory fields (register, amount, type, category). In service card — "Consumables" tab (tech card). When completing a visit — Checkout Hub: receipt, cash register selection, materials write-off (accordion with adjustment option).

**Key endpoints:** cashbox, transactions, service consumables, booking complete + payments.

### 4.6b. Memberships & Loyalty (Loyalty Engine)

**Page:** `/admin/loyalty`; "Memberships" tab in Patient Drawer; Checkout Hub; "Money in the air" block in Finance; PWA — "My Memberships".  
**Document:** `TPF_MODULE_LOYALTY.md`.

- **Digital Pass (PWA):** Membership cards in Apple Wallet style (progress, expiry date), "Book using membership" button → wizard with filtered package services; checkout "Paid by membership".
- **Family Sharing:** In Patient Drawer, "Memberships" tab, "+ Add family member" button (Spotlight search over patients); linked members see the card with "Access: [owner name]" label.
- **Auto-Checkout:** In Checkout Hub when a matching package is available — checkbox "Use 1 visit from membership"; complete with `use_subscription_id`.
- **Liability Dashboard:** In Finance, "Money in the air" block (Unearned Revenue) — total amount of unearned memberships.

**Key endpoints:** patient/loyalty/subscriptions, booking checkout-info (eligible subscriptions), complete with use_subscription_id, admin/loyalty/packages/{id}/family, admin/clinics/{id}/finance/liability.

### 4.7. Forms (Paperless)

**Page:** Settings section or separate `/admin/forms`.  
**Document:** `TPF_MODULE_FORMS.md`.

- List of form templates, "Create template" button. In patient or booking context menu — "Send form": generate a unique link and send via WhatsApp/SMS.

**Key endpoints:** form_templates CRUD, send form link (patient/booking, template).

### 4.8. Analytics & Marketing

- Metrics (budget, ad revenue, CAC, ROMI), funnel (traffic → leads → bookings → payment), UTM campaign table with drill-down in Drawer (patients by source), AI Marketing Advisor (text insights).

### 4.9. Attention Feed & Mission Control

- Attention Feed (event types, "Take into work" buttons), My Focus (tasks by SLA, Time-Bomb), AI Supervisor Summary for the owner.

### 4.10. Retention (Smart Retention Engine)

- AI segments, personalised offer generation, omnichannel cascade (WA → Push → SMS), campaign ROI (funnel to checkout).

### 4.11. Omni-Vault (media & export)

- Media gallery (Masonry), filters by type and date. Hovering over a file card — overlay with client avatar, channel icon (TG/WA), date.
- Voice messages: displayed as waveform (wavesurfer.js or equivalent), auto-transcription block below the player.
- Data Export Builder (drag columns, preview, Excel/CSV), Full Backup (button → progress → link in Telegram).

### 4.12. PWA (Patient App)

**Document:** `TPF_MODULE_PWA.md`.

- Bottom Navigation, Next Visit Ticket (ticket with QR), Stories Bar, Booking Wizard (visual doctor selection, date carousel), Digital Wallet in profile, Pull-to-Refresh.

---

## Part V. Micro-interactions and system feel

- **Premium Empty States:** Illustration + clear text + one main button; not an empty table.
- **Smart Spotlight:** Cmd+K is not just navigation, optionally "Ask AI" (query to AI agent).
- **Skeleton Loaders:** On all loading pages — skeleton following the content shape.
- **Optimistic Updates:** Drag, status change, message sending — instant UI response with rollback on error.

---

## Part VI. Backend connection and implementation plan

- **Contracts:** For each new screen or action in TPF_MODULE_*, the endpoint used is specified (see FUNCTIONAL_MAP_CURRENT, TECH_PASSPORT_BACKEND).
- **Priorities and criteria:** Follow `REV_CRITERIA_IMPLEMENTATION.md` (priority P0–P5, quality checklist, alignment with V2).
- **RAG:** Full innovation map and binding to pages/endpoints — in `REV_RAG_MAP_INNOVATIONS.md`.

---

*This document is the "bible" of UI functionality for Business OS. Detailed breakdown per module — in TPF_MODULE_* files. During implementation, reference TPF_MASTER and the relevant TPF_MODULE_* in DEV_PROMPTS and tickets.*
