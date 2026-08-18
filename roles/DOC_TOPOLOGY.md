# DOC_TOPOLOGY.md
# Documentation and artifact topology canon — layout by document nature.
# Universal for any project. Six natures + 2 supports. Solves "chaos": ADR mixed with passports, delivery mixed with commerce, runbooks without a home.

> **Principle:** "A document has a nature. Chaos is where natures mixed in one folder. `dev_execution/` is clean because it is single-natured (plan only). Lay out by nature, not by project and not into 'misc artifacts'."
>
> **Connection:** extends `roles/TEMPLATE_DOCUMENTATION_ARCHITECTURE.md` and the seed `DOC_TOPOLOGY` (minimal versions) · gives a home to `roles/PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL.md` (it fills `execution/`) · aligned with `roles/FILE_MAP.md`, `roles/SYSTEM_FILES_MASTER.md`, `roles/ENGINEERING_PLAN.md`.

---

## 0. FIRST CUT: `roles/` ↔ `docs/`

Before laying out by nature — one top-level question: **reusable between projects or a fact of a specific project?**

```
Reusable standard / process / role / template     → roles/   (Layer P — global system)
Implementation fact of a specific project          → docs/    (further — by nature, §1)
```

Example: `roles/RUN_SERVICES.md`, `roles/MIGRATIONS_PLAYBOOK.md`, `roles/DEPLOY_VPS_STEP_BY_STEP.md` — **universal** ops protocols → `roles/`. But "how **this specific** product is deployed", its runbook and envelopes → `docs/operations/` (§2). Same principle: token canon — in `roles/`, project token values — in `docs/`.

---

## 1. SIX DOCUMENT NATURES (+ 2 supports)

| # | Nature | Question | Edit discipline | Folder |
|---|---------|--------|-------------------|-------|
| 1 | **Decision** | *why* we chose X | **append-only**: not edited, only `Superseded by ADR_NNN` | `docs/decisions/` |
| 2 | **Knowledge / truth** | *what exists now* (as designed) | **lives**: edited, reflects current truth | `docs/knowledge/` |
| 3 | **Plan** | *what we will build*, screen by screen and step by step, before code | **versioned as pack**: changes before wave start | `docs/execution/` |
| 4 | **Delivery** | *what we execute per wave* | **per wave**: created/closed within a wave | `docs/artifacts/waves/[N]/` |
| 5 | **State from code** | *what is confirmed by code* | **follows code**: openapi/schemas from implementation | `docs/product_state/` |
| 6 | **Operations** | *how to deploy/start/recover* | **procedure**: runbooks, updated per infra fact | `docs/operations/` |
| — | **Commerce** | selling | lives | `docs/commercial/` |
| — | **History** | was | frozen | `docs/archive/` |


**Rule in one phrase:** before creating a file, ask — *"what is its nature?"* — and put it in its single folder. No "different files in artifacts mixed together".

---

## 2. WHAT IS IN EACH FOLDER

### `docs/decisions/` — DECISIONS (ADR)
Only architectural decisions, append-only. **Never** mix with passports/plan.
```
decisions/
├── ADR_REGISTRY.md             # index: ID, title, status (Accepted/Rejected/Superseded), date
└── ADR_[NNN]_[SLUG].md         # one decision = one file; only status is edited
```
Why separate: ADR is a **causality log**, its value lies in immutability. When it sits mixed with living passports in one project folder, both are lost: ADR gets "polluted" by neighbour edits, passports drown among ADR.

### `docs/knowledge/` — KNOWLEDGE / TRUTH (passports)
Designed current truth about the product. Lives and is edited.
```
knowledge/
├── BUSINESS_LOGIC.md           # schema — roles/TEMPLATE_BIZ_LOGIC.md; source of truth for business rules
├── BUSINESS_ROUTES.md          # money/role/data routes
├── ARCHITECTURE_SPINE.md       # architecture spine (stack, layers, errors, observability)
├── AGENT_GRAPH_PASSPORT.md     # agent graph (if AI)
├── RAG_PASSPORT.md             # retrieval pipeline (if RAG)
├── MARKET_AUDIT.md             # market / competitor audit
├── DEVELOPMENT_PLAN.md  ·  DEVELOPMENT_ROADMAP.md   # phases and gates
├── METRICS_REGISTRY.md         # metrics registry
└── EVAL_AND_GOLDEN_SET.md  +  golden/   # golden sets for eval
```

### `docs/execution/` — PRE-CODE PLAN (Execution Pack — the "gem")
Full direction: every screen and every step planned and documented **before the first line of code**. Structure and process — `roles/PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL.md`.
```
execution/                       # was dev_execution/ — functional name
├── README.md  ·  PRODUCT_LOGIC_MAP.md  ·  SCREEN_REGISTRY_FULL.md  ·  COMPLIANCE_MATRIX_SCREENS.md
├── 00_MASTER_CHAIN.md  ·  00_ROLE_LAYERING.md
├── frontend/  (00_SYSTEM_ARCHITECTURE … 08_*_ENGINEERING)
├── database/  ·  overlays/  ·  metrics/
└── waves/  (P0_FOUNDATION … Pn)        # PLAN of waves
```
> Boundary with `artifacts/waves/`: in `execution/waves/Px` — **full plan** of the wave (B1…Bn, F1…Fn, file paths, acceptance). In `artifacts/waves/[N]/` — **execution** (short DEV_PROMPTS index + QA/Visual results). Plan — in execution, delivery — in artifacts.

### `docs/artifacts/waves/[N]/` — DELIVERY (wave-first)
Everything for wave N together (see `roles/FRONTEND_CONSOLIDATION.md` Patch 13):
```
artifacts/waves/[N]/
├── DEV_PROMPTS_WAVE_[N].md     # wave index → link to execution/waves/Px
├── QA_REPORT_*.md  ·  VISUAL_QA_REPORT_*.md
├── DESIGN_SPEC_*.md  ·  MOTION_CONCEPT_*.md  ·  MICRO_SPEC_*.md
└── ACCEPTANCE_TEST_REGISTRY_*.md  ·  ARCH_MODULE_*.md (if for this wave)
```
Cross-cutting (living the full project) — in root `docs/artifacts/`: indexes `ARTIFACT_MAP.md`. ADR registry — in `decisions/`.

### `docs/product_state/` — STATE FROM CODE
```
product_state/
├── openapi/    # API contracts from implementation
└── schemas/    # JSON Schema bodies
```

### `docs/operations/` — OPERATIONS (project runbooks)
Operational procedures for **this specific** product: how to deploy, start, migrate, recover, plus scale envelopes (load/latency/degradation for this specific installation).
```
operations/
├── DEPLOY_RUNBOOK.md           # step-by-step deployment of this product (based on roles/DEPLOY_VPS_STEP_BY_STEP.md)
├── RUN_SERVICES.md             # how to bring up services locally/prod (based on roles/RUN_SERVICES.md)
├── MIGRATION_RUNBOOK.md        # migration order for this product (base — roles/MIGRATIONS_PLAYBOOK.md)
├── INCIDENT_RUNBOOK.md         # recovery, rollback, on-call
└── SCALE_ENVELOPES.md          # target loads/latency/limits (from SYSTEM_DESIGN)
```
> Boundary with `roles/`: **universal** ops protocols (how to do migrations in general, how Docker infra passport is structured) — in `roles/`. **This** product (its commands, hosts, order) — in `docs/operations/`. Boundary with `knowledge/`: operations are a *procedure* (what to do), not *truth* (what was designed); architecture passport stays in `knowledge/`.

> **Architecture note:** architecture passport + scale envelopes = `knowledge/ARCHITECTURE_SPINE.md` (+ envelopes can be extracted to `operations/SCALE_ENVELOPES.md` if read during operations). We do not introduce a separate top-level `docs/architecture/` to avoid splitting "architecture truth" across two folders; for large volumes of arch references, a `knowledge/architecture/` sub-folder is acceptable.

### `docs/commercial/` — COMMERCE
`COMMERCIAL_OFFER_*`, `COMMERCIAL_PROPOSAL_*`, `BRIEF_QUESTIONS_FOR_CLIENT`, `FEASIBILITY_*`, presentations. **Not** in `artifacts/` — this is a different nature (selling, not delivery).

### `docs/archive/` — HISTORY
`history/`, `_transient_meeting_rudiments/`, outdated versions. Frozen context, not a source of truth.

---

## 3. DE-FACTO → TOPOLOGY MIGRATION (from current repository)

| Currently (de-facto) | Nature | Where to |
|-------------------|---------|------|
| Mixed project folder: `ADR_*.md`, `ADR_MASTER_*` | decision | `decisions/` |
| Mixed project folder: `ARCHITECTURE_*`, `BUSINESS_LOGIC_*`, `BUSINESS_ROUTES_*`, `AGENT_GRAPH_PASSPORT`, `MARKET_AUDIT`, `DEVELOPMENT_ROADMAP`, `EVAL_AND_GOLDEN_SET`, `golden/` | knowledge | `knowledge/` |
| `dev_execution/` (all) | plan | `execution/` |
| `artifacts/DEV_PROMPTS_WAVE_*`, `DESIGN_SPEC_*`, `ARCH_MODULE`, `ACCEPTANCE_TEST_REGISTRY`, `OPENAPI_DELTA`, `EVAL_PLAN` | delivery | `artifacts/waves/[N]/` |
| `artifacts/COMMERCIAL_*`, `BRIEF_QUESTIONS_FOR_CLIENT`, `FEASIBILITY_*`, `docs/presentation/` | commerce | `commercial/` |
| `artifacts/history/`, `_transient_meeting_rudiments/` | history | `archive/` |
| `product_state/openapi`, `schemas` | state | stays in `product_state/` |
| `roles/DEPLOY_VPS_STEP_BY_STEP`, `RUN_SERVICES`, `MIGRATIONS_PLAYBOOK` (universal) | operations (canon) | stay in `roles/`; project runbooks → `docs/operations/` |

> Folder names in the topology are **functional** (`decisions/`, `knowledge/`). The project name goes either into passport file names (`ARCHITECTURE_[PROJECT].md`) or into a wrapper `docs/[project]/` in a multi-product repository (see §5).

---

## 4. PLACEMENT RULE (1 question → folder)

```
Is this a decision "why we chose X"?                    → decisions/        (append-only)
Is this current designed truth?                          → knowledge/        (lives)
Is this a plan "what we will build" before code?         → execution/        (pack)
Is this the execution of a specific wave?                → artifacts/waves/[N]/
Is this a contract/schema from code?                     → product_state/
Is this a procedure to deploy/start/recover?             → operations/
Is this a sale?                                          → commercial/
Is this already obsolete / transient?                    → archive/
```
Deterministic: nature is determined by the file's essence, not by "where it landed". `@LEAD`/`@ARCH` apply this rule when creating any document.

---

## 5. MULTI-PRODUCT REPOSITORY

One product in repo → folders at `docs/` root (as above).
Multiple products → wrapper by project, **same topology inside**:
```
docs/[project-a]/{decisions,knowledge,execution,artifacts,product_state,commercial,archive}/
docs/[project-b]/{...}
docs/  (shared: DOCUMENTATION_SYSTEM.md, cross-project)
```
Project name — at the wrapper level, not inside. Inside — always five natures.

---

## 6. SOURCE OF TRUTH (priority on conflict)

```
1. Code and tests
2. Active wave contracts             → artifacts/waves/[N]/
3. State from code                   → product_state/
4. Knowledge / passports             → knowledge/
5. Decisions (context "why")         → decisions/
6. Plan (before code; after start — reference only) → execution/
7. Reusable process rules            → roles/
```
Commerce and archive — outside truth priority (not a source for implementation).

---

## 7. ADR DISCIPLINE (why "sacred-separate")

- One ADR = one file, **immutable in substance**. Changed mind — new ADR with status, old one → `Superseded by ADR_NNN`. Do not edit the body retroactively.
- `decisions/` **never** contains passports, plans, specs — only ADR + registry.
- ADR references knowledge (`knowledge/…`) and plan (`execution/…`), but does not store them.
- This eliminates the root of "chaos": ADR stops drowning among architecture passports, and passports stop drowning among ADR.

---

## 8. @LEAD CHECKLIST "topology is clean"

```
□ decisions/ — only ADR + ADR_REGISTRY (zero passports/plans)
□ knowledge/ — passports live; NO ADR here
□ execution/ — Execution Pack per PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL
□ artifacts/ — only delivery per waves/[N]/ + cross-cutting indexes in root; zero commerce/history
□ product_state/ — openapi/schemas from code
□ commercial/ — sales extracted from artifacts
□ operations/ — project runbooks (deploy/run/migrate/incident); universal ops protocols remain in roles/
□ archive/ — history extracted from working folders
□ Folder names are functional (not the project name inside the topology)
```

---

## 9. NAVIGATION (entry points)

- **Master system file index:** `roles/SYSTEM_FILES_MASTER.md`
- **System composition (how `roles/` and `docs/` work together):** `docs/DOCUMENTATION_SYSTEM.md`
- **File and artifact map:** `roles/FILE_MAP.md`
- **How `execution/` is filled:** `roles/PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL.md`
- **Delivery layer `artifacts/` layout:** `roles/FRONTEND_CONSOLIDATION.md` (Patch 13, wave-first)

---

Reference: `roles/TEMPLATE_DOCUMENTATION_ARCHITECTURE.md` (minimal version) · `roles/PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL.md` (fills execution/) · `roles/FILE_MAP.md` · `roles/FRONTEND_CONSOLIDATION.md` (Patch 13 wave-first) · `roles/SYSTEM_FILES_MASTER.md` · `roles/ENGINEERING_PLAN.md` · `roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md` · `roles/TEMPLATE_BIZ_LOGIC.md`
Version: 1.0 | 2026-06-14
