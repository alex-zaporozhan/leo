# TPF_MODULE_SHELL — App Shell

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (Part II), `REV_RAG_MAP_INNOVATIONS.md` (section 2).

---

## 0. Design Reference and Visual Standard

**The standard to reach:** main navigation (dark sidebar + light main)
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

The shell sets the tone for the entire product. The administrator opens the system in the morning and must instantly feel: this is a professional tool, not a website.

Three key feelings:
1. **Orientation in 0.5 sec** — the active menu item catches the eye immediately
2. **Workspace is separated from navigation** — dark sidebar as "frame", light main as "canvas"
3. **Spotlight is faster than any click** — Cmd+K opens instantly, not "after animation"

Frequent actions: navigate between sections, global patient search, quick jump to a booking.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Sidebar dark (dark.8), main light (gray.0) — not the same background
- [ ] Active item: accent colour, not just underline or bold
- [ ] Collapse sidebar works — 260px ↔ 80px, state in localStorage
- [ ] Spotlight opens on Cmd+K globally from any page
- [ ] No global Header — heading only in the page Context Bar
- [ ] Tabler icons, sidebar — stroke={1.5}, size=20
- [ ] Transitions between menu items — no white screen page reload

---

## 1. Purpose

The admin panel must feel like professional desktop software, not a 2010s website. The shell defines navigation, workspace, and global search mechanisms.

---

## 2. Components and structure

### 2.1. AppShell (Mantine)

- **Header:** Absent. The top global `<AppShell.Header>` is not used.
- **Navbar (Sidebar):**
  - Background: dark (`bg="dark.8"` or `var(--bg-dark)`). Text and icons: white/semi-transparent (`c="gray.3"`).
  - Width: expanded 260px, collapsed 80px. Collapse button at the bottom of the sidebar.
- **Main:** Light background (`bg="gray.0"`). Page content wrapped in `Paper` with `border: 1px solid gray.2`, `radius="xl"`.

### 2.2. Navigation groups

Grouped with headings (uppercase, small font):

- **OPERATIONS:** Dashboard, Schedule, Chat.
- **BUSINESS:** CRM, Finance, Loyalty (if enabled).
- **SYSTEM:** Settings, Team (admins), Integrations.

Active item: bright accent (`bg="indigo.6"`, `c="white"` or per theme).

### 2.3. Context Bar (inside Main)

At the top of each page's content:

- Left: page title (and breadcrumbs if needed).
- Right: main action buttons (e.g. "New booking", "Create task").

No global header — context is defined by the page.

### 2.4. Spotlight (Cmd+K / Ctrl+K)

- At the top of the sidebar: button/placeholder "Search... (⌘K)".
- On Cmd+K, `@mantine/spotlight` opens: search by sections, search for patients and bookings (if the corresponding API is implemented).
- Optional: "Ask AI" tab or mode — text query input and calling the AI agent (Function Calling).

---

## 3. Endpoints and data

| Element | API | Note |
|---------|-----|------|
| List of sections | — | Static route configuration. |
| Spotlight: search | `GET /api/v1/admin/search?q=...` or filtering existing lists | On input — patients, bookings, navigate to section. |
| AI Command | `POST /api/v1/ai/agent` or equivalent (function calling) | Text query from Spotlight. |

---

## 4. Developer rules

- Do not add a global Header. All headings and actions — in the page Context Bar.
- Sidebar always collapsible. State (expanded/collapsed) stored in localStorage or context.
- Spotlight must open on Cmd+K globally (useHotkeys in root layout).
- Escape closes Spotlight and any open Drawer (if handled globally).

---

## 5. Code references (pointers)

- Layout: `frontend/src/.../AdminLayout.tsx` (or equivalent).
- Routes: per `FUNCTIONAL_MAP_CURRENT.md`, admin section.
- Theme: `theme.ts`, variables for dark sidebar and light main.
