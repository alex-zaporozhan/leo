# Architecture of the light UI logic for admin panel (repository)

> **[v6.16] Token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md` (single source).** This file — the INJECTION POINTS of tokens into the repository (`theme.ts`, `index.css`), not their definition. Geometry — `roles/LAYOUT_INVARIANTS.md`.

**Purpose:** the single point for **injecting** light working zone norms and **Crisp SaaS** tokens into this repository. The general UI logic canon — `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` (§1–6, **§7** light theme, **§8** Roadmap, **§9** Premium Micro-Design Codex — **reference for Mantine v7 micro-layout**). Routes and stack — `docs/artifacts/ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md`.

**Current code state:** `frontend/src/theme.ts` — light operational theme (**`primaryColor: indigo`**, multi-layer shadows, `Paper`/`Card`/`Button` — see file header). This document and **§7–§9** of the tech passport are a checklist to stay aligned with the canon.

---

## 1. Files

| File | Role |
|------|------|
| `frontend/src/theme.ts` | `createTheme`: `colors`, `shadows`, `components` (`Paper`, `Card`, `Button`, `Modal`, …), `primaryColor` |
| `frontend/src/index.css` | Global classes (e.g. `.glass-light`), `:root` if needed |
| `frontend/src/admin/layouts/AdminLayout.tsx` | `AppShell.Main` background, admin shell |
| Pages with "heavy" widgets | E.g. `frontend/src/admin/pages/AdminDashboardPage.tsx` — remove solid colour fills in favour of white cards + coloured icons |

---

## 2. Target tokens (sync with `TECH_PASSPORT` §7)

### 2.1. Background and elevation

- **Level 0:** main background — `gray.0` / `#F8F9FA` (Mantine `colors.gray[0]` or CSS variable `--mantine-color-gray-0`).
- **Level 1:** cards — white background `#ffffff` + border `1px solid var(--mantine-color-gray-2)` (or equivalent by contrast).

### 2.2. Shadows (Crisp / layered)

Override `shadows` in `createTheme`, for example:

- `xs`: `0 1px 2px rgba(0, 0, 0, 0.04)`
- `sm`: `0 1px 3px rgba(0,0,0,0.05), 0 1px 2px rgba(0,0,0,0.03)`
- `md`: `0 4px 6px -1px rgba(0,0,0,0.05), 0 2px 4px -1px rgba(0,0,0,0.03)`
- `lg`: `0 10px 15px -3px rgba(0,0,0,0.05), 0 4px 6px -2px rgba(0,0,0,0.025)`
- `xl`: `0 20px 25px -5px rgba(0,0,0,0.05), 0 10px 10px -5px rgba(0,0,0,0.02)`

Adjust alpha when changing dark sidebar / contrast. Goal — **two layers**, no "soap-bubble" blur from Mantine defaults.

### 2.3. `Paper` / `Card`

- Default: `withBorder: true`, white background, `shadow: 'sm'` (or aligned with §2.2), `radius` from theme.
- If needed, explicitly set `borderColor` via `styles.root` to `gray.2`.

### 2.4. Buttons

- **`primaryColor`:** in the target light redesign — **`indigo`** (align with brand and `ARCH_FRONTEND_DESIGN_SYSTEM_MIDNIGHT.md` when evolving the system).
- **`Button` `variant="default"`:** white background, `gray.3` border, `gray.7` text, light `xs` shadow; `:hover` — `gray.0` background.
- **`variant="filled"`:** main CTAs; limited instances per screen.

### 2.5. Light glass (sticky / context)

In `index.css` (or style module), class for sticky table headers and context bars:

```css
.glass-light {
  background: rgba(255, 255, 255, 0.7);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--mantine-color-gray-2);
}
```

Increase opacity to `0.85` if needed for readability on dense tables.

### 2.6. Sidebar — active item

- Background: `indigo.0` (or `theme.colors.indigo[0]`).
- Text and icon: `indigo.6`.
- Avoid a neutral grey "pillow" without brand tint for the active state.

### 2.7. Dashboard

- All KPI widgets — uniform white cards per §2.1; accent — **icons/trend indicators**, not solid card fill (including removal of heavy single blue block if present in markup).

---

## 3. Implementation checklist (@FRONTEND)

1. [ ] `AppShell.Main` / main wrapper: background `gray.0`.
2. [ ] `theme.ts`: `shadows` — multi-layer values §2.2.
3. [ ] `theme.ts`: `Paper`/`Card` — defaults with border and aligned shadow.
4. [ ] `theme.ts`: `Button` — styles for `default` §2.4; `primaryColor` → `indigo` when aligned with design doc.
5. [ ] `index.css`: class `.glass-light` §2.5; connect to sticky headers as refactoring proceeds.
6. [ ] `AdminLayout.tsx` (nav): active item §2.6.
7. [ ] `AdminDashboardPage.tsx` (and equivalents): widgets §2.7.

---

## 4. Related documents

- `docs/artifacts/DEV_PROMPTS_ADMIN_CRISP_SAAS_UI_2026.md` — **prompts for @DEV**: work order, DoD, unified `DEV_PROMPT_CRISP_UI_001` block for global §7 implementation.
- `docs/artifacts/ARCH_FRONTEND_DESIGN_SYSTEM_MIDNIGHT.md` — historical/current visual base; on name conflict — resolution via epic and update of both documents.
- `roles/TEMPLATE_DESIGN_UX.md` — marketing pages only, not admin panel.

Version: 1.2 | 2026-03-26 — reference to `TECH_PASSPORT` §9 (Premium Micro-Design Codex); `theme.ts` description updated
