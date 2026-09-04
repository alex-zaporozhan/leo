# 📁 FILE_MAP — System Structure
> Where each file lives. @LEAD and @ARCH create files according to this map.
> Synchronised with ENGINEERING_PLAN.md §5 and ARCHITECTURE_DOCUMENTATION_STANDARD.md

---

## PROJECT ROOT (/)

```
.cursorrules              ← system constitution (read on every request)
.env                      ← secrets (NEVER in git)
.env.example              ← variable template (in git)
.gitignore                ← roles/ and .env excluded
docker-compose.yml
Makefile                  ← make up / down / logs / backup
README.md                 ← for client only (startup, setup)
```

---

## /roles — private layer (.gitignore, does not go to client)

Universal roles, protocols and canons. Not tied to a specific project.

### Roles (invoked via @mention)

```
roles/ROLE_LEAD.md
roles/ROLE_CREATOR.md
roles/ROLE_ARCH.md
roles/ROLE_DEV.md
roles/ROLE_FRONTEND.md
roles/ROLE_DESIGN.md
roles/ROLE_QA.md
roles/ROLE_QA_ARCH.md
roles/ROLE_SEC.md
roles/ROLE_AUDITOR.md
roles/ROLE_PERF.md
roles/ROLE_OPS.md
roles/ROLE_BIZ.md
roles/ROLE_LAWYER.md
roles/ROLE_DOMAIN_EXPERT.md
roles/ROLE_AI_ENGINEER.md
roles/ROLE_PRINCIPLE.md
roles/ROLE_SCRIBE.md
roles/ROLE_CREATOR.md
```

### System protocols

```
roles/ENGINEERING_PLAN.md              ← state machine, Transmission Protocol, Quality Gate
roles/STACK_SELECTION.md               ← global stack (Python/Java/Go/Mobile/AI/K8s)
roles/SYSTEM_DESIGN_PROTOCOL.md        ← design for load (Load Profile, Latency Budget)
roles/DATA_STORE_SELECTION.md          ← storage selection (PostgreSQL/Redis/Kafka/ClickHouse/pgvector)
roles/CACHE_STRATEGY.md                ← caching policy
roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md ← maturity norms, scorecard, KPI targets
roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md ← arch documentation standard + ADR
roles/DEV_EXECUTION_PASSPORT.md        ← @DEV checkpoint map + pattern catalogue
roles/NONFUNCTIONAL_SCORECARD.md       ← live NFR tracking per project
roles/DOMAIN_STANDARDS.md             ← minimum business standard by page type
roles/ENGINEERING_PLAN.md             ← system protocols
roles/PROCESS_LAUNCH.md               ← product launch phases (MVP → stability → growth)
roles/TESTING_CANON.md                ← check category base for @QA, @ARCH, @AUDITOR
roles/SEED_PROTOCOL.md                ← seed data protocol
roles/METRICS_PROTOCOL.md             ← metric creation and maintenance rules
roles/MIGRATIONS_PLAYBOOK.md          ← Alembic/Flyway, safe migrations
roles/LOGGING_OBSERVABILITY_PROTOCOL.md ← logging standard
roles/DOCKER_INFRA_PASSPORT.md        ← Docker, images, CI/CD passport
roles/ENV_COMPOSE_CENTRALIZATION.md   ← environment variable centralisation
roles/JENKINS_PIPELINE_PROTOCOL.md    ← CI/CD pipeline protocol
roles/RAG_CANON.md                    ← source order for AI in Cursor
```

### v6.16 additions — new canons

```
roles/LAYOUT_INVARIANTS.md        ← layout invariants (equal-height, reserved height, zero-shift)
roles/ROLE_QA_VISUAL.md           ← render sensor (render→measure→compare; V1–V10; baseline)
roles/HERO_ARCHETYPES.md          ← 8 hero archetypes + selection protocol
roles/MOTION_AMBITION_DIAL.md     ← @MOTION ambition dial + MICRO mode
roles/FRONTEND_CONSOLIDATION.md   ← single source map + TPF reclassification
roles/CONCEPT_DNA_LIBRARY.md      ← 12 concept worlds with recipes (palettes/fonts/effects/motion/Mantine skins)
roles/VISUAL_CONCEPT_PROTOCOL.md  ← concept before code: TASTE GATE, passport derivation, RESKIN, model economics
roles/CONCEPT_ANATOMY.md          ← concept anatomy: 8 DNA axes, coherence, reference protocol, world builder
roles/LAYOUT_COMPOSITION.md       ← layout grammar: 3 space laws, 8 primitives, collision diagnostics
roles/ROLE_SEO.md                 ← Head of SEO: CORE/ONPAGE/TECH/MONITOR modes, showcase deploy gate
roles/SEO_CANON.md                ← SEO canon: semantics→IA, SSG/SSR rendering, tech minimum, local, monitoring
roles/ASYNC_WORKERS_CANON.md      ← worker contract: three planes, AZ-1…10, JOB PASSPORT, T-A…G
roles/DATA_INTEGRITY_CANON.md     ← integrity under concurrency: protection hierarchy, race recipes, ledger, T-H
roles/ARCH_SPINE_PROTOCOL.md      ← decision spine: 12 numbered vertebrae, complexity ladder, timeouts/deadlines
roles/DOC_TOPOLOGY.md             ← docs/ topology by document nature (decisions/knowledge/execution/artifacts/…)
roles/INTEGRATION_PATCHES.md      ← v6.16 patch log
roles/SYSTEM_UPGRADE_MANIFEST.md  ← upgrade manifest
```

### @LEAD protocols

```
roles/LEAD_PRODUCT_GATE_PROTOCOL.md   ← quality gates GATE-0..GATE-6
roles/LEAD_ANTI_CHECKBOX_PROTOCOL.md  ← detecting checkboxes without evidence
roles/SECOND_PASS_PROTOCOL.md          ← the clean-context audit pass (SP-0…SP-3, role set by task class, derivation chain, false-green catalogue)
roles/MOTION_REFLEX.md                 ← the motion reflex: R1–R12 literal greps @DEV runs over their own diff before handoff, mirrored at @QA_VISUAL
roles/MOTION_CRAFT_CANON.md            ← the craft of movement: THE MOTION FLOOR (§1), the grammar of the in-between (§2 order·offset·overlap·origin·verb·keyframes), the M1–M12 stiffness catalogue (§3)
roles/RULE_INTEGRITY_PROTOCOL.md       ← the seven tests a rule must pass before it enters the system, and the ladder between two rules that are both true (run on a finding before it is accepted)
roles/LOGIC_MODELING_CANON.md          ← the domain model before the structure (Law 42): seven layers, twelve adversaries
roles/LEAD_PRODUCT_LOGIC_EXCELLENCE.md ← product logic excellence reference
```

### Templates

```
roles/TEMPLATE_MODULE_DEV.md          ← module development standard (for @ARCH)
roles/TEMPLATE_BIZ_LOGIC.md           ← business logic template
roles/TEMPLATE_COMMERCIAL_PACK.md     ← commercial package template
roles/TEMPLATE_DESIGN_UX.md           ← UI/UX for marketing pages
roles/TEMPLATE_ADMIN_UI_UX.md         ← UI/UX for operational screens (admin/app)
roles/TEMPLATE_QA_FRONTEND_VISUAL_CANON.md
roles/DOMAIN_STANDARDS.md (§5 Analytics/Reports)
roles/TEMPLATE_PROJECT_PROFILE.md
```

### Optional

```
roles/ROLE_LEAD.md (§CRYSTALLIZATION)                     ← only upon @LEAD proposal + user confirmation
roles/NICHE_BOOTSTRAP_PROTOCOL.md
```

---

## /docs/artifacts — working layer (committed)

Project artifacts of current waves. Created and updated during development.

### Business and market (@CREATOR, @BIZ, @DOMAIN_EXPERT)

```
docs/artifacts/BUSINESS_LOGIC.md      ← what we are building and why (living document)
docs/artifacts/BUSINESS_ROUTES.md     ← money, role, data routes
docs/artifacts/MARKET_AUDIT.md        ← competitive analysis
```

### Architecture (@ARCH, @AI_ENGINEER, @PRINCIPLE)

```
docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md  ← main architecture document
docs/artifacts/SYSTEM_DESIGN_[PROJECT].md    ← system design (load, latency, bottleneck)
docs/artifacts/ADR_REGISTRY.md               ← architectural decision registry
docs/artifacts/ADR_[NN]_[TOPIC].md           ← individual ADR
docs/artifacts/ARCH_MODULE_[TOPIC].md        ← modular architecture
docs/artifacts/PRINCIPLE_FINDINGS_[TOPIC].md ← invariants (@PRINCIPLE)
docs/artifacts/RAG_PASSPORT.md               ← RAG specification (@AI_ENGINEER)
docs/artifacts/AGENT_GRAPH_PASSPORT.md       ← Agent graph (@AI_ENGINEER)
docs/artifacts/EVAL_PLAN.md                  ← AI quality evaluation plan
docs/artifacts/EVAL_RESULTS_[DATE].md        ← eval run results
docs/artifacts/METRICS_REGISTRY.md          ← metrics registry M-XX
```

### Development (@LEAD, @ARCH)

```
docs/artifacts/DEVELOPMENT_PLAN.md           ← current phase plan (living document)
docs/artifacts/DEV_PROMPTS_[NAME].md         ← instructions for @DEV
```

### QA and security (@QA_ARCH, @QA, @SEC, @AUDITOR)

```
docs/artifacts/QA_REPORT_[MODULE].md         ← business audit report (@QA_ARCH)
docs/artifacts/QA_[PROJECT].md               ← testing report (@QA)
docs/artifacts/SEC_[PROJECT].md              ← security report (@SEC)
docs/artifacts/AUDITOR_[TOPIC].md            ← diagnostics report (@AUDITOR)
```

### Design (@DESIGN)

```
docs/artifacts/DESIGN_SPEC_[NAME].md         ← design specification
docs/artifacts/DESIGN_AUDIT_[NAME].md        ← design audit
```

### Product documentation (@SCRIBE)

```
docs/artifacts/PRODUCT_KNOWLEDGE_BASE.md    ← RAG document about product for AI
docs/artifacts/SALES_PITCH.md               ← pitch for investor/client
docs/artifacts/USER_DOCS/                   ← documentation for end user
  docs/artifacts/USER_DOCS/INDEX.md
  docs/artifacts/USER_DOCS/QUICK_START.md
  docs/artifacts/USER_DOCS/[MODULE].md
```

### Commercial and operational (@BIZ, @OPS)

```
docs/artifacts/COMMERCIAL_PACK_[PROJECT].md  ← commercial package
docs/artifacts/OPS_[PROJECT].md              ← infrastructure solution
```

### System

```
docs/artifacts/SESSION_STATE.md             ← optional (session on/off/save)
docs/artifacts/REFLEX_[DATE].md             ← REFLEX
docs/artifacts/archive/                     ← obsolete; do not use for execution
```

---

## docs/ topology by document nature (canon: `roles/DOC_TOPOLOGY.md`)

```
docs/decisions/                ← DECISIONS: ADR (append-only) + ADR_REGISTRY. "Why" only.
docs/knowledge/                ← KNOWLEDGE/TRUTH: BUSINESS_LOGIC, BUSINESS_ROUTES, ARCHITECTURE_SPINE,
                                  AGENT_GRAPH/RAG_PASSPORT, MARKET_AUDIT, DEVELOPMENT_PLAN, METRICS_REGISTRY, EVAL+golden/
docs/execution/                ← PRE-CODE PLAN (Execution Pack; was dev_execution/): PRODUCT_LOGIC_MAP,
                                  SCREEN_REGISTRY, frontend/00-08, database/, overlays/, waves/ (plan)
docs/artifacts/waves/[N]/      ← WAVE DELIVERY TOGETHER: DEV_PROMPTS_*, QA_REPORT_*, VISUAL_QA_REPORT_* (@QA_VISUAL),
                                  DESIGN_SPEC_*, MOTION_CONCEPT_*, MICRO_SPEC_* (@MOTION MICRO)
docs/artifacts/roadmap/pass1/  pass2/   ← Roadmap passes separate
docs/artifacts/reference/tpf/  ← TPF_* as project example
docs/product_state/            ← STATE FROM CODE: openapi/, schemas/
docs/operations/               ← OPERATIONS: project runbooks (deploy/run/migrate/incident, scale envelopes); universal ops — in roles/
docs/commercial/  docs/archive/ ← commerce and history (extracted from artifacts)
frontend/tests/visual/__baseline__/     ← @QA_VISUAL harness and baseline anchor (with code, not in wave)
```

---

## /docs/product_state — state layer (committed)

Snapshot of the current actual state of the product. Updated by @SCRIBE and @LEAD.

```
docs/product_state/INDEX.md
docs/product_state/BACKEND_PASSPORT.md      ← actual backend state
docs/product_state/FRONTEND_PASSPORT.md     ← actual frontend state
docs/product_state/PRODUCT_KNOWLEDGE_BASE.md (mirror from artifacts/)
docs/product_state/SALES_PITCH.md          (mirror from artifacts/)
docs/product_state/USER_DOCS/              (mirror from artifacts/)
docs/product_state/RAG_NAVIGATION_S_LAYER.md ← Layer S navigation for RAG
```

---

## /docs/[project-name] — project layer (committed)

When a dedicated project folder exists. Optional.

```
docs/[project-name]/README.md
docs/[project-name]/DEVELOPMENT_PLAN.md
docs/[project-name]/BUSINESS_LOGIC_[PROJECT].md
docs/[project-name]/BUSINESS_ROUTES_[PROJECT].md
```

---

## /documentation — public layer (in git, goes to client)

```
documentation/README.md
documentation/LOCAL_CICD_SETUP.md
documentation/CICD_JENKINS_S3_RUNBOOK.md
documentation/USER_DOCS/                   ← client copy
```

---

## /deploy — final build for client (@OPS)

```
deploy/
├── docker-compose.yml
├── .env.example
├── nginx.conf
├── Makefile
├── LICENSE.txt                             ← @LAWYER fills before handover
└── README.md                              ← for client ONLY
```

---

## /src, /backend, /frontend — code

```
src/ or backend/         ← server code
frontend/                ← client code
tests/
  tests/rag/             ← golden-set for RAG eval (EVAL_PLAN.md)
scripts/
  scripts/seeds/         ← seed data (never in migrations)
alembic/ or migrations/  ← DB migrations
```

---

## RULES

**Not in git:** `.env`, `roles/` (entirely), `__pycache__`, `node_modules`, `deploy/` with real secrets.

**In git:** everything else including `docs/` in full — RAG build history is preserved.

**Artifacts are updated in place** — no new files like `ARCH_v2.md`. Git stores history. Exception: DEV_PROMPTS and QA_REPORT — new file per wave/module.

**DEVELOPMENT_PLAN.md** — living document of the current phase. Completed phases collapse to one ✅ line.

**@CREATOR creates:** only `BUSINESS_LOGIC.md` — everything else is handed off via Transmission Protocol to @LEAD.

**New file in roles/** — add to RAG_CANON.md (reading order) and to this FILE_MAP.

---

Reference: roles/ENGINEERING_PLAN.md §5 · roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md · roles/RAG_CANON.md · .cursorrules PROJECT MEMORY
Version: 2.0 | 2026-05-22 (v6.16: new canons added, docs/ topology merged)
