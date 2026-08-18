# TPF_MODULE_TASKS — Kanban Tasks (Prod Standard 2026)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> Prefix `TPF_` — Tech Passport Frontend. Module tech passport.
> Connections: `TPF_MASTER.md`, `docs/artifacts/ARCH_TASKS_NEXT.md`, `docs/TECH_PASSPORT_FRONTEND_UI_LOGIC.md`.

---

## 0. Design Reference and Visual Standard

**Reference:** Linear Issues — density + WIP indicators; Jira (visual column density only)
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

Tasks are the team discipline tool. The manager looks at the board and must instantly understand: where is the overload, where is the overdue, what needs my attention.

Three key feelings:
1. **WIP limit visible in column header** — "7/5" in red when overloaded, no need to count
2. **Overdue tasks are unmissable** — red date, not small text
3. **Bulk action in 2 clicks** — select multiple, change status

Frequent actions: move a task, take an AI task into work, view details.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] WIP in column header: "current/limit", red on overflow
- [ ] Overdue date: text c="red.6", fw={600} — visible without hover
- [ ] Blocked badge: orange.0 bg + orange.5 border + "Blocked"
- [ ] Bulk checkbox: appears on card hover
- [ ] Drag handle: visible on hover, grip icon
- [ ] Task modals: centred (GlassModal), not Drawer
- [ ] Columns aligned — no "staircase" from differing card heights

---

## 1. Purpose

`/admin/tasks` — single production kanban for clinic task execution:

- task flow by stage (manual + AI + system);
- team load control (WIP);
- time control (SLA/Aging);
- managed transitions (workflow rules);
- transparent change history (audit trail).

---

## 2. UI and Interactions

### 2.1. Layout

- Single kanban without a side context panel, with strict column alignment.
- Separate compact filter/swimlane: **"Awaiting my approval"**.
- Quick filters: assignee, priority, due date (`all/today/overdue`).
- Bulk panel: mass status change for selected cards.

### 2.2. Task card

- Fields: title, priority, due date, assignee, AI mark (if applicable).
- States:
  - `blocked` (visual + badge + reason in details);
  - `overdue` (SLA expired);
  - `aging` (in progress too long, per threshold).
- Controls:
  - drag handle for mouse;
  - checkbox for bulk;
  - keyboard move `Alt+ArrowLeft/Right`.

### 2.3. Columns and statuses

- Base statuses: `open`, `in_progress`, `on_hold`, `review`, `done`, `cancelled`.
- Additional statuses from API supported dynamically.
- Each column displays:
  - task counter;
  - WIP (`current/limit`, if limit is set);
  - SLA overdue;
  - Aging 48h+.

### 2.4. Modals/details

- Centred modals (`GlassModal`) as the single standard shell.
- Task details must include workflow controls:
  - `blocked / blocked_reason`;
  - `done checklist` (confirming completion criteria).

---

## 3. Kanban Hardening Pack (mandatory minimum)

### 3.1. WIP limits

- Limits set per column.
- On overflow:
  - column is highlighted as overloaded;
  - moving new cards into the column is blocked (except internal reordering).

### 3.2. In-column sorting

- Drag-drop to position within a column is supported.
- Card order stored as rank-map on the frontend (locally) until a server-side `rank/order` contract is available.

### 3.3. Transition rules

- Transition to `done` is allowed only if:
  - the completion checklist is checked;
  - the task is not in `blocked`.
- Rule violations display a clear message and cancel the transition.

### 3.4. SLA / Aging

- SLA overdue: past `due_at`.
- Aging: exceeding the lifecycle duration threshold (e.g. 48h+ since `created_at`).
- Metrics shown at column level.

### 3.5. Blocked state

- A task can be manually blocked.
- A `blocked_reason` is stored for blocked tasks.
- Blocked tasks cannot be moved to `done`.

### 3.6. Bulk and quick filters

- Bulk: select multiple cards and mass-apply a status.
- Quick filters mandatory at page level:
  - assignee;
  - priority;
  - due segment.

### 3.7. Audit trail

- Any transition between statuses is recorded in the log:
  - `task`, `from`, `to`, `timestamp`.
- Until a backend audit endpoint is available — log is stored in UI session.

### 3.8. Keyboard accessibility

- Mouse-free accessibility is mandatory:
  - focus on a card;
  - move between adjacent columns via hotkeys.

---

## 4. API contract and current limitations

### 4.1. Endpoints used

- `GET /v1/admin/tasks`
- `POST /v1/admin/tasks`
- `PATCH /v1/admin/tasks/{id}` (status)
- `POST /v1/admin/tasks/{id}/claim`
- `GET/POST /v1/admin/tasks/{id}/comments`

### 4.2. Temporary frontend compensation until backend extension

- `rank/order`, `blocked`, `blocked_reason`, extended workflow audit not yet in API.
- Until backend contract is fixed, UI-local storage/session is used for:
  - card order;
  - blocked/checklist state;
  - audit feed.

---

## 5. Recommended next backend phase

1. Add server-side fields to the task model:
   - `rank`, `blocked`, `blocked_reason`, `stage_entered_at`.
2. Add server-side status transition rules (single source of truth).
3. Add a transition history / timeline endpoint.
4. Add server-side WIP policies per clinic/team.

---

## 6. Acceptance criteria

- Kanban is aligned, cards have no "staircase".
- All task modals open centred.
- Buttons/actions in modals always accessible via scroll.
- DnD works between all stages and within a stage.
- Transitions to `done` are validated by rules.
- WIP/SLA/Aging/Blocked/Bulk/Audit/Keyboard work without regressions.
