# TEMPLATE_PROJECT_FRONTEND_PASSPORT.md

> **[v6.16] §1.4 — project TOKEN VALUES for a specific product; RULES and scale — canon `roles/FRONTEND_DESIGN_EXCELLENCE.md`.** Geometry — `roles/LAYOUT_INVARIANTS.md`. Project hex/radii are recorded here, canon rules are not overridden.
# Frontend tech passport template for a new project
# Created by @FRONTEND at project start — before the first screen
# Saved as: docs/artifacts/FRONTEND_PASSPORT_[PROJECT].md
# Updated by: @FRONTEND after each major wave

> **Principle:** the tech passport is a living document, not a "write once" artifact.
> @DEV reads it before every task. @QA_ARCH refers to it during audit.
> If the passport diverges from the code — update the passport (or the code).

---

## §Surfaces (fill BEFORE the first screen — @FRONTEND proposes, @ARCH confirms)

> Read by **@QA_VISUAL** as its measurement viewport set and by **@DESIGN** as the row set of every Responsive
> Matrix. An unfilled §Surfaces means the project has no declared narrow-viewport behaviour — and every
> "adaptive" claim after that is unverifiable rather than true.

| Surface | Exists? | Breakpoints / frame | Notes |
|---------|---------|---------------------|-------|
| web-desktop | [yes/no] | [px] | |
| web-mobile | [yes/no] | [px — the project's own joints, not a default list] | |
| PWA | [yes/no] | [px + installed-shell behaviour] | |
| iOS native | [yes/no] | [frame + safe areas] | |
| Android native | [yes/no] | [frame + safe areas] | |
| embed / widget | [yes/no] | [host constraints] | |

**Scroll ownership:** [per surface — page scroll, an internal container, or both]
**Full-height shells:** `dvh` only — `100vh` is a defect on every mobile surface.

---

## HOW TO FILL IN

1. @FRONTEND fills **§Surfaces first**, then §1–§4, before the first screen (30–60 min). §Surfaces is the canonical list of this product's surfaces: @QA_VISUAL reads it as its viewport set and @DESIGN as the row set of every Responsive Matrix, so no other file restates it — they reference it.
2. §5 (Modules) is filled in incrementally as development progresses
3. §6 (Live state) is updated after each wave
4. @DEV reads §1–§4 before every task
5. @QA_ARCH reads §1–§4 during audit (especially §1.3 Visual Identity)

---

## §1. PROJECT IDENTITY

### 1.1. Product essence
```
Product: [name]
For whom: [user type — clinic admin / buyer / manager]
Main user task: [one sentence — "book a client in 3 clicks"]
Usage context: [desktop/mobile/both, frequent/rare, pro/novice]
```

### 1.2. Two contours (fill both)

| Contour | Zones | Present in project |
|--------|------|----------------|
| Operational (Admin/App) | `/admin/*`, `/app/*` | Yes / No |
| Showcase (Marketing) | `/`, landings | Yes / No |

### 1.3. Visual Identity (most important)

```
Product feel: [one word — Professional / Warm / Bold / Playful]
Primary reference: [Linear / Stripe Dashboard / Notion / Revolut / Figma]
Why this reference: [one sentence of reasoning]

Operational contour — reference: [specific screen: "Linear Issues list"]
Showcase — reference: [specific screen: "linear.app homepage"]

Cannot: [what definitely not to do — "no dark theme", "no glass in admin", "no many colours"]
```

### 1.4. Design Tokens

```css
/* Fill before the first build. All values — in index.css */

/* Palette */
--primary: ;          /* main accent */
--primary-hover: ;    /* accent on hover */
--primary-light: ;    /* light accent variant */
--bg-main: ;          /* app background (gray.0 = #F8F9FA) */
--bg-card: ;          /* card background (#ffffff) */
--text-main: ;        /* main text */
--text-muted: ;       /* secondary text */
--border-card: ;      /* card border (rgba(0,0,0,0.06)) */

/* Typography */
--font-family: ;      /* Inter / Plus Jakarta Sans / other */

/* Radii */
--radius-sm: 6px;
--radius-md: 10px;
--radius-lg: 16px;
--radius-xl: 24px;
```

---

## §2. STACK AND ARCHITECTURE

### 2.1. Technologies

| Element | Technology | Version | Note |
|---------|-----------|--------|-----------|
| Build | Vite | | |
| Framework | React | 18+ | |
| TypeScript | strict | | Required |
| UI components | Mantine | v7 | |
| Server state | TanStack Query | v5 | Required |
| Routing | React Router | v6 | |
| Icons | @tabler/icons-react | | stroke={1.5} default |
| Dates | Day.js | | |

### 2.2. frontend/src/ structure

```
frontend/src/
├── main.tsx
├── App.tsx
├── theme.ts              ← Mantine theme (primaryColor, shadows, radius)
├── index.css             ← CSS variables (:root), reset
├── api/
│   └── client.ts         ← HTTP wrapper, Bearer, 401 redirect
├── hooks/                ← TanStack Query hooks
├── [zone]/               ← admin/ | app/ | landing/
│   ├── layouts/
│   └── pages/
└── shared/
    └── ui/
        ├── EmptyState.tsx
        ├── DataSkeleton.tsx
        └── [other shared components]
```

### 2.3. Zones and routes

| Zone | Path | Description | Layout |
|------|------|---------|--------|
| Admin | `/admin/*` | | AdminLayout |
| App/PWA | `/app/*` | | AppLayout |
| Public | `/` | | LandingLayout |

---

## §3. COMPONENTS AND PATTERNS

### 3.1. Created shared components

| Component | Path | Description | When to use |
|-----------|------|---------|-------------------|
| EmptyState | shared/ui/EmptyState.tsx | Empty state with CTA | Everywhere when length===0 |
| DataSkeleton | shared/ui/DataSkeleton.tsx | Skeleton loading | All lists/tables |
| | | | |

### 3.2. Mantine component conventions

```
Paper: withBorder + radius="md" + shadow="xs" — standard card
Drawer: position="right", size="md"/"lg" — for forms (not Modal)
Modal: only for confirm dialogs
Table: verticalSpacing="sm" — compact rows
ActionIcon: variant="subtle" — three dots in row
```

### 3.3. State patterns (mandatory everywhere)

```tsx
// Standard for EVERY component with data:
if (isLoading) return <DataSkeleton />
if (isError) return <ErrorState onRetry={refetch} />
if (!data?.length) return <EmptyState ... />
return <ActualContent />
```

---

## §4. API AND DATA

### 4.1. API client

```
Base URL: [from .env]
Authentication: Bearer token
401 redirect: [where to]
Error format: {"detail": "...", "code": "SNAKE_CASE"}
```

### 4.2. TanStack Query — conventions

```ts
// Query key naming
['resource-name']                    // list
['resource-name', id]                // single record
['resource-name', { filters }]       // with filters

// Standard hooks — one per resource
// hooks/useBookings.ts → useBookings(), useBooking(id), useCreateBooking()

// After mutation — invalidate ALL related keys
onSuccess: () => {
  queryClient.invalidateQueries({ queryKey: ['bookings'] })
  queryClient.invalidateQueries({ queryKey: ['schedule'] })
}
```

---

## §5. MODULES (filled in as development progresses)

| Module | Path | Components | API endpoints | Status | Reference |
|--------|------|-----------|--------------|--------|--------|
| Dashboard | /admin | DashboardPage.tsx | /api/v1/stats | ✅ / 🔄 / ❌ | Stripe Dashboard |
| | | | | | |

---

## §6. LIVE STATE (update after each wave)

### 6.1. What is implemented

```
Last updated: [date]

✅ Complete and working:
  - [module / screen]
  - [module / screen]

🔄 In progress:
  - [module / screen] — what remains

❌ Planned, not started:
  - [module / screen]

⚠️ Technical debt:
  - [what and why]
```

### 6.2. Known discrepancies (code vs passport)

```
[If code diverges from passport — record here until fixed]
- [What diverges]: [as in passport] vs [as in code]
```

### 6.3. What cannot be changed without an epic

```
- theme.ts: primaryColor, shadows, radius — only via @ARCH + @FRONTEND
- AdminLayout: sidebar + main structure — only via @ARCH
- api/client.ts: base URL, headers — only via @ARCH
```

---

## §7. NEW SCREEN CHECKLIST

Before every new screen, @FRONTEND / @DESIGN runs through:

```
□ Reference defined (Linear/Stripe/etc. — specific screen)
□ @DESIGN SPEC created (for a new screen — always)
□ Visual Identity from §1.3 observed
□ Design Tokens from §1.4 used
□ Components from §3.1 reused (no duplicates)
□ TanStack Query hooks created per convention §4.2
□ All 4 states: Loading/Empty/Error/Success
□ Visual Quality Gate from FRONTEND_DESIGN_EXCELLENCE.md §6 passed
□ Module added to §5
□ §6 updated after implementation
```

---

Reference: `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/ROLE_FRONTEND.md` · `roles/ROLE_DESIGN.md` · `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` · `roles/TEMPLATE_DESIGN_UX.md` · `roles/DOMAIN_STANDARDS.md`
Version: 1.0 | 2026-05-22
