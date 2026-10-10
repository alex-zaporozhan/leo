# Template: frontend visual canon — checklist for @QA_ARCH

> **[v6.16] Tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; render measurement — `roles/ROLE_QA_VISUAL.md`.** This checklist covers structural repository elements, not rule definitions. Render verification (equal-height, overflow, CLS, zero-shift) is performed by @QA_VISUAL using numbers/diff, not by reading code.

> After copying to `documentation/QA_FRONTEND_VISUAL_CANON_<SLUG>.md`, replace the first heading with  
> `# Frontend visual canon (Product Name) — checklist for @QA_ARCH`  
> Role name order and link to @QA_ARCH: **`roles/TEMPLATE_QA_FRONTEND_VISUAL_CANON.md`**.

> Replace placeholders `{PROJECT_NAME}`, `{FRONTEND_ROOT}`, `{FILTER_BAR_CLASS}`, `{ENTITY_CARD_CLASS}`, `{ACCEPTANCE_PROTOCOL_REF}` as needed.

This document defines **structural elements** and **acceptance criteria** to keep PWA/web screens consistent. Typical style paths: `{FRONTEND_ROOT}/src/index.css`, `{FRONTEND_ROOT}/src/styles/components.css`, `{FRONTEND_ROOT}/src/styles/tokens.css`.

---

## 1. Layout structural elements (what is unified)

| Element | Classes / pattern | Purpose |
|--------|-------------------|------------|
| **Page root** | e.g. `main` + `.card` | Main content container |
| **Section with padding** | `.subcard` | Group under main block |
| **Nested block** | `.subcard.nested` | Secondary background, inner grouping |
| **Block stack** | `.section-stack` | Vertical column of sections |
| **Form section heading** | `.form-section-title` | Block sub-heading |
| **Sub-heading** | `.form-section-lead` + `.muted` | Secondary text |
| **Form grid** | `.form-grid-2` | Adaptive fields |
| **Form** | `.form` | Field column |
| **List filters** | `{FILTER_BAR_CLASS}` e.g. `.filters.*` | Unified grid / height / font |
| **Lists** | `.list`, `.list-row` | Rows |
| **Entity card in list** | `{ENTITY_CARD_CLASS}` | "Heading + metrics" pattern |
| **KPI tiles** | `.grid` + `.metric` | Summary numbers |
| **Detail screen tiles** | `.grid--dense` + `.metric--dense` | Dense blocks without giant font size |
| **Buttons** | see `components.css` | Variants primary / secondary / ghost / danger |
| **Input fields** | global rules | Including `file`, textarea |
| **Feedback** | `.feedback-*`, inline components | Success / error / info |

*Add to the table classes specific to {PROJECT_NAME} (branded lists, tables, kanban, etc.).*

---

## 2. "Unified style" acceptance criteria (universal checklist)

Check **every new or changed screen**:

### 2.1. Hierarchy and containers
- [ ] Main content in agreed root container; nested groups — per canon, no "bare" headings without padding.
- [ ] Heading levels `h1` / `h2` / `h3` consistent with other product screens.

### 2.2. Entity lists
- [ ] Unified card/row pattern (as in §1), no mixing "wall of text" and cards in the same section without reason.
- [ ] No extra horizontal scroll at narrow widths: `min-width: 0`, line wrapping.

### 2.3. Metrics
- [ ] KPI / dashboard — large font size where specified.
- [ ] Detail screens — dense metrics where specified; no meaningless mixing of large and micro in the same section.

### 2.4. Forms and filters
- [ ] Unified border, border-radius, hover/focus/disabled states.
- [ ] File inputs and non-standard controls are styled, not raw browser defaults.
- [ ] Filter bar: aligned elements (grid or flex with uniform height/font rules).

### 2.5. Interactivity and mobile
- [ ] Minimum tap zones for primary actions and key links (target ~44px height, where applicable to the product).
- [ ] Visible `focus-visible` on interactive elements.

### 2.6. Breakpoints (smoke)
- [ ] Canon-defined breakpoints (e.g. ≤480px, ≤520px, ≥640px) verified manually or in E2E viewport.

---

## 3. Quick code check

- Tokens: `{FRONTEND_ROOT}/src/styles/tokens.css` (or repo path).
- Buttons / utilities: `components.css`.
- Remaining layout: `index.css` / theme modules — per actual repository.

---

## 4. Connection to other documents

- Acceptance protocol for this repository: `{ACCEPTANCE_PROTOCOL_REF}` (e.g. `documentation/QA_ACCEPTANCE_PROTOCOL_<SLUG>.md`).
- Universal @QA_ARCH name and step protocol: **`roles/TEMPLATE_QA_FRONTEND_VISUAL_CANON.md`**.

*Update this canon when new global classes or patterns are introduced.*
