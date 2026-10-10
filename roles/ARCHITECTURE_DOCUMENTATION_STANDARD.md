# ARCHITECTURE_DOCUMENTATION_STANDARD.md
# Standard for maintaining architectural documentation
# Solves: "where to save", ADR registry, naming, who reads what
# Source of truth for @ARCH, @LEAD, @QA_ARCH when creating and finding artifacts

> **Principle:** documentation lives next to the code, is updated when decisions change,
> and any team member finds the needed file in 30 seconds without questions in chat.

---

## FOLDER MAP — what goes where

```
roles/                          ← CLOSED LAYER (gitignore)
│                                 Universal roles and protocols
│                                 Not project-specific
├── ROLE_*.md                   ← System roles
├── DOMAIN_STANDARDS.md         ← Business standards by page type
├── ENGINEERING_PLAN.md         ← System protocols
├── SYSTEM_DESIGN_PROTOCOL.md   ← Design under load protocol
├── DATA_STORE_SELECTION.md     ← Storage selection canon
├── STACK_SELECTION.md          ← Global stack selection
├── CACHE_STRATEGY.md           ← Caching policy
├── ARCHITECTURE_EXCELLENCE_PASSPORT.md
├── ARCHITECTURE_DOCUMENTATION_STANDARD.md  ← this file
└── MIGRATIONS_PLAYBOOK.md

docs/artifacts/                 ← WORKING LAYER (committed)
│                                 Project decisions of current waves
├── BUSINESS_LOGIC.md           ← What we are building and why (@CREATOR/@BIZ)
├── BUSINESS_ROUTES.md          ← Business route map (@DOMAIN_EXPERT)
├── MARKET_AUDIT.md             ← Competitive analysis (@BIZ)
├── DEVELOPMENT_PLAN.md         ← Current phase plan (@LEAD)
├── SYSTEM_DESIGN_[PROJECT].md  ← System Design document (@ARCH)
├── ADR_REGISTRY.md             ← Architectural decision registry (@ARCH)
├── SAAS_ARCHITECTURE_SPINE_2026.md  ← Main architectural document
├── ARCH_MODULE_[TOPIC].md      ← Modular architecture (@ARCH)
├── DEV_PROMPTS_[NAME].md       ← Instructions for @DEV (@ARCH/@LEAD)
├── QA_REPORT_[MODULE].md       ← Audit report (@QA_ARCH)
├── QA_[PROJECT].md             ← Testing report (@QA)
├── SEC_[PROJECT].md            ← Security report (@SEC)
├── AUDITOR_[TOPIC].md          ← Diagnostic report (@AUDITOR)
├── DESIGN_SPEC_[NAME].md       ← Design specification (@DESIGN)
├── PRINCIPLE_FINDINGS_[TOPIC].md ← Invariants (@PRINCIPLE)
├── RAG_PASSPORT.md             ← RAG specification (@AI_ENGINEER)
├── AGENT_GRAPH_PASSPORT.md     ← Agent graph (@AI_ENGINEER)
├── EVAL_PLAN.md                ← AI quality evaluation plan (@AI_ENGINEER)
├── EVAL_RESULTS_[DATE].md      ← Eval run results
├── METRICS_REGISTRY.md         ← Metrics registry M-XX (@ARCH)
├── SESSION_STATE.md            ← Session state (optional, @LEAD)
└── archive/                    ← Outdated; do not use for execution

docs/product_state/             ← STATE LAYER (committed)
│                                 Snapshot of the current product state
├── INDEX.md                    ← Layer navigation
├── BACKEND_PASSPORT.md         ← Actual backend state
├── FRONTEND_PASSPORT.md        ← Actual frontend state
├── PRODUCT_KNOWLEDGE_BASE.md   ← Knowledge base for AI (@SCRIBE)
├── SALES_PITCH.md              ← Pitch and sales formats (@SCRIBE)
└── USER_DOCS/                  ← User documentation

docs/[project-name]/            ← PROJECT LAYER (if present)
│                                 Specifics of a concrete product
├── README.md                   ← Project index
├── DEVELOPMENT_PLAN.md         ← Plan for this project
├── BUSINESS_LOGIC_[PROJECT].md
└── BUSINESS_ROUTES_[PROJECT].md

documentation/                  ← PUBLIC LAYER (in git, to client)
│                                 Only what goes outside
├── README.md
├── LOCAL_CICD_SETUP.md
└── USER_DOCS/

src/ / backend/ / frontend/     ← CODE
```

---

## NAMING RULES

### Principle: read the name — know the content without opening the file

```
Format:   [TYPE]_[SUBJECT]_[QUALIFIER].md

Examples:
  ARCH_MODULE_PAYMENTS.md         ← payment module architecture
  DEV_PROMPTS_SCHEDULE_WAVE3.md   ← DEV instructions, schedule, wave 3
  QA_REPORT_FINANCE_2026_05.md    ← QA report on finance, May 2026
  PRINCIPLE_FINDINGS_BOOKING.md   ← invariants for the booking module
  DESIGN_SPEC_DASHBOARD_V2.md     ← dashboard design version 2
  SYSTEM_DESIGN_CLINIC_SAAS.md    ← system design for clinic SaaS
  ADR_001_QUEUE_SELECTION.md      ← ADR #001, queue selection
```

### Forbidden names
```
❌ architecture.md           (too generic)
❌ notes.md                  (says nothing)
❌ new_arch.md               (does not say what is new)
❌ temp.md / draft.md        (keep in personal notes, not in the repo)
❌ ARCH_v2_final_FINAL.md    (versions — in git, not in the filename)
```

---

## ADR REGISTRY — architectural decisions

### What is an ADR

An ADR (Architecture Decision Record) — a document that records **one architectural decision**: context, options considered, the choice and its justification.

**Why:** after 6 months no one remembers "why we chose Redis Streams instead of Kafka". An ADR answers that question in 2 minutes.

### File: `docs/artifacts/ADR_REGISTRY.md`

```markdown
# ADR Registry

| ID | Name | Status | Date | Owner | File |
|----|------|--------|------|-------|------|
| ADR-001 | Queue selection: Redis Streams vs Kafka | Accepted | 2026-03-01 | @ARCH | ADR_001_QUEUE.md |
| ADR-002 | Multi-tenancy strategy | Accepted | 2026-03-05 | @ARCH | ADR_002_MULTITENANCY.md |
| ADR-003 | Embedding model selection | Accepted | 2026-04-10 | @AI_ENGINEER | ADR_003_EMBEDDING.md |
| ADR-004 | API horizontal scaling | In discussion | 2026-05-20 | @ARCH | ADR_004_SCALING.md |

Statuses: Proposed / In discussion / Accepted / Superseded / Rejected
```

### Format of a single ADR

```markdown
# ADR-[NN]: [Decision name]
> Status: [Proposed / Accepted / Superseded]
> Date: [date accepted]
> Owner: @[role]
> Affected components: [list]

## Context

[Why this decision needs to be made. What will change. What are the requirements.]

## Options considered

### Option 1: [name]
**Pros:** [list]
**Cons:** [list]
**Migration cost:** [estimate]

### Option 2: [name]
**Pros:** [list]
**Cons:** [list]

## Decision

**Winner: Option [N]**

[Justification — why exactly this option for this context]

## Consequences

**Positive:**
- [what improves]

**Negative / trade-offs:**
- [what is accepted]

**Risks:**
- [what can go wrong and how it is mitigated]

## Condition for review

[At what signal this decision should be reconsidered]

## Related decisions
- ADR-[NN]: [name] — [how it is related]
```

### When to create an ADR

| Situation | ADR mandatory |
|---------|--------------|
| Choosing a new storage (DB, queue, cache) | Yes |
| Changing embedding model or ANN backend | Yes |
| Multi-tenancy architecture | Yes |
| Scaling strategy | Yes |
| Protocol choice (REST vs gRPC vs GraphQL) | Yes |
| API versioning strategy | Yes |
| Deployment method (rolling vs blue-green vs canary) | Yes |
| Choosing between two same-class libraries | No (in code comment) |
| Variable naming, code style | No (in linting rules) |

---

## SPINE — the main architectural document

`docs/artifacts/SAAS_ARCHITECTURE_SPINE_2026.md` — the project's central document. All other artifacts either supplement it or reference it.

### Mandatory spine sections

```markdown
# [Project name] — Architecture Spine
> Version: [N] | Mode: [SAAS/ENTERPRISE] | Updated: [date]

## §1. System overview
[One diagram: components and their connections]

## §2. Stack
[Table: component → technology → version → justification]

## §3. Error contract (unified for the entire API)
{"detail": str, "code": str, "field": str|null}
[Error code table]

## §4. Multi-tenancy
[Tenant model, isolation, indexes]

## §5. DB schema (main tables)
[Critical tables only; details — in ARCH_MODULE_*.md]

## §6. API contracts (main endpoints)
[Key ones only; details — in ARCH_MODULE_*.md]

## §7. Authorisation system
[RBAC matrix, roles, how it is verified]

## §8. Financial contour
[Payment pattern, webhook flow, idempotency]

## §9. Queues and background tasks
[Celery queues, Kafka topics if any]

## §10. Observability
[Which metrics, logs, traces are mandatory]

## §11. Integration status
| Integration | Status | What is implemented |
|------------|--------|---------------------|
| [name] | Full flow / STUB | [description] |

## §12. ADR references
[List of key decisions with links to ADR_REGISTRY.md]
```

---

## WHO CREATES AND READS WHAT

| Artifact | Creates | Reads | Updates when |
|---------|---------|--------|-------------|
| BUSINESS_ROUTES.md | @DOMAIN_EXPERT | @ARCH, @LEAD | New module, domain change |
| SYSTEM_DESIGN_*.md | @ARCH | @LEAD, @OPS, @PERF | Load change, new component |
| ADR_REGISTRY.md + ADR_*.md | @ARCH | All roles | Any architectural decision |
| SAAS_ARCHITECTURE_SPINE_*.md | @ARCH | @DEV, @QA_ARCH, @LEAD | Every wave |
| ARCH_MODULE_*.md | @ARCH | @DEV, @QA_ARCH | Module development |
| DEV_PROMPTS_*.md | @ARCH / @LEAD | @DEV | Before every @DEV task |
| QA_REPORT_*.md | @QA_ARCH | @LEAD, @DEV | After every review |
| PRINCIPLE_FINDINGS_*.md | @PRINCIPLE | @ARCH, @LEAD | After concept revision |
| RAG_PASSPORT.md | @AI_ENGINEER | @DEV, @QA_ARCH | Pipeline change |
| METRICS_REGISTRY.md | @ARCH (rows) | @QA_ARCH, @PRINCIPLE | New metric |
| ADR_REGISTRY.md | @ARCH | All | On new ADR |

---

## ARTIFACT LIFECYCLE

```
Created → Active → Superseded → Archive

Rules:
- An active artifact reflects the CURRENT state of the system
- On update — do not create a new file, update the existing one
- Exception: DEV_PROMPTS_* and QA_REPORT_* — new file per wave/module
- A superseded artifact → docs/artifacts/archive/ with a note in the header
- Git stores history; the file contains only the current state (Law 3)
```

### Marking a deprecated artifact

```markdown
> ⚠️ ARCHIVE: this document is superseded.
> Current version: [link to new file]
> Date archived: [date]
> Reason: [one sentence]
```

---

## HEADER TEMPLATE FOR ANY ARCHITECTURAL DOCUMENT

Every `ARCH_MODULE_*.md` and `DEV_PROMPTS_*.md` starts with:

```markdown
# [Module / task name]
> Project: [name]
> Wave / Phase: [N]
> Mode: [SAAS / ENTERPRISE / HIGHLOAD]
> Created: [date] | Updated: [date]
> Status: [Draft / Active / Superseded]
> Owner: @[role]
> Related: [links to spine, ADR, DEV_PROMPTS]
```

---

## UPDATE RULES

```
On DB schema change:
  → update ARCH_MODULE_*.md or spine §5
  → create/update ADR if the decision is non-trivial
  → write migration (MIGRATIONS_PLAYBOOK.md)

On API contract change:
  → update spine §6 or ARCH_MODULE_*.md
  → if breaking change → create ADR with compatibility policy

On technology/library change:
  → create ADR
  → update spine §2 (stack)

On adding a new metric:
  → add row to METRICS_REGISTRY.md
  → fill in the card (@PRINCIPLE G4)

On closing a development wave:
  → update DEVELOPMENT_PLAN.md (mark completed phases)
  → DEV_PROMPTS_*.md of the closed wave → docs/artifacts/archive/
  → update spine if architecture changed
```

---

## QUICK LOOKUP — "where is X"

| What I'm looking for | Where to look |
|---------------------|--------------|
| Why we chose Redis instead of Kafka | ADR_REGISTRY.md → ADR_*.md |
| DB schema of the users table | SAAS_ARCHITECTURE_SPINE → §5, or ARCH_MODULE_*.md |
| Current development plan | DEVELOPMENT_PLAN.md |
| Instructions for @DEV on the current task | DEV_PROMPTS_[NAME].md |
| Results of the last QA_ARCH audit | QA_REPORT_[MODULE].md |
| What metrics exist in the system | METRICS_REGISTRY.md |
| What multi-tenancy means in the project | SAAS_ARCHITECTURE_SPINE → §4 |
| System load characteristics | SYSTEM_DESIGN_*.md |
| Invariants of the booking module | PRINCIPLE_FINDINGS_BOOKING.md |
| RAG pipeline contract | RAG_PASSPORT.md |
| Current actual backend state | docs/product_state/BACKEND_PASSPORT.md |

---

## ONBOARDING — reading order for a new team member

A new person on the project must get up to speed in 2–4 hours without questions in chat. The reading order is fixed:

```
Step 1 — Business (30 min)
  docs/artifacts/BUSINESS_LOGIC.md          ← what we are building and why
  docs/artifacts/BUSINESS_ROUTES.md         ← where the money is, who does what

Step 2 — Architecture (45 min)
  docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md  ← technical contract
  docs/artifacts/ADR_REGISTRY.md               ← why exactly this way
  docs/artifacts/SYSTEM_DESIGN_*.md            ← load characteristics

Step 3 — Current state (20 min)
  docs/artifacts/DEVELOPMENT_PLAN.md           ← where we are now
  docs/product_state/BACKEND_PASSPORT.md       ← actual code
  docs/product_state/FRONTEND_PASSPORT.md

Step 4 — Process (15 min)
  roles/ENGINEERING_PLAN.md                    ← how the system works
  .cursorrules                                 ← system laws
```

**Rule:** if after reading these files questions about the business or architecture remain — the documentation is incomplete. This is a signal for @LEAD to update the corresponding artifact.

---

## EXAMPLE OF A FILLED ADR_REGISTRY.md

```markdown
# ADR Registry
> Project: [name] | Updated: [date]

| ID | Name | Status | Date | Owner | File |
|----|------|--------|------|-------|------|
| ADR-001 | Celery + Redis instead of Kafka | Accepted | 2026-03-01 | @ARCH | ADR_001_QUEUE.md |
| ADR-002 | Multi-tenancy: shared DB + tenant_id | Accepted | 2026-03-02 | @ARCH | ADR_002_MULTITENANCY.md |
| ADR-003 | pgvector instead of Qdrant | Accepted | 2026-04-05 | @AI_ENGINEER | ADR_003_VECTOR_STORE.md |
| ADR-004 | Soft delete via deleted_at | Accepted | 2026-03-02 | @ARCH | in spine §4 |
| ADR-005 | TimescaleDB for metrics instead of ClickHouse | In discussion | 2026-05-20 | @ARCH | ADR_005_TIMESERIES.md |
| ADR-006 | React Native Expo instead of native | Accepted | 2026-05-01 | @ARCH | ADR_006_MOBILE.md |

## Superseded
| ID | Name | Reason superseded | Replaced by |
|----|------|-------------------|------------|
| ADR-000 | Redis as primary DB (MVP) | Impossible JOINs and transactions | ADR-002 (PostgreSQL) |
```

**Rule:** ADR_REGISTRY.md is a living document. Every architectural decision appears here **before** it ends up in spine or DEV_PROMPTS. Not after.

---

Reference: roles/ROLE_ARCH.md · roles/ENGINEERING_PLAN.md §5 · roles/RAG_CANON.md · .cursorrules PROJECT MEMORY
Version: 1.1 | 2026-05-22
