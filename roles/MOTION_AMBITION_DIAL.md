# MOTION_AMBITION_DIAL.md
# Ambition Dial + MICRO mode for @MOTION
# Extends ROLE_MOTION: (1) boldness level as an INPUT parameter for a concept;
#                     (2) MICRO mode — ownership of micro-moments on operational screens.

> **Two principles of this file:**
> 1. *"Restraint is a choice, not a default. If the system is not configured, it clamps up. The dial sets the boldness consciously."*
> 2. *"The micro-moment of a button on a work screen must belong to someone. Now it belongs to @MOTION (MICRO mode), not to @DEV improvisation."*

---

## PART 1 — AMBITION DIAL

### Problem it solves
`@MOTION` has a rich arsenal (`MOTION_LIBRARY`: kinetics, WebGL, scroll narrative), but **the default framing is conservative**: "three questions before WebGL", "WebGL NOT justified", the golden library leads with linear.app ("no animation"). Without an explicit signal the system picks the safest option and converges to minimalism. The result — "clamped ambition", 3D always last, the same techniques everywhere.

The dial makes the boldness level an **explicit input parameter** of the concept. Performance rules (`MOTION_LIBRARY §VII`, `LAYOUT_INVARIANTS §10`, `prefers-reduced-motion`) are **never weakened** — the dial changes *expressiveness and technique priority*, not the right to violate performance and accessibility.

### Where it is set
The ambition level enters the public site brief — it is set by the user, `@CREATOR` (by brand), or `@LEAD`. If not set — **default `confident`** (NOT `restrained`). This is the key inversion: previously the implicit default was "clamped"; now it is "confident".

`@MOTION` records the level as the first line of `MOTION_CONCEPT_*.md`:
```
Ambition: [restrained | confident | bold | experimental]
Basis:    [brand / audience / risk / user request]
```

### Four levels

| Level | Meaning | What is encouraged | `MOTION_LIBRARY` technique priority | Hero archetypes |
|-------|---------|-------------------|-------------------------------------|----------------|
| **restrained** | Quiet confidence. Trust over effect. | Calm reveal, atmospheric background at low intensity, flawless typography | `T5`, `T6`, `B1`, `B2` (weak) | A, B, G |
| **confident** *(default)* | Character is present, but does not shout. | Kinetic heading, live background, meaningful stagger, hover with weight | `T1`, `T2`, `B1`, `B2`, `B5`, `I1`, `S2` | A, B, D, G, H |
| **bold** | Memorable, stands out. | Large kinetics, scroll narrative, cursor as an element, **3D considered on equal footing** | `T1–T4`, `S1–S4`, `I1–I3`, `W2`, `TR1` | C, D, E, F, H |
| **experimental** | Site as an impression. Goal — "you want to show it to others". | WebGL/shaders **in priority** when metaphor is justified, non-standard transitions, custom cursor | `W1–W4`, `T1–T4`, `S1–S4`, `I2`, `TR1` | C, E, F |

### What the dial changes in `@MOTION` behaviour

1. **Order of technique consideration.** At `bold/experimental`, 3D/WebGL (`W1–W4`) is considered **on equal footing or first**, not "as a last resort". The three `W1` questions remain (the metaphor must be justified), but 3D ceases to be "an extreme measure".
2. **Amplitude.** Dominant type size, reveal distance, duration and easing character grow with the level: `restrained` — `power2.out` short; `experimental` — `power4.out`/`elastic`/`back` with range (`ROLE_MOTION` principle 2).
3. **Right to a "face".** At `bold+` the system must propose a **distinctive** technique (custom cursor, kinetics, scene), not a template. "The same stagger everywhere" at `bold+` = `🔴` anti-pattern.
4. **Prohibition of degenerating into a template.** At any level above `restrained`, repeating the same Linear-style technique without connection to the metaphor is a violation (see `ROLE_MOTION` anti-pattern "micro-animation instead of character"). Including [v6.20]: a single `power2.out`/`ease-in-out` across all project techniques = personality degeneration — 🔴 at `confident+`. Easing and durations are taken from the world's motion personality (`docs/artifacts/VISUAL_CONCEPT_*` ← `roles/CONCEPT_DNA_LIBRARY.md`):

| Personality | Easing | Range |
|-------------|--------|-------|
| Absorption (paper/ink) | cubic-bezier(.25,.46,.45,.94) + blur 2→0 | 400–600ms |
| Snap (grid/instruments) | cubic-bezier(.7,0,.3,1) / linear / steps() | 100–220ms |
| Spring (pop/craft) | cubic-bezier(.34,1.56,.64,1) | 220–300ms |
| Velvet (luxury/museum) | ease / cubic-bezier(.4,0,.2,1) | 500–700ms |
| Acceleration (speed) | cubic-bezier(.7,0,.2,1) + skew entry | 140–260ms |
| Page-turn (gloss) | cubic-bezier(.77,0,.18,1) | 600–900ms |

### What the dial does NOT change (safety rails)
```
✗ Does not weaken Lighthouse Performance ≥ 85 mobile
✗ Does not cancel prefers-reduced-motion (at any level, reduced = heavy disabled)
✗ Does not permit animating layout properties (LAYOUT_INVARIANTS §10 — transform/opacity only)
✗ Does not cancel mobile degradation (parallax/3D fallback — MOTION_LIBRARY §VII)
✗ Does not permit background competing with content (opacity < 10%, ROLE_MOTION principle 5)
```
Ambition is about **expressiveness**, safety is about **rails**. Rails do not move.

### Level selection hint (for @LEAD/@CREATOR)
```
Personal brand / portfolio / agency / creative tool         → bold / experimental
Premium product, need to stand out in a dull market         → bold
Mass-market product, B2B, trust over wow                   → confident (default)
Regulated / enterprise / conservative audience             → restrained
AI / spatial / immersive product                           → bold–experimental (3D justified)
```

---

## PART 2 — MICRO MODE (micro-moments on operational screens)

### Problem it solves
Animation on operational screens (`/admin`, `/app`) was effectively forbidden (`FRONTEND_DESIGN_EXCELLENCE §1` — "reveal only on public site"; `ROLE_MOTION` — "does not handle admin/app"). As a result, micro-interactions on work screens **belonged to no one**: `@DESIGN` provides statics (ceiling — 150ms hover), `@MOTION` is forbidden, `@DEV` improvises — and buttons "run off" and jitter. This is a responsibility gap.

`MICRO` mode gives operational micro-moments to `@MOTION` — but within **strict work interface boundaries**: this is not public-site expression, but calibrated functional micro-feedback at the level of Linear/Stripe/Google Calendar.

### MICRO mode boundary
```
MICRO ≠ public-site animation. This is functional micro-feedback:
- transform/opacity only, short durations (see table),
- only meaningful (confirms an action / shows state / directs attention),
- prefers-reduced-motion is mandatory,
- does NOT bring "brand character" — brings clarity and responsiveness.

Public-site expression (kinetics, WebGL, scroll narrative) on /admin and /app — still NOT transferred
(FRONTEND_DESIGN_EXCELLENCE §1). MICRO is about work tool responsiveness, not wow.
```

### Catalogue of operational micro-moments

Each: **what → principle → permitted technique → duration → red flag**. All — `transform/opacity` only, zero layout shift (`LAYOUT_INVARIANTS §7, §10`).

| Moment | Principle | Technique | Duration | 🔴 Red flag |
|--------|-----------|-----------|----------|------------|
| **Row/card hover** | "live interface" | `background`/`box-shadow` change | 120–150ms ease | geometry shift, jitter |
| **Focus-visible** | state visibility | `outline`/ring without flow shift | instant/100ms | no focus-visible; layout shift |
| **Press / active** | tactile response | `transform: scale(0.98)` | 80–120ms | `width/margin` animation; "jump" |
| **Success confirmation** | close the action | short check/toast `opacity`+`translateY(4px)` | 150–250ms | no feedback; toast shifts content |
| **List enter/exit** | explain the change | `opacity`+`translateY` for the new row | 150–200ms small stagger | entire list reorder as "jump" |
| **Value change** (counter, status) | notice the change | gentle `opacity` pulse or colour transition | 150–200ms | change without signal; number "jumps" (need `tabular-nums`, `LAYOUT_INVARIANTS §3`) |
| **Drag affordance** (kanban) | show draggability | ghost + `transform`, drop-zone highlight | follow + 150ms | real reflow during drag; no ghost |
| **State transition** (loading→content) | without "jump" | crossfade skeleton↔content at equal height | 150ms | skeleton at different height → CLS (`LAYOUT_INVARIANTS §5`, V3) |
| **Drawer/Modal open** | spatial connection | `transform: translateX/Y` + `opacity` | 200–250ms | `left/width` animation; jerk |
| **Expand/collapse** (accordion, inspector) | continuity | `grid-template-rows`/`transform`, not `height:auto` animation | 200ms | `height` animation from/to auto → jank |

### What MICRO mode produces

`MICRO_SPEC_[MODULE].md` (or section in `DESIGN_SPEC`/`DEV_PROMPTS`):
```markdown
## MICRO SPEC: [screen/component]
Contour: operational (/admin | /app)
Responsiveness reference: [Linear list / Stripe table / Google Calendar event]

| Moment | Trigger | Property (transform/opacity) | Duration · easing | reduced-motion |
|--------|---------|------------------------------|-------------------|----------------|
| press  | :active | scale(0.98)                  | 100ms · ease-out  | no scale, instant |
| ...    | ...     | ...                          | ...               | ... |

Prohibitions: no layout properties; no public-site "character"; zero geometry shift.
Criterion for @QA_VISUAL: V7 geometryShiftOnState == 0; V8 0 layout animations, reduced ok.
```

### Who does what in MICRO mode
- **`@MOTION` (MICRO):** writes `MICRO_SPEC` — catalogue of screen moments with exact properties/timings.
- **`@DESIGN`:** statics and states already in `DESIGN_SPEC`; MICRO supplements, does not conflict.
- **`@DEV`:** implements strictly from `MICRO_SPEC` + `LAYOUT_INVARIANTS §7/§10`.
- **`@QA_VISUAL`:** verifies `V7` (zero shift) and `V8` (transform/opacity only, reduced-motion) at runtime.

### When to invoke MICRO
```
□ New operational screen with interactions (table, kanban, chat, dashboard, drawer forms)
□ Complaint "buttons/rows jitter, run off, glitch" on /admin or /app
□ @QA_VISUAL recorded 🔴 V7/V8 on a work screen → escalate to @MOTION (MICRO)
```
When NOT: a simple static screen without interactions; fixing an existing MICRO-pattern (then `@DEV` follows the canon).

---

## EMBEDDING (summary)

- `@MOTION` receives **5th mode — MICRO** (in addition to DIAGNOSIS/CONCEPT/SPEC/AUDIT) and **Ambition input parameter** for CONCEPT.
- Public site chain: `Ambition (brief) → @MOTION CONCEPT (archetype from HERO_ARCHETYPES + MOTION_LIBRARY hooks) → @DESIGN → @DEV → @QA_VISUAL`.
- Operational micro-moments chain: `@DESIGN SPEC → (on interactions) @MOTION MICRO → @DEV → @QA_VISUAL (V7/V8)`.
- `.cursorrules` ROLE MAP: `@MOTION` is supplemented with MICRO mode and a note about operational micro-moments (see `INTEGRATION_PATCHES.md`).

---

Reference: `roles/ROLE_MOTION.md` (modes, principles, §5 Motion Quality Gate) · `roles/MOTION_LIBRARY.md` (T/B/I/S/W/TR codes, §VII Performance) · `roles/HERO_ARCHETYPES.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` §1 (two contours), §4 (public site) · `roles/LAYOUT_INVARIANTS.md` §3, §5, §7, §10 · `roles/ROLE_QA_VISUAL.md` (V7, V8) · `roles/ROLE_DESIGN.md`
Version: 1.1 | 2026-07-06
