# 📐 ENGINEERING_PLAN — System Protocols
> How the system works internally: session states, role handoffs, escalation, quality gate.
> Version: 4.0 | Synchronised with role system v6.35

> **Authority note.** The single source of truth for the routing graph is the **CHAIN PROTOCOL in `.cursorrules`** and the gate map in **`roles/LEAD_PRODUCT_GATE_PROTOCOL.md`** + **`roles/ROLE_LEAD.md`**. This file is the **operational state-machine view** of that same chain — the two are mirrors; on any divergence the `.cursorrules` CHAIN and the ABSOLUTE LAWS win (esp. Law 38 security = @PENTEST blocking / @SEC advisory · Law 39 craft floor · Law 41 production-readiness). Keep this file in sync when the CHAIN changes.

---

## 1. SESSION STATE MACHINE

<!-- MIRROR OF: .cursorrules CHAIN PROTOCOL | semantic (operational state-machine view of the same routing graph, not verbatim) | index: CONFLICT_REGISTRY.md -->
@LEAD manages the project state from start to deployment.

```
[IDLE]
  │ user writes to @LEAD
  ▼
[START]
  └── LPA (Law 24) — leverage-point analysis on a non-trivial task, before @CREATOR/@ARCH
  └── [New project / domain / module with unclear logic?]
        └── @CREATOR (one question to the user)
              → internally: INDUSTRY INTELLIGENCE
              → internally: @BIZ → KILL SIGNAL + MARKET_AUDIT.md
              → internally: @DOMAIN_EXPERT → BUSINESS_ROUTES.md
              → to user: ready package for approval
              → artifacts: BUSINESS_LOGIC.md + MARKET_AUDIT.md + BUSINESS_ROUTES.md
        └── [Has UI?]  → VISUAL CONCEPT (@CREATOR Step 5.5.A, VISUAL_CONCEPT_PROTOCOL)
              → world from CONCEPT_DNA_LIBRARY → TASTE GATE (cliché ban-list) → VISUAL_CONCEPT_[PROJECT].md
              → 4 passports by derivation (Step 5.5.B; no [hex] placeholders remain)
        └── [Has a public site?] → @SEO CORE (parallel with concept, exchanging demand-language ↔ world)
              → SEMANTIC_CORE_[PROJECT].md + page/URL map + rendering requirement
              → @ARCH fixes SSG/SSR by ADR (Law 29)
        └── [Public site needs generated media?] → @MEDIA_ENGINEER renders the approved concept
              → MEDIA_PASSPORT_[PROJECT].md + manifest + model routing + automated judge → clean plates
              (brand mark stays a CSS/SVG layer, never baked into pixels — Law 28)
  │ user approved the package
  ▼
[PLANNING]
  └── @LEAD: PRE-PLAN GATE (7 items)
  └── @ARCH / @FRONTEND → DRAFT: SYSTEM_DESIGN_[PROJECT].md + SAAS_ARCHITECTURE_SPINE_*.md
             + ARCH_SPINE_[EPIC].md (12 vertebrae, Law 31) + a DRAFT DEV_PROMPTS_*.md
             (with Domain Checklist from DOMAIN_STANDARDS.md) + ADR_REGISTRY.md updated
             + multi-wave foundation designed for the WHOLE product (Law 41)
  └── [@PRINCIPLE triggers?] → @PRINCIPLE MODE:MODEL (BEFORE final DEV_PROMPTS) → PRINCIPLE_FINDINGS_*.md
             → on 🟡/🔴 @ARCH closes the contract; only then DEV_PROMPTS become final
  └── [@AI_ENGINEER triggers?] → @AI_ENGINEER → RAG_PASSPORT / AGENT_GRAPH_PASSPORT / EVAL_PLAN
  └── [Async/queues/pipeline?] → JOB_PASSPORTS_[PROJECT].md / PIPELINE_PASSPORT_*.md (Law 30) before code
  └── ★ FORESIGHT / COMPLETENESS GATE (step 0.8, Law 41 — foundation-touch trigger):
             production-completeness rollup + Completeness Ledger present; foundation NOT tagged LATER;
             concept lock done before @MOTION/@DESIGN. @LEAD spot-checks 2–3 ledger rows against the artifact.
  └── [Security surface touched? — S1–S12, mechanical grep of the plan/diff]
             YES → @PENTEST S-0 MODE:THREAT_MODEL → a "## Security Contract" block written INTO DEV_PROMPTS
                   (a surface epic without one = incomplete artifact → GATE-1 blocker, Law 38)
             NO  → record `[SECURITY SURFACE: none]`
  └── [Landing / public site / portfolio?] → @MOTION CONCEPT (metaphor → archetype → motion language; MOTION_SPEC)
  └── [A new screen OR a new pattern/composition?] → @DESIGN SPEC → DESIGN_SPEC_*.md
             (public indexable page input = VISUAL_CONCEPT + SEO_ONPAGE_*) → @FRONTEND Visual Quality Gate §6
             (@DESIGN MODE:VERIFY may re-check after the ARCH draft)
  │ plan approved by user
  ▼
[TUNNEL] ← sequential execution of phases
  ├── @DEV → code → report (task matrix from DEV_EXECUTION_PASSPORT; CONTRACT SHEET + ASYNC_AWAIT_REFLEX)
  ├── @QA_ARCH → QA_REPORT_*.md → 🟢 or back to @DEV        ← MANDATORY BUSINESS GATE (on the code)
  ├── @QA_VISUAL → VISUAL_QA_REPORT_*.md → 🟢 or back to @DEV  ← MANDATORY FOR ANY UI CHANGE (on the render)
  ├── @QA → testing (risk-tiered floor T0–T3 + negative baseline, TESTING_CANON)
  ├── @SEC → 18-pillar audit (advisory — feeds @PENTEST)
  ├── @PENTEST → S-Wave adversarial gate INSIDE GATE-4 (surface touched): CODE_RECON→CHAIN→ATTACK→CRASH
  │              → any 🔴 blocks GATE-4; each finding → owner + red-green regression test in tests/pentest/
  ├── @SEO → TECH gate (public site): SEO_TECH_AUDIT — blocker on par with @PENTEST ("curl without JS returns content")
  │
  │ on anomaly → [AUDIT] → @AUDITOR (step 0: prove the artifact is the built one, Law 36) → return to tunnel
  │ on systemic defect → [PLANNING] (ARCH revision + ADR)
  │ on performance degradation → @PERF → report → @ARCH/@DEV
  ▼
[QUALITY GATE] ← all P0/P1 closed; @QA_ARCH 🟢; @QA_VISUAL 🟢 (UI); @PENTEST S-Wave no 🔴 (surface)
  │ user: "deploy"
  ▼
[DONE] → @OPS (manually) → @LAWYER (if needed)
         → [milestone: first pilot / release tag / surface-widening] @PENTEST S-Global GLOBAL_AUDIT
         → @SCRIBE (pitch, user docs, knowledge base — on request; public outputs in documentation/)
```

**Publish boundary (Law 40):** the chain stops at **ready-to-commit**. The agent never runs `git commit/push/tag` or merges a PR on its own — @OPS/deploy proceed only after the human has published.

**Transition rules:**

| From | To | Condition |
|------|----|-----------|
| START | START (concept) | Has UI → VISUAL_CONCEPT_*.md created; TASTE GATE passed; passports derived (no [hex] left) |
| START | START (SEO/media) | Public site → SEMANTIC_CORE + rendering ADR; media needed → MEDIA_PASSPORT + plates |
| START | PLANNING | BUSINESS_LOGIC + MARKET_AUDIT + BUSINESS_ROUTES created; KILL SIGNAL ✅ |
| PLANNING | PLANNING (model) | @PRINCIPLE triggered → MODE:MODEL verdict before DEV_PROMPTS final |
| PLANNING | PLANNING (foresight) | ★ FORESIGHT/COMPLETENESS GATE: rollup + ledger present, foundation not LATER, concept locked |
| PLANNING | PLANNING (security S-0) | Surface touched → Security Contract in DEV_PROMPTS (else GATE-1 blocker) |
| PLANNING | TUNNEL | PRE-PLAN GATE passed; LPA done; SYSTEM_DESIGN + spine created; DEV_PROMPTS final; user approved |
| TUNNEL (after DEV) | TUNNEL (QA_ARCH) | @DEV reported all to-dos with proof; self-pentest ran on any surface change |
| TUNNEL (QA_ARCH) | TUNNEL (QA_VISUAL) | @QA_ARCH 🟢 on all applicable vectors (1–19; Vector 11 if AI, Vector 12 if money/webhook) |
| TUNNEL (QA_VISUAL) | TUNNEL (QA) | @QA_VISUAL 🟢 for all affected UI routes (geometry V1–V12 + craft floor + aesthete verdict) |
| TUNNEL (QA/SEC/PENTEST) | QUALITY GATE | @QA floor green; @PENTEST S-Wave no 🔴 (surface); @SEO TECH pass (public site) |
| TUNNEL (QA_ARCH) | TUNNEL (DEV) | @QA_ARCH found 🔴 — return with a specific list |
| TUNNEL (PENTEST) | TUNNEL (DEV/ARCH) | 🔴 finding → code owner + red-green regression test before re-gate; architectural hole → @ARCH spine |
| TUNNEL | AUDIT | 3 DEV↔QA_ARCH iterations without result / production anomaly |
| AUDIT | TUNNEL | Root cause found (artifact-provenance confirmed), prompt for @DEV ready |
| AUDIT | PLANNING | @AUDITOR found a systemic architectural defect |
| QUALITY GATE | DONE | User explicitly said "deploy"; human has published the commit (Law 40) |

---

## 2. TRANSMISSION PROTOCOL

A role passes control only with an explicit contract. @LEAD formulates it if the role did not.

```
HANDOFF @[SENDER] → @[RECEIVER]

Context:       [task essence in one sentence]
Input:         [files, contracts, data for work]
Expected:      [specific artifact on output]
Criterion:     [how to verify it is done correctly]
Blockers:      [what is missing / what is unknown]
```

**Chain addition:** `@QA_ARCH → @QA_VISUAL → @QA` is a mandatory sequence for any UI change; on the security surface `@QA → (@SEC advisory + @PENTEST S-Wave blocking)` runs inside GATE-4 (see `.cursorrules` chain, `roles/ROLE_LEAD.md`, `roles/SECURITY_GATE_PROTOCOL.md`). Connection: `roles/ROLE_QA_VISUAL.md` · `roles/LAYOUT_INVARIANTS.md`.

**Role examples:**

```
HANDOFF @DEV → @QA_ARCH

Context:   Finance/Cashier module implemented
Input:     @src/pages/FinancePage.tsx @src/hooks/useFinance.ts
           @src/api/routers/finance.py
Expected:  QA_REPORT_Finance.md
Criterion: all applicable vectors checked (1–19; Vector 12 for money/webhook),
           verdict 🟢 or list of 🔴 for @DEV
Blockers:  none
```

```
HANDOFF @ARCH → @PRINCIPLE

Context:   Payments module architecture draft ready
Input:     @docs/artifacts/ARCH_SPINE_PAYMENTS.md + BUSINESS_ROUTES §Money Routes
Mode:      MODEL (before final DEV_PROMPTS)
Expected:  inline or PRINCIPLE_FINDINGS_Payments.md
Criterion: verdict 🟢/🟡/🔴 + MAIN RISK block; all triggers verified; forward-questions answered
Blockers:  none
```

```
HANDOFF @LEAD → @PENTEST

Context:   Payments epic touches the security surface (S1 identity, S3 money, S4 object access)
Input:     @docs/artifacts/ARCH_SPINE_PAYMENTS.md + DOMAIN_MODEL + DEV_PROMPTS_Payments.md (draft)
Mode:      THREAT_MODEL (S-0, planning)
Expected:  a "## Security Contract" block written INTO DEV_PROMPTS_Payments.md
Criterion: every touched S-element has abuse-cases with a NAMED guarantee; named leaves referenced
Blockers:  none (S-0 is never skipped for a missing spine — derive from S1–S12 + domain)
```

```
HANDOFF @LEAD → @AI_ENGINEER

Context:   RAG pipeline for knowledge base changed — new document corpus
Input:     @docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md §AI + @src/rag/
Mode:      RAG_AUDIT
Expected:  RAG_PASSPORT updated; all Pillars closed with a number or N/A+reason
Criterion: tenant isolation shown in code; EVAL_PLAN with a numeric threshold exists
Blockers:  none
```

```
HANDOFF @CREATOR → @LEAD

Context:   project [name] start complete
Input:     BUSINESS_LOGIC + MARKET_AUDIT + BUSINESS_ROUTES + VISUAL_CONCEPT (if UI)
           + SEMANTIC_CORE (if public site) + Completeness Ledger (Law 41 rollup)
Expected:  PRE-PLAN GATE + LPA + FORESIGHT gate + architectural planning
Criterion: foundation designed for the WHOLE product; SYSTEM_DESIGN + spine; DEV_PROMPTS_Phase1 ready
Blockers:  [none / what remains unclear]
```

---

## 3. ESCALATION LEVELS

A role does not stay silent and does not guess — it escalates.

```
Level 1 — the role handles it alone
Level 2 — Round Table (opinion of adjacent roles needed)
Level 3 — escalation to @LEAD (decision beyond the role's scope)
Level 4 — @LEAD blocks the phase, requests user input
```

**Triggers:**

**Level 2:**
- @ARCH proposes a solution affecting security → feeds @PENTEST S-0 (STRIDE sketch) + calls @SEC (advisory)
- @ARCH changes storage → creates ADR + calls @PRINCIPLE if triggers fire
- @DEV sees that implementation will change the API contract → calls @ARCH (does not decide alone)
- @QA_ARCH found a bug with unclear cause → calls @ARCH + @DEV
- @QA_ARCH found Missing Feature → calls @DOMAIN_EXPERT for gap analysis
- @AI_ENGINEER detected RAG quality degradation → calls @ARCH + @QA_ARCH
- @PENTEST found an architectural hole (tenant model / trust boundary / race) → back to @ARCH (spine + ADR), not a @DEV patch
- @FRONTEND: the backend cannot surface a capability the experience needs → CAPABILITY_MAP gap → @ARCH (grow the backend, Law 34)

**Level 3:**
- The role cannot formulate the acceptance criterion
- The decision requires changing the entire architecture
- A risk was discovered that KILL SIGNAL did not catch
- @QA_ARCH finds the same 🔴 after three @DEV iterations
- @AI_ENGINEER: quality dispute without reproducibility metrics — blocker until EVAL_PLAN exists
- @PENTEST: a repeated same-class finding across waves → REFLEX (is it @DEV execution or a thin @ARCH contract?)
- @DEV hits a MODEL BLOCKER (a contract hole that will not close on paper, Law 37) → @LEAD routes to @ARCH/@PRINCIPLE/@BIZ

**Level 4:**
- Contradiction between a business requirement and a technical constraint
- Need to delete or rewrite > 50% of the code
- A legal problem discovered (→ @LAWYER)
- KILL SIGNAL fires on an already-launched project
- Stack or storage change mid-project (→ Stack Change Protocol in ROLE_ARCH)
- An open 🔴 from @PENTEST that cannot be risk-accepted (Law 38 — never accepted; fixed or nothing ships)

---

## 4. QUALITY GATE BEFORE DEPLOYMENT

@LEAD runs personally, does not delegate. (Full gate map: `roles/LEAD_PRODUCT_GATE_PROTOCOL.md`.)

```
□ @QA_ARCH:       all modules received 🟢; QA_REPORT_*.md exist;
                   all applicable vectors (1–19); Vector 11 if AI contour; Vector 12 if money/webhook
□ @ARCH:          all decisions documented in spine / ARCH_SPINE_* / ARCH_MODULE_*.md;
                   ADR_REGISTRY.md current; single error contract fixed; foundation designed for full product (Law 41)
□ @PRINCIPLE:     if called — all findings either reflected in the contract, or explicitly rejected (date + reason)
□ @AI_ENGINEER:   if AI contour — RAG_PASSPORT + EVAL_PLAN exist; last eval run above the blocking threshold
□ @QA_VISUAL:     visual render audit 🟢 for all affected UI routes (geometry/overflow/CLS/states/micro
                   under hostile content; craft floor V15–V21 + aesthete crime-verdict table; baseline current)
□ @QA:            risk-tiered floor T0–T3 + negative baseline; no open P0/P1
□ @SEC:           18-pillar audit — advisory input; exploitable gaps handed to @PENTEST
□ @PENTEST:       (surface touched) PENTEST_REPORT_[WAVE] closed, no open 🔴; Security Contract lines verified HELD;
                   every 🔴/🟠 has a red-green regression test in tests/pentest/; T-series green for applicable classes
□ @SEO:           (public site) SEO_TECH_AUDIT pass — "curl without JS returns content"; sitemap/robots/301 map
□ @AUDITOR:       if called — recommendations applied or explicitly rejected (date + reason); artifact provenance proven
□ @PERF:          if called — critical findings resolved; Latency Budget from SYSTEM_DESIGN met
□ @OPS:           configs ready; client README written; health check passes
□ SEED:           prod-seed run and idempotent (roles/SEED_PROTOCOL.md)
□ Publish (Law 40): changes are ready-to-commit; the human runs commit/push — the agent does not
□ User:           explicitly said "deploy"
```

---

## 5. ARTIFACT NAMING AND STORAGE CONVENTION

### Folder structure

```
roles/                          ← CLOSED LAYER (.gitignore)
│                                 Universal roles, protocols, canons
├── ROLE_*.md                   ← System roles
├── ENGINEERING_PLAN.md         ← this file
├── SYSTEM_FILES_MASTER.md      ← canonical index of global system files
├── CONFLICT_REGISTRY.md        ← single tracker of cross-file conflict winners
├── PRODUCTION_READINESS_CANON.md · PLANNING_MATURITY_CANON.md   ← Law 41 doctrine
├── DOMAIN_STANDARDS.md · STACK_SELECTION.md · DATA_STORE_SELECTION.md
├── SYSTEM_DESIGN_PROTOCOL.md · ARCH_SPINE_PROTOCOL.md
├── SECURITY_GATE_PROTOCOL.md · PENTEST_SCENARIOS.md
├── ASYNC_WORKERS_CANON.md · DATA_INTEGRITY_CANON.md · DATABASE_RUNTIME_CANON.md
├── VISUAL_CRAFT_CANON.md · EDITORIAL_CRAFT_CANON.md · INTERFACE_CRAFT_CANON.md
│   · CANVAS_CRAFT_CANON.md · CRAFT_LINT_SPEC.md · QA_VISUAL_AESTHETE_SENSOR.md
├── CONCEPT_DNA_LIBRARY.md · VISUAL_CONCEPT_PROTOCOL.md · CONCEPT_ANATOMY.md
├── SEO_CANON.md · MEDIA_SYNTHESIS_CANON.md
├── DEV_EXECUTION_PASSPORT.md · ASYNC_AWAIT_REFLEX.md · MIGRATIONS_PLAYBOOK.md
└── LAYOUT_INVARIANTS.md · LAYOUT_COMPOSITION.md · FRONTEND_CAPABILITY_CANON.md

docs/artifacts/                 ← WORKING LAYER (committed)
│                                 Project decisions and wave artifacts
├── BUSINESS_LOGIC.md · BUSINESS_ROUTES.md · MARKET_AUDIT.md · DEVELOPMENT_PLAN.md
├── VISUAL_CONCEPT_[PROJECT].md      ← the world (@CREATOR Step 5.5)
├── SEMANTIC_CORE_[PROJECT].md       ← semantic core + page/URL map (@SEO CORE)
├── MEDIA_PASSPORT_[PROJECT].md      ← generated-media pipeline (@MEDIA_ENGINEER)
├── SYSTEM_DESIGN_[PROJECT].md       ← System Design (@ARCH)
├── SAAS_ARCHITECTURE_SPINE_*.md · ARCH_SPINE_[EPIC].md · ARCH_MODULE_[TOPIC].md
├── ADR_REGISTRY.md · ADR_[NN]_[TOPIC].md
├── CAPABILITY_MAP_[MODULE].md       ← what the backend makes possible (@FRONTEND, Law 34)
├── JOB_PASSPORTS_[PROJECT].md · PIPELINE_PASSPORT_[pipeline].md   ← async contour (Law 30)
├── DEV_PROMPTS_[NAME].md            ← incl. "## Security Contract" when the surface is touched
├── QA_REPORT_[MODULE].md · QA_[PROJECT].md
├── PENTEST_REPORT_[WAVE].md · PENTEST_REPORT_GLOBAL_[DATE].md     ← @PENTEST (S-Wave / S-Global)
├── SECURITY_RISK_ACCEPTANCE_[DATE].md   ← human-owned, time-boxed (§4A of SECURITY_GATE_PROTOCOL)
├── SEC_[PROJECT].md · SEO_ONPAGE_*.md · SEO_TECH_AUDIT_*.md
├── DESIGN_SPEC_[NAME].md · DESIGN_AUDIT_[NAME].md · MOTION_SPEC_*.md
├── PRINCIPLE_FINDINGS_[TOPIC].md
├── RAG_PASSPORT.md · AGENT_GRAPH_PASSPORT.md · EVAL_PLAN.md · EVAL_RESULTS_[DATE].md
├── METRICS_REGISTRY.md · OPS_[PROJECT].md · COMMERCIAL_PACK_[PROJECT].md
├── SESSION_STATE.md · REFLEX_[DATE].md · SYSTEM_EVOLUTION_LOG.md
└── archive/

docs/artifacts/waves/           ← WAVE ARTIFACTS LAYER (committed)
├── [N]/VISUAL_QA_REPORT_[MODULE].md  ← visual render audit (@QA_VISUAL)
└── [N]/MICRO_SPEC_[MODULE].md        ← micro-moments of operational screens (@MOTION MICRO)

frontend/tests/visual/__baseline__/   ← REFERENCE ANCHOR (golden screenshots; @QA_VISUAL,
                                          updated by @LEAD decision)
tests/pentest/                        ← permanent security regression tests (append-only; @DEV from @PENTEST PoC)

docs/product_state/             ← STATE LAYER (committed)
docs/[project-name]/            ← PROJECT LAYER (if present)
documentation/                  ← PUBLIC LAYER (to client, in git)
```

### File naming

```
Format: [TYPE]_[SUBJECT]_[QUALIFIER].md

Examples:
  ARCH_SPINE_PAYMENTS.md
  DEV_PROMPTS_SCHEDULE_WAVE3.md
  QA_REPORT_FINANCE_2026_05.md
  PENTEST_REPORT_WAVE3.md
  VISUAL_CONCEPT_CLINIC.md
  SEMANTIC_CORE_CLINIC.md
  ADR_001_QUEUE_SELECTION.md
  EVAL_RESULTS_2026_05_22.md
```

---

## 6. WHO READS THIS FILE AND WHEN

| Role | When | Which section |
|------|------|---------------|
| @LEAD | on every phase transition | 1, 4 |
| @LEAD | on role handoff | 2 |
| @LEAD | when choosing where to save an artifact | 5 |
| @AUDITOR | when diagnosing a stuck bug | 3 |
| @QA_ARCH | when escalating a found problem | 3 |
| @QA, @SEC, @PENTEST, @SEO, @OPS | before deployment | 4 |
| @ARCH, @DEV | when creating artifacts | 5 |
| @AI_ENGINEER, @MEDIA_ENGINEER | when creating RAG/Agent/media artifacts | 5 |
| @CREATOR, @DESIGN, @MOTION | when placing concept / design / motion artifacts | 5 |

---

Reference: `.cursorrules` (CHAIN PROTOCOL — source of truth) · roles/ROLE_LEAD.md · roles/LEAD_PRODUCT_GATE_PROTOCOL.md · roles/ROLE_QA_ARCH.md · roles/ROLE_QA_VISUAL.md · roles/ROLE_PENTEST.md · roles/SECURITY_GATE_PROTOCOL.md · roles/ROLE_AUDITOR.md · roles/ROLE_ARCH.md · roles/ROLE_PRINCIPLE.md · roles/ROLE_AI_ENGINEER.md · roles/ROLE_MEDIA_ENGINEER.md · roles/ROLE_SEO.md · roles/PRODUCTION_READINESS_CANON.md · roles/PLANNING_MATURITY_CANON.md · roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md · roles/SYSTEM_EVOLUTION_PROTOCOL.md (REFLEX/@EVOLVE) · roles/ROLE_LEAD.md (§CRYSTALLIZATION) (crystallised routes)
Version: 4.0 | 2026-07-23
