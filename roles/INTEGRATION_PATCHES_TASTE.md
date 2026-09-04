# INTEGRATION_PATCHES_TASTE.md
# Integrations for v6.20 upgrade "Concept Before Code": taste as executable recipes + layout collisions.
# STATUS: ✅ APPLIED 2026-07-06 to all listed files (this file is a record, following the INTEGRATION_PATCHES.md v6.16 pattern).
# Base at time of application: system v6.17 → "Concept Before Code" Law received number **28** (27 occupied by "Licence Purity" v6.17);
# role addenda continued v6.16/6.18/6.19 → v6.20 series. Below "Law 27" reads as "Law 28".
# New Layer P files: roles/CONCEPT_DNA_LIBRARY.md · roles/VISUAL_CONCEPT_PROTOCOL.md

> After applying — add a record to `SYSTEM_UPGRADE_MANIFEST.md` and verify links.
> Patches 5–6 (collisions) are applicable independently of the concept layer — they close the "buttons overlapping" symptom deterministically.

---

## PATCH 1 — `.cursorrules`

### 1.1 — New ABSOLUTE LAW (after Law 27 "Licence Purity")

**Anchor:** Law 27 "Licence Purity" (end).
**Action:** insert Law 28 (numbering shifted: 27 is occupied by licences).

```
28. **Concept Before Code** — for any product with a showcase, aesthetics are born ONCE as
    docs/artifacts/VISUAL_CONCEPT_[PROJECT].md (@CREATOR, protocol roles/VISUAL_CONCEPT_PROTOCOL.md):
    concept phrase "a screen is a [world object of the niche]" + world from roles/CONCEPT_DNA_LIBRARY.md
    (palette/typography/effect kit/motion personality — as ready recipes, not slots).
  - Palettes and font pairs are not generated from scratch — taken from the world; any replacement = one line with a reason.
  - TASTE GATE (cliché blacklist K1–K10 + "remove the logo" test) — blocker for passing the concept forward.
  - **Grey SaaS** (the category's converged low-chroma minimalism) — not the default world for a showcase; it is the operational contour.
  - Changing the concept on a live project — only @DESIGN mode RESKIN (skeleton stays, skin is replaced entirely).
  - Showcase lives in the world entirely; /admin,/app inherit palette/font/radii, effect kit is not transferred.
```

### 1.2 — Addition to Law 26 (collisions and layers)

**Anchor:** Law 26, after the bullet "hover/focus/press do NOT change geometry…".
**Action:** insert a bullet.

```
  - Interactive elements do not overlap: pairs of visible buttons/links/fields have zero
    bounding-box intersection on 360/768/1280/1920 (except parent-descendant); z-index — only from
    the project Z-scale (raw numbers are forbidden); absolute/fixed — only inside the declared
    anchor container with reserved space. Canon: roles/LAYOUT_INVARIANTS.md §12; sensor: @QA_VISUAL V12.
```

### 1.3 — ROLE MAP

**Anchor:** `@CREATOR` row.
**Action:** replace the purpose cell.

```
| @CREATOR | Visionary. 1 question → INDUSTRY INTELLIGENCE → BUSINESS_LOGIC.md → VISUAL_CONCEPT (world from roles/CONCEPT_DNA_LIBRARY.md, protocol roles/VISUAL_CONCEPT_PROTOCOL.md) → 4 passports by derivation | Once per project |
```

**Anchor:** `@DESIGN` row.
**Action:** replace the purpose cell.

```
| @DESIGN | Product Designer & UI Arbiter. AUDIT · SPEC · VERDICT · **RESKIN** → DESIGN_*.md. **Called for ANY new screen before @DEV.** Tier 0 of references = project world (VISUAL_CONCEPT); SaaS library — only operational patterns. RESKIN — world change without skeleton change (roles/VISUAL_CONCEPT_PROTOCOL.md §6). Constitution: roles/FRONTEND_DESIGN_EXCELLENCE.md | Before @DEV (any new screen); after @QA_ARCH (design issues); RESKIN — per protocol triggers |
```

### 1.4 — Layer P (process file list)

**Action:** add lines.

```
- **`roles/CONCEPT_DNA_LIBRARY.md`** — 12 concept worlds as executable recipes (hex palettes, font pairs with Cyrillic, CSS effect kits, motion personalities, Mantine skins) + Custom World Constructor + niche→world router
- **`roles/VISUAL_CONCEPT_PROTOCOL.md`** — "Concept before code": concept birth by @CREATOR, TASTE GATE (cliché blacklist), transfer map to passports, RESKIN mode for @DESIGN, model economics
```

### 1.5 — CHAIN PROTOCOL

**Anchor:** new project launch block, line `→ @CREATOR (…package for approval)`.
**Action:** add a line after it.

```
  → @CREATOR VISUAL CONCEPT: world from CONCEPT_DNA_LIBRARY → TASTE GATE → docs/artifacts/VISUAL_CONCEPT_[PROJECT].md → 4 passports by derivation
```

**Anchor:** showcase branch `→ @MOTION (CONCEPT or DIAGNOSIS first — always)`.
**Action:** replace with:

```
  → @MOTION (CONCEPT or DIAGNOSIS first — always; Step 0: reads VISUAL_CONCEPT and inherits world motion personality)
```

---

## PATCH 2 — `roles/ROLE_CREATOR.md`

### 2.1 — Replace Step 5.5

**Anchor:** heading `### STEP 5.5: VISUAL BOOTSTRAP — starting the project visual system` and its entire block.
**Action:** replace the block entirely.

```markdown
### STEP 5.5: VISUAL CONCEPT + BOOTSTRAP — aesthetics are born once

If the product has any user-facing UI — before handing over to @LEAD:

**5.5.A — VISUAL CONCEPT** (`roles/VISUAL_CONCEPT_PROTOCOL.md` §2–§4):
- World selection protocol (`roles/CONCEPT_DNA_LIBRARY.md`): Q1 niche material · Q2 audience posture · Q3 ambition (default confident);
- two-pass rule (plan → self-critique against default) + Chanel rule;
- TASTE GATE: cliché blacklist K1–K10 + "remove the logo" test — blocker;
- output: `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` — concept phrase, world, signature,
  copy-paste tokens (palette/fonts/motion), three "nevers".
Palette and fonts are NOT invented — copied from the world recipe; replacement = one line with a reason.

**5.5.B — Passports by derivation** (`VISUAL_CONCEPT_PROTOCOL.md` §5 — transfer map):
- `01_DESIGN_PASSPORT.md`, `02_MOTION_LANGUAGE.md`, `10_TYPOGRAPHY_PASSPORT.md`, `11_UI_COMPOSITION_PASSPORT.md`
  are filled with values from the concept; `roles/DESIGN_DECISION_LIBRARY.md` adds operational models
  (navigation, status pairs, color balance). No `[hex]` placeholders remain in passports.

Layer rule unchanged: project `docs/` receive only finished passports + VISUAL_CONCEPT; no links to private `roles/` in them.
```

### 2.2 — Summary for the user

**Anchor:** block "Summary shown to the user for approval", after the line `MVP contains: …`.
**Action:** insert a line.

```
CONCEPT: [world from CONCEPT_DNA_LIBRARY] — signature: [one phrase] — "a screen is a [object]"
```

---

## PATCH 3 — `roles/ROLE_DESIGN.md`

### 3.1 — Fourth mode

**Anchor:** `## THREE MODES OF OPERATION` block.
**Action:** replace with:

```
## FOUR MODES OF OPERATION

MODE 1: AUDIT   — finished screen → verdict + list of fixes
MODE 2: SPEC    — new screen → specification → handover to @DEV
MODE 3: VERDICT — two solutions → one winner + justification
MODE 4: RESKIN  — concept world change on a live project → Change Map (skin), skeleton is untouchable
```
And add to triggers: `"change concept / different character / rebranding / make it in style X" → MODE 4: RESKIN (protocol: roles/VISUAL_CONCEPT_PROTOCOL.md §6; output docs/artifacts/DESIGN_RESKIN_[PROJECT]_v[N].md)`.

### 3.2 — Golden library: Tier 0 + extension beyond SaaS

**Anchor:** `## GOLDEN REFERENCE LIBRARY` heading, before the Tier 1 table.
**Action:** insert.

```markdown
### Tier 0 — Project World (primary reference)

`docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` + world recipe from `roles/CONCEPT_DNA_LIBRARY.md`.
For showcase, the "Reference" field in SPEC is filled with the world ("World 2 INK & SEAL: letterpress headline, leaf-on-table"),
not someone else's SaaS. Tier 1–2 below apply ONLY to operational patterns (drawer vs modal, table density,
inbox/kanban mechanics) — and only after compatibility check with the world. This generalises the v6.18 rule
(project-native benchmark) to all projects: every project now has a concept.

### Tier 1.5 — References beyond SaaS (by worlds)
| World | Compositional references |
|-------|--------------------------|
| Gloss / luxury / museum | print gloss (spreads), fashion and watch brand storefronts, auction catalogues, Apple product pages (photo direction) |
| Paper / document | official letterheads, book typesetting, print-like editorial sites, distraction-free Writer |
| Poster / pop | posters and zines, covers, consumer super-app promo mechanics |
| Instruments / speed | dashboards and oscilloscopes, car configurators, telemetry |
| Garden / care | apothecary labels and herbaria, contemporary clinic environments |
Full recipes (hex/fonts/effects/motion) — in world recipes in `CONCEPT_DNA_LIBRARY`; do not duplicate here.
```

### 3.3 — Showcase SPEC entry

**Anchor:** MODE 2 SPEC, "Entry:" line.
**Action:** extend.

```
Entry (showcase): + docs/artifacts/VISUAL_CONCEPT_[PROJECT].md — required. No concept → stop,
request @CREATOR (Step 5.5.A). SPEC for showcase without concept = SPEC without Component Map (blocker).
```

---

## PATCH 4 — `roles/FRONTEND_DESIGN_EXCELLENCE.md`

### 4.1 — New §8. MANTINE DE-BRANDING (required theme depth)

**Anchor:** after §7, before Reference.
**Action:** insert section.

```markdown
## §8. MANTINE DE-BRANDING — Mantine as an engine, not a face

> Acceptance criterion: **a screen cannot be identified as "a Mantine site"**. If you put
> a screenshot from Mantine docs next to our screen — only the behaviour is common, not the face.
> Default Mantine blue/radii/shadows on a showcase = 🔴 at @QA_ARCH (Visual Quality Gate).

### 8.1 Required theme overrides (minimum for any project)

```ts
// theme.ts — values FROM VISUAL_CONCEPT (project world), not from imagination
export const theme = createTheme({
  fontFamily: 'var(--font-body)',                     // from world
  headings: { fontFamily: 'var(--font-display)', fontWeight: '600' },
  primaryColor: 'brand',
  colors: { brand: brandScale /* 10 steps from --accent of world: generate-colors or manual */ },
  defaultRadius: 'md',
  radius: { xs:'…', sm:'…', md:'…', lg:'…', xl:'…' }, // world radius language (0 for SWISS/GLOSSY, 16 for SOFT/POP)
  shadows: { /* world shadow recipe: "leaf on table" / hard offset / pedestal */ },
  components: {
    Button:{ styles:{ root:{ /* world button language: uppercase/letter-spacing/clip-path/border */ } } },
    Paper:{ styles:{ root:{ /* world surface */ } } },
    Input:{ styles:{ input:{ /* world field: form line / instrument / soft border */ } } },
  },
});
```
Plus `cssVariablesResolver` — pipe world tokens (`--accent`, `--ink`, `--ease-*`) into Mantine variables,
so tokens live in one place (`index.css` of the world) and Mantine consumes them.

### 8.2 World effect kit — a separate layer

`frontend/src/theme/effects.css` — only world effect kit classes (`.gloss`, `.seal`, `.btn-br`, …)
from `CONCEPT_DNA_LIBRARY`. Mantine components receive effects via `className`, not inline copy-paste.
At RESKIN this file is replaced entirely (Change Map, step 4) — this is "skin separate from skeleton".

### 8.3 Underutilised Mantine power map (use, do not reinvent)
| Capability | Where it wins |
|------------|---------------|
| Styles API (`styles`/`classNames` on slots) | any component → world language without forking |
| `cssVariablesResolver` + CSS variables | single token point; RESKIN = replacing the token file |
| Polymorphism `component=`/`renderRoot` | button-as-link, card-as-article — without wrapper hacks |
| `Spotlight` | Cmd+K search — ready, styled to the world |
| `Carousel` (embla) | carousels with §11 LAYOUT_INVARIANTS contract instead of custom |
| `NumberFormatter` / `tabular-nums` | money/metrics without "jumping" numbers (§3) |
| `Menu`/`HoverCard`/`Popover` layers | z-index from Mantine layer scale — integrates with §12 LAYOUT_INVARIANTS |
| `Transition`/`Collapse` | entry/exit and accordions without height:auto animation (§10) |
| `use-move`, `use-resize-observer`, `use-intersection` | drag scales, in-view autoplay — hooks instead of hand-rolling |

### 8.4 Showcase: right to headless

For showcase pages with a strong effect kit, this is acceptable: Mantine — behaviour (Modal/Drawer/Carousel/Spotlight),
face — own T1 primitives on world tokens. Documented by @ARCH in the epic as one line; mixing
two design system styles on one screen is forbidden.
```

### 4.2 — Pointer in §5

**Anchor:** §5 "REFERENCES — EXAMPLES FOR EACH SCREEN TYPE", first line.
**Action:** insert before the table.

```
> The table below covers OPERATIONAL screen types. SHOWCASE reference — the project world:
> `docs/artifacts/VISUAL_CONCEPT_*` + `roles/CONCEPT_DNA_LIBRARY.md` (Tier 0 in ROLE_DESIGN).
```

### 4.3 — Visual Quality Gate (§6) — addition

**Anchor:** "SHOWCASE (marketing):" block.
**Action:** add items.

```
□ VISUAL_CONCEPT exists; screen palette/fonts match the world (replacements — documented)
□ TASTE GATE: none of clichés K1–K10; one signature element present exactly once
□ Mantine theme de-branded (§8.1): headings/radius/shadows/Button — world language, not default
```

---

## PATCH 5 — `roles/LAYOUT_INVARIANTS.md`

### 5.1 — New §12. COLLISION & STACKING — elements do not overlap

**Anchor:** after §11, before "CHECKLIST @DEV".
**Action:** insert section.

```markdown
## §12. COLLISION & STACKING — interactive elements do not overlap, layers by scale

> Symptom this section closes: "buttons overlap each other", badge covers
> a neighbouring link, sticky bar covers CTA, dropdown goes under a card.
> Causes are always the same: raw z-index, absolute outside anchor, button groups on margin,
> fixed widths in flex children without wrap, negative margins on interactive elements.

### 12.1 Z-SCALE — raw z-index is forbidden

The single project scale (index.css), aligned with Mantine layers:

```css
:root {
  --z-base: 0;        /* flow */
  --z-raised: 1;      /* raised card, active tile */
  --z-sticky: 20;     /* sticky table headers, local bars */
  --z-page-overlay: 50; /* in-page overlays (drag-ghost, spoiler) */
  /* System layers — ONLY Mantine variables, not numbers:
     app 100 · overlay/modal 200 · popover 300 · max 9999 */
}
```
Rules: in components — only `var(--z-*)` or Mantine layer variable; literal `z-index: 999`
in application code = 🔴; new scale level — @FRONTEND decision, not a local improvisation;
`position: relative` without need — do not create (extra stacking contexts break layer order).

### 12.2 ANCHOR POSITIONING — absolute/fixed only inside a declared anchor

- `position:absolute` is allowed only inside an anchor parent (`position:relative`) that
  **reserves space** for the element: badge on a card lives in the padding zone of the anchor
  (`top:8px; right:8px` with `padding-top≥40px` or a dedicated corner), not "hanging" over neighbours.
- Going beyond the anchor bounds (`top:-N`, "sticker half outside") — only if the anchor
  compensates with outer margin ≥ protrusion: `margin-top: calc(N + 4px)` on the anchor or wrapper.
- `position:fixed` (headers, bottom bars, FAB): body/layout reserves space
  (`padding-block` = bar height + safe-area `env(safe-area-inset-*)`); fixed element
  has no right to cover interactive elements of the flow on any viewport.
- Tooltips/dropdowns — via layered components (Portal/Popover), not a custom absolute in the flow.

### 12.3 INTERACTIVE DISTANCE — action groups are not built with margins

- Any group of buttons/chips/icons: flex/grid container + `gap ≥ 8px` (touch zones 44px — §Ergonomics)
  + `flex-wrap: wrap` by default. Chains of `margin-left` between sister-buttons are forbidden.
- Negative margins on interactive elements are forbidden (avatar stack — exception:
  decorative, not clickable individually, or only the top one is clickable).
- Flex children with fixed width: container must support wrap OR child has `min-width:0` + truncate (§8);
  "two 240px items in a 400px container without wrap" — source of overlaps, caught by V12.
- Text next to an icon-button: interactive does not "stick" to foreign text — 8px minimum.

### 12.4 CRITERION (deterministic, for @QA_VISUAL V12)

```
At 360 / 768 / 1280 / 1920, states default + hover + open-menu:
  pairwiseIntersection(visible [button, a, input, select, [role=button], [tabindex]]) — 
  bounding-box intersection area of any pair = 0 (parent-descendant pairs excluded).
  zIndexAudit: computed z-index ∉ {var(--z-*), Mantine layers} → violation.
  fixedCoverage: fixed/sticky elements do not intersect flow interactive after scroll to anchors.
Any number > 0 = 🔴 with pair of selectors and intersection area in px².
```
```

### 5.2 — Checklist @DEV

**Anchor:** "CHECKLIST @DEV (before delivering a UI task)".
**Action:** add items.

```
[ ] §12.1: no literal z-index in the task — only --z-* / Mantine layers
[ ] §12.2: every absolute — inside an anchor with reserve; fixed bars compensated by layout padding
[ ] §12.3: action groups = flex + gap + wrap; no margin-overlap, no negative margin on interactive
```

### 5.3 — Checklist @QA_VISUAL (mapping)

**Anchor:** "CHECKLIST @QA_VISUAL (invariant → vector mapping)".
**Action:** add a row.

```
§12 Collisions and layers → V12 (pairwiseIntersection = 0 · zIndexAudit · fixedCoverage)
```

---

## PATCH 6 — `roles/ROLE_QA_VISUAL.md`

### 6.1 — New vector V12

**Anchor:** after V11 (before the "sensor core" block).
**Action:** insert.

```markdown
### V12 — Interactive Collisions and Layer Discipline (Collision & Stacking)

- **Measured:** `pairwiseIntersection(interactives)` at 360/768/1280/1920 in states
  default / hover / open menu-drawer; `zIndexAudit()` — computed z-index outside the
  `--z-*`/Mantine scale; `fixedCoverage()` — flow coverage by fixed/sticky elements.
- **🔴:** intersection area of any pair of visible interactive elements > 0 px² (except parent-descendant);
  literal z-index in application code; fixed bar covers CTA/field on any viewport.
- **🟡:** distance between adjacent interactive elements < 8px in touch context; stacking context
  without reason (relative+z-index on static).
- **Finding format:** `[V12] .card-actions .btn-edit ∩ .btn-delete = 96px² @360 default —
  cause: margin chain without wrap — rule §12.3 — fix: flex+gap+flex-wrap`.
- Canon of rules: `roles/LAYOUT_INVARIANTS.md §12`. Escalation of systemic cases
  (layer architecture, monster header) → @DESIGN/@FRONTEND.
```

**Anchor:** line "Vectors V1–V4, V7–V8 and V11 — sensor core".
**Action:** replace with: `Vectors V1–V4, V7–V8, V11 and **V12** — sensor core (geometry, overflow, interactions, scroll stability, collisions).`

### 6.2 — TASTE check in gate

**Anchor:** acceptance/output section VISUAL_QA_REPORT.
**Action:** add item.

```
[ ] Concept-conformance: showcase screenshot against docs/artifacts/VISUAL_CONCEPT_* —
    palette/display-font/signature match; clichés K1–K10 not found.
    Mismatch = 🟡 (local) / 🔴 (systemic) with owner @DESIGN, not @DEV.
```

---

## PATCH 7 — `roles/ROLE_MOTION.md` + `roles/MOTION_AMBITION_DIAL.md`

### 7.1 — `ROLE_MOTION.md`, MODE 2 CONCEPT: Step 0

**Anchor:** "MODE 2: CONCEPT", before "Step 1: One word".
**Action:** insert.

```
**Step 0: Read VISUAL_CONCEPT.** The project world has already defined the motion personality
(easing tokens, durations, signature move, hooks, archetype affinities — world recipe in
roles/CONCEPT_DNA_LIBRARY.md). "One word" (Step 1) refines and develops the world personality,
it does not replace it. Conflict of word with world — escalate to @CREATOR/@DESIGN (RESKIN possible),
not a silent physics substitution. No VISUAL_CONCEPT → stop, request @CREATOR.
```

### 7.2 — `MOTION_AMBITION_DIAL.md`, rule against easing degeneration

**Anchor:** "What the dial changes in @MOTION behaviour", item 4.
**Action:** extend the item.

```
Also: a single `power2.out`/`ease-in-out` on all project techniques = personality degeneration —
🔴 at confident+. Easing and durations are taken from the world motion personality (VISUAL_CONCEPT):
ink absorbs ≠ sticker slaps ≠ instrument clicks ≠ foil flows. Extended vocabulary:

| Personality | Easing | Range |
|-------------|--------|-------|
| Absorption (paper/ink) | cubic-bezier(.25,.46,.45,.94) + blur 2→0 | 400–600ms |
| Snap (grid/instruments) | cubic-bezier(.7,0,.3,1) / linear / steps() | 100–220ms |
| Spring (pop/craft) | cubic-bezier(.34,1.56,.64,1) | 220–300ms |
| Velvet (luxury/museum) | ease / cubic-bezier(.4,0,.2,1) | 500–700ms |
| Acceleration (speed) | cubic-bezier(.7,0,.2,1) + skew entry | 140–260ms |
| Page turn (gloss) | cubic-bezier(.77,0,.18,1) | 600–900ms |
```

### 7.3 — `MOTION_AMBITION_DIAL.md`, @MOTION golden library

**Anchor:** golden library mention (bruno-simon · cuberto · obys · linear · activetheory) — in `.cursorrules` ROLE MAP and in ROLE_MOTION.
**Action:** extend the line.

```
+ project world compositional references (Tier 1.5 ROLE_DESIGN / world recipe CONCEPT_DNA_LIBRARY).
linear.app — reference ONLY for restrained operational tasks, not the showcase default.
```

---

## PATCH 8 — Header pointers (de-duplication, FRONTEND_CONSOLIDATION PART 3 style)

### 8.1 — `roles/DESIGN_DECISION_LIBRARY.md`

```
> **Semantic layer above this file — `roles/CONCEPT_DNA_LIBRARY.md`:** the project world defines VALUES
> (hex palette, font pairs, effects, motion personality). Here remain OPERATIONAL models
> (navigation, card/button models, contour motion profiles, color balance). Palette/Typography
> Directions below are legacy hints for the operational contour; for showcase, values come from the world.
```

### 8.2 — `roles/PROJECT_VISUAL_BOOTSTRAP_PROTOCOL.md`

```
> **Step 0 (mandatory, before Step 1): `roles/VISUAL_CONCEPT_PROTOCOL.md`** — birth of
> VISUAL_CONCEPT_[PROJECT].md from the world in `roles/CONCEPT_DNA_LIBRARY.md` + TASTE GATE.
> Step 2 "Choose A Design Direction" is performed WITHIN the chosen world (world = values,
> decision-library = operational models). Cross-Check extended: [ ] TASTE GATE passed,
> [ ] passports have no [hex] placeholders.
```

### 8.3 — `roles/HERO_ARCHETYPES.md` (Selection Protocol)

```
> Q1–Q3 are aligned with VISUAL_CONCEPT: the project world has archetype affinities (world recipe).
> @MOTION/@DESIGN choose from world affinities; going beyond affinities — with justification through material.
```

---

## PATCH 9 — `SYSTEM_UPGRADE_MANIFEST.md`

**Action:** add record.

```
v6.20 "Concept Before Code": Law 28; +roles/CONCEPT_DNA_LIBRARY.md (12 worlds as recipes),
+roles/VISUAL_CONCEPT_PROTOCOL.md (concept @CREATOR, TASTE GATE, RESKIN @DESIGN, model economics);
ROLE_CREATOR Step 5.5.A/B; ROLE_DESIGN mode 4 RESKIN + Tier 0/1.5; FRONTEND_DESIGN_EXCELLENCE §8
Mantine De-Branding; LAYOUT_INVARIANTS §12 Collision & Stacking (+Law 26 bullet);
ROLE_QA_VISUAL V12 + concept-conformance; @MOTION Step 0 motion personality inheritance,
easing personality vocabulary; header pointers DESIGN_DECISION_LIBRARY / VISUAL_BOOTSTRAP / HERO_ARCHETYPES.
```

---

## PATCHES 10–12 — Consumer roles and registries (applied beyond the plan)

### PATCH 10 — `v6.20 ADDENDUM` at end of files (house style v6.16/6.18/6.19)

| File | Addendum summary |
|------|-----------------|
| `roles/ROLE_LEAD.md` | VISUAL CONCEPT node in new project chain; routing "change concept" → @DESIGN RESKIN (single-axis rule); TASTE GATE and V12 gates; model economics; `@LEAD → @DESIGN (RESKIN)` handover template |
| `roles/ROLE_FRONTEND.md` | Mantine de-branding ownership (§8) and Z-scale (§12.1); `theme/effects.css` layer; Visual Quality Gate extended with concept items |
| `roles/ROLE_DEV.md` | §12.1–12.3 checklist (z-index/anchors/gap+wrap); tokens and effects only from passports/`effects.css`, escalation instead of improvisation; V12 acceptance |
| `roles/ROLE_QA_ARCH.md` | Concept-conformance in Visual Quality Gate; by code — literal z-index/absolute outside anchor (Vector 6.1), render — at V12; RESKIN wave: "do not touch" contract by diff |
| `roles/ROLE_DESIGN.md` | Addendum v6.20 (beyond §3.1–3.3 edits): RESKIN protocol, TASTE control in AUDIT |
| `roles/ROLE_MOTION.md` | Addendum v6.20: personality inheritance, easing degeneration ban, linear only for restrained |

### PATCH 11 — Registries (system rule "new file in roles/ → FILE_MAP + RAG_CANON")

- `roles/FILE_MAP.md` — +2 lines (CONCEPT_DNA_LIBRARY, VISUAL_CONCEPT_PROTOCOL) in protocol cluster.
- `roles/RAG_CANON.md` — +2 lines in frontend cluster; priority line extended: "project aesthetic values → VISUAL_CONCEPT_* (world from CONCEPT_DNA_LIBRARY)" — first.
- `roles/FRONTEND_CONSOLIDATION.md` — single source map: +2 lines (concept world = values; protocol = process), geometry extended to §1–§12.

### PATCH 12 — Numbering fix in new files

`roles/VISUAL_CONCEPT_PROTOCOL.md` §8: "Law 27" → "Law 28".

---

## DEPLOYMENT ORDER (half an hour, no risk)

```
1. Place two new files in roles/ (Patch 0 — the files themselves).
2. Patches 5–6 (collisions §12 + V12) — independent, applied first: immediately fix "overlapping buttons".
3. Patches 1–4, 7–8 — concept layer.
4. Patch 9 — manifest. Run MIRROR filter: changes close [VDG] (aesthetics had no value-source owner),
   [RG] (collisions belonged to no §), [CON] (Linear showcase default contradicted HERO_ARCHETYPES/AMBITION_DIAL — lifted by Tier 0).
5. First pilot run: 1 showcase through full cycle CONCEPT → passports → MOTION → SPEC → DEV → V1–V12.

> Steps 1–4 completed 2026-07-06 (files in this package). Step 5 — pilot — remains.
```

---

## PATCHES 13–14 — Generative layer (applied; closes "understanding", not just recipes)

### PATCH 13 — `roles/CONCEPT_ANATOMY.md` (new) + integration
Concept anatomy: 8 DNA axes (material·light·color-logic·type-character·form·composition·motion-physics·signature) with variant tables; axis coherence rules + forbidden combinations; **reference protocol** — format SOURCE→EXTRACT→TRANSFER→NOT TAKE, criterion "a third person reproduces the technique without seeing the source", ≤1 SaaS, reference typology (print/object/environment/screen worlds); constructor §5 — main path, 12 worlds = presets (threshold ≥6 axes).
Integrated: `.cursorrules` (Law 28 + Layer P), `ROLE_CREATOR` 5.5.A (DNA passport + references), `VISUAL_CONCEPT_PROTOCOL` (header + artifact template: DNA table and reference block), `CONCEPT_DNA_LIBRARY` (header: anatomy presets), `ROLE_DESIGN` Tier 1.5 (reference format in SPEC), registries.

### PATCH 14 — `roles/LAYOUT_COMPOSITION.md` (new) + integration
Construction grammar: three space laws; algebra of 8 primitives with CSS core and Mantine mapping; proximity law (×2, one STACK — one gap); dimension policy; action grammar G1–G6; three questions before absolute; overlap diagnosis protocol S1–S5; §8 flexibility (retreat — only named aloud).
Integrated: `.cursorrules` (Law 26 + Layer P), `LAYOUT_INVARIANTS` (header pair "builds/checks"), `ROLE_DEV` (grammar before code + checklist), `ROLE_FRONTEND` (primitive map and scale owner), `ROLE_QA_VISUAL` V12 (root causes), registries.

---

## PATCHES 15–16 — v6.21 "Visibility" (applied)

### PATCH 15 — @SEO role + canon (`roles/ROLE_SEO.md`, `roles/SEO_CANON.md`)
Law 29 in `.cursorrules`; @SEO in ROLE MAP (gate-role modelled on @SEC); chain nodes: CORE after VISUAL CONCEPT (exchange "demand language ↔ delivery world"), ONPAGE in @DESIGN entry for indexable pages, TECH gate next to @SEC before showcase deploy, MONITOR after launch; SEO artifacts (SEMANTIC_CORE / SEO_ONPAGE / SEO_TECH_AUDIT / SEO_REPORT) in Layer W; addenda: `ROLE_ARCH` (rendering ADR: showcase SSG/SSR, indexable SPA forbidden), `ROLE_CREATOR` (5.5.A exchange with semantic core), `ROLE_DESIGN` (SPEC entry += ONPAGE), `ROLE_DEV` (technical SEO line by line + curl-without-JS self-check), `ROLE_LEAD` (routing 4 points, conflict resolved by intent); registries FILE_MAP/RAG_CANON.

### PATCH 17 — v6.22 "Async Contour" (applied)

> Completed 2026-07-07 (after interruption): +Law 30 "Async Discipline" in `.cursorrules` (+Layer P records and JOB_PASSPORTS artifact in Layer W); pointer in `SYSTEM_DESIGN_PROTOCOL` Step 6 (canon = discipline, step = choice; output += JOB_PASSPORTS); addendum `ROLE_QA_ARCH` — Async vector (grep heuristics AP-1/2/13/14, passport control, T-series acceptance); addendum `ROLE_PENTEST` — T-A…T-G in CRASH_TEST arsenal (T-D = regression §0); canon reinforcements: AL-2 escalation hard-kill + UI truth (cancelling→cancelled), §5 prefetch/acks_late by class, AP-13 "waiting its turn", AP-14 "transaction over external call/enqueue".
`roles/ASYNC_WORKERS_CANON.md` (new): §0 incident "supervisor task" → laws; 3 planes; AL-1…10; patterns Saga/Outbox/Flow/limiter (BullMQ/Celery); JOB PASSPORT; sizing; T-A…G; AP-1…12. Integrations: `ROLE_ARCH` addendum (ADR + JOB_PASSPORTS before code), `ROLE_DEV` AL checklist, `ROLE_PENTEST` CRASH_TEST+=T-vectors, `SYSTEM_DESIGN_PROTOCOL` Step 6 blocker pointer, `.cursorrules` Layer P (v6.22), FILE_MAP/RAG_CANON, manifest.

### PATCH 16 — v6.20 refinements based on review
`VISUAL_CONCEPT_PROTOCOL` §2 PASS 1 → anatomy constructor (preset at ≥6 axes; conflict "world choice vs constructor" resolved); `LAYOUT_COMPOSITION` §5 += G7 form grammar (one column, STACK label→field→error with height reserve — error does not shift fields; field+button row = CLUSTER flex-start); `CONCEPT_ANATOMY` §4.2 += "competitor output = intent map (@SEO), but aesthetic ANTI-reference (T1)".
```

---

### PATCH 18 — v6.23 "Decision Backbone" (applied 2026-07-07)
`roles/ARCH_SPINE_PROTOCOL.md` (new): 12 vertebrae of every architectural decision as numbers before code; complexity ladder 0–4 with upgrade triggers; synchronous path timeouts/deadlines (gap: 0 mentions across the whole system before this file); one-page ARCH_SPINE template. Integrated: `.cursorrules` (Law 31, Layer P/W, v6.23), `ROLE_ARCH` (STEP 0 += tier/SLO/tenancy/DR; backbone with ADR), `ROLE_QA_ARCH` (Spine vector: completeness, ladder as number, grep T5 timeouts, tenancy leak, restore freshness), `SYSTEM_DESIGN_PROTOCOL` (steps → vertebrae 8–10), registries.

### PATCH 19 — v6.24 "Integrity Under Concurrency" (applied 2026-07-07)

`roles/DATA_INTEGRITY_CANON.md` (new): invariant protection hierarchy (DB schema > lock/version > code hint); race catalog with SQL recipes (slot exclusion, atomic balance guard, optimistic version+409, partial unique, per-tenant counters, SKIP LOCKED); transaction discipline; Idempotency-Key + webhook inbox; money as minor integers; time UTC/[start,end)/branch TZ; tenant isolation by schema (composite FK, per-tenant unique, RLS safety net); soft-delete and audit; INVARIANT LEDGER in ADR; race tests T-H1…H7; anti-patterns. Distinction from Backbone: this canon — **Law 32 / v6.24** (Backbone remained Law 31 / v6.23). Integrated: `.cursorrules` (Law 32, Layer P), addenda `ROLE_ARCH` (INVARIANT LEDGER before code, schema rules §5–§8), `ROLE_DEV` (data checklist + local T-H), `ROLE_QA_ARCH` (Data-Race vector + ledger↔migration cross-check), `ROLE_PENTEST` (T-H series, IDOR-matrix T-H5), `MIGRATIONS_PLAYBOOK` (constraints on live tables: NOT VALID→VALIDATE, CONCURRENTLY, tenant retrofit); registries FILE_MAP/RAG_CANON; manifest v6.24.

### PATCH 20 — v6.25 "Pipeline Resilience" (applied 2026-07-08)

Real incident #2 (RAG ingest: D1–D9) canonised. `ASYNC_WORKERS_CANON.md` → v2.0: PART II (§9 breakdown, §10 AL-11 lease-single-clock + heartbeat=progress, §11 AL-13 atomic rate limiter at the wire, §12 AL-12 single retry owner + cross-cutting taxonomy, §13 PIPELINE PASSPORT + "five questions before a pipeline", §14 T-I1…I6 + AP-15…20). Integrated: `.cursorrules` (Law 30 v2.0 block, PIPELINE_PASSPORT in Layer W, v6.25), addenda `ROLE_ARCH` (five questions before code), `ROLE_DEV` (external call checklist), `ROLE_QA_ARCH` (Liveness/Integration vector), `ROLE_PENTEST` (T-I).

---

Reference: `roles/CONCEPT_DNA_LIBRARY.md` · `roles/VISUAL_CONCEPT_PROTOCOL.md` · `roles/LAYOUT_INVARIANTS.md` · `roles/ROLE_QA_VISUAL.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/MOTION_AMBITION_DIAL.md` · `roles/MIRROR_PROTOCOL.md` · `SYSTEM_UPGRADE_MANIFEST.md`
Version: 1.3 (system v6.25, applied; patches 1–20) | 2026-07-08
