# FRONTEND_CONSOLIDATION.md
# Frontend canon consolidation directive (Patch 14) + auto artifact layout (Patch 13).
# Eliminates "text multiplication": the same rules no longer duplicated across 8 files.

> **Principle (from your own `MIRROR_PROTOCOL`):** "Signal over volume. One canonical body of a document + references — not five copies of a rule in five files."
> This directive does not delete knowledge — it assigns a **single source of truth** to each rule and converts duplicates into references. Nothing is lost; only the repetition that masked gaps is removed.

---

## PART 1 — MAP OF SINGLE SOURCES OF TRUTH

After the upgrade, each rule lives in exactly one canon. Other files **reference**, not repeat.

| Rule class | Single canon | Who was duplicating before (now — reference) |
|--------------|--------------------|------------------------------------------|
| **Taste tokens** (colour, elevation, shadows, status colours, typography, icons, buttons) | `roles/FRONTEND_DESIGN_EXCELLENCE.md` | `TECH_PASSPORT_FRONTEND_UI_LOGIC §7/§9`, `ARCH_FRONTEND_UI_LOGIC`, `TEMPLATE_DESIGN_UX §3`, `TEMPLATE_QA_FRONTEND_VISUAL_CANON`, `TEMPLATE_MODULE_DEV §1.1`, `TEMPLATE_PROJECT_FRONTEND_PASSPORT §1.4` |
| **Layout geometry / stability** (equal-height, reserved height, min-width, aspect-ratio, zero-shift, no-layout-animation, scroll-containment, **motion islands / scroll stability**, **collision & stacking**) | `roles/LAYOUT_INVARIANTS.md` (§1–§12) | new — previously no one owned this (that was the gap) |
| **Project concept world** [v6.20] (aesthetic VALUES: hex palette, font pairs, effect kit, motion personality, Mantine skin) | `roles/CONCEPT_DNA_LIBRARY.md` → project `docs/artifacts/VISUAL_CONCEPT_*` | `DESIGN_DECISION_LIBRARY` (Palette/Typography Directions → legacy operational contour), "golden library" in ROLE_DESIGN (→ Tier 0/1.5) |
| **Concept creation/change process** [v6.20] (TASTE GATE, passport derivation, RESKIN, model economics) | `roles/VISUAL_CONCEPT_PROTOCOL.md` | new — aesthetics previously had no process owner (that was the source of "tastelessness") |
| **Concept anatomy and references** [v6.20+] (8 DNA axes, coherence, what a reference must convey, world builder) | `roles/CONCEPT_ANATOMY.md` | new — "visual literacy" was a list of names without extraction criteria |
| **Layout construction grammar** [v6.20+] (space laws, primitives, proximity, button groups) | `roles/LAYOUT_COMPOSITION.md` | new — construction lived in the model's head; invariants only verified |
| **Component-first / UI registry** | `roles/COMPONENT_REGISTRY.md` | ROLE_DESIGN, ROLE_FRONTEND, TEMPLATE_PROJECT_FRONTEND_PASSPORT §3 |
| **Layout measurement** (render→measure→compare) | `roles/ROLE_QA_VISUAL.md` | new |
| **Business minimum by page type** (what a screen must be able to do) | `roles/DOMAIN_STANDARDS.md` | `TPF_MASTER` Part I/IV, `TPF_MODULE_*`, `TECH_PASSPORT §1–§6` |
| **Module type direction** (list/card/panel/dashboard/dnd + document skeleton) | `roles/TEMPLATE_MODULE_DEV.md §2–§4` | `TPF_MASTER` Part II–III, `TPF_MODULE_*` |
| **Showcase / hero composition** | `roles/HERO_ARCHETYPES.md` | `FRONTEND_DESIGN_EXCELLENCE §5` (hero row), `TEMPLATE_DESIGN_UX §3.2` |
| **Showcase: tokens/landing build order** | `roles/TEMPLATE_DESIGN_UX.md` | `FRONTEND_DESIGN_EXCELLENCE §4` (general, references) |
| **Project business logic** | Project spec + `@BIZ` → `docs/artifacts/BUSINESS_LOGIC.md` (schema — `TEMPLATE_BIZ_LOGIC.md`) + `BUSINESS_ROUTES.md` | `TPF_*` no longer **a** source of business logic |

**De-duplication rule:** on the next touch of any file in the "was duplicating" column — delete the repeated rules block, leaving a reference line pointing to the canon (texts below, §3). Module/route/behaviour context in the file stays — only the copy of the general rule is removed.

---

## PART 2 — DECISION ON `TPF_MASTER` + `TPF_MODULE_*` (11 files)

### Diagnosis
These files are the "frontend functionality tech passport" for a **specific product** (dental Business OS): Dashboard, Schedule, OmniChat, CRM, Tasks, Finance, Loyalty, Forms, PWA, Shell, Entities. Their content breaks into two parts:
- **Universal direction** (module type → mandatory elements) — already covered by `TEMPLATE_MODULE_DEV §2` and `DOMAIN_STANDARDS`.
- **Domain-specific detail** (patient tabs, appointment slots, clinic cashboxes) — this is the **state of a specific product**, not a universal process.

Previously this made sense: you did not know how to capture business logic, and TPF served both purposes. Now business logic = spec + `@BIZ` + `BUSINESS_LOGIC.md` + `BUSINESS_ROUTES.md` + `DOMAIN_STANDARDS`. So TPF as a **universal** `roles/`-canon is "a stick in the wheel": 12 domain-tied files in the process layer.

### Decision (reclassification, not deletion)
1. **`TPF_MASTER` + `TPF_MODULE_*` are moved from Layer P (`roles/`, universal process) to the project layer** as a reference example of a specific product:
   - target location — `docs/artifacts/reference/tpf/` (or `docs/[project]/tpf/`), marked as **"project example, not universal canon"**.
   - Universal direction stays in `TEMPLATE_MODULE_DEV §2`; domain minimums — in `DOMAIN_STANDARDS`.
2. **A pointer is added to the header of each `TPF_*`** (§3.6 below): "This is a project example. Universal direction — `TEMPLATE_MODULE_DEV §2`; business minimum — `DOMAIN_STANDARDS`; business logic — spec + `BUSINESS_LOGIC.md`. Do not use as universal canon."
3. **References that pointed to `TPF_MODULE_[X].md` as input (`@DESIGN SPEC`, `@ARCH`)** are replaced with a pair: `DOMAIN_STANDARDS` (business minimum) + `TEMPLATE_MODULE_DEV §2` (module type direction) + (if same-domain project) reference to the project TPF as example.
4. **For a new project TPF files are not created by default.** Only what is actually needed: `BUSINESS_LOGIC.md` (schema `TEMPLATE_BIZ_LOGIC`), `BUSINESS_ROUTES.md`, and if needed one project `MODULE_*` per the skeleton of `TEMPLATE_MODULE_DEV §3` — without 11 universal tech passports.

### What this achieves
- −12 domain-tied files from the process layer; knowledge preserved (universal — in 2 canons, domain — in project layer as example).
- `@DESIGN`/`@ARCH` stop "accidentally" relying on dental-specific content as universal norm.
- Business logic has one route: spec → `@BIZ` → `BUSINESS_LOGIC.md`/`BUSINESS_ROUTES.md`.

### What `TEMPLATE_BIZ_LOGIC.md` is
**Not deleted.** This is the **schema** of the `BUSINESS_LOGIC.md` artifact (sections 1–14: product, audience, MVP, business rules, constraints, A-B decisions, stubs, metrics). Reclassification: this is **a schema filled by spec + `@BIZ`/`@CREATOR`**, not "a template instead of business logic". In the header — a pointer: "Content source — project spec + `@BIZ`; this is structure, not source of truth for a specific rule — the rule lives here after filling".

---

## PART 3 — READY HEADER POINTERS (copy-paste)

Insert as the first line after the heading of the corresponding file. Then on the next touch — remove the duplicated rules block under the pointer.

### 3.1 — `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md`
```
> **Token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md` (single source).** §7/§9 here — do NOT duplicate tokens, they describe Business OS screen behaviour/structure. Geometry — `roles/LAYOUT_INVARIANTS.md`. On token conflict, FRONTEND_DESIGN_EXCELLENCE takes priority.
```

### 3.2 — `roles/ARCH_FRONTEND_UI_LOGIC.md`
```
> **Token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md`.** This file — INJECTION POINTS of tokens into the specific repository (`theme.ts`, `index.css`), not their definition. Geometry — `roles/LAYOUT_INVARIANTS.md`.
```

### 3.3 — `roles/TEMPLATE_DESIGN_UX.md`
```
> **Taste token canon — `roles/FRONTEND_DESIGN_EXCELLENCE.md`.** Here — only showcase specifics (landing build order, background protocol). Hero composition — `roles/HERO_ARCHETYPES.md` (not "text left + mockup right" by default). Geometry — `roles/LAYOUT_INVARIANTS.md`.
```

### 3.4 — `roles/TEMPLATE_QA_FRONTEND_VISUAL_CANON.md`
```
> **Tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; render measurement — `roles/ROLE_QA_VISUAL.md`.** This checklist — structural elements of a specific repository, not rule definitions. Render verification (equal-height, overflow, CLS) is performed by @QA_VISUAL.
```

### 3.5 — `roles/TEMPLATE_MODULE_DEV.md` (§1.1 — replace UI laws table with pointer)
```
> §1.1 UI laws — canon in `roles/FRONTEND_DESIGN_EXCELLENCE.md` (tokens/patterns), `roles/LAYOUT_INVARIANTS.md` (geometry), `roles/DOMAIN_STANDARDS.md` (business minimum). Do not repeat here — reference only. Unique value of this file — §2 (module typology) and §3 (module document skeleton).
```

### 3.6 — each `TPF_MASTER.md` / `TPF_MODULE_*.md`
```
> **Project example (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project spec + `docs/artifacts/BUSINESS_LOGIC.md`. For a new project these files are not created by default.
```

### 3.7 — `roles/TEMPLATE_BIZ_LOGIC.md`
```
> This is the **schema** of the artifact `docs/artifacts/BUSINESS_LOGIC.md`, filled from the project spec + @BIZ/@CREATOR. Once filled — the single source of truth on project business rules (sections 5–9). Note: template = structure, filled BUSINESS_LOGIC.md = truth.
```

### 3.8 — `roles/TEMPLATE_PROJECT_FRONTEND_PASSPORT.md` (§1.4 Design Tokens)
```
> §1.4 — project TOKEN VALUES for a specific product; RULES and scale — canon `roles/FRONTEND_DESIGN_EXCELLENCE.md`. Project hex/radii are recorded here, canon rules are not overridden.
```

---

## PART 4 — PATCH 13: ARTIFACT LAYOUT — WAVE-FIRST (applied)

> This is a special case of the general topology `roles/DOC_TOPOLOGY.md` (layout of ALL `docs/` by document nature). Here — only the delivery layer `docs/artifacts/`. Decisions (ADR) → `docs/decisions/`, passports → `docs/knowledge/`, plan → `docs/execution/` (see DOC_TOPOLOGY).

Previously everything was dumped flat into `docs/artifacts/`. The main layout axis — **development wave**: everything belonging to a stage (DEV_PROMPTS, QA_REPORT, VISUAL_QA_REPORT, DESIGN_SPEC, MOTION_CONCEPT, MICRO_SPEC) sits **together** in the wave folder. Inside the wave, files are grouped by name prefix. This matches how development actually flows: one wave — one folder.

```
docs/artifacts/
├── waves/                        ← MAIN axis: artifacts by wave
│   └── [N]/                      ← everything for wave N TOGETHER:
│       ├── DEV_PROMPTS_*.md
│       ├── QA_REPORT_*.md          (@QA_ARCH)
│       ├── VISUAL_QA_REPORT_*.md   (@QA_VISUAL)
│       ├── DESIGN_SPEC_*.md  ·  DESIGN_AUDIT_*.md
│       ├── MOTION_CONCEPT_*.md  ·  MICRO_SPEC_*.md
│       └── PRINCIPLE_FINDINGS_*.md (if for this wave)
(ADR — in docs/decisions/, see roles/DOC_TOPOLOGY.md — separate nature, not under artifacts)
├── roadmap/pass1/  pass2/        ← Roadmap passes separate (do not mix first and second)
├── reference/tpf/                ← reclassified TPF_* (project examples)
└── (root)                        ← CROSS-CUTTING, living the full project:
                                     BUSINESS_LOGIC.md · BUSINESS_ROUTES.md · SAAS_ARCHITECTURE_SPINE_*.md ·
                                     DEVELOPMENT_PLAN.md · METRICS_REGISTRY.md · RAG_PASSPORT · AGENT_GRAPH_PASSPORT
```
@QA_VISUAL render baselines — with code: `frontend/tests/visual/__baseline__/` (not in wave, updated by @LEAD decision).

**Rules:**
- **Main axis — wave.** Stage artifact → `waves/[N]/`. No need to split by type inside the wave — name prefix (`DEV_PROMPTS_`, `QA_REPORT_`, `VISUAL_QA_REPORT_`, `DESIGN_SPEC_`, `MICRO_SPEC_`) already groups.
- **Cross-cutting — to root.** BUSINESS_LOGIC/ROUTES, SPINE, DEVELOPMENT_PLAN, METRICS_REGISTRY, RAG/AGENT passports live the full project → root `docs/artifacts/`.
- **Decisions — to `docs/decisions/`** (separate nature, canon `roles/DOC_TOPOLOGY.md`), NOT under artifacts. Roadmap pass 1 and 2 — **always** `roadmap/pass1` and `roadmap/pass2`, not mixed.
- When creating a file, `@LEAD`/`@ARCH` determine the wave (or "cross-cutting/registry") by the artifact name — deterministically. `FILE_MAP` and this section are the source of truth.

> Matches the wave development flow: open `waves/N/` — see the entire stage at once.

---

## PART 5 — REFERENCES TO UPDATE AFTER TPF RECLASSIFICATION

These places referenced `TPF_MODULE_[X].md` as input — replace with canon pair:

| Where | Was | Now |
|-----|------|-------|
| `ROLE_DESIGN.md` SPEC, input | `TPF_MODULE_[X].md` | `roles/DOMAIN_STANDARDS.md` (business minimum) + `roles/TEMPLATE_MODULE_DEV.md §2` (module type direction) + project TPF as example (if same domain) |
| `ROLE_LEAD.md` @DESIGN SPEC handoff template | `Input: TPF_MODULE_[X].md + ...` | `Input: roles/DOMAIN_STANDARDS.md §[type] + roles/TEMPLATE_MODULE_DEV.md §2 + docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md` |
| `ROLE_QA_ARCH.md` (audit input) | TPF references as canon | `DOMAIN_STANDARDS` + `TECH_PASSPORT` (behaviour) + `LAYOUT_INVARIANTS` (geometry) |
| `TEMPLATE_MODULE_DEV.md §5` (examples) | "TPF_MODULE_* — reference" | "project examples in `docs/artifacts/reference/tpf/`" |

(These edits are included in the updated roles in the final bundle `SYSTEM_v6.16_COMPLETE.md`.)

---

## CONSOLIDATION RESULT

- 4 rules → 4 single canons; duplicates → references (ready headers §3).
- 12 TPF files: universal knowledge compressed into 2 canons, domain knowledge reclassified as project example. Net minus ~12 files from the process layer.
- Business logic has one route: spec + `@BIZ` → `BUSINESS_LOGIC.md`/`BUSINESS_ROUTES.md`; TPF no longer a source.
- Artifacts laid out wave-first (Patch 13): everything for the wave in `waves/[N]/`; cross-cutting — in root; Roadmap pass1/pass2 — separate.

---

Reference: `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/LAYOUT_INVARIANTS.md` · `roles/ROLE_QA_VISUAL.md` · `roles/HERO_ARCHETYPES.md` · `roles/DOMAIN_STANDARDS.md` · `roles/TEMPLATE_MODULE_DEV.md` · `roles/TEMPLATE_DESIGN_UX.md` · `roles/TEMPLATE_BIZ_LOGIC.md` · `roles/FILE_MAP.md` · `roles/RAG_CANON.md` · `roles/MIRROR_PROTOCOL.md` (anti-pattern "text multiplication") · `roles/INTEGRATION_PATCHES.md`
Version: 1.1 | 2026-07-06
