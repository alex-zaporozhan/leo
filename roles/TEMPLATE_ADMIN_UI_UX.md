# TEMPLATE: design and UX for admin panel (operational screens)

> **Read `roles/INTERFACE_CRAFT_CANON.md` before using this template.** This file holds the **patterns** of an operational screen (what a table, a drawer, a form looks like here). The interface-craft canon holds the **capabilities** that make a console an instrument rather than a CRUD form: the I1–I12 inventory (command palette · inline edit · bulk select · **undo instead of confirm** · optimistic UI · saved views · contextual row actions · keyboard path · deep linking · no blocking loaders · fast search · empty states that teach), the craft of trees and repositories (§2), data density (§3), progressive disclosure (§5) and the **stiffness detector** (§7, ST1–ST12).
>
> A screen built from the patterns below and **none** of the capabilities above is correct, tidy — and miserable to use. That is the "prim console" failure, and it is not fixed by styling.

> **[v6.16] Token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md` (+ `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` for project values).** Geometry — `roles/LAYOUT_INVARIANTS.md`; module minimum — `roles/DOMAIN_STANDARDS.md`; module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`. §7/§8/§10 here — do NOT duplicate rules, they point to the canon. Render verification (equal-height/overflow/CLS/zero-shift) is performed by `roles/ROLE_QA_VISUAL.md` after @QA_ARCH. Micro-moments on operational screens — `roles/MOTION_AMBITION_DIAL.md` (MICRO mode).

> **Version:** 1.0 · **Target:** Business OS (admin `/admin` + PWA `/app`)  
> **Default stack:** Mantine v7 + `frontend/src/theme.ts` + `frontend/src/index.css`

---

## 0. Scope

| Zone | Which document leads |
|------|----------------------|
| **Admin panel `/admin`, PWA `/app`, operational screens** | **This file** + `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` + `roles/DOMAIN_STANDARDS.md` |
| **Marketing: `/`, landings, promo** | `roles/TEMPLATE_DESIGN_UX.md` |

Rule: marketing visual patterns (glass hero, decorative backgrounds) are not transferred to the admin panel as the norm.

---

## 1. Template purpose

The template defines how to design and build operational screens so that:
- the user solves the task in 1–3 actions;
- the UI is dense, readable, and predictable;
- @DEV does not improvise design behaviour on the spot.

---

## 2. Admin Design Gate (before development)

Before creating/changing an admin screen, record:

```
□ User role (who works on this screen)
□ Main screen task (1 sentence, an action)
□ Screen type (Schedule / Finance / CRM / Dashboard / Analytics / Settings / Loyalty)
□ Critical scenario A→B (what the user must complete without workarounds)
□ API/data constraints (ARCH_*.md)
□ UI mode: local polish or @DESIGN needed (SPEC/VERDICT)
```

---

## 3. Base admin screen composition

### 3.1. Shell
- Dark sidebar for navigation.
- Light work zone (`main`) for data.
- Contextual bar inside the work zone instead of a heavy global header.

### 3.2. Layout
- Data in priority: tables/panels/kanban without visual noise.
- Level 0 background: neutral light.
- Level 1 cards: white with micro-border.

### 3.3. No global page scroll on heavy screens
- For chats/calendars/kanban: scroll in content columns via `ScrollArea`.
- Sticky column/section headers preserve context during scrolling.

---

## 4. Decision patterns (set once and for all)

### 4.1. Drawer vs Modal
- `Drawer`: creating/editing entities, tabbed cards, history.
- `Modal`: only confirm/alert/one short action.

### 4.2. Table vs Cards vs Kanban
- Table: record comparison, sorting, filters, >5 fields.
- Cards: visual overview, <5 fields, mobile context.
- Kanban: status progression + drag-and-drop as the main action.

### 4.3. One Primary CTA
- One main `primary` per screen.
- Secondary actions via `default/light/subtle` or `ActionMenu`.

---

## 5. State Spec (mandatory for every data component)

```
Loading:
- Skeleton matching content shape
- Critical buttons disabled

Empty:
- EmptyState: icon + title + hint + CTA
- For filter "nothing found" — text without CTA

Error:
- Alert/Toast in plain language
- "Retry" button
- no stack trace in UI

Success:
- form reset
- Drawer closed (if scenario is complete)
- invalidate/refetch executed
```

---

## 6. Data Integrity and UX invariants

- Do not show UUIDs in UI.
- Long strings: `truncate` + tooltip/title.
- Destructive actions — by action class (`roles/INTERFACE_CRAFT_CANON.md` I4): reversible single-entity → execute + Undo toast (5–10s); irreversible / money / PII / cross-user / wide-blast → confirm dialog (typed-confirm for high blast radius). A blanket confirm on every destructive action is the ST2 stiffness crime.
- Submit during mutation: `disabled`.
- After mutation: `invalidateQueries`.
- No `null.map()` — safe defaults for collections.

---

## 7. Visual norms (Crisp SaaS + Swiss Slate / Ink)

- **Palette/token precedence (C4):** the generic floor is `roles/FRONTEND_DESIGN_EXCELLENCE.md`; the **project's VISUAL_CONCEPT / passport overrides it for that project** (concrete hex live in the project passport `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md`, not hard-coded here). Universal `roles/` files carry no project hex — the passport is authoritative for a given project.
- Table text no smaller than 13px, content no smaller than 15px.
- Table density: at least 12px vertical spacing.
- Icons without labels — with tooltip.
- Status colour = meaning (success/warn/error/info) and consistency.
- Hover/active states on all interactive elements.

---

## 8. Module contracts (minimum)

| Module type | What must be present |
|------------|----------------------|
| Schedule | clickable slots, statuses, doctor filters, date navigation |
| CRM | search + debounce, client card, quick actions |
| Finance | cashiers, transactions, period totals, drill-down |
| Dashboard | KPIs + deltas, attention feed, quick actions |
| Tasks/Kanban | statuses, assignee, deadline, DnD with feedback |
| Settings | key reference tables and safe deletion constraints |

Source of verification: `roles/DOMAIN_STANDARDS.md`.

---

## 9. Accessibility and Keyboard

- Focus-visible on interactive elements.
- Escape closes drawer/modal.
- Tab order does not break the working scenario.
- Touch target at least 44×44 on mobile screens.

---

## 10. Performance for admin UI

- Animations: `transform` + `opacity` only.
- Lists of 1000+ rows: pagination/virtualisation.
- Debounce for search 300–500ms.
- Heavy blur effects — sparingly, without scroll degradation.

---

## 11. Definition of Done (admin screen)

- [ ] Admin Design Gate passed (§2)
- [ ] Pattern chosen and followed (§4)
- [ ] All 4 data states implemented (§5)
- [ ] Data/mutation invariants observed (§6)
- [ ] Visual norms observed (§7)
- [ ] Module standard in `DOMAIN_STANDARDS` verified (§8)
- [ ] Keyboard/A11y/perf baseline closed (§9–§10)

---

## 12. Transmission Template for @LEAD/@FRONTEND → @DEV

```markdown
HANDOFF @LEAD/@FRONTEND → @DEV

Context:   [screen, role, user task]
Input:     roles/TEMPLATE_ADMIN_UI_UX.md, roles/DOMAIN_STANDARDS.md#[type], docs/artifacts/ARCH_*.md, (optional) docs/artifacts/DESIGN_SPEC_*.md
Expected:  [specific files and UI scenarios]
Criterion: scenario A→B completes without workarounds; State Spec closed; npm/build/test OK
```

---

Reference: `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` · `roles/DOMAIN_STANDARDS.md` · `roles/TEMPLATE_MODULE_DEV.md` · `roles/ROLE_FRONTEND.md` · `roles/ROLE_DESIGN.md`
