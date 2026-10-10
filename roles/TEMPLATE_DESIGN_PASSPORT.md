# TEMPLATE_DESIGN_PASSPORT

> The project's visual passport. Fill it once per project, at Step 5.5.B (@CREATOR), from the world chosen in
> `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md`. This is where the **values** live; the **physics** lives in the canons.

**Before filling:** read `roles/VISUAL_CRAFT_CANON.md`. This template asks you to make decisions; the canon tells
you which decisions read as expensive and which read as 2008. A passport filled without craft produces a screen
that is technically correct and visually cheap. Every section below names the canon rule it answers.

**No `[hex]` placeholder may survive.** Either a value from the world's recipe, or an explicit
`ASSUMPTION: [value] — [why]`. A passport with empty slots is not a passport; it is an invitation to improvise,
and improvisation is how the 2008 default gets in.

---

# Design Passport — [Project Name]

**Concept:** `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` · World: [№ + name from `CONCEPT_DNA_LIBRARY`]
**Concept phrase:** "The screen is [an object from the niche's world]"
**References:** [primary: carrier + exact screen] · [secondary] · [secondary] — format `CONCEPT_ANATOMY §4.1`
**Signature:** [the one owned element — see §7]

---

## 1. MATERIAL THESIS — the layer stack

> Not "a style". A **stack of physical layers** the user is looking at. This is what makes a product feel like
> a made object rather than a template. Name every layer and what it is made of.

```
LAYER 1 — THE FIELD        [what the page ground IS: paper / lacquer / metal / dusk]     → token: page.bg + atmosphere
LAYER 2 — THE SURFACE      [what a card IS: a sheet on the desk / a plate / a card]      → token: surface + shadow
LAYER 3 — THE SIGNATURE    [the owned gesture: a seal / a gloss sweep / a perforation]   → §7
LAYER 4 — THE ANCHOR       [the object that carries trust: a document / an instrument]   → the domain pattern
LAYER 5 — THE INK          [where the dark lives: footer / offer / one card — not more]  → ink.base
```

| Law | How it shows up in the UI |
|-----|---------------------------|
| [Law 1 — from the world] | [a concrete rule, not a mood] |
| [Law 2] | |
| [Law 3] | |
| [Law 4] | |

**Emotional contour:** from [what we are NOT] → to [what we ARE].
**The three NEVERs** (`CONCEPT_ANATOMY` axis 8; these are 🔴 for @QA_VISUAL):
1. [ ] 2. [ ] 3. [ ]

---

## 2. ATMOSPHERE — the field (the layer most passports forget, and the one that makes it expensive)

> A flat `background-color` is the single cheapest-looking decision available. A **field** has depth: a wash,
> a barely-there grain, a soft vignette. Together they cost ~15 lines of CSS and change everything.
> Craft: `VISUAL_CRAFT_CANON` §2 (tonal depth), §4.2 (tinted neutrals).

```css
/* [project]/theme/atmosphere.css */
.atmosphere {
  min-height: 100vh;
  background-color: var(--page-bg);
  background-image:
    radial-gradient(ellipse 120% 80% at 50% -20%, [light wash], transparent 55%),
    linear-gradient(180deg, [field-top] 0%, [field-bottom] 100%);
}
.atmosphere::before {                 /* grain — the tactility of the material */
  content: ''; position: fixed; inset: 0; pointer-events: none;
  opacity: [≤0.04];                   /* HARD CEILING: above 0.05 it reads as dirt, not material */
  background-image: url("data:image/svg+xml,[noise]");
  mix-blend-mode: multiply;
}
```

| Token | Value | Note |
|-------|-------|------|
| `--page-bg` | `[hex]` | tinted toward the material — never a pure grey (§4.2) |
| `--field-top` | `[hex]` | |
| `--field-bottom` | `[hex]` | the wash gives the field a direction |
| grain opacity | `[≤0.04]` | |

**Section bands** — rhythm without chaos (choose 2–3, no more):

| Band | Background | Where |
|------|-----------|-------|
| `default` | transparent (the atmosphere shows through) | most sections |
| `soft` | `[hex]` + hairline top/bottom | [which sections] |
| `ink` | `ink.base` | [ONLY: footer / offer — name them] |

**N/A is allowed** — but say so explicitly: `ATMOSPHERE: N/A — flat field is the world's material (print/poster)`.
Silence here means somebody will ship a flat `#FFFFFF` page.

---

## 3. SURFACES AND ELEVATION — one light, tonal steps

> Craft: `VISUAL_CRAFT_CANON` §2 (the one-separation rule) and §3 (one light source).
> **A surface earns exactly ONE method of separation: tone, OR a hairline, OR a shadow. Never two. Never three.**

**The tonal ladder** (the delta is what makes a card readable without an outline):

| Surface | Value | Delta from the ground |
|---------|-------|-----------------------|
| `ground` | `[hex]` | — |
| `surface` | `[hex]` | +2…4% lightness |
| `raised` | `[hex]` | +2…3% more (hover/active) |
| `sunken` | `[hex]` | −2…3% (wells, inputs, code) |

**The shadow system** — one light, from above, tinted with the world's **ink**, never `#000`:

| Token | Value | Use |
|-------|-------|-----|
| `--e0` | `none` | rows, chrome, dividers |
| `--e1` | `0 1px 2px rgba([ink] / .04)` | resting cards |
| `--e2` | `0 2px 8px rgba([ink] / .06), 0 1px 2px rgba([ink] / .04)` | hover, popovers |
| `--e3` | `0 8px 24px rgba([ink] / .08), 0 2px 6px rgba([ink] / .04)` | drawers, modals |
| `--e-signature` | `[layered, if the world calls for it]` | the ONE hero object (§7) |
| `--inset-top` | `inset 0 1px 0 rgba(255,255,255,.7)` | the highlight on a sheet's top edge — optional, costs nothing, reads as craft |

**Hairline:** `rgba([ink] / .06–.10)` — a 1px border at full opacity is a wall, not a hairline.
**Ceiling:** shadow opacity ≤ 10% on operational surfaces. Above that you are drawing, not lighting.

---

## 4. PALETTE — tokens with an area budget

File: `[project token file]` — the single source of truth. **No hex outside it.**

| Token | Value | Role | Max area (§4 inverse-area law) |
|-------|-------|------|--------------------------------|
| `brand.primary` | `[hex]` | the one accent | buttons, small marks — **not a 100vw ground** |
| `brand.hover` | `[hex]` | | |
| `brand.action` | `[hex]` | links, focus | |
| `page.bg` | `[hex]` | the field (§2) | large → **low chroma, tinted** |
| `surface` / `surface.soft` / `sunken` | `[hex]` | §3 | |
| `ink.base` | `[hex]` | the dark layer | **≤ [N]% of a viewport** — name the number |
| `text.primary` / `.secondary` / `.label` | `[hex]` | ink, not colour (§4.3) | |
| `border.subtle` | `rgba(...)` | 6–10% | |

**The accent budget:** [N] accents per hero viewport, [N] inside a section (`VISUAL_CONCEPT` — spectrum/accent budget).
**Neutrals are tinted toward:** [warm / cool / the material] — a pure `#888` belongs to no world (§4.2).

---

## 5. SEMANTIC TINT PAIRS — status, muted to the world

> Status colours do not arrive from Bootstrap. They are the semantic hues pulled toward the world's temperature
> and chroma. Form: **a tinted background + a coloured dot or left border — never a saturated fill** (§4.4).

| Category | Dot | Tint bg | Ink on tint | Meaning |
|----------|-----|---------|-------------|---------|
| progress | `[hex]` | `[hex]` | `[hex]` | active work |
| wait | | | | awaiting user/system |
| success | | | | done |
| error | | | | blocker |
| neutral | | | | draft / idle |

Rules: colour is never the only carrier of status (a label is mandatory) · tint-on-tint is 🔴 · @QA_VISUAL rejects
dark-on-dark and white-on-light.

---

## 6. GEOMETRY — one radius language

| Token | Value | Where |
|-------|-------|-------|
| `radius.card` | `[px]` | |
| `radius.control` | `[px]` | inputs, buttons — **one value**, not three |
| `radius.tag` | `[px]` | |
| `radius.[signature]` | `[px]` | **only** the signature object, if the world asks for it |

**Nested radii are concentric** (§6): outer = inner + padding. A 12px card with 8px padding has a **4px** inner
radius — not 12. Parallel radii are the cheap tell; concentric ones are the premium one.
**Hard deny:** more than 2 radius values for controls on one screen · asymmetric radius on buttons · `inset` focus bars.

---

## 7. THE SIGNATURE — the one owned element

> One per project. Not two. It is what survives the "remove the logo" test.

**What it is:** [description]
**Where it lives** (≥3 carriers): [hero] · [an interaction: button/card] · [a detail: divider/bullet/loader]
**Implementation:** [CSS/SVG, ≤ 30 lines]

The five criteria (`CONCEPT_ANATOMY` axis 8) — all must hold:
- [ ] traces to the niche's object (not "a gradient in the header")
- [ ] ≤ 30 lines of CSS/SVG, no heavy assets
- [ ] recognisable in 1 second, without the logo
- [ ] lives on ≥ 3 carriers
- [ ] not on the cliché ban-list C1–C10

---

## 8. BUTTON MATRIX — by surface, not by variant

> The mistake most passports make: defining a button once and hoping. A button lives on **different surfaces**,
> and on each it must be a different thing. Fill the grid.

| Surface | Primary | Secondary | Ghost / tertiary |
|---------|---------|-----------|------------------|
| The page field | | | |
| A card / surface | | | |
| The ink layer / a dark band | | | |
| A gradient / image (if any) | | | |

**One primary per screen region.** Loading and disabled keep the width stable (no layout shift).
Destructive is **not** a big red block — it is a text/ghost in the error hue, promoted only when the risk is real.

| Size | Height | Padding X | Use |
|------|--------|-----------|-----|
| sm | `[px]` | | card CTA |
| md | `[px]` | | default, forms |
| lg | `[px]` | | the one hero CTA |

**Focus:** `outline: 2px solid [border.focus]; outline-offset: 2px` — no glow, no inset bar, no geometry change.

---

## 9. COMPETITIVE CONTRAST — how we beat the benchmark

> Fill this against the 2–3 products the client will compare us to. Vague ambition ("make it nicer") produces
> nothing; a named delta produces a design.

| Element | [Competitor A] | [Competitor B] | **Us** |
|---------|----------------|----------------|--------|
| Hero | | | |
| Cards | | | |
| Colour | | | |
| Trust / proof | | | |
| Motion | | | |
| Density | | | |

**The "better" criterion:** at the same informativeness — [less visual noise / stronger sense of X].
**The smell test:** an unfamiliar person, shown our hero and theirs without logos, names the niche correctly.

---

## 10. ANTI-PATTERNS (project-specific, 🔴 for @QA_VISUAL)

> The generic ban-list is `VISUAL_CRAFT_CANON` §9 (X1–X12) and `VISUAL_CONCEPT_PROTOCOL` §4 (C1–C10).
> **Here you list what is forbidden IN THIS WORLD specifically** — the things a well-meaning developer would
> otherwise add because they look fine in isolation.

- [ ] [e.g. `backdrop-blur` — the world is paper, not glass]
- [ ] [e.g. gradient CTA on a gradient hero]
- [ ] [e.g. the superseded palette `#XXXXXX` — it is a different product now]
- [ ] [...]

---

## 11. CRAFT GATE (before any screen ships)

```
□ Atmosphere is wired (§2) — the field is not a flat colour, or N/A is declared with a reason
□ ONE separation method per surface (§3) — no card with border + shadow + tint
□ Shadows are ink-tinted, from the e-scale, ≤10% opacity — no rgba(0,0,0,.2)
□ Large areas are low-chroma; the accent budget (§4) is respected
□ Neutrals are tinted; no #888, no #000 text
□ Radii: one control value; nested radii concentric
□ The signature exists, on ≥3 carriers, and passes the 5 criteria (§7)
□ tabular-nums on every numeric column
□ The cheapness detector X1–X12 (VISUAL_CRAFT_CANON §9): 0 hits
□ The reduction protocol (§10 of the canon) was run: [what was removed]
```

---

## Decision Log

| Date | Decision | Rationale | Supersedes |
|------|----------|-----------|------------|
| | | | |

---

Reference: `roles/VISUAL_CRAFT_CANON.md` (the physics: restraint, tone, light, chroma, the floor) · `roles/CONCEPT_ANATOMY.md` (the 8 DNA axes, the signature criteria, the reference protocol) · `roles/CONCEPT_DNA_LIBRARY.md` (the world recipes — palettes and fonts are copied, not invented) · `roles/VISUAL_CONCEPT_PROTOCOL.md` (TASTE GATE, C1–C10, RESKIN) · `roles/TEMPLATE_UI_COMPOSITION_PASSPORT.md` · `roles/TEMPLATE_TYPOGRAPHY_PASSPORT.md` · `roles/TEMPLATE_MOTION_LANGUAGE.md` · `roles/ROLE_DESIGN.md` · `roles/ROLE_FRONTEND.md`
Version: 2.0 | 2026-07-12
