# UI Logic Tech Passport (product frontend, Business OS)

> **[v6.16] Token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md` (single source).** §7/§9 here — do NOT duplicate tokens, they describe the behaviour/structure of Business OS screens. Geometry — `roles/LAYOUT_INVARIANTS.md`. Render verification — `roles/ROLE_QA_VISUAL.md`. On token conflict, FRONTEND_DESIGN_EXCELLENCE takes priority.

**Purpose:** canon of **behaviour and structure** for admin and PWA screens (data density, drawers, action menus, tabbed entities). Reduces risk of "empty" CRUD and domain desynchronisation.

**Universality:** this document sets norms for products of the **Business OS** class (operational admin + client application), not "this repository only". Route, zone and stack **facts** of a **specific** repo — in the project passport in the table below.

**Does not replace:**

| Document | Purpose |
|----------|--------|
| `roles/DOMAIN_STANDARDS.md` | Page-type checklists for @QA_ARCH |
| `roles/TEMPLATE_MODULE_DEV.md` | Module skeleton for @ARCH design |
| `roles/TEMPLATE_DESIGN_UX.md` | Visual system for **marketing** pages (landing, glass, hero) |
| `docs/artifacts/ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md` | Repository facts: routes, zones, stack |
| `roles/ARCH_FRONTEND_UI_LOGIC.md` | Repository: admin light theme, Mantine tokens, injection points (`theme.ts`, CSS, layout); **micro-norms §9** of this tech passport |
| `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` §5 (showcases), `roles/DOMAIN_STANDARDS.md` | NFR: ERP reporting showcases — not a UI spec; field/metadata contract with backend |

**Historical drafts with prompts and duplicates** — `docs/artifacts/archive/`; brief index `docs/archive/` — `docs/archive/README.md`. Programme **8.5+** — `docs/artifacts/85 plus/`.

---

## 0. Admin Shell (App Shell)

Target reference **Enterprise Pro-SaaS** (navigation separated from work):

- **Bipartite scheme:** contrasting **dark sidebar** (navigation) + **light main** (tables, forms, cards).
- Sidebar **collapse** to an icon strip; items grouped by domain (OPERATIONS, CRM, ERP, SYSTEM, etc.).
- **No heavy global header:** page title and primary action — in the **Context Bar** inside the work zone.
- **Spotlight / Cmd+K** — global search and quick navigation (as data grows).
- **Light work zone (main):** background is not "pure white", but **level 0** — neutral grey (`gray.0` / `#F8F9FA`), so **level 1** (cards, `Paper`/`Card`) reads as white "sheets" with a micro-border and optional light shadow. **Crisp SaaS** norms — **§7**; operational micro-style (scroll, cards, statuses, typography, buttons) — **§9**.

Actual layout and components — verify with `frontend/src/admin/layouts/AdminLayout.tsx` and the project passport; light theme tokens and file map — `roles/ARCH_FRONTEND_UI_LOGIC.md`.

---

## 1. Universal Interface Laws

1. **Relational integrity** — forms do not create "dangling" entities:
   - **Task:** mandatory `assignee_id`, `due_date`; optional link to patient/lead/appointment.
   - **Financial transaction:** `cashbox_id`, `amount`, `type`, `category`.
2. **Drawer > Modal for data** — `Modal` centred only for **Confirm / Alert**. Entity creation and editing — in `<Drawer position="right" size="md" | "lg">`, list context is preserved.
3. **Data density** — compact tables (`verticalSpacing="sm"`); each row has an **ActionMenu** (three dots) on the right with business actions.
4. **EmptyState** — when `length === 0`, not an empty screen: icon, heading, description, **one CTA** (see `.cursorrules` and DOMAIN_STANDARDS).
5. **Zero-click context** — patient/doctor names and key entities — `HoverCard` / `Tooltip` with phone, LTV, visit date where appropriate (no UUID in UI).
6. **Keyboard** — `Escape` closes Drawer; `Cmd/Ctrl+K` — Spotlight (when connected). **Omni Chat** (`/admin/omni-chat`, `AdminOmniChatPage`): `mod+J` — focus dialog search; `mod+Enter` — send message; `Escape` — close Drawer form/task; **`mod+shift+l`** — collapse/expand the **right** "Work Centre" inspector (does not fire in input fields by default). Canon: `ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md` §2.2.1.

7. **Owner reports (ERP / period analytics)** — do not mix with marketing visuals:
   - Backend may serve aggregates from a **showcase** (pre-aggregate) with fallback to a "raw" query; **portable** norms — `roles/DOMAIN_STANDARDS.md`; contract and facts for this repository — `docs/artifacts/ARCH_DEV_ERP_VITRINES_026.md`, `docs/artifacts/ARCH_PERF_ENGINE_L2_DEEP_2026.md` §6.
   - **UI:** same four states (Loading / Empty / Error / Success), see `ARCHITECTURE_EXCELLENCE_PASSPORT.md` §11. If the API returns a stale-showcase flag (e.g. `aggregate_stale`), an **unobtrusive** indicator or "refresh report" hint is acceptable (copy and placement — per product); **do not** show users internal table names, Celery internals or UUID unless necessary.
   - Screen-level finance/ROI detail — in `roles/TPF_MASTER.md` §4.6 / corresponding `TPF_MODULE_*`.

---

## 2. Modules (expected direction)

Briefly **what** a screen must do; API detail — in `docs/artifacts/ARCH_DEV_*` and backend contracts.

| Module | Essence |
|--------|------|
| **Dashboard** | Metrics; **Attention Feed** (~60%) + day timeline/cards (~40%). |
| **Omni-chat** | 3 columns: inbox + dialog + inspector ("Work Centre" + tabs); right column can collapse to an icon strip; actions without leaving the page. Grid, `fluid` main and hotkey facts — §2.2.1 of the project tech passport. |
| **CRM / Sales** | Kanban, DnD, stage totals; lead card → Drawer. |
| **Tasks** | Table and/or kanban; priority, deadline, entity link; AI task pool if available. |
| **Finance / ERP** | Cashboxes, transactions; service has a consumables tab (tech card). |
| **Forms** | Templates; send link from patient/appointment context. |

---

## 3. Enriched Entities (tabs in Drawer)

Rule: an entity card **does not reduce to 3 fields** — `Tabs` / related tables by domain.

### 3.1. Patient

Header: avatar, full name, contacts, tags, LTV, loyalty balance if available.

| Tab | Content |
|---------|------------|
| Main | DOB, gender, category, personal data consents, UTM |
| Appointments | Visit history |
| Finance | Payments, subscriptions, balances |
| Medical record / notes | Protocols, attachments |
| Communications | Notifications, quick link to chat |

### 3.2. Appointment (visit)

| Tab | Content |
|---------|------------|
| Details | Patient, doctor, slot, visit status |
| Services & bill | Multiple services, discounts, cashbox |
| Consumables | Per tech card ± manual edits |
| Tasks | Tasks linked to the visit |

### 3.3. Employee

Profile, schedule, motivation (if in product), permitted services.

### 3.4. Service

Description, price, duration; practitioners; tech card; online booking flags.

### 3.5. Checkout Hub

On visit completion: services/goods, payment (cashbox / subscription), consumables stock in `Accordion` if needed.

---

## 4. Omni-chat (in depth)

**Implementation fact in repository** (`AdminOmniChatPage`, see also `ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md` §2.2.1, `ARCH_FRONTEND_DESIGN_SYSTEM_MIDNIGHT.md` §5.1):

- **Inbox:** filters and row previews; meta-status (**OPEN**, last actor, AI) — **compact single line** smaller than name/phone; long names with ellipsis; row highlight neutral (not "primary for the full card").
- **Dialog field:** input, send, hide messages; quick record/form buttons per context.
- **Right column ("Work Centre"):** quick CRM / Schedule / Tasks links; tabs Client / Forms / Timeline / AI; can **collapse** to a vertical icon strip (`localStorage`), hotkey **`Ctrl+Shift+L` / `⌘⇧L`**.
- **Product goal** (as API becomes available): "waiting for reply" / AI badges, rich payment cards, identification and funnel in inspector — without leaving the page.

---

## 5. Micro-interactions and System Feel

- **TanStack Query:** for DnD and status changes — **optimistic** updates with rollback on error where possible.
- **Loading:** **Skeleton** following content outline, not fullscreen `Loader` for lists/tables.
- **Spotlight:** "AI question" mode extension only when an endpoint exists and @ARCH has decided (no fallback to imaginary APIs).

---

## 6. Patient PWA (`/app`) — target UX

Goal: feel like an **application**, not a "mobile website":

- Bottom navigation for key sections; safe-area for iOS.
- Upcoming appointment "ticket"; step-by-step booking wizard; feed/promotions — per product tasks.

Implementation only via `DEV_PROMPTS` + contracts; this section does not require all features "at once".

---

## 7. Admin Light Theme: Crisp SaaS / Enterprise (visual hierarchy)

**Purpose:** establish the **product** style for the light work zone (the feel of expensive professional software): layer depth, micro-contrasts, button hierarchy — without "flat" grey-white mush and without heavy solid fills on metrics. This is **not** a marketing glass landing (`TEMPLATE_DESIGN_UX.md`), but an **operational** interface.

### 7.1. Elevation (depth)

| Level | Purpose | Token / meaning |
|--------|------------|----------------|
| **0** | Application background in the main zone (`AppShell.Main`, body if needed) | `gray.0` (`#F8F9FA`) — **not** `#ffffff` |
| **1** | Cards, content panels, main "sheet" | `#ffffff`, mandatory **micro-border** `1px solid` on Mantine `gray-2` scale (or equivalent `rgba(0,0,0,0.06)`), otherwise white "floats" on grey |

Floating layers (modals, Drawer, dropdowns) use **layered** shadows from theme (§7.3), not blurred "blobs".

### 7.2. Borders and cards

- **`Paper` / `Card`:** by default with a **border** and meaningful `radius`; shadow — moderate (`sm` and above by intent), consistent with the theme's shadow palette. Goal — "cut paper" effect, not infinite flat.
- **Context bar, sticky table headers:** **light glass** is acceptable — semi-transparent white background + `backdrop-filter` + bottom border (class/style in global CSS, see repository passport).

### 7.3. Shadows (Crisp / layered)

- Avoid **default "blurry"** shadows; define **multi-layer** `xs`–`xl` values in `createTheme` (two `box-shadow` layers with small alpha) so modal and card borders are crisp.
- Specific strings for this repository — in `roles/ARCH_FRONTEND_UI_LOGIC.md` (single sync point with `frontend/src/theme.ts`).

### 7.4. Button hierarchy

- **Primary (1–2 per screen):** main action — `variant="filled"`, theme accent colour (in the target light redesign — **`indigo`** as `primaryColor`, if decided by @ARCH; until theme switch — current primary from code).
- **Secondary / cancel / filters:** `variant="default"` — white background, visible grey outline, `gray.7` text, on hover `gray.0` background; **do not** fill secondary actions with the same grey as the page background.
- **Light / soft accent (optional):** semi-transparent primary-shade background (`*.0`) + saturated shade text — for "Add"/secondary positive action without competing with the single main CTA.

### 7.5. Navigation (sidebar)

- Active item **not** a dirty grey fill across full width without meaning; target pattern — light brand-shade background (**e.g.** `indigo.0`) + text/icon **`indigo.6`** (or consistent theme pair), so the active state reads at first glance.

### 7.6. Dashboard and metrics

- Metric widgets — **white cards** §7.1 with trend-coloured icons/badges (green/red etc.), without **heavy** solid colour fill of the entire block (historical anti-pattern "one blue blanket").
- Attention Feed / timeline meaning and proportions unchanged (§2); only visual lightness changes.

### 7.7. Repository implementation

- Code points, @FRONTEND checklist and sync with Midnight/Graphite — **`roles/ARCH_FRONTEND_UI_LOGIC.md`**. Prompts and tasks — via `DEV_PROMPTS` / epics, not mandatory "HANDOFF @LEAD" blocks from drafts.

---

## 8. Roadmap / extended scope (not a norm without an epic)

Ideas at the level of **Marketing ROI drill-down**, **Retention Engine**, **Omni-Vault**, **Revenue Hunter**, **Family Pass / packages** are captured in `docs/artifacts/BUSINESS_LOGIC.md` (or equivalent) and `docs/artifacts/ARCH_DEV_*`, and enter code **only** with explicit `DEV_PROMPTS` and acceptance. Do not use old "HANDOFF @LEAD" inserts from archived files as mandatory specification.

---

## 9. Premium Micro-Design Codex (Mantine v7)

**Mandatory for @FRONTEND when laying out any product screens** (`/admin/*`, `/app/*`). Goal: the interface feels like a **premium desktop application**, not a loose 2010s website.

Connection to the rest of the tech passport: **§7** — light theme tokens and global theme; **§1–6** — modules and entities; **below** — specific JSX/Mantine patterns. On wording conflicts, priority goes to agreed **§7** + `frontend/src/theme.ts`, then §9.

### 9.1. "Desktop App" Rule (layout and scroll)

- **No global page scroll** on operational screens: chat, calendar (grid), kanban, dense tables — **prohibited** to scroll the entire page like a "long landing".
- **Implementation:** the root work-area container gets a height constraint, e.g. `style={{ height: "calc(100vh - …px)" }}` or a chain of `flex: 1` / `minHeight: 0` from `AppShell` to the target block. Inside, columns are wrapped in `<ScrollArea>` where only **content** scrolling is needed (message feed, table body, day card list).
- **Column headers** (chat header, calendar day header, kanban toolbar) — **outside** `ScrollArea` or with `position: sticky` + bottom border, so they don't "slide away" on scroll.

### 9.2. "Cut Paper" Rule (Crisp Cards)

- **Ban on dirty "cloud" shadows** on ordinary cards in list/dashboard flow.
- **Implementation:** card (`Paper`, `Card`) — **white** background (`bg="white"` / `#ffffff`), **thin** border `1px solid var(--mantine-color-gray-2)` (or `withBorder` + theme token), **minimal** shadow: target minimum — `shadow="xs"`; `sm` is acceptable if set globally in theme for `Paper` — but **not** `md`/`lg` on every card in the list.
- Shadows **`md` / `lg` / `xl`** — **only** for modals, `Drawer`, `Menu.Dropdown`, `Popover`, intentional hover-elevate on an interactive tile (see §7.3 and `ARCH_FRONTEND_UI_LOGIC.md`).

### 9.3. Colour Signal Rule (Status Colors)

- **Ban on heavy solid fill** of a status entity (appointment, task, calendar event) across the full card depth.
- **Implementation (Google Calendar style):**
  - Background: **0** shade of the status palette, e.g. `bg="orange.0"`, `var(--mantine-color-teal-0)`.
  - Accent: **left border** of **3–4px** at shade **5–6** of the same scale, e.g. `style={{ borderLeft: "4px solid var(--mantine-color-orange-5)" }}`.
  - Text: **`c="*.9"`** (e.g. `orange.9`) for the main content inside the block.
- Result: readability and a "professional" look without colour panels competing with text.

### 9.4. Micro-typography Rule (Data Hierarchy)

- **Field labels:** small, uppercase, with light tracking.
  - Example: `<Text size="xs" tt="uppercase" c="dimmed" fw={600} style={{ letterSpacing: 0.5 }}>Visit date</Text>`
- **Values:** contrasting, body text.
  - Example: `<Text size="sm" c="gray.9" fw={500}>25 March, 14:00</Text>`
- **Grouping:** label + value pair in `<Stack gap={4}>` or `gap={2}` — minimal vertical gap between label and value.

### 9.5. Button Discipline Rule (Action Cleanliness)

- **One primary CTA rule:** by intent there is **one** main button with `variant="filled"` (or `gradient`, if accepted in the epic) on screen. All other actions — `variant="default"`, `light`, `subtle`.
- **Hidden actions:** if a table row or card has **more than two** actions — secondary ones are hidden in a **`Menu`** triggered by `<ActionIcon variant="subtle">` (three dots). Do not multiply "Edit", "Delete", "Copy" as a row of identical buttons.
- **Icons** in buttons: Tabler — **`stroke={1.5}`**; size `16` or `18` px, not "thick" 24+ without reason.

### 9.6. "Live Interface" Rule (Ghost Hover)

- Interactive table rows, dialog lists, lead/event cards must respond on hover.
- **Implementation:** `styles` or `className` with `&:hover { backgroundColor: "var(--mantine-color-gray-0)" }` (or `theme.colors.gray[0]`); for a clickable row — `cursor: "pointer"`.

### 9.7. Repository implementation

- Global tokens — `frontend/src/theme.ts`, `frontend/src/index.css`, `roles/ARCH_FRONTEND_UI_LOGIC.md`.
- **@FRONTEND** role: during review and specification, verify **§9** compliance alongside §7.

---

Reference: `roles/DOMAIN_STANDARDS.md` · `roles/TEMPLATE_MODULE_DEV.md` · `roles/TEMPLATE_DESIGN_UX.md` · `docs/artifacts/ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md` · `roles/ARCH_FRONTEND_UI_LOGIC.md` · `roles/ROLE_FRONTEND.md` · `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` §5 · `roles/DOMAIN_STANDARDS.md`

Version: 2.5 | 2026-03-26 — **§9 Premium Micro-Design Codex (Mantine v7)**; fixed Crisp reference in §0 (§7); §7–§8 Roadmap numbering unchanged
