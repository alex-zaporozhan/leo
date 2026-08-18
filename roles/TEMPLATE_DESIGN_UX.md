# TEMPLATE: design and UX for frontend (marketing and showcase pages)

> **[v6.16] Taste token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md`.** This file covers only showcase-specific details. Hero composition — `roles/HERO_ARCHETYPES.md` (NOT "text left + mockup right" by default). Geometry — `roles/LAYOUT_INVARIANTS.md`. Ambition/motion level — `roles/MOTION_AMBITION_DIAL.md`.

> **Version:** 2.0 · **Target:** premium marketing (landing, promo pages).  
> **Default stack in this template:** Mantine v7 + `frontend/src/index.css` (CSS variables). **Tailwind is NOT used in this canon** — only if @ARCH has separately recorded an exception for a specific page.

---

## 0. Scope (prevents gaps)

| Zone | Which document leads |
|------|----------------------|
| **Admin panel `/admin`, PWA `/app`, operational screens** | `docs/TECH_PASSPORT_FRONTEND_UI_LOGIC.md`, `docs/DOMAIN_STANDARDS.md`, `docs/TEMPLATE_MODULE_DEV.md`, `docs/artifacts/ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md` |
| **Marketing: `/`, landings, promo, "showcase"** | **This file** + if needed, a separate brief in `docs/artifacts/` |

**Error:** applying glass-hero and "zebra backgrounds" to admin tables as mandatory norm. **Correct:** admin — light work zone and cards from `ROLE_FRONTEND` / UI passport; marketing — tokens and composition below.

---

## 1. Purpose of this document

Closes the gap between business text and the **finished visual** of a marketing page: geometry, hero+mockup, unified background, typography, motion — with a checklist and work order.

---

## 2. Relationship to other documents

```
docs/artifacts/BUSINESS_LOGIC.md  → WHAT we sell and to whom
        ↓
docs/artifacts/DEV_PROMPTS_*.md   → Task for the page / sections
        ↓
TEMPLATE_DESIGN_UX.md             → HOW the marketing page LOOKS and in what ORDER to build
        ↓
docs/ROLE_FRONTEND.md             → Who hands off to @DEV, two visual contours (admin vs marketing)
```

NFR and overall maturity: `docs/ARCHITECTURE_EXCELLENCE_PASSPORT.md`.

---

## 3. Design decisions layer (record before building)

### 3.1. Tokens in `:root` (`frontend/src/index.css`)

| Category | Examples | Purpose |
|-----------|---------|------------|
| Radii | `--radius-sm` … `--radius-xl` | Buttons, cards, mockup |
| Shadows / glow | `--shadow-*`, `--card-glow-hover`, `--glow-accent` | Cards, hero pedestal |
| Container | `--max-w-content` | Content column width |
| Motion | `--transition-smooth`, reveal | Hover and section entrance |
| Background pattern | `.page-gradient::before` (grid + mask) | Premium background without noise |

### 3.2. Composition

- **Hero:** grid (e.g. 7/5); left: heading → subheading → bullets → 2 CTAs; right: **pedestal** (glass/mockup) with large radius and accent glow.
- **Below hero:** **unified continuous background** for the page; section separation — **spacing and cards**, not alternating backgrounds ("zebra" is forbidden, see 3.5).

### 3.3. Typography (Mantine — guidelines)

Set via `Title` / `Text` and variables, not via Tailwind classes.

| Level | Mantine (example) | Rule |
|---------|------------------|---------|
| Hero H1 | `Title order={1}` + large `fz` on breakpoints | One largest H1 per page |
| Section H2 | `Title order={2}` | Smaller than hero |
| Card H3 | `Title order={3}` or `fw={600}` | |
| Body | `Text size="sm"` / `md` | `c="dimmed"` for secondary |
| Eyebrow | `Text size="xs" tt="uppercase" fw={600}` | Optional capsule |

### 3.4. Buttons

Primary / ghost / outline; hovers with `transition` from tokens; do not duplicate "magic" px radii in each component.

### 3.5. Background protocol (mandatory for marketing)

- One gradient/pattern "field" per page.
- `.section` / `.section-alt` — **vertical spacing only**, `background: transparent`.
- Visual sectioning — cards (`glass-card` etc.) and padding.

### 3.6. Stack

- Mantine Carousel + embla when needed; version aligned with Mantine.
- Icons: `@tabler/icons-react`.
- Reveal: `useReveal` + classes `.reveal` / `.reveal-visible` (details — §10).

---

## 4. Marketing front artifacts

| Artifact | Path (repository) |
|-----------|-------------------|
| Tokens | `frontend/src/index.css` |
| Mantine theme | `frontend/src/theme.ts` |
| Sections | `frontend/src/.../sections/*` (or agreed path in task) |
| Hero / UI | reusable components alongside the landing |

---

## 5. Step-by-step plan (marketing page)

### Step 0. Brief

Page type, section list, CTA texts, reference (URL) — in the task or `docs/artifacts/DEV_PROMPTS_*.md`.

### Step 1. Tokens and theme

1. All variables in `frontend/src/index.css` (`:root`).
2. `theme.ts` aligned with variables (`defaultRadius`, brand).
3. Do **not** add `tailwind.config` by default.

**Criterion:** changing `--radius-lg` or glow updates cards and CTAs without editing dozens of components.

### Step 2. Layout

`.container-page`, `.section`, `.page-gradient`, section grid; no striped background.

### Step 3. Base UI

Buttons, cards, `SectionHeader` — from tokens.

### Step 4. Hero + mockup

"Text + demo block" scene; responsive without horizontal scroll.

### Step 5. Remaining sections

Per the brief list; unified card system.

### Step 6. Marketing page header / footer

If using a fixed header with blur — see §9.2 (scroll); for admin panel — separate rules in `ROLE_FRONTEND`.

### Step 7. Reveal and polish

Connect reveal; verify hover, WCAG AA contrast, shadow clipping in carousels.

### Step 8. Checklist

- [ ] Geometry tokens used in components  
- [ ] Hero with dedicated mockup  
- [ ] Single background, no zebra  
- [ ] One main H1  
- [ ] No horizontal scroll  
- [ ] Forms and CTAs with loading/error states  

---

## 6. "Where to find what" summary

| Question | Document |
|--------|----------|
| Operational screens, tables, Drawer | `TECH_PASSPORT_FRONTEND_UI_LOGIC.md`, `DOMAIN_STANDARDS.md` |
| Module layout for @ARCH | `TEMPLATE_MODULE_DEV.md` |
| Landing visuals, build order | This file |
| Project route and stack facts | `docs/artifacts/ARCH_FRONTEND_TECH_PASSPORT_DENTAL_BOOKING.md` |

---

## 7. Marketing page brief (insert into DEV_PROMPTS)

```markdown
## Page Brief: [Name]

### Type
- [ ] Single-page landing
- [ ] Section on an existing site

### Sections in order
1. Hero — …
2. …

### Texts and CTAs
- Source: …

### Visual
- Dark/light theme, accent, reference URL

### Integrations
- Forms → API …
```

---

## 8. Reference "premium" blocks (marketing)

**Trigger:** user requests a "design solution" for a **marketing** empty zone.

- CSS: `.floating-element`, `@keyframes float`, `pulse` for badges.
- Abstract compositions: 3D card tilt, blur-blob, skeleton stripes + Tabler icons — **without** random stock images.
- Everything from `:root` tokens; consistent with the page's dark/light theme.

Detailed snippets — when needed in the same epic as the code; do not bloat system files with copy-paste.

---

## 9. Performance (motion, blur, background)

> Connection to `ROLE_FRONTEND`: blur/reveal protocol for **showcases**; for **admin** — speed and readability priority, glass not required.

### 9.1. Reveal

Animate only `transform` and `opacity`; `will-change` before animation, remove in `transitionend`; `IntersectionObserver` with `unobserve` after first show.

### 9.2. Backdrop-filter and scroll

During active scrolling — add `.is-scrolling` class to `body`, reduce or disable blur for heavy nodes; `scroll` listener with `{ passive: true }`.

### 9.3. Background images

Heavy PNG > 50 KB or > 1000×1000 — replace with CSS/SVG tile or inline SVG data-URI.

---

Reference: `roles/ROLE_FRONTEND.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` §4 (showcase) · `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` · `roles/DOMAIN_STANDARDS.md` · `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md`
