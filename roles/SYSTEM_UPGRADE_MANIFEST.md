# SYSTEM_UPGRADE_MANIFEST.md
# System upgrade log. Newest first.

---

# UPGRADE v6.35 — "Production-readiness & drift cleanup" (2026-07-23)

> **One sentence:** the system now builds production-grade products by default (Law 41 — "MVP" is a delivery
> schedule, not a quality level; foundation complete / delivery phased; raw planning = a `# TODO` in the
> foundation), and the long-standing cross-file conflicts were resolved to single winners.

## What was added
- **Law 41 (production-readiness by default)** in `.cursorrules` + two canons: `roles/PRODUCTION_READINESS_CANON.md`
  (the bar) and `roles/PLANNING_MATURITY_CANON.md` (the self-audit loop DRAFT→RED-TEAM SELF-PASS→REFINE, the
  Completeness Ledger + spot-check, the three criteria responsibility/foresight/completeness).
- **FORESIGHT / COMPLETENESS GATE** in `ROLE_LEAD` (+ CHAIN step 0.8, + GATE-0/GATE-1 blockers in
  `LEAD_PRODUCT_GATE_PROTOCOL`): production rollup + ledger before @ARCH; concept lock before @MOTION/@DESIGN;
  foundation-touch trigger; rework-cost REFLEX.
- **`roles/CONFLICT_REGISTRY.md`** — single winners for C1–C8, all applied:
  C1 Law 33→38 (security numbering) · C2 @DESIGN scope = new pattern · C3 confirm/undo by action class ·
  C4 palette precedence (no project hex in universal roles) · C5 stiffness S1–S12 → ST1–ST12 (security S1–S12 kept) ·
  C6 aesthete catalogue A–H · C7 @SEC advisory / @PENTEST blocking · C8 SEO TECH on par with @PENTEST.
- **QA_ARCH Vector 19 (UI Contract & List Semantics)** — pagination/DST/concurrent-edit/partial-failure/
  permission-UI/CSV-injection/focus/skeleton/… (distinct from CRAFT_LINT V19 Toy-Graph).
- **ACTIVATES_CANONS** headers in LEAD/CREATOR/BIZ/ARCH/PRINCIPLE/DESIGN/MOTION.
- **Multi-wave foundation** note (@ARCH), **Foresight forward-questions** (@PRINCIPLE), production completeness
  rollup (@CREATOR/@BIZ).

## Applied (2026-07-23, cont.)
- **ENGINEERING_PLAN.md full rebase** (v3.0/v6.13 → v4.0/v6.35): state machine, transitions, escalation, Quality Gate and folder-structure resynced to current roles/gates (VISUAL CONCEPT · @SEO CORE · @MEDIA_ENGINEER · FORESIGHT/COMPLETENESS gate · @PRINCIPLE MODE:MODEL · @PENTEST S-0/S-Wave/S-Global · Law 40 publish-boundary). STALE header removed; carries an authority-note (SoT = `.cursorrules` CHAIN; a semantic mirror).
- **§5.2 PENTEST deep detail:** `ROLE_PENTEST` v2.1 NAMED LEAVES (30 greppable leaves) + `PENTEST_SCENARIOS` v1.1 §§10–15 (SSRF/CORS/file/IDOR-matrix/webhook/refresh-session) + `SECURITY_GATE_PROTOCOL` §3 v1.1 extended Security Contract (+ semantic mirror in ROLE_PENTEST MODE 0).
- **§5.3 DEV:** CONTRACT SHEET gains conditional mandatory blocks (FRONTEND CONTRACT / LIST-QUERY BOUNDS / MIGRATION SANITY / SECURITY CROSS-REF by leaf id) + a 15-recurring-mistake catalog mapped to sheet blocks (`ROLE_DEV`).
- **§5.4 DESIGN:** 7 mandatory DESIGN_SPEC subsections (Responsive Matrix · Intermediate states · i18n & overflow · Permission matrix · Motion detail · Focus & keyboard · List UI), blocking (present or N/A+reason); semantic mirror State Spec ↔ QA_ARCH Vector 3/19 (`ROLE_DESIGN`). **All §5 deep detail closed.**

## Known remaining (next waves)
- Phase 4 mirror-markers (incl. three `semantic` mirrors: Security Contract, routing graph, state vocabulary), Phase 5 hygiene (TPF move; **ROLE_DESIGN at 44.5 KB → offload candidate to FRONTEND_DESIGN_EXCELLENCE**), Phase 6 coda evidence-audit on a live project.
- TEMPLATE_BIZ_LOGIC.md `[FILL IN]` reconciliation check.
- Project `.cursorrules` overlays are pre-v6.35 snapshots (validation copies) — intentionally not synced.

Plan of record: `docs/ROLE_SYSTEM_RECONSTRUCTION_PLAN.md`.

---

# UPGRADE v6.25 — "Pipeline Resilience" (2026-07-08)

> **One sentence:** real incident #2 (RAG ingest: rate limiter held itself full, retries bypassed it,
> heartbeat revived zombies, the task lifecycle had three time zones) was broken down into laws —
> these defects can no longer be committed because @DEV receives them resolved in the passport BEFORE code.

## What was added (ASYNC_WORKERS_CANON upgraded to v2.0 + addenda; no new files)
| Where | What |
|-------|------|
| `ASYNC_WORKERS_CANON.md` v2.0 | PART II: §9 incident #2 breakdown (9 defects D1–D9 → laws) · §10 AL-11 lease model (single clock, one alive predicate, reclaim ≤ TTL/2, recovery window as a number, **heartbeat = progress**, deadline on every await) · §11 AL-13 rate limiter at the wire (atomic check-and-consume, failure does not consume capacity, pacing default, concurrency = budget) · §12 AL-12 single retry owner + cross-cutting taxonomy (broad catch = AP-18) · §13 **PIPELINE PASSPORT** with template + "five questions before a pipeline" · §14 tests T-I1…I6 and AP-15…20 |
| `.cursorrules` | Law 30 extended with v2.0 block; PIPELINE_PASSPORT artifact in Layer W |
| `ROLE_ARCH` | "five questions before code" — mandatory pipeline entry; passport before @DEV |
| `ROLE_DEV` | external call checklist: rate limiter on every attempt, narrow catches, timeout on every await, T-I1/I3/I5 locally |
| `ROLE_QA_ARCH` | Liveness/Integration vector: greps AP-15…20, number cross-check passport↔config |
| `ROLE_PENTEST` | series T-I1…I6 (including mock-hung provider — regression D1) |

## Measurably changed
Rate limiter failure does not consume capacity (T-I1 as number) · retries do not break budget (T-I2: RPS ≤ limit) ·
dead worker releases slot within declared recovery window (T-I4) · zombie with live heartbeat
detected by stuck predicate (T-I6) · "which retry levels are disabled" — a passport line, not log archaeology.

---

# UPGRADE v6.24 — "Integrity Under Concurrency" (2026-07-07)

> **One sentence:** workers stopped hanging in v6.22 — now data stops lying too: every business invariant
> is protected by a layer that cannot be bypassed, not just an `if` in code.

## Diagnosis in one line
Double booking, oversell, duplicate payment, overwritten edits, and cross-tenant leaks — the entire class
"system is live but data lies" had no rules: invariants lived in code checks that fail under concurrency.

## New file (`roles/`)
| File | What it does |
|------|-------------|
| `DATA_INTEGRITY_CANON.md` | Protection hierarchy (§1: DB schema > lock/version > code hint); race catalog with SQL recipes (§2); transaction discipline (§3); Idempotency-Key + webhook inbox (§4); money as minor integers (§5); time as UTC/[start,end)/branch TZ (§6); tenant isolation by schema (§7); soft-delete and audit (§8); **INVARIANT LEDGER** in ADR (§9); race tests T-H1…H7 (§10); anti-patterns (§11) |

## Integrations
`.cursorrules`: **Law 32** + Layer P · `ROLE_ARCH`: ledger required before code, schema rules §5–§8 ·
`ROLE_DEV`: data checklist + local T-H run · `ROLE_QA_ARCH`: Data-Race vector (check-then-act,
float-money, naive-time, tenant-scope, ledger↔migration cross-check) · `ROLE_PENTEST`: T-H series in CRASH_TEST
(T-H5 — isolation IDOR matrix) · `MIGRATIONS_PLAYBOOK`: constraints on live tables (NOT VALID→VALIDATE,
CONCURRENTLY, tenant retrofit) · registries FILE_MAP/RAG_CANON.

## Measurably changed
Double booking/oversell impossible by schema (T-H1/H2 — exactly N successes as a number) · POST repeat is safe (T-H3) ·
edits are not lost (T-H4: 409 instead of overwrite) · tenants are isolated by matrix (T-H5: 0 leaks) ·
money does not float · every invariant is visible in ledger ADR.

---

# UPGRADE v6.23 — "Decision Backbone" (2026-07-07)

> **One sentence:** architectural knowledge was complete but scattered across six files and was not enforced
> at decision time — now every decision pulls them into 12 vertebrae AS NUMBERS before code, and the last two
> gaps (synchronous path timeouts and complexity discipline) are closed by sections §3–§4.

## Diagnosis in one line
DOMAIN_STANDARDS/EXCELLENCE_PASSPORT/SYSTEM_DESIGN knew about tenancy, idempotency, RBAC, money, UTC —
but nothing forced @ARCH to pull them into a specific ADR (hence "prolonged fine-tuning"); outbound call
timeouts were mentioned nowhere (0 matches across the system); "when to split, when not to" lived as taste, not numbers.

## New file: `roles/ARCH_SPINE_PROTOCOL.md`
12 vertebrae (tier · SLO · tenancy+leak test · timeouts for all dependencies · idempotency ·
constraints-first · additive-only contracts · JOB_PASSPORTS · capacity · failure/radius · DR with
restore-test date · STRIDE sketch) · **Complexity Ladder 0–4** with numeric trigger thresholds (anti-overengineering:
"future-proof" = 🔴, downgrade is legitimate) · **Timeouts/deadlines** (§4: default table, deadline propagation,
retry budget ≤1, grep-invariant T5) · one-page artifact template.

## Integrations
`.cursorrules` Law 31 + Layer P/W · `ROLE_ARCH` (STEP 0 += 4 lines; backbone with ADR; ladder — movement law;
timeout table → project config) · `ROLE_QA_ARCH` Spine vector (completeness, ladder as number,
grep T5, tenancy leak, restore-test freshness) · `SYSTEM_DESIGN_PROTOCOL` (steps map to vertebrae 8–10) · registries FILE_MAP/RAG_CANON.

## Measurably changed
No outbound call without a timeout (grep-🔴) · microservice/Kafka appear only with a numeric trigger ·
every decision has a page where tenancy, DR and contracts are answered before code · restore-test — with a date, not faith ·
@PENTEST receives vertebrae 3/11/12 as a target map.

---

# UPGRADE v6.22 — "Async Contour by Contract" (2026-07-06)

> **One sentence:** queues stopped being "put the function in — hopefully it runs": every task gets
> a passport-contract, management is separated from work, and the "task-supervisor" deadlock became
> impossible by construction.

## Diagnosis in one line
The system could choose a broker (DATA_STORE §2) and calculate sizes (SYSTEM_DESIGN Step 6, Celery-bias),
but did not define TASK SEMANTICS: cancellation, idempotency, plane separation, bulkhead — the agent
improvised, leading to a real incident (supervisor task in the data queue + closed event loop
with 2 slots = dead queue).

## New file: `roles/ASYNC_WORKERS_CANON.md`
§0 incident breakdown as canonical anti-example → 5 causes → 5 laws · §1 three planes
(data/control/supervision: worker never manages a worker) · §2 laws AL-1…10 · §3 pattern catalog
(cron-scheduler with lock, Chain/Flow, Saga, **Outbox**, external API rate limiter, batch,
dedupe window; BullMQ/Celery mapping; Postgres queue only via SKIP LOCKED) · §4 **JOB PASSPORT** —
task type contract (task cannot be written without a passport) · §5 topology and sizing (worker = separate
container; capacity formula) · §6 metrics/alerts M-ASYNC · §7 crash tests T-A…G · §8 anti-patterns AP-1…12.

## Integrations
`ROLE_ARCH` (ADR async contour + JOB_PASSPORTS_[PROJECT].md before code; lesson-canon) · `ROLE_DEV`
(AL checklist before delivery + self-check "if task waits for a task — stop") · `ROLE_PENTEST`
(CRASH_TEST += T-A…G) · `SYSTEM_DESIGN_PROTOCOL` Step 6 (pointer: without passports the step is not passed) ·
`.cursorrules` Layer P · registries.

## Measurably changed
Incident hang is caught three times: forbidden on review (AP-1), does not pass passport (checkpoints
required), and if it does — stalled detector returns the task (T-D). Task loss/duplication — testable
property (T-A/B/E), not luck. Slow wave no longer blocks fast operations (bulkhead, T-F).

---

# UPGRADE v6.21 — "Visibility by Design" (2026-07-06)

> **One sentence:** a site "worth millions" that does not appear in search results for the client's demand
> does not exist — the system gained an owner of search visibility (@SEO, gate-role modelled on @SEC) and
> a law about showcase rendering.

## Diagnosis in one line
18 roles — and not one owned visibility: no semantics at all (pages were invented from imagination,
not from demand), and the default Vite-SPA stack returned an empty `<div id="root">` to search engines — invisibility by design.

## New files (`roles/`)
| File | What it does |
|------|-------------|
| `ROLE_SEO.md` | Head of SEO: 4 modes — CORE (semantic core + page map BEFORE IA/design), ONPAGE (H-structure/meta/schema — entry to @DESIGN), TECH (deploy blocker for showcase, criterion "curl without JS returns content"), MONITOR (Search Console/Webmaster, M-SEO, quarterly cadence); boundaries with 9 roles; anti-manipulation prohibition is absolute |
| `SEO_CANON.md` | Knowledge: semantic core methodology (5 steps, intents, "1 cluster = 1 page"), **rendering table SSG/SSR** (showcase separate from SPA-app), tech minimum with budgets (CWV field, JS ≤150KB), title/description/H formulas, schema table by niche, local SEO (Google Business Profile, geo pages without duplicates), E-E-A-T, AI search (llms.txt, extractable answers), anti-patterns, monitoring |

## Integrations
`.cursorrules`: **Law 29** + @SEO in ROLE MAP + chain nodes (CORE after concept; ONPAGE in @DESIGN entry;
TECH next to @SEC before deploy; SEO artifacts in Layer W) · `ROLE_ARCH`: addendum — rendering ADR required
BEFORE showcase code · `ROLE_CREATOR`: "demand language ↔ delivery world" exchange in 5.5.A · `ROLE_DESIGN`: SPEC
entry for indexable page += SEO_ONPAGE · `ROLE_DEV`: SEO technical checklist line by line + self-check curl-without-JS ·
`ROLE_LEAD`: routing 4 points + "beauty vs semantics" conflict resolved by intent · registries.

## v6.20 refinements in the same wave
`VISUAL_CONCEPT_PROTOCOL` §2: PASS 1 migrated to anatomy constructor (preset — at ≥6 axes) —
"world choice vs constructor" conflict resolved · `LAYOUT_COMPOSITION` §5 += **G7 form grammar**
(one column, STACK label→field→error with height reserve — error does not shift fields) · `CONCEPT_ANATOMY` §4.2 +=
"competitor output = intent map, but aesthetic ANTI-reference".

## Measurably changed
Pages exist for real regional demand, not from imagination · showcase is readable by bot without JS (gate as number) ·
title/H1 use client words from keyword research · deploy without Search Console/Webmaster/sitemap is impossible ·
positions become metric M-SEO with quarterly cadence, not wishful thinking.

---

# UPGRADE v6.20 — "Concept Before Code" (2026-07-06)

> **One sentence of the upgrade:** taste moved from model weights to files — aesthetics are born once as a
> concept-world with executable recipes, and layout collisions gained an owner-paragraph and a sensor-number.

## Diagnosis in one line
Passports were `[hex]` slots without values → cheap models filled them with defaults (blue/Inter/grey SaaS),
expensive ones — with improvisation at $60–70; references — monoculture Linear/Stripe; Mantine — with factory face;
button collisions belonged to no rule.

## New files (`roles/`)
| File | What it does | Symptom closed |
|------|-------------|----------------|
| `CONCEPT_DNA_LIBRARY.md` | 12 concept worlds as executable recipes: hex palettes, font pairs (Cyrillic tested), CSS effect kits, motion personalities, Mantine skins, archetype affinities + Custom World Constructor + niche→world router | "tasteless colors and buttons", "cannot build a concept for the product meaning", "everything under Linear" |
| `VISUAL_CONCEPT_PROTOCOL.md` | "Concept before code": concept birth by @CREATOR (Step 5.5.A/B), TASTE GATE (cliché blacklist K1–K10 + "remove the logo" test), transfer map to 4 passports, RESKIN mode for @DESIGN, model economics | "concept must be created immediately", "@DESIGN should be able to change concept", "$60–70 per project" |
| `CONCEPT_ANATOMY.md` | Generative core: 8 DNA axes with variant spaces, axis coherence rules, **reference protocol** (format SOURCE→EXTRACT→TRANSFER→NOT TAKE, ≤1 SaaS, source typology), world constructor; 12 worlds reclassified as presets (≥6 axes) | "unclear reference scheme, no criteria for what a reference transfers", "don't want to stop at 5–6 references", "agent must UNDERSTAND what a concept consists of" |
| `LAYOUT_COMPOSITION.md` | Grammar of layout CONSTRUCTION: three space laws (children don't push each other · width from above/height from content · flow is law, absolute is license), algebra of 8 primitives, proximity law ×2, button group grammar G1–G6, overlap diagnosis protocol | "keeps messing up layout", "buttons, micro-bugs, one thing overlaps another" — overlap is now impossible by construction, and §12/V12 remain as insurance |

## Integrations (full texts — `roles/INTEGRATION_PATCHES_TASTE.md`)
- **`.cursorrules`:** Law 28 "Concept Before Code"; Law 26 extended with collisions; ROLE MAP (@CREATOR +VISUAL CONCEPT, @DESIGN +RESKIN/Tier 0, @MOTION +Step 0); Layer P +2 files; chain +concept node. Version 6.20.
- **`ROLE_CREATOR.md`:** Step 5.5 → 5.5.A (concept + TASTE GATE) and 5.5.B (passports by derivation, no `[hex]` placeholders); summary and handover extended.
- **`ROLE_DESIGN.md`:** MODE 4 RESKIN; Tier 0 "project world" + Tier 1.5 "references beyond SaaS"; showcase SPEC entry requires VISUAL_CONCEPT; addendum v6.20.
- **`FRONTEND_DESIGN_EXCELLENCE.md`:** new §8 MANTINE DE-BRANDING (createTheme from world, cssVariablesResolver, `theme/effects.css` layer, underutilised Mantine power map, showcase right to headless); pointer in §5; gate §6 +3 items.
- **`LAYOUT_INVARIANTS.md`:** new §12 COLLISION & STACKING (Z-scale, anchor positioning, interactive distance, deterministic criterion) + @DEV/@QA_VISUAL checklists.
- **`ROLE_QA_VISUAL.md`:** vector V12 (pairwiseIntersection == 0 px² · zIndexAudit · fixedCoverage) — in sensor core; concept-conformance in verdict.
- **`ROLE_MOTION.md` / `MOTION_AMBITION_DIAL.md`:** CONCEPT Step 0 — inheriting world motion personality; ban on easing degeneration (6-personality dictionary); linear — only restrained operational mode.
- **`ROLE_LEAD.md` / `ROLE_FRONTEND.md` / `ROLE_DEV.md` / `ROLE_QA_ARCH.md`:** addenda v6.20 — concept and RESKIN orchestration, Z-scale and de-branding ownership, §12 checklist, concept-conformance in gate.
- **Header pointers:** `DESIGN_DECISION_LIBRARY` (values → world), `PROJECT_VISUAL_BOOTSTRAP_PROTOCOL` (Step 0), `HERO_ARCHETYPES` (world affinity).
- **Registries:** `FILE_MAP`, `RAG_CANON`, `FRONTEND_CONSOLIDATION` (source map +2 lines).

## Deployment order
1. Two new files in `roles/`. 2. Collisions (§12 + V12) — independent, fix "overlapping buttons" immediately. 3. Concept layer (other integrations). 4. Pilot: one showcase through full cycle CONCEPT → passports → MOTION → SPEC → DEV → V1–V12.

## Measurably changed
- Palette/fonts stop being a lottery: copied from world recipe, replacement = one line with reason.
- "Everything under Linear" lifted: Tier 0 = project world; SaaS library — only operational patterns.
- Buttons stop overlapping: `pairwiseIntersection == 0 px²` — rule with an owner (§12) and a sensor (V12), not "fix it by eye".
- Mantine stops being a face: criterion "screen is not identified as Mantine docs" is in the gate.
- Character change = RESKIN by Change Map (skin), skeleton and baseline discipline preserved.
- Economics: strong model — 1–2 calls (custom world/FUSION); rest assembled by cheap models from recipes.

---

# UPGRADE v6.16 — "Closing the Frontend Control Loop"
# What was added, how it was integrated, in what order to install, what remains your decision.

> **One sentence of the upgrade:** the system gained its missing sense organ — the render sensor — and
> deterministic geometry rules that it measures; plus an "ambition axis" (archetypes + ambition dial)
> and an owner of micro-moments on operational screens.

---

## WHY (diagnosis in one line)

Before the upgrade the system **managed the recipe, not the dish**: backend — closed loop (code/SQL/tests
observable → stable), frontend — open loop (visual "gates" checked tokens, not rendered geometry; agent
self-signed them without rendering). Hence: jumping cards, "house of cards" (4–8 passes), buggy buttons,
Linear-hero monoculture, constrained motion, pre-production passing itself off as done.

The upgrade closes the loop and separates two axes: **stability** (geometry) and **ambition** (expressiveness).

---

## NEW FILES (placed in `roles/`)

| File | What it does | Symptom closed |
|------|-------------|----------------|
| `ROLE_QA_VISUAL.md` | Render sensor: Playwright harness, measurement tools, vectors V1–V10, baseline anchor, debug cycle. New mandatory gate after @QA_ARCH for any UI. | "pre-production passes itself off as done"; absence of visual feedback in principle |
| `LAYOUT_INVARIANTS.md` | 10 deterministic layout rules (equal-height, reserved height, min-width, aspect-ratio, zero-shift, no-layout-animation) + text of Law 26. | "jumping cards", "house of cards", "buggy buttons" |
| `HERO_ARCHETYPES.md` | 8 hero archetypes + selection protocol (Q1–Q3). Removes the "text left + mockup right" default. | "Linear everywhere", "hero = text+card", "text in hero is a blind spot" |
| `MOTION_AMBITION_DIAL.md` | Ambition dial (input to @MOTION CONCEPT, default `confident`) + MICRO mode (micro-moments for /admin·/app). | "constrained motion", "3D always last", "buttons bug out, nobody owns micro-moments" |
| `INTEGRATION_PATCHES.md` | 14 targeted integrations into existing files (copy-paste with anchors). | makes all of the above alive in the system |
| `SYSTEM_UPGRADE_MANIFEST.md` | This file. | upgrade navigation |

---

## AFFECTED FILES (via `INTEGRATION_PATCHES.md`)

| File | Patch | Integration summary |
|------|-------|---------------------|
| `.cursorrules` | 1 | Law 26; @QA_VISUAL in ROLE MAP; @MOTION +MICRO/ambition; chain +@QA_VISUAL; Layer P +4 files; QA_ARCH GATE delegates render-geometry; version 6.16 |
| `ROLE_QA_ARCH.md` | 2 | Vector 6: separation "code level (me) vs pixel level (@QA_VISUAL)" |
| `ROLE_DESIGN.md` | 3 | SPEC showcase — archetype from HERO_ARCHETYPES; Pixel Decisions — stability per LAYOUT_INVARIANTS |
| `ROLE_MOTION.md` | 4 | 5th mode MICRO; Step 0 Ambition; Step 2.5 Archetype |
| `ROLE_FRONTEND.md` | 5 | Pillar 9 +LAYOUT_INVARIANTS; Visual Quality Gate extended with @QA_VISUAL render check |
| `ROLE_QA.md` | 6 | requires 🟢 @QA_VISUAL before final UI pass |
| `ROLE_AUDITOR.md` | 7 | receives symptom from @QA_VISUAL; typical causes + layout invariant violations |
| `LEAD_PRODUCT_GATE_PROTOCOL.md` | 8 | GATE-4 + @QA_VISUAL blockers; release DoD +R8 |
| `ROLE_LEAD.md` | 9 | routing: @QA_VISUAL, ambition, @MOTION MICRO, escalations |
| `FILE_MAP.md` | 10 | new files, VISUAL_QA_REPORT/MICRO_SPEC artifacts, frontend/tests/visual |
| `RAG_CANON.md` | 11 | reading order +4 files |
| `ENGINEERING_PLAN.md` | 12 | artifact convention +visual; Quality Gate +@QA_VISUAL |

---

## BEFORE AND AFTER

```
BEFORE:
  @DEV → @QA_ARCH (reads code: tokens, states, mutations) → @QA
        visual "gates" = token checklist, self-signed without render → open loop

AFTER:
  @DEV → @QA_ARCH (code level 🟢)
       → @QA_VISUAL (render level: render→measure→compare; V1–V10; baseline) 🟢
       → @QA → @SEC
        output value (geometry) is measured as a number and diff → closed loop
```

Two axes separated:
- **Stability** — `LAYOUT_INVARIANTS` (rules) + `@QA_VISUAL` (measurement). Deterministic.
- **Ambition** — `HERO_ARCHETYPES` (composition) + `MOTION_AMBITION_DIAL` (boldness). Controlled by an explicit parameter, not inertia.

---

## INSTALLATION ORDER

1. Copy 6 new files into `roles/`.
2. Apply `INTEGRATION_PATCHES.md` Patches 1–12 (by anchors, in order). These are safe additive integrations (Law 1 — only what is touched).
3. Run system self-check (optional, via your `MIRROR_PROTOCOL`): pairs `@QA_ARCH→@QA_VISUAL` (gap: who sees the render) and `@DESIGN→@MOTION` (gap: micro-moments) — confirm gaps are closed.
4. On the first real UI module: create `frontend/tests/visual/specs/[module].visual.spec.ts` and the baseline — `@QA_VISUAL` runs for the first time and anchors the reference.
5. Decide on Patches 13–14 (see below).

---

## LEFT TO YOUR DECISION

| # | What | Recommendation |
|---|------|----------------|
| Patch 13 | Auto-layout of artifacts into sub-folders (ADR/waves/roadmap/qa) | Minor, you correctly deferred it. Enable after frontend stabilisation. |
| Patch 14 | Frontend canon consolidation (tokens duplicated in 5+ files — "textual proliferation") | Low-risk variant: declare `FRONTEND_DESIGN_EXCELLENCE.md` the single token canon + references in the others. Physical file merging — as a separate task with review. Your decision. |

---

## LINK VERIFICATION (completed)

- All `roles/*.md` links in new files resolve to existing system files (74) or new ones (6). **No broken links.**
- Artifact outputs (`docs/artifacts/VISUAL_QA_REPORT_*`, `MICRO_SPEC_*`) and code paths (`frontend/tests/visual/*`) — new conventions, registered in `FILE_MAP` (Patch 10) and `ENGINEERING_PLAN` (Patch 12).
- New files added to `RAG_CANON` (Patch 11) and `FILE_MAP` (Patch 10) — per the `.cursorrules` rule "new file in roles/ → add to RAG_CANON and FILE_MAP".

---

## WHAT WILL MEASURABLY CHANGE IN PRACTICE

- Cards stop jumping: `siblingHeightDelta == 0` on the `longtext` fixture — a rule, not an eye.
- "House of cards" is healed by baseline: a change in a neighbouring component is caught by diff against baseline before the whole screen drifts.
- Buttons stop "running off": `geometryShiftOnState == 0` (V7) + only transform/opacity (V8) — with an owner (@MOTION MICRO).
- Hero stops being one template: archetype is chosen by Q1–Q3, A is only one of eight.
- Motion opens up on request: ambition dial, default `confident`, 3D at `bold+` on equal footing.
- Pre-production becomes honest: visual gate with "fact, not intention" (number/diff), not a self-signed checklist.

---

Version: 6.25 | 2026-07-08
Composition v6.25: ASYNC_WORKERS_CANON v2.0 (PART II) + addenda ARCH/DEV/QA_ARCH/PENTEST + Law 30 v2.0 + PIPELINE_PASSPORT
Composition v6.24: DATA_INTEGRITY_CANON (+Law 32, integrations ARCH/DEV/QA_ARCH/PENTEST/MIGRATIONS, registries)
Composition v6.23: ARCH_SPINE_PROTOCOL (+Law 31, integrations ARCH/QA_ARCH/SYSTEM_DESIGN, registries)
Composition v6.22: ASYNC_WORKERS_CANON (+integrations ARCH/DEV/PENTEST/SYSTEM_DESIGN, registries)
Composition v6.21: ROLE_SEO · SEO_CANON (+Law 29, integrations in 8 files, v6.20 refinements)
Composition v6.20: CONCEPT_DNA_LIBRARY · VISUAL_CONCEPT_PROTOCOL · CONCEPT_ANATOMY · LAYOUT_COMPOSITION · INTEGRATION_PATCHES_TASTE (+integrations in 16 files)
Composition v6.16: ROLE_QA_VISUAL · LAYOUT_INVARIANTS · HERO_ARCHETYPES · MOTION_AMBITION_DIAL · INTEGRATION_PATCHES · SYSTEM_UPGRADE_MANIFEST
