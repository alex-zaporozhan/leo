# ARCH_SPINE_PROTOCOL.md
# The spine of an architectural decision: 12 vertebrae without which a decision doesn't count as made.
# The diagnosis the file cures: the system's knowledge (DOMAIN_STANDARDS, ARCHITECTURE_EXCELLENCE_PASSPORT,
# SYSTEM_DESIGN_PROTOCOL, ASYNC_WORKERS_CANON, DATA_STORE/STACK_SELECTION) is complete, but NOTHING forced
# it to be pulled together at the moment of a concrete decision — hence the "long tuning" of architecture after the fact.
# The spine = forcing the canons with a NUMBER before code + two missing sections: timeouts (§4) and the complexity ladder (§3).
# Owners: @ARCH (fills it in), @QA_ARCH (verifies via the Spine vector), @LEAD (the Law 18 gate).

> **Dogma:** an architectural decision without a number is an opinion. Every vertebra answers with a number, a reference to
> a source canon, or the words "not applicable + why". An empty vertebra = the decision is not made = code is not written.

---

## §1. WHEN THE SPINE IS MANDATORY

```
Triggers (any one): a new project · a new service/process · a new store/broker · an external
integration · a new queue/task class · a data-schema change with a migration · a change of the
complexity tier (§3) · a public API contract.
Output: docs/artifacts/ARCH_SPINE_[PROJECT|EPIC].md (template §5). Lives next to the ADR: the ADR answers
"why this way", the spine — "are all the invariants pulled together and by what are they measured".
A small task inside an already-described spine — a new one is NOT needed: a reference to the existing one.
```

---

## §2. THE TWELVE VERTEBRAE (question → answer format → source canon)

| # | Vertebra | Question | Answer format | Source of rules |
|---|----------|----------|----------------|-----------------|
| 1 | TIER | Which tier of the complexity ladder do we live on and why not higher/lower | tier 0–4 + a trigger number (§3) | §3 of this file |
| 2 | SLO CLASS | The availability/latency class of the module | class A/B/C + a p95 target, consequences (redundancy, retry budget) | `ARCHITECTURE_EXCELLENCE_PASSPORT` |
| 3 | TENANCY | The multi-tenancy model and the isolation invariant | single / tenant_id / schema + "every query is filtered" + a leak test in the T-series | `DOMAIN_STANDARDS` |
| 4 | TIMEOUTS | The latency budget and deadlines of EVERY outgoing dependency | the §4 table filled (dependency → connect/read → retry → fallback) | §4 of this file + SYSTEM_DESIGN Step 2/5 |
| 5 | IDEMPOTENCY | A map of mutating operations | endpoint/task → key → mechanism (UPSERT/unique index/Idempotency-Key) | `DOMAIN_STANDARDS` · `ASYNC_WORKERS_CANON` AW-4 |
| 6 | INTEGRITY | Data invariants at the DB level, not the application | a list of constraints (FK/unique/check/exclusion/not null) + optimistic-lock points (row version) + the epic's **INVARIANT LEDGER** | `DATA_INTEGRITY_CANON` (Law 32) · `DOMAIN_STANDARDS` · `MIGRATIONS_PLAYBOOK` |
| 7 | CONTRACTS | The API version and the evolution rule | /v1 + additive-only; breaking → a new version + a deprecation window (a date) | `ARCHITECTURE_DOCUMENTATION_STANDARD` |
| 8 | ASYNC | The background contour is lawful | a reference to `JOB_PASSPORTS_*` (all task types) + `PIPELINE_PASSPORT_*` for pipelines with an external provider/progress, or "no background tasks" | `ASYNC_WORKERS_CANON` (Law 30, PART II) |
| 9 | CAPACITY | The load is computed, not guessed | a reference to the completed Steps 1–4 of SYSTEM_DESIGN + total connections ≤ the DB limit | `SYSTEM_DESIGN_PROTOCOL` |
| 10 | FAILURE | Failure points and the blast radius | the Step 5 table filled; for every external dependency: circuit/fallback/degradation; the blast radius of one death | `SYSTEM_DESIGN_PROTOCOL` Step 5 |
| 11 | DR CLASS | What we lose in a catastrophe | RPO/RTO as numbers + a backup schedule + **the date of the last restore test** (a backup without a restore = not a backup) | the project's deploy runbook in `docs/operations/` · `MIGRATIONS_PLAYBOOK` |
| 12 | THREAT SKETCH | What here will be stolen/forged/uploaded | 5 lines of STRIDE-lite: asset → vector → control (an input for @SEC/@PENTEST, not a replacement for the audit) | `ROLE_SEC` · `PENTEST_SCENARIOS` |

Filling rule: each vertebra — 1–3 lines. A spine on one page, not a treatise: if a vertebra
requires an essay — the decision is not ripe, back to the drawing board.

**Delegation map (the spine is a table of contents — each vertebra needing depth delegates to a "sibling" canon, so @ARCH opens the right canon while filling it in):**

```
Vertebra 1  TIER ────────► the tier ladder 0–4 (this file §3)
Vertebra 3  TENANCY ─────► DATA_INTEGRITY_CANON §7 (tenant_id, composite FKs, per-tenant unique, RLS)
Vertebra 5  IDEMPOTENCY ─► DATA_INTEGRITY_CANON §4 (Idempotency-Key) + ASYNC §3 (Outbox)
Vertebra 6  INTEGRITY ───► DATA_INTEGRITY_CANON §1–§2 (the protection hierarchy, race recipes) + §9 INVARIANT LEDGER
Vertebra 8  ASYNC ───────► ASYNC_WORKERS_CANON: JOB_PASSPORT (§4) + PIPELINE_PASSPORT (PART II §13)
                            └─ the @DEV-side mirror: ASYNC_AWAIT_REFLEX (§1 greps) — the passport numbers you
                               write here are the ones @DEV greps for before shipping and @QA_ARCH greps for after.
                               A passport number that cannot be grepped is a number nobody will check.
Vertebra 9  CAPACITY ────► SYSTEM_DESIGN_PROTOCOL Steps 1–5 (load/latency/bottleneck)
Vertebra 10 FAILURE ─────► ASYNC §2 (bulkhead/backpressure) + §10 lease/recovery window
Vertebra 11 DR CLASS ────► MIGRATIONS_PLAYBOOK + the project's deploy runbook in docs/operations/ (a restore test with a date)
Vertebra 12 THREATS ─────► ROLE_SEC / ROLE_PENTEST (STRIDE, T-H5 isolation)
```

Completeness rule: if vertebra 6 or 8 is answered "there are constraints" / "there are jobs" WITHOUT a reference to a
concrete INVARIANT_LEDGER / JOB_PASSPORTS / PIPELINE_PASSPORT artifact — the vertebra is not closed
(@QA_ARCH Spine vector: a reference to an artifact is mandatory, not general words). This way the spine keeps all
the architectural laws (30/31/32) pulled together in one place, while the canons give the executable depth.

---

## §3. THE COMPLEXITY LADDER — rationality as a number (anti-overengineering)

```
TIER 0  A modular monolith: one deploy, boundaries — by modules/packages inside. THE DEFAULT of any start.
TIER 1  + Dedicated contour processes: queue workers, the scheduler, the SSG public site (Laws 29/30).
        This is NOT microservices — it is isolation of execution profiles. The typical tier of our products.
TIER 2  + A separate service for one domain (billing, notifications) with ITS OWN store.
TIER 3  + An event bus (Kafka/Streams) between services, CQRS fragments, read replicas.
TIER 4  Sharding, multi-region, cell-based. (Not our scale — fixed for the honesty of the scale.)
```

**Rise triggers (need AT LEAST ONE, as a number in vertebra 1):**
- deploy conflicts: a module's releases block others ≥ 2 times/week over a month;
- a scaling profile: the module needs ≥ 5× the replicas of the rest OR a different hardware class (GPU/memory);
- SLO isolation: a class-A module suffers from class-C neighbours (by metrics, not feelings);
- organisation: > 1 independent team with a self-directed release cycle;
- data: the volume/access pattern requires a different store (per `DATA_STORE_SELECTION`), not "Postgres is cramped in general".

**Ladder laws:** a rise of only ONE tier at a time · a jump over a tier is forbidden ·
a "descent" (simplification, merging services) is a legitimate architectural decision and is documented by the same spine ·
Kafka/microservices/GraphQL federation "for growth" without a trigger number = 🔴 @QA_ARCH ·
the cost of a tier is named aloud: +a service = +a deploy, +monitoring, +network failures, +contract versions.

---

## §4. TIMEOUTS AND DEADLINES — the safety of the synchronous path (a hole this file closes)

> The async contour is protected by Law 30. The synchronous path fails differently: one external call without
> a timeout → hung connections → an exhausted pool → EVERYTHING is down. The rule: **a call without a timeout does not exist**.

### 4.1 Defaults (overridden in the spine only with a justification)

| Dependency | connect | read/statement | Retry (sync) |
|------------|---------|----------------|--------------|
| Internal HTTP (own service) | 1s | 5s | ≤1, idempotent only, with jitter |
| External HTTP (providers) | 2s | 10s (payment providers — per the provider SLA) | ≤1 + circuit breaker |
| PostgreSQL | 1s (pool acquire 3s) | statement_timeout 10s (reports — a separate pool 30–60s) | 0 (the layer above retries) |

**The database has a clock too** (`roles/DATABASE_RUNTIME_CANON.md` §1) — these belong in this table, as numbers, because an abandoned transaction holds its locks **forever** and nothing else in the system has an opinion about that:

| Session guard rail | Default | Why it exists |
|--------------------|---------|---------------|
| `lock_timeout` | 10s (maintenance/migrations: 1–3s) | nobody waits for someone else's lock forever — Cancel must not queue behind a corpse |
| `idle_in_transaction_session_timeout` | 5min | **Postgres kills the abandoned transaction and frees its locks by itself** — the one setting that ends the outage without a human |
| `statement_timeout` | 120s (OLTP 5–15s; reports on a separate pool) | a query has a ceiling |
| **Connection budget** | `API×pool + workers×pool + beat + migrations ≤ max_connections × 0.8` | summed across **all** consumers, not per service (this is vertebra 9's number, and it is nearly always wrong the first time) |
| Redis | 300ms | 500ms | 1 |
| S3/files | 2s | 30s | ≤1 |

### 4.2 Path rules

```
T1. THE DEADLINE PROPAGATES: an incoming request has a budget (e.g. 10s at the gateway); the REMAINDER is passed
    down (X-Request-Deadline); a call whose timeout > the remainder does not start — immediate degradation.
T2. THE RETRY BUDGET of the synchronous path: in total ≤ 1 retry per user request. A cascade of retries
    on every layer (gateway×API×DB client) = self-DDoS — forbidden; retries live in ONE layer.
T3. POOLS: an acquire timeout is mandatory (waiting for a connection is also a call); pool overflow = a fast
    503 rejection + a metric, not an infinite waiting queue.
T4. SLOW → ASYNC: an operation that doesn't fit the budget (SYSTEM_DESIGN Step 2: > 10% of the latency budget)
    is not "waited on longer" but goes to the queue (Law 30) with a 202 response + a status endpoint.
T5. GREP INVARIANT (@QA_ARCH): httpx/fetch/axios/requests without a timeout · create_engine without
    pool_timeout/statement_timeout · a Redis client without socket_timeout → 🔴 by the code, no discussion.
```

---

## §5. ARTIFACT TEMPLATE `docs/artifacts/ARCH_SPINE_[PROJECT].md`

```markdown
# ARCH SPINE — [project/epic] · [date] · ADR references: [...]
1. TIER: 1 (monolith + workers). No rise triggers: deploy conflicts 0, profiles even.
2. SLO: API class B (p95 300ms, 99.5%); reports class C. Consequences: no geo-redundancy; retry budget T2.
3. TENANCY: tenant_id row-level; invariant: all queries via a scoped repository; leak test T-TEN in the series.
4. TIMEOUTS: the §4 table accepted; exception: PDF render read 30s (justification: the provider), a circuit exists.
5. IDEMPOTENCY: POST /bookings → Idempotency-Key + unique (tenant, slot); jobs — JOB_PASSPORTS.
6. INTEGRITY: INVARIANT_LEDGER — the slot EXCLUSION, stock an atomic guard, payment UNIQUE(order); FK everywhere; unique(tenant,phone); check(status ∈ …); optimistic lock: bookings.version; money as integer minor units (Law 32).
7. CONTRACTS: /api/v1, additive-only; field deprecation — 60 days with a Sunset header.
8. ASYNC: JOB_PASSPORTS_[project].md — 6 types; the pipeline rag.ingest — PIPELINE_PASSPORT (lease/limiter/retry-owner as numbers).
9. CAPACITY: SYSTEM_DESIGN Steps 1–4 done [reference]; connections: 8×10 + 2×8 + 10 = 106 < 200 (max 300).
10. FAILURE: the Step 5 table [reference]; the SMS provider dies → the queue accumulates, the UI shows an honest status; radius: notifications only.
11. DR: RPO 24h (nightly dump + WAL), RTO 4h; restore test: 2026-07-01 ✅ (next — a quarter).
12. THREATS: client PII → export → permissions+audit; slots → overwrite by a competitor → optimistic lock; ...
```

---

## §6. GATES AND OWNERS

@ARCH fills the spine together with the ADR (Step 0 of the role) · @QA_ARCH — the **Spine** vector: all 12 vertebrae
answered with a number/reference, the T5 grep invariants and the canon anti-patterns are clean, the tier trigger is a number ·
@LEAD — the Law 18 gate: an epic with a §1 trigger does not go to @DEV without the spine · @PENTEST receives vertebrae
3/11/12 as a target map (the tenant-leak test, "the backup died", the STRIDE lines) ·
Spine revision: on every tier rise and once a quarter for a live product (together with SEO/metrics).

---

Reference: `roles/ROLE_ARCH.md` · `roles/SYSTEM_DESIGN_PROTOCOL.md` · `roles/DATABASE_RUNTIME_CANON.md` (vertebra 4: the DB's own clock) · `roles/ASYNC_AWAIT_REFLEX.md` (the @DEV/@QA_ARCH grep mirror of vertebra 8) · `roles/DATA_INTEGRITY_CANON.md` · `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` · `roles/DOMAIN_STANDARDS.md` · `roles/ASYNC_WORKERS_CANON.md` · `roles/DATA_STORE_SELECTION.md` · `roles/MIGRATIONS_PLAYBOOK.md` · `roles/DOCKER_INFRA_PASSPORT.md` · `roles/ROLE_QA_ARCH.md` · `roles/LEAD_PRODUCT_GATE_PROTOCOL.md`
Version: 2.0 (system v6.25) | 2026-07-09
