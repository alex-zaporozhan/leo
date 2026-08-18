# PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL.md
# Pre-development and Execution Pack protocol for @DEV
# Universal step-by-step process for any project in this system

> **Purpose:** fix **how** we bring a product from business canon to **executable** prompts with file paths, API, DB and block UI specs — **before the first line of application code**.
>
> **Reference implementation:** `docs/dev_execution/` (project "Digital Trainer", pack v3.2).
>
> **Connection:** supplements `roles/ENGINEERING_PLAN.md` (PLANNING phase), does not replace `roles/DEV_EXECUTION_PASSPORT.md` (work of @DEV **while** coding).

---

## 1. When to apply

| Situation | Protocol |
|----------|----------|
| New product / new domain | **Full** — phases 0→7 |
| Large module (orchestration, billing, new app shell) | Phases 2→7 for module + new wave in pack |
| Single screen fix / bug fix | **Not needed** — `DEV_PROMPTS_[wave].md` is enough |
| "Add a field to a form" | **Not needed** |

**@LEAD blocker:** Execution Pack **does not start** until `roles/ROLE_LEAD.md` **PRE-PLAN GATE** (7 items) has been passed and `BUSINESS_LOGIC.md` + `BUSINESS_ROUTES.md` are fixed.

---

## 2. Place in the lifecycle

```
[START] @CREATOR → BUSINESS_LOGIC + MARKET_AUDIT + BUSINESS_ROUTES
        ↓
[PLANNING]
        ├── PRE-PLAN GATE (@LEAD)
        ├── LPA — for non-trivial tasks (Law 24)
        ├── @ARCH → spine + SYSTEM_DESIGN + ADR + OpenAPI draft
        ├── @PRINCIPLE / @AI_ENGINEER — per triggers
        ├── @DESIGN / @FRONTEND / @MOTION — per UI triggers
        │
        ├── ★ EXECUTION PACK (this protocol) — phases 0→7
        │       output: docs/execution/ (+ DEV_PROMPTS index in artifacts)
        │
        └── User approves pack + wave order
        ↓
[TUNNEL] @DEV reads waves/Px → code → @QA_ARCH → @QA
```

**Rule:** `docs/artifacts/DEV_PROMPTS_*.md` — **brief wave index** (goal, gates, link to `docs/execution/waves/Px_*.md`). **Full to-do volume** — in Execution Pack, do not duplicate a third time without reason.

---

## 3. Principles (process invariants)

1. **State, not history** — pack describes the **current** solution; history is stored in git.
2. **@DEV does not design** — chooses only where the to-do explicitly says `DEV_CHOICE`; otherwise paths and contracts are fixed.
3. **Overlay + canon + wave** — cross-cutting laws are not copied into 10 waves; screens are not assembled from 4 files by eye (see §6).
4. **OpenAPI / JSON Schema before code** — contract in `docs/product_state/` does not lag behind pack.
5. **Triple DB check** — business → normalisation → RLS/security in one `DATABASE_CANON_*.md`.
6. **Pilot / Scale explicitly** — every screen and gap is tagged with a release phase; "deferred" = document + redirect/placeholder, not a hole.
7. **Pack audit** — before handoff to @DEV: criteria from §8; without 🟢 on gaps — no wave P0 start.

---

## 4. Step-by-step process (phases 0→7)

### Phase 0 — Product framework and terminology

| Step | Role | Artifact | Readiness criterion |
|-----|------|----------|---------------------|
| 0.1 | @DOMAIN_EXPERT + @BIZ | `docs/artifacts/BUSINESS_ROUTES.md` (+ project folder if present) | Money, role, data routes, J0…Jn |
| 0.2 | @BIZ / @CREATOR | `docs/artifacts/BUSINESS_LOGIC.md` | Why we are building, Pilot scope |
| 0.3 | @LEAD | `docs/execution/PRODUCT_LOGIC_MAP.md` | Modules, flows, glossary (project = org + program etc.) |

---

### Phase 1 — Architecture and engineering plan

| Step | Role | Artifact | Readiness criterion |
|-----|------|----------|---------------------|
| 1.1 | @ARCH | `docs/artifacts/SYSTEM_DESIGN_[PROJECT].md` | Load, latency, failure modes (`roles/SYSTEM_DESIGN_PROTOCOL.md`) |
| 1.2 | @ARCH | `docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md` | Stack, layers, errors, observability |
| 1.3 | @ARCH | `docs/decisions/ADR_REGISTRY.md` + `docs/decisions/ADR_*.md` | Each disputed decision — ADR with status "Accepted" |
| 1.4 | @ARCH | `docs/product_state/openapi/[product]_v1.yaml` | Tags by domains; single `ErrorResponse` |
| 1.5 | @ARCH | `docs/product_state/schemas/*.json` | JSON Schema for bodies validated outside OpenAPI |
| 1.6 | @PERF (per trigger) | Section in `PERFORMANCE_*` or spine | SLO, queues, limits — before pack |

**@PRINCIPLE triggers:** money, statuses, aggregates, concurrency → `PRINCIPLE_FINDINGS_*.md` → alignment with @ARCH before pack finalisation.

**@AI_ENGINEER triggers:** RAG, agent graph, eval → `RAG_PASSPORT`, `AGENT_GRAPH_PASSPORT`, `EVAL_PLAN`.

---

### Phase 2 — Roadmap and waves

| Step | Role | Artifact | Readiness criterion |
|-----|------|----------|---------------------|
| 2.1 | @LEAD + @ARCH | `docs/knowledge/DEVELOPMENT_ROADMAP_*.md` or `DEVELOPMENT_PLAN.md` | Phases P0…Pn, merge gates |
| 2.2 | @LEAD | `docs/execution/00_MASTER_CHAIN.md` | Invariants I1…Ix, wave order — **do not change** without ADR |
| 2.3 | @LEAD | `docs/execution/00_ROLE_LAYERING.md` | Layers A/B/C and where to write role nuances |

**Wave naming rule:** `P0_FOUNDATION`, `P1_IDENTITY_TENANT`, … — backend and frontend to-do in **one** or **paired** files (`P4b_BACKEND_*`, `P4b_FRONTEND_*`).

---

### Phase 3 — Data (backend canon)

| Step | Role | Artifact | Readiness criterion |
|-----|------|----------|---------------------|
| 3.1 | @ARCH | `docs/execution/database/DATABASE_CANON_[PROJECT].md` | ER, full table list |
| 3.2 | @ARCH | Same §Pass 1 | Business: entities, money, lifecycle |
| 3.3 | @ARCH | Same §Pass 2 | Normalisation, FK, indexes |
| 3.4 | @ARCH | Same §Pass 3 | RLS, tenant, secrets not in OLTP |
| 3.5 | @ARCH | Cross-check with OpenAPI | Each API entity has a table or explicit "external system" |

---

### Phase 4 — Screen registry and requirement compliance

| Step | Role | Artifact | Readiness criterion |
|-----|------|----------|---------------------|
| 4.1 | @DOMAIN_EXPERT + @FRONTEND | `docs/execution/SCREEN_REGISTRY_FULL.md` | Each screen: ID, route, component file, role, **wave**, Pilot/Scale |
| 4.2 | @BIZ + @LEAD | `docs/execution/COMPLIANCE_MATRIX_SCREENS.md` | Rows: requirement × screen × status 🟢🟡🔴 |
| 4.3 | @LEAD | `docs/execution/G_SCR_*` or GAPs section | Each 🟡/🔴 → ADR or Pilot/Scale boundary doc |

**Screen ID format:** `A-01` (admin), `L-01` (learner/app), `M-01` (marketing) — single registry per project.

---

### Phase 5 — Frontend canon (Layer A)

**Who writes:** @FRONTEND + @DESIGN (+ @MOTION for micro-motion / showcase).

| File (template) | Contents |
|---------------|------------|
| `frontend/00_SYSTEM_ARCHITECTURE.md` | Monorepo, apps, `src/` folders, API client, React Query keys, guards |
| `frontend/01_DESIGN_PASSPORT.md` | Tokens, StatusStripe, typography (Visual Quality Gate) |
| `frontend/02_MOTION_LANGUAGE.md` | Durations, easing, reduced-motion |
| `frontend/03_*_APP_SHELL.md` | Routes, sidebar, **page file names — canon** |
| `frontend/04_*_APP_SHELL.md` | Second application (learner/marketing) |
| `frontend/05…08_*_ENGINEERING.md` | **Block specs** by domain: block → element → API → 4 states |

**Block spec rule (as in 05–08):**

```markdown
## §[ScreenId] ComponentName (`/route`)

| Block | Elements | API | States |
|------|----------|-----|-----------|
| Toolbar | … | GET … | Loading / Empty / Error / Success |
```

**Forbidden:** "build the settings page" without a block table.

---

### Phase 6 — Backend / AI overlays (Layer B)

| File (template) | Role | Contents |
|---------------|------|------------|
| `overlays/ARCH_INVARIANTS.md` | @ARCH | RLS, ports, error codes, idempotency |
| `overlays/AI_ENGINEER_RAG_PIPELINE.md` | @AI_ENGINEER | Ingest, retrieval, tenant, empty retrieval |
| `overlays/DESIGN_FRONTEND_ADMIN.md` | @DESIGN | Cross-cutting UI gate for admin/app |

Changing an invariant → **one** overlay file; waves receive the line "see overlays/ARCH §N".

---

### Phase 7 — Waves (Layer C) and DEV_PROMPTS index

| Step | Role | Artifact | Readiness criterion |
|-----|------|----------|---------------------|
| 7.1 | @ARCH + @FRONTEND | `docs/execution/waves/Px_*.md` | Numbered B1…Bn, F1…Fn; file paths; acceptance |
| 7.2 | @ARCH | `docs/artifacts/waves/[N]/DEV_PROMPTS_WAVE_Px.md` | 1–2 pages: goal, preconditions, link to wave, Domain Checklist |
| 7.3 | @LEAD | `docs/execution/README.md` | Reading order; pack version |
| 7.4 | @LEAD | `docs/execution/INTERACTIVE_UX_ENHANCEMENTS.md` | IX-01… — P0 interactive moved to wave to-do |
| 7.5 | @LEAD | Audit §8 | `EXECUTION_PACK_AUDIT_REPORT_*.md` — 🟢 before P0 |

**Every frontend to-do line must have:** link to `SCREEN_REGISTRY` ID (`A-12`) and `frontend/0x` §.

---

## 5. Universal folder structure

Location: **`docs/execution/`** for one product at repo root
or **`docs/[project-name]/execution/`** in a multi-product repository.

```
docs/execution/                              # or docs/[project]/execution/
├── README.md                                # reading order, pack version
├── PRODUCT_LOGIC_MAP.md
├── SCREEN_REGISTRY_FULL.md
├── COMPLIANCE_MATRIX_SCREENS.md
├── INTERACTIVE_UX_ENHANCEMENTS.md           # optional, recommended
├── EXECUTION_PACK_AUDIT_CRITERIA.md         # 10 vectors — universal template
├── EXECUTION_PACK_AUDIT_REPORT_[DATE].md
├── G_SCR_*_PILOT_SCALE_BOUNDARY.md          # as gaps close
├── 00_MASTER_CHAIN.md                       # invariants + wave order
├── 00_ROLE_LAYERING.md                      # layers A/B/C
├── database/
│   └── DATABASE_CANON_[PROJECT].md
├── frontend/
│   ├── 00_SYSTEM_ARCHITECTURE.md
│   ├── 01_DESIGN_PASSPORT.md
│   ├── 02_MOTION_LANGUAGE.md
│   ├── 03_[ADMIN]_APP_SHELL.md
│   ├── 04_[LEARNER]_APP_SHELL.md
│   └── 05…NN_*_ENGINEERING.md               # by domain
├── overlays/
│   ├── ARCH_INVARIANTS.md
│   ├── AI_ENGINEER_RAG_PIPELINE.md          # if AI present
│   └── DESIGN_FRONTEND_ADMIN.md
├── waves/
│   ├── P0_FOUNDATION.md
│   ├── P1_….md
│   └── Pn_….md
└── metrics/                                 # optional
    └── CUSTOMER_METRICS_ANALYSIS.md
```

**Parallel in `docs/artifacts/` (Layer W):** spine, ADR, DEV_PROMPTS index, QA_REPORT, DESIGN_SPEC.

**Parallel in `docs/product_state/` (Layer S):** OpenAPI, JSON Schema, passports after code appears.

**Artifact layout by DOC_TOPOLOGY.md canon:**
- **ADR** (phase 1.3) → `docs/decisions/` (append-only, separate from passports), registry — `docs/decisions/ADR_REGISTRY.md`.
- **Passports/truth** (BUSINESS_LOGIC, BUSINESS_ROUTES, ARCHITECTURE_SPINE, AGENT_GRAPH/RAG_PASSPORT, MARKET_AUDIT, DEVELOPMENT_PLAN/ROADMAP, EVAL+golden) → `docs/knowledge/`.
- **Execution Pack** (this protocol, §5) → `docs/execution/` (was `docs/dev_execution/` — functional name).
- **Wave delivery** (DEV_PROMPTS index, QA/VISUAL_QA, DESIGN_SPEC) → `docs/artifacts/waves/[N]/` (wave-first).
- **OpenAPI/Schemas** → `docs/product_state/`.

Process (phases 0→7) and pack contents — unchanged; only target folders change to canonical. In a multi-product repo — `docs/[project]/` wrapper with the same topology inside (DOC_TOPOLOGY §5).

---

## 6. Three execution layers (@DEV)

| Layer | Folder | When @DEV reads | Who decides |
|------|-------|-------------------|------------|
| **A** | `frontend/00…04` + domain `05…08` | P0 once; then by link from wave | @FRONTEND / @DESIGN |
| **B** | `overlays/*` | Start of each wave | @ARCH / @AI_ENGINEER |
| **C** | `waves/Px_*.md` | Whole wave | Only `DEV_CHOICE` if marked |

**Why the hybrid is mandatory:** overlay only — @DEV assembles UI from memory; wave only — 10 copies of RLS and file name mismatches.

---

## 7. Backend in pack (no separate `backend/` folder)

Backend spec **inside**:

- `database/DATABASE_CANON_*.md` — schema and policies;
- `overlays/ARCH_INVARIANTS.md` — ports, `backend/app/…` modules (tree in SYSTEM_DESIGN or wave B1);
- `waves/Px_*.md` §Backend to-do — tables, routes, Celery, tests T1…Tn.

**For large projects:** a `docs/execution/backend/BACKEND_MODULE_MAP.md` is acceptable — one file with directory tree and layer boundaries (domain/application/infra/api).

---

## 8. Execution Pack Audit (gate before @DEV)

**Criteria file:** copy template from reference `docs/execution/EXECUTION_PACK_AUDIT_CRITERIA.md` or use universal vectors:

| Vector | Source role | Minimum 2/3 |
|--------|---------------|-------------|
| V-QA | @QA_ARCH | 4 states, EmptyState, mutations |
| V-PR | @PRINCIPLE | Statuses, idempotency |
| V-PF | @PERF | Limits, debounce, virtualize |
| V-AR | @ARCH | OpenAPI ↔ DB ↔ spine |
| V-BZ | @BIZ | COMPLIANCE_MATRIX |
| V-SC | @DOMAIN_EXPERT | SCREEN_REGISTRY completeness |
| V-IX | @DESIGN | INTERACTIVE UX P0 in waves |
| V-DB | @ARCH | 3 passes DATABASE_CANON |
| V-TST | @QA | Acceptance criteria in each wave |
| V-TR | @LEAD | Wave order, no contradictions |

**Verdict:** average score ≥ 2.2/3, **0** open 🔴 gaps without ADR or boundary doc.

**Who runs it:** @LEAD (can delegate the pass to @QA_ARCH read-only).

---

## 9. Pilot / Scale and gaps

| Status | Meaning | Action |
|--------|----------|----------|
| 🟢 | In Pilot scope, spec ready | Row in wave P0…Pk |
| 🟡 Pilot partial | UX simplified; Scale — full version | `G_SCR_*_PILOT_SCALE_BOUNDARY.md` |
| 🔴 | Outside Pilot | Explicit "not doing" in COMPLIANCE or ADR "rejected" |

**Forbidden:** leaving a screen in the registry without a phase and without a placeholder — this is an "open" gap.

---

## 10. @LEAD checklist "Execution Pack is ready"

```
□ PRE-PLAN GATE passed
□ PRODUCT_LOGIC_MAP + SCREEN_REGISTRY + COMPLIANCE_MATRIX exist
□ DATABASE_CANON — 3 passes filled
□ 00_MASTER_CHAIN — wave order and invariants
□ frontend/00 + shells (03/04) — routes = SCREEN_REGISTRY routes
□ Domain frontend/05… — blocks for all Pilot screens
□ overlays/ARCH (+ AI if needed)
□ waves/P0…Pk — each Pilot wave with B* and F* to-do
□ OpenAPI covers all endpoints from waves (no "in audit, not in YAML")
□ JSON Schema for import/export bodies if in protocol
□ DEV_PROMPTS_WAVE_* — indexes with links to waves/
□ INTERACTIVE IX P0 — rows in corresponding waves
□ EXECUTION_PACK_AUDIT_REPORT — 🟢
□ User approved order P0→…→Pn
```

Only after this: **@DEV starts P0** (`roles/DEV_EXECUTION_PASSPORT.md` §1 Pre-implementation Scan).

---

## 11. Anti-patterns

| Anti-pattern | Why it is bad | Instead |
|-------------|--------------|-------------|
| Only `DEV_PROMPTS` without pack | @DEV improvises structure | Full `docs/execution/` |
| Component names only in wave | Divergence between waves | `SCREEN_REGISTRY` + shell |
| "We'll describe the API later" | Code and UI diverge | OpenAPI in phase 1 |
| One huge `frontend.md` | Can't find screen block | Files 05…08 by domain |
| Skip audit "it's obvious" | 🔴 surface in P3 | §8 is mandatory |
| Pilot = Scale in registry | Empty screens in prod | Boundary doc + redirect |

---

## 12. Quick start for a new project

1. Copy the tree from §5 to `docs/execution/` (empty H2 headings).
2. Fill phases 0→4 (business, arch, DB, registry).
3. @FRONTEND fills `frontend/00…04`.
4. @ARCH + domain roles fill `05…08` and `overlays/`.
5. @ARCH/@LEAD slice `waves/P0…`.
6. Run §8 → fixes → user approval.
7. Create `docs/artifacts/waves/[0]/DEV_PROMPTS_WAVE_P0.md` with link to `waves/P0_FOUNDATION.md`.

---

## 13. Related system files

| File | Connection |
|------|-------|
| `roles/ENGINEERING_PLAN.md` | Session state machine |
| `roles/ROLE_LEAD.md` | PRE-PLAN GATE, role chain |
| `roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md` | Where to put ADR, spine |
| `roles/DEV_EXECUTION_PASSPORT.md` | After pack — while writing code |
| `roles/DEV_PROMPTS` → artifacts | Wave indexes |
| `roles/DOMAIN_STANDARDS.md` | Domain Checklist in DEV_PROMPTS |
| `roles/SYSTEM_DESIGN_PROTOCOL.md` | Phase 1.1 |
| `roles/FRONTEND_DESIGN_EXCELLENCE.md` | Visual Quality Gate |
| `docs/execution/README.md` | Reference pack |

---

## APPENDIX A — Execution Pack README Template

```markdown
# DEV Execution Pack — [Product Name]

**Pack version:** 1.0 · **Date:** YYYY-MM-DD
**Wave order:** P0 → P1 → … (do not change without ADR)

## How to use

1. PRODUCT_LOGIC_MAP.md
2. SCREEN_REGISTRY_FULL.md
3. COMPLIANCE_MATRIX_SCREENS.md
4. database/DATABASE_CANON_[PROJECT].md
5. 00_MASTER_CHAIN.md + 00_ROLE_LAYERING.md
6. frontend/00 … → domain 05…
7. overlays/ — start of each wave
8. waves/Px_*.md

**Rule:** to-do references screen ID (A-xx / L-xx) and § in frontend.

## Layer W artifact index

- DEV_PROMPTS: docs/artifacts/waves/[N]/DEV_PROMPTS_WAVE_*.md
- Spine: docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md
- OpenAPI: docs/product_state/openapi/
```

---

*Protocol version: 1.0 · 2026-05-27 · @LEAD · reference: ai_mentor / docs/execution/*
