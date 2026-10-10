# INTEGRATION_PATCHES.md
# Targeted integrations into existing system files.
# Each patch: FILE → ANCHOR (where) → ACTION → BLOCK (what to insert/replace).
# Principle: minimal changes (Law 1), only what is touched, copy-paste without rewriting full files.

> Apply in order. After applying — see `SYSTEM_UPGRADE_MANIFEST.md` (link verification).
> All new files go into `roles/`. All new artifact outputs go into `docs/artifacts/`. Render baselines — into `frontend/tests/visual/`.

---

## PATCH 1 — `.cursorrules`

### 1.1 — New ABSOLUTE LAW (after Law 25)

**Anchor:** line with Law 25 "Design Constitution", before `---` and `## ROLE MAP`.
**Action:** insert Law 26.

```
26. **Layout Invariants** — geometry is stable under variable content and across all viewports,
  deterministically, not "by eye".
  - Cards/tiles/rows at the same level — equal height regardless of text length (equal-height grid + line-clamp with reserved height).
  - Elements of variable width (buttons, badges, statuses) — `min-width` for the longest variant; numbers — `tabular-nums`.
  - Any size driven by content has a ceiling (max-height / line-clamp / overflow guard); media — with reserved `aspect-ratio`.
  - hover/focus/press do NOT change geometry in the flow (only color/shadow/transform/opacity); horizontal overflow is forbidden on any viewport.
  - Animations — only `transform`/`opacity`; `prefers-reduced-motion` is respected.
  Canon: `roles/LAYOUT_INVARIANTS.md`. Verified by **@QA_VISUAL** (render → measure → compare): measurable invariant violation = 🔴, @QA_VISUAL does not issue 🟢.
```

Also add a version header line:
```
# v6.16: Law 26 — layout invariants; role @QA_VISUAL (render sensor) added to ROLE MAP and chain after @QA_ARCH; @MOTION extended with MICRO mode and ambition dial; Layer P: LAYOUT_INVARIANTS, ROLE_QA_VISUAL, HERO_ARCHETYPES, MOTION_AMBITION_DIAL; QA_ARCH GATE: render-geometry delegated to @QA_VISUAL.
```
And at the end — `Version: 6.16 | 2026-06-14`.

### 1.2 — ROLE MAP: new role

**Anchor:** `## ROLE MAP` table, row `@QA_ARCH`.
**Action:** add a row after `@QA_ARCH` (and before `@QA`).

```
| @QA_VISUAL | Render sensor. render → measure → compare: geometry, overflow, CLS, states, micro-moments under hostile content → docs/artifacts/VISUAL_QA_REPORT_*.md. Baseline anchor frontend/tests/visual/__baseline__ | After @QA_ARCH 🟢, before @QA — for any UI |
```

**Anchor:** `@MOTION` row in ROLE MAP.
**Action:** replace the "When/Purpose" cell, adding MICRO and ambition.

```
| @MOTION | Creative Director & Motion Engineer. DIAGNOSIS · CONCEPT · SPEC · AUDIT · **MICRO**. Showcase: visual metaphor → archetype (`roles/HERO_ARCHETYPES.md`) → motion language; boldness level — `roles/MOTION_AMBITION_DIAL.md`. **MICRO** — micro-moments of operational screens (focus/press/success/transition). Golden library: bruno-simon · cuberto · obys · linear · activetheory. Constitution: `roles/ROLE_MOTION.md` | Before @DESIGN/@DEV (showcase); MICRO — for interactions in /admin·/app |
```

### 1.3 — CHAIN PROTOCOL: insert @QA_VISUAL

**Anchor:** `## CHAIN PROTOCOL` block, fragment:
```
@QA_ARCH → docs/artifacts/QA_REPORT_[NAME].md        ← mandatory gate
  → 🔴 found? → back to @DEV with specific list of fixes
  → 🟢 clean? → pass to @QA
```
**Action:** replace with:
```
@QA_ARCH → docs/artifacts/QA_REPORT_[NAME].md        ← mandatory business gate (by code)
  → 🔴 found? → back to @DEV with specific list of fixes
  → 🟢 clean? → [UI change?] → @QA_VISUAL ; otherwise → @QA
  ↓
@QA_VISUAL → docs/artifacts/VISUAL_QA_REPORT_[NAME].md  ← mandatory visual gate (by render)
  → render → measure → compare against baseline; vectors V1–V10
  → 🔴 (number/diff)? → back to @DEV (rule LAYOUT_INVARIANTS + criterion-number) or escalate @DESIGN/@MOTION/@AUDITOR
  → 🟢 clean? → pass to @QA
```

**Anchor:** in "Chain rules" after the line about `@QA_ARCH is mandatory`.
**Action:** add:
```
- **@QA_VISUAL is mandatory for any UI change** — between @QA_ARCH (🟢 by code) and @QA. Measures rendered geometry (does not only read code): equal-height, overflow, CLS, states, micro-moments — under fixtures empty/single/typical/many/longtext/i18n at viewports 360/768/1280/1920. Without its 🟢 the visual part of GATE-4 is not closed.
```

**Anchor:** showcase chain block `[Landing / showcase / portfolio?] → @MOTION (CONCEPT...)`.
**Action:** replace with:
```
[Landing / showcase / portfolio?]
  → Ambition set in brief (restrained/confident/bold/experimental; default confident) — roles/MOTION_AMBITION_DIAL.md
  → @MOTION (CONCEPT or DIAGNOSIS first — always): metaphor → ARCHETYPE (roles/HERO_ARCHETYPES.md) → motion language → MOTION_SPEC
[Operational screen with interactions?]
  → @MOTION (MICRO): MICRO_SPEC micro-moments (focus/press/success/transition) — roles/MOTION_AMBITION_DIAL.md
```

### 1.4 — QA_ARCH GATE: delegate render-geometry

**Anchor:** `## QA_ARCH GATE` block, item `□ **Visual Quality Gate** (...)`.
**Action:** add at the end of this item:
```
  - **Render-geometry delegated to @QA_VISUAL.** @QA_ARCH checks token/state presence by code; the actual rendered geometry (equal-height, overflow, CLS, zero-shift interactions) is measured by @QA_VISUAL after @QA_ARCH 🟢. @QA_ARCH does not issue the final visual 🟢 for render — only for code level.
```

### 1.5 — PROJECT MEMORY → Layer P: new files

**Anchor:** `### Layer P — process norm` block, after `roles/MOTION_LIBRARY.md`.
**Action:** add:
```
- **`roles/LAYOUT_INVARIANTS.md`** — canon of deterministic layout invariants (equal-height, reserved height, min-width, aspect-ratio, zero-shift, no-layout-animation); verified by @QA_VISUAL
- **`roles/ROLE_QA_VISUAL.md`** — render sensor: Playwright harness, measurement tools, vectors V1–V10, baseline anchor, debug cycle
- **`roles/HERO_ARCHETYPES.md`** — library of 8 hero/composition archetypes + selection protocol (removes the "text+mockup" default)
- **`roles/MOTION_AMBITION_DIAL.md`** — ambition dial (input to @MOTION CONCEPT) + MICRO mode (micro-moments of operational screens)
```

### 1.6 — Reference (`.cursorrules` footer)

**Anchor:** `Reference: ...` line at the end.
**Action:** append to the end of the list:
```
 · roles/ROLE_QA_VISUAL.md · roles/LAYOUT_INVARIANTS.md · roles/HERO_ARCHETYPES.md · roles/MOTION_AMBITION_DIAL.md
```

---

## PATCH 2 — `roles/ROLE_QA_ARCH.md`

**Anchor:** heading `### Vector 6 — Frontend Visual & UX Quality`, start of section.
**Action:** insert right under the heading:
```
> **Separation from @QA_VISUAL:** this vector checks visual requirements **by code** (presence of tokens, states, hover in styles, reference in the spec). **Rendered geometry** — card height equality, overflow, layout shift, zero-shift interactions, behaviour under hostile content — is measured by **@QA_VISUAL** (render → measure → compare) after your 🟢. Do not duplicate or substitute: you are code-level, it is pixel-level. If you see a geometry risk that cannot be confirmed by code — mark it as "→ @QA_VISUAL: verify by render", do not issue the final visual 🟢 for render.
```

**Anchor:** `Reference: ...` footer.
**Action:** append `· roles/ROLE_QA_VISUAL.md · roles/LAYOUT_INVARIANTS.md`.

---

## PATCH 3 — `roles/ROLE_DESIGN.md`

**Anchor:** `## MODE 2 — SPEC`, step `**Step 3 — Structure (Layout Decision)**`.
**Action:** add at the end of Step 3:
```
For **showcase/landing** — choose a composition archetype from `roles/HERO_ARCHETYPES.md` (Selection Protocol), do not default to "text left + mockup right". Record the line "Archetype: [A–H] — rationale". If @MOTION already chose an archetype in CONCEPT — follow it, do not change without VERDICT.
```

**Anchor:** `**Step 6 — Micro-design (Pixel Decisions)**`.
**Action:** add at the end of Step 6:
```
**Geometry stability** — do not leave @DEV guessing: specify equal-height for grids, line-clamp + height reserve for headings, min-width for variable buttons/badges, aspect-ratio for media (canon `roles/LAYOUT_INVARIANTS.md`). This is part of the specification, not "polish later".
```

**Anchor:** `REFERENCE` footer.
**Action:** append `· roles/HERO_ARCHETYPES.md · roles/LAYOUT_INVARIANTS.md · roles/ROLE_QA_VISUAL.md`.

---

## PATCH 4 — `roles/ROLE_MOTION.md`

**Anchor:** `## FOUR MODES` heading.
**Action:** rename to `## FIVE MODES` and after `### MODE 4: AUDIT` add:
```
---

### MODE 5: MICRO
*When: an operational screen (/admin, /app) with interactions — refined micro-moments are needed*

This is NOT showcase expression. This is functional micro-feedback at the level any dense professional tool holds itself to: short durations, zero layout shift, prefers-reduced-motion. Catalogue of moments (focus/press/success/list enter/value change/drag/transition/drawer/expand), exact properties and timings, mode boundary — in `roles/MOTION_AMBITION_DIAL.md` (Part 2).

Outputs: `docs/artifacts/MICRO_SPEC_[MODULE].md` → @DEV implements → @QA_VISUAL verifies V7 (zero-shift) and V8 (only transform/opacity, reduced-motion).
```

**Anchor:** `### MODE 2: CONCEPT`, `**Step 1: One word**` (before it).
**Action:** insert before Step 1:
```
**Step 0: Ambition level.** Fix the concept boldness from the brief — `restrained | confident | bold | experimental` (default `confident`, NOT restrained). Canon: `roles/MOTION_AMBITION_DIAL.md` (Part 1). The level determines the amplitude and priority of techniques: at `bold/experimental`, 3D/WebGL is considered on equal footing/first, not "as a last resort". Performance rails (§VII, reduced-motion, only transform/opacity) do not move at any level.
```

**Anchor:** `### MODE 2: CONCEPT`, after `**Step 2: Metaphor → Visual Language**` (or at the end of CONCEPT before "Outputs").
**Action:** add:
```
**Step 2.5: Composition archetype.** By metaphor and ambition level, choose a hero archetype from `roles/HERO_ARCHETYPES.md` (Selection Protocol Q1–Q3). Record: "Archetype: [A–H] — rationale". This removes the "text left + mockup right" default.
```

**Anchor:** `Reference: ...` / `References: ...` footer.
**Action:** append `· roles/MOTION_AMBITION_DIAL.md · roles/HERO_ARCHETYPES.md · roles/ROLE_QA_VISUAL.md (V7/V8) · roles/LAYOUT_INVARIANTS.md §10`.

---

## PATCH 5 — `roles/ROLE_FRONTEND.md`

**Anchor:** `## PILLARS @FRONTEND` block, Pillar 9 "UI Performance".
**Action:** add at the end of Pillar 9:
```
Geometry stability — per `roles/LAYOUT_INVARIANTS.md` (equal-height siblings, heading height reserve, min-width for variable controls, aspect-ratio for media, zero-shift hover/focus). These are deterministic rules verifiable by render, not taste.
```

**Anchor:** `## HANDOVER TO @DEV` block, after `**Visual Quality Gate — must be run before handover:**` checklist.
**Action:** add a line under the checklist:
```
Visual Quality Gate (@FRONTEND/@DESIGN, by code) is complemented by the **@QA_VISUAL** render check after @DEV: tokens are verified by frontend before code, rendered geometry is measured by @QA_VISUAL after code. Neither replaces the other.
```

**Anchor:** `Reference: ...` footer.
**Action:** append `· roles/LAYOUT_INVARIANTS.md · roles/ROLE_QA_VISUAL.md · roles/HERO_ARCHETYPES.md · roles/MOTION_AMBITION_DIAL.md`.

---

## PATCH 6 — `roles/ROLE_QA.md`

**Anchor:** where @QA entry is described (after @QA_ARCH).
**Action:** add a line:
```
Before the final @QA pass for UI changes, 🟢 must come not only from @QA_ARCH (business audit by code), but also from **@QA_VISUAL** (visual audit by render: geometry, overflow, CLS, states, micro-moments). @QA does not duplicate visual measurements — it relies on VISUAL_QA_REPORT.
```

---

## PATCH 7 — `roles/ROLE_AUDITOR.md`

**Anchor:** `## WHEN CALLED` block, `✅` list.
**Action:** add an item:
```
✅ @QA_VISUAL recorded a visual symptom not reproducible by measurement after 2 cycles (layout "twitches/drifts" but metrics V1–V10 are clean) → layer-by-layer diagnosis
```

**Anchor:** Pillar 8 "Typical causes", *Frontend* block.
**Action:** add at the end of the *Frontend* block:
```
layout invariant violation (`roles/LAYOUT_INVARIANTS.md`): siblings not equal-height; heading without line-clamp/reserve → "jump"; hover changes a layout property → jitter; layout property animation → jank; missing aspect-ratio → CLS.
```

---

## PATCH 8 — `roles/LEAD_PRODUCT_GATE_PROTOCOL.md`

**Anchor:** `## GATE-4 — QUALITY AND SECURITY`, `### Blockers @QA:` block.
**Action:** add before the @QA block a sub-block:
```
### Blockers @QA_VISUAL (for any UI change):
□ VISUAL_QA_REPORT_*.md exists, status 🟢 (no open 🔴)
□ Run on viewport × fixture matrix (including longtext and many) completed
□ V1 equal-height: siblingHeightDelta == 0; V2 overflow.x ≤ 0; V3 CLS ≤ 0.1
□ V7 zero-shift hover/focus; V8 no layout animations, reduced-motion respected
□ Baseline is current or explicitly created in this wave (not "N/A")
```

**Anchor:** `### Definition of Done` block, R1–R7 table.
**Action:** add a row:
```
| R8 | For affected UI — 🟢 @QA_VISUAL (geometry/overflow/CLS/states/micro under hostile content); baseline is current | GATE-4, @QA_VISUAL |
```

---

## PATCH 9 — `roles/ROLE_LEAD.md`

**Anchor:** `## ROUTING (when to call whom)` block.
**Action:** add lines:
```
UI change completed by @DEV and passed @QA_ARCH 🟢 → @QA_VISUAL (visual audit by render) → only then @QA
Showcase/landing → set Ambition (restrained/confident/bold/experimental, default confident; roles/MOTION_AMBITION_DIAL.md) → @MOTION CONCEPT (archetype from roles/HERO_ARCHETYPES.md)
Operational screen with interactions ("buttons jitter/run off") → @MOTION MICRO (MICRO_SPEC) → @DEV → @QA_VISUAL
@QA_VISUAL gave 🔴 systemically-compositional (not local CSS) → @DESIGN (AUDIT); not reproducible by measurement in 2 cycles → @AUDITOR
```

---

## PATCH 10 — `roles/FILE_MAP.md`

**Anchor:** `### System protocols` block (list `roles/...`).
**Action:** add:
```
roles/LAYOUT_INVARIANTS.md             ← layout invariants (equal-height, reserved height, zero-shift)
roles/HERO_ARCHETYPES.md               ← 8 hero archetypes + selection protocol
roles/MOTION_AMBITION_DIAL.md          ← @MOTION ambition dial + MICRO mode
```

**Anchor:** `### Roles (called by @mention)` block.
**Action:** add `roles/ROLE_QA_VISUAL.md` after `roles/ROLE_QA_ARCH.md`.

**Anchor:** `## /docs/artifacts — working layer`, `### QA and security` section.
**Action:** add:
```
docs/artifacts/VISUAL_QA_REPORT_[MODULE].md  ← visual audit report (@QA_VISUAL)
```

**Anchor:** `### Design (@DESIGN)` section / near MOTION artifacts.
**Action:** add:
```
docs/artifacts/MICRO_SPEC_[MODULE].md        ← micro-moments spec for operational screens (@MOTION MICRO)
```

**Anchor:** `## /src, /backend, /frontend — code`, `tests/` sub-block.
**Action:** add:
```
  frontend/tests/visual/             ← visual audit harness (@QA_VISUAL)
  frontend/tests/visual/__baseline__/  ← baseline anchor (golden screenshots)
```

---

## PATCH 11 — `roles/RAG_CANON.md`

**Anchor:** Layer P reading order (where role/protocol files are listed).
**Action:** add new files to the reading order next to the frontend cluster:
```
roles/LAYOUT_INVARIANTS.md
roles/ROLE_QA_VISUAL.md
roles/HERO_ARCHETYPES.md
roles/MOTION_AMBITION_DIAL.md
```
(Rule from `.cursorrules`: "New file in roles/ — add to RAG_CANON.md and FILE_MAP" — done.)

---

## PATCH 12 — `roles/ENGINEERING_PLAN.md`

**Anchor:** `## 5. NAMING AND ARTIFACT STORAGE CONVENTION`, artifact list.
**Action:** add:
```
docs/artifacts/VISUAL_QA_REPORT_[MODULE].md  — visual audit by render (@QA_VISUAL)
docs/artifacts/MICRO_SPEC_[MODULE].md        — micro-moments of operational screens (@MOTION MICRO)
frontend/tests/visual/__baseline__/          — baseline anchor (golden screenshots, updated by @QA_VISUAL per @LEAD decision)
```

**Anchor:** `## 4. QUALITY GATE BEFORE DEPLOY`.
**Action:** add an item:
```
[ ] @QA_VISUAL — visual audit by render 🟢 for all affected UI routes (geometry/overflow/CLS/states/micro; baseline is current)
```

---

## PATCH 13 (OPTIONAL) — auto artifact layout for @LEAD

> Minor item (you correctly deferred it). Enable after frontend stabilisation. Not mandatory.

**File:** `roles/FILE_MAP.md`, `## RULES` block.
**Action:** add a routing rule:
```
**Auto artifact layout — WAVE-FIRST:** when creating, @LEAD/@ARCH place by wave:
- Wave artifacts (DEV_PROMPTS, QA_REPORT, VISUAL_QA_REPORT, DESIGN_SPEC, MOTION_CONCEPT, MICRO_SPEC) of wave N → `docs/artifacts/waves/[N]/` (TOGETHER)
- Cross-cutting (BUSINESS_LOGIC, BUSINESS_ROUTES, SPINE, DEVELOPMENT_PLAN, METRICS_REGISTRY) → `docs/artifacts/` root
- Decisions: ADR → `docs/decisions/` (separate nature, canon `roles/DOC_TOPOLOGY.md`). Roadmap pass 1 and 2 → `docs/artifacts/roadmap/pass1/`, `pass2/` (do not mix)
- Project TPF examples → `docs/artifacts/reference/tpf/`
Wave is determined at creation; within a wave, name prefix groups — no need to separate by type.
```

---

## PATCH 14 (DECISION — do not apply without confirmation) — frontend canon consolidation

> This is a "stick in the wheel" from the analysis: token rules are duplicated in 5+ files (`FRONTEND_DESIGN_EXCELLENCE`, `TECH_PASSPORT §7/§9`, `ARCH_FRONTEND_UI_LOGIC`, `TEMPLATE_DESIGN_UX`, `TEMPLATE_QA_FRONTEND_VISUAL_CANON`, `TEMPLATE_PROJECT_FRONTEND_PASSPORT`). This violates the "textual proliferation" anti-pattern from your own `MIRROR_PROTOCOL` and masks the geometry gap.

**Low-risk variant (recommended, non-destructive):**
- Declare `roles/FRONTEND_DESIGN_EXCELLENCE.md` **the single token canon** (background/border/shadows/typography/statuses/icons).
- Add a line to other file headers: `> Tokens — canon in roles/FRONTEND_DESIGN_EXCELLENCE.md. Do not duplicate here — only reference/extend with context (module/route/behaviour).`
- At the next touch of each file — remove the duplicating token block, leaving the reference.

**Do not do without your "yes":** physically merging/deleting files. This is a separate task with review of each one to avoid losing module-specific context.

→ Decision is yours: apply low-risk variant now / defer / leave as-is.

---

Reference: `.cursorrules` · `roles/ROLE_QA_VISUAL.md` · `roles/LAYOUT_INVARIANTS.md` · `roles/HERO_ARCHETYPES.md` · `roles/MOTION_AMBITION_DIAL.md` · all affected role/protocol files above
Version: 1.0 | 2026-06-14
