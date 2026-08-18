# TEMPLATE_MODULE_DEV — Module development standard (for @ARCH and new projects)

> **[v6.16] §1.1 UI laws — canon in `roles/FRONTEND_DESIGN_EXCELLENCE.md` (tokens/patterns), `roles/LAYOUT_INVARIANTS.md` (geometry), `roles/DOMAIN_STANDARDS.md` (business minimum). Do not repeat here — reference only.** The unique value of this file is §2 (module typology) and §3 (module document skeleton): this is the universal direction for any new project.

> **Purpose:** Distillation of tech passports and architectural values. Used as a reference when designing **any new module** in the current and other projects.  
> **@ARCH must** keep this document in mind when planning module architecture and when creating ARCH_* and TPF_MODULE_* files (or equivalents).

**Sources (in this project):** `roles/FRONTEND_DESIGN_EXCELLENCE.md` (taste constitution — read first), `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` (NFR and delivery discipline), `roles/DOMAIN_STANDARDS.md` (page types and checklists), `roles/NONFUNCTIONAL_SCORECARD.md` (team KPIs — if maintained), `docs/artifacts/FRONTEND_PASSPORT_[PROJECT].md` (project tech passport).

---

## 1. Universal standards (applied to every module)

### 1.1. UI laws (mandatory)

| Standard | Rule | Verification |
|----------|---------|----------|
| **Visual Identity** | Before the first screen: name the reference (Linear/Stripe/etc.) and verify §1.3 in the project FRONTEND_PASSPORT. All decisions = aligned with the reference, not "as it comes out". | Reference named in module spec. |
| **Drawer > Modal** | Creating and editing entities — only in Drawer (position right, size lg/md). Modal — only Confirm/Alert. | Module spec explicitly states: "form in Drawer". |
| **ActionMenu in row** | In every table/list row on the right — a "three dots" button → Menu with actions (Edit, Delete + contextual). | For each list, menu items and endpoints are listed. |
| **EmptyState** | When the list is empty (`length === 0`) — component with icon, heading, description, and one CTA button. | Mockup/spec describes EmptyState for each empty state. |
| **Skeleton, not Loader** | Data loading — Skeleton matching content shape. Full-screen Loader is forbidden. | "Loading" section specifies Skeleton, not spinner. |
| **Statuses — Google Calendar style** | Status colours: light shade background (color.0) + left border (color.5). No badge-filled spanning the full card. | Status table with hex/tokens in spec. |
| **Relational Integrity** | Forms do not create entities without the minimum required relations (e.g. Task: assignee_id, due_date; Transaction: cashbox_id, amount, type, category). | API contract and form list required fields; backend returns 422 with details. |
| **Zero-Click Context** | Where people/counterparty names are displayed — on hover: HoverCard/Tooltip (phone, key metric). | Spec states where applied; if needed — lightweight API for summary. |

### 1.2. API and backend laws

| Standard | Rule | Verification |
|----------|---------|----------|
| **Contract first** | Every UI action relies on an explicit endpoint (existing or designed). Method and path are specified in ARCH_* or the module document. | Endpoint table in module document. |
| **Idempotency** | Operations with Optimistic UI (drag, status change) — idempotent PATCH/PUT or with `idempotency_key` for finances. | Recorded in ARCH. |
| **Tenant isolation** | All requests in a multi-tenant context carry tenant_id (from token/context). | Stated in contract and dependencies. |
| **Versioning** | API path with version (`/api/v1/`). Backward compatibility on changes. | Version in endpoint paths. |

### 1.3. System goals (what we maximise)

- **Operational efficiency:** Minimum clicks to result; context is not lost (Drawer); one screen — one control panel.
- **Data density and meaning:** Compact tables; enriched cards (tabs), not forms with 3 fields; context without navigation (HoverCard).
- **Proactivity:** Attention feed with action buttons on cards; SLA/Time-Bomb where appropriate; AI as assistant (hints, task pools).
- **Speed feeling:** Optimistic UI for mutations; Skeleton on load; Premium Empty States.

---

## 2. Module types and mandatory elements

When designing, identify the module type and apply the corresponding minimum.

### 2.1. List module (entity table)

**Examples:** Patients, Appointments, Doctors, Services, Leads (table), Tasks (table), Cashboxes, Form templates.

**Required:**

- Page with table (Data Density: `verticalSpacing="sm"`).
- Context Bar on top: heading + main action button (e.g. "Create task").
- In every row — ActionMenu with action set (minimum: Edit, Delete; plus contextual).
- Clicking "Create" / "Edit" opens **Drawer** with form or card (tabs for enriched entity).
- EmptyState when list is empty with CTA.
- Loading: Table Skeleton.
- Endpoints: GET list (with filters/pagination), GET by id (for Drawer), POST/PUT/DELETE; for each ActionMenu item — explicit endpoint or combination.

### 2.2. Entity card module (enriched)

**Examples:** Patient card, appointment, doctor, service, lead.

**Required:**

- Open in Drawer (position right, size lg).
- Structure: **Header** (avatar/key fields/tags/metrics) + **Tabs** (multiple tabs with logically grouped data). A "3-field form" is not acceptable at Business OS level.
- Tabs: minimum 2–4 (e.g. [Main], [History/Related], [Finance], [Notes]). Each tab — sub-tables, forms, or content bound to the entity.
- API: GET by id with nested collections or separate requests per tab; PUT/PATCH for editing; required relation validation.

### 2.3. Control panel module (three columns)

**Examples:** Chat (chats | conversation | client context), CRM (filters | kanban | lead Drawer), Tasks (filters | kanban/table | task Drawer).

**Required:**

- Layout: column 1 (list/filters) | column 2 (main content) | column 3 (details/context or Drawer on selection).
- In list column — status badges/indicators (e.g. "Awaiting reply", "AI handling"); filter pills if needed.
- Actions without leaving the screen: buttons in content or Action Bar (e.g. Appointment, Invoice, Form in chat) open Drawer or compact Modal.
- Hotkeys: Escape closes Drawer; Cmd+Enter to send, Cmd+J to focus search if needed.
- Endpoints: list with flags/aggregates, selected item details, actions (create appointment, send link, etc.).

### 2.4. Dashboard module

**Examples:** Main dashboard (metrics + attention feed + timeline).

**Required:**

- Top row: 2–4 metric widgets with value and dynamic (% or Sparkline).
- One or two content columns: event feed (Attention Feed) with action buttons on cards; timeline/today's list if needed.
- EmptyState for empty feed.
- Loading: widget and feed Skeleton.
- Endpoints: aggregates for metrics, feed (event list with types and payload for buttons).

### 2.5. Drag-and-drop module (kanban/calendar)

**Examples:** CRM Kanban, appointment calendar, tasks (kanban).

**Required:**

- Drag-and-Drop with **optimistic update**: UI changes before server responds; on error — rollback and notification.
- API: PATCH/PUT with identifier and new state (stage_id, slot, etc.); operation is idempotent.
- Column/day headers show aggregates (total, count) if needed.
- Clicking an item opens Drawer with entity card (per "entity card module" type).
- EmptyState for empty columns (or "Drop here" placeholder).

---

## 3. Module document skeleton (for @ARCH)

When creating a new module, fill in a document with the following structure. In this project the prefix `TPF_MODULE_` and link to `TPF_MASTER` are used; in another project you can use your own prefix (e.g. `MODULE_`, `SPEC_`).

```markdown
# [PREFIX]_MODULE_[NAME] — [Module Name]

> Link to master document and RAG/map (if any).

---

## 1. Purpose
One or two sentences: why the module exists, which control panel/task it closes. "The admin does not leave the screen to…".

---

## 2. Screen structure
- Layout (columns, blocks). For list — table + Context Bar. For control panel — three columns. For card — Header + Tabs.
- List of blocks/components with a brief description (what is shown, which buttons).
- Where Drawer, EmptyState, ActionMenu.

---

## 3. Endpoints
| Data / Action | Method/path | Note (required fields, idempotency, tenant) |

---

## 4. UI rules
- Module-specific (e.g. "Escape closes Drawer", "Rich Bubbles for payments").
- Reference to universal laws (Drawer, EmptyState, Skeleton, ActionMenu).

---

## 5. Links
- Page (path). Entities (domain objects). Relationship with other modules.
```

---

## 4. Module planning checklist (@ARCH)

Before finalising architecture and handing off to DEV, verify:

- [ ] **Module type identified** (list / card / control panel / dashboard / drag-and-drop) and the minimum from section 2 applied.
- [ ] **Drawer for create/edit** planned; Modal only for Confirm/Alert.
- [ ] **ActionMenu** for every list/table with list of actions and endpoints.
- [ ] **EmptyState** described for every empty state.
- [ ] **Loading** — Skeleton, not Loader.
- [ ] **Relational Integrity** for forms: required fields listed; backend validates and returns 422 with details.
- [ ] **API contracts** listed in module document (method, path, body, error codes).
- [ ] **Optimistic UI** for drag/status change: operation is idempotent, contract is recorded.
- [ ] **Tenant isolation** accounted for in requests.
- [ ] Module document filled per **skeleton** in section 3 (purpose, structure, endpoints, rules, links).

---

## 5. Reference examples (in this project)

| Module | Document | Reference for |
|--------|----------|------------|
| Control panel (three columns) | TPF_MODULE_OMNICHAT | Chat, operational environment, Action Bar, right-side context. |
| Entity card | TPF_MODULE_ENTITIES | Patient, Booking, Doctor, Service — tabs, no "short" forms. |
| List + CRUD | TPF_MODULE_TASKS, TPF_MODULE_FINANCE | Table, ActionMenu, Drawer, EmptyState, Relational Integrity. |
| Kanban | TPF_MODULE_CRM | Columns, header totals, Lead Rotting, Drawer on click. |
| Dashboard | TPF_MODULE_DASHBOARD | Metrics, Attention Feed with buttons, timeline. |
| Shell | TPF_MODULE_SHELL | Sidebar, Collapse, Context Bar, Spotlight. |

In another project you can create your own references using the same structure (module type → example document).

---

## 6. Connection to roles and artifacts

- **@ARCH:** When planning any new module — open this template; fill in the module document skeleton (section 3); run through the checklist (section 4); specify in ARCH_* the contracts and compliance with universal standards (section 1).
- **@LEAD:** When assigning a task for a new module — require from @ARCH a document per TEMPLATE_MODULE_DEV and a reference to it in DEV_PROMPTS.
- **In other projects:** Copy sections 1–4 and 3 (skeleton); adapt prefixes and examples (section 5); record in the @ARCH role the mandatory use when planning modules.

---

*End of template. This document is the module development standard; @ARCH always keeps it in mind when planning the architecture of any module.*
