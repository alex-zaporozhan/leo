# RAG_CANON — source reading order for AI

> Version: 2026-05-22
> Purpose: a single, verifiable source reading order without broken links and without guesswork.

---

## 1. Strict priority on conflict

1. **Code and tests** of the repository.
2. **Layer W:** `docs/artifacts/` — active project contracts of the current wave.
3. **Layer S:** `docs/product_state/` — summary of current product state, confirmed by code.
4. **Layer P:** `roles/` — roles, protocols, templates, and process standards.
5. **Archive and external user materials** (`docs/artifacts/archive/`, `documentation/`) — context only.

When documents at the same level conflict, the more recent document with an explicit version/date and owner wins.

---

## 2. Base reading route for AI

Order for any project. Read sequentially — each level refines the previous one.

### Layer W — current project contracts

1. `docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md` — current technical contract (stack, DB, API, errors)
2. `docs/artifacts/BUSINESS_LOGIC.md` — what we are building and why
3. `docs/artifacts/BUSINESS_ROUTES.md` — money, role, and data routes
4. `docs/artifacts/SYSTEM_DESIGN_[PROJECT].md` — load characteristics, Latency Budget, Failure Modes
5. `docs/artifacts/ADR_REGISTRY.md` — architectural decision registry (why exactly this way)
6. `docs/artifacts/DEVELOPMENT_PLAN.md` — current phase and plan

### Layer S — product state by facts

7. `docs/product_state/INDEX.md` — Layer S navigation
8. `docs/product_state/BACKEND_PASSPORT.md` — actual backend state
9. `docs/product_state/FRONTEND_PASSPORT.md` — actual frontend state

### Layer P — process and roles (when working with roles or protocols)

10. `roles/ENGINEERING_PLAN.md` — state machine, protocols, Quality Gate
11. `roles/ROLE_*.md` — the role relevant to the task

**Frontend cluster — mandatory additions to Layer P:**
```
roles/LAYOUT_INVARIANTS.md      — geometry (read with FRONTEND_DESIGN_EXCELLENCE)
roles/ROLE_QA_VISUAL.md         — visual render audit
roles/HERO_ARCHETYPES.md        — public site composition
roles/MOTION_AMBITION_DIAL.md   — ambition + MICRO
roles/FRONTEND_CONSOLIDATION.md — map of single sources (priority of tokens/geometry/direction)
roles/CONCEPT_DNA_LIBRARY.md    — concept worlds: aesthetic values as recipes (read during VISUAL CONCEPT / RESKIN)
roles/VISUAL_CONCEPT_PROTOCOL.md — concept process: TASTE GATE, derivation, RESKIN (read with ROLE_CREATOR/ROLE_DESIGN)
roles/CONCEPT_ANATOMY.md        — concept generative core: 8 axes, reference protocol (read FIRST during VISUAL CONCEPT)
roles/LAYOUT_COMPOSITION.md     — layout construction grammar (read by @DEV BEFORE UI code, with LAYOUT_INVARIANTS)
roles/ROLE_SEO.md               — visibility process: 4 modes, gate (read during public site with ROLE_ARCH)
roles/SEO_CANON.md              — SEO knowledge: core, rendering, technical baseline, on-page (canon for CORE/TECH)
roles/ASYNC_WORKERS_CANON.md    — background task contract (read by @ARCH/@DEV with any queue; T-tests by @PENTEST)
roles/DATA_INTEGRITY_CANON.md   — data invariants under concurrency (read by @ARCH before schema; ledger in ADR)
roles/ARCH_SPINE_PROTOCOL.md    — decision spine: 12 vertebrae, complexity ladder, timeouts (read by @ARCH with ADR, @QA_ARCH spine vector)
```

**Single sources (priority on conflict):** project aesthetic values → `VISUAL_CONCEPT_*` (world from `CONCEPT_DNA_LIBRARY`); tokens → `FRONTEND_DESIGN_EXCELLENCE`; geometry → `LAYOUT_INVARIANTS`; business minimum → `DOMAIN_STANDARDS`; module direction → `TEMPLATE_MODULE_DEV §2`; business logic → requirements + `BUSINESS_LOGIC.md`. `TPF_*` — a project example, not a canon.

### Extended route — by task type

**For tasks with AI contour (RAG, agents, embedding):**
- `roles/ROLE_AI_ENGINEER.md` — Pillars, passports, EVAL_PLAN
- `roles/RAG_ARCHITECTURE_STACK_2026.md` — RAG stack canon (if present in the project)
- `docs/artifacts/RAG_PASSPORT.md` — retrieval pipeline specification
- `docs/artifacts/AGENT_GRAPH_PASSPORT.md` — agent graph specification
- `docs/artifacts/ADR_[NN]_*.md` — specific ADRs on AI decisions

**For architectural design tasks:**
- `roles/SYSTEM_DESIGN_PROTOCOL.md` — design under load protocol
- `roles/DATA_STORE_SELECTION.md` — storage selection (PostgreSQL/Redis/Kafka/ClickHouse/pgvector)
- `roles/STACK_SELECTION.md` — global stack
- `roles/CACHE_STRATEGY.md` — caching policy

**For development tasks (@DEV):**
- `roles/DEV_EXECUTION_PASSPORT.md` — checkpoint map + pattern catalogue
- `roles/DOMAIN_STANDARDS.md` — business standards by page type
- `roles/MIGRATIONS_PLAYBOOK.md` — for migration tasks

**For quality audit tasks (@QA_ARCH):**
- `roles/ROLE_QA_ARCH.md` — audit vectors (including Vector 11 for AI)
- `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` — maturity norms
- `roles/NONFUNCTIONAL_SCORECARD.md` — current project NFR status
- `docs/artifacts/METRICS_REGISTRY.md` — metrics registry M-XX

**For invariant and data model tasks (@PRINCIPLE):**
- `roles/ROLE_PRINCIPLE.md` — Gates G1-G7, checklists A-E

**For product documentation (@SCRIBE):**
- `docs/artifacts/PRODUCT_KNOWLEDGE_BASE.md` — RAG document about the product
- `docs/product_state/PRODUCT_KNOWLEDGE_BASE.md` — current version

---

## 3. Anti-hallucination rules

- Use only references that exist in the repository.
- Do not treat `[UNDOCUMENTED]` as implemented functionality.
- For facts about system behaviour — cross-reference with code, not ARCH_*.md.
- For process decisions — rely on `roles/`, not `docs/artifacts/`.
- `[STUB]` in code or an artifact = functionality not implemented; do not describe as working.
- Any new reference in the canon — add to this file in the appropriate §2 section.

---

## 4. Layer responsibility map

| Layer | Folder | Owner | Purpose |
|-------|-------|---------|---------|
| W — working contracts | `docs/artifacts/` | @LEAD/@ARCH | Project decisions of the current wave |
| S — product state | `docs/product_state/` | @SCRIBE/@LEAD | Actual state per code |
| P — process and roles | `roles/` | @LEAD | Universal standards (not in git) |
| Public | `documentation/` | @SCRIBE | Goes to client |

---

Reference: `roles/ENGINEERING_PLAN.md` · `roles/FILE_MAP.md` · `roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md`
Version: 2026-05-22
