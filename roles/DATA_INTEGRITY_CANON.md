# DATA_INTEGRITY_CANON.md
# The canon of data integrity under concurrency: business invariants that cannot be bypassed.
# The sister of ASYNC_WORKERS_CANON: there — the discipline of WORK, here — the discipline of DATA. Together they close the class
# "the system is alive but the data lies". Owners: @ARCH (the Invariant Ledger in the ADR), @DEV (execution),
# @QA_ARCH (the Data-Race vector), @PENTEST (T-H race and isolation tests).

> **The canon's dogma:** an `if` check in application code does NOT protect an invariant — it illustrates it.
> Between `if free:` and `insert` there is always a gap, and under concurrency two drive into it.
> An invariant is protected only if it cannot be violated IN PRINCIPLE: by the DB schema or a lock/version.

---

## §0. THE CANONICAL INCIDENTS OF THE CLASS (our niches, typical failures)

| Incident | Mechanics | Rule |
|----------|-----------|------|
| Double-booking a slot (schedule) | two clients passed `if slot_free` simultaneously | §2: exclusion/unique on the slot |
| Overselling (marketplace) | 50 buyers read qty=5 before the first decrement | §2: an atomic decrement with a guard |
| A duplicate payment (finance) | a network retry repeated POST /pay | §4: Idempotency-Key |
| A lost update (CRM) | two managers saved the card — the second overwrote the first | §2: optimistic version + 409 |
| Another tenant's data (SaaS) | a query without a tenant filter / IDOR by id | §7: tenancy at the schema level |

---

## §1. THE INVARIANT-PROTECTION HIERARCHY (the main principle)

```
LEVEL 1 — THE DB SCHEMA (cannot be bypassed by anyone, ever):
  UNIQUE / PARTIAL UNIQUE / CHECK / FK / EXCLUSION constraint / NOT NULL.
  The default for any invariant expressible declaratively.

LEVEL 2 — A LOCK OR A VERSION (cannot be bypassed within a transaction):
  SELECT ... FOR UPDATE (pessimistic, short critical sections) ·
  optimistic version (UPDATE ... WHERE id=? AND version=? → 0 rows = 409 Conflict) ·
  a pg advisory lock (cross-table/cross-process invariants) ·
  an atomic update with a guard (UPDATE ... SET x=x-1 WHERE x>0).

LEVEL 3 — A CHECK IN CODE: only UX (an early "slot taken" hint), NEVER protection.
```

**Selection rule:** expressible as a constraint → level 1. Not expressible → level 2, and level 1 is added
as a last line where possible (unique catches what the lock missed due to a bug). Level 3 without 1/2 = 🔴.
**Level 1–2 errors are a protocol, not an exception:** a unique/exclusion violation and a `version mismatch`
are mapped to a meaningful response (409/422 with a reason code), not to a 500.

---

## §2. RACE AND RECIPE CATALOG (copy-paste)

| Race | Recipe (Postgres) |
|------|-------------------|
| Double-booking an interval | `EXCLUDE USING gist (resource_id WITH =, tstzrange(start_at,end_at) WITH &&) WHERE (status <> 'cancelled')` — the DB itself forbids overlap; slots are the half-interval `[start,end)` |
| Double-booking a discrete slot | `UNIQUE (resource_id, slot_at)` + INSERT; a conflict → 409 "taken" |
| Overselling stock/a limit | `UPDATE stock SET qty = qty - :n WHERE id=:id AND qty >= :n` → rowcount 0 = rejection. NEVER read-modify-write |
| A lost update | a `version int` column; `UPDATE ... SET ..., version=version+1 WHERE id=? AND version=?` → 0 rows = 409 + a fresh version to the client |
| "Exactly one active …" (a plan, a shift, a default card) | `CREATE UNIQUE INDEX ... ON t(owner_id) WHERE status='active'` (partial unique) |
| A double form submit | §4 Idempotency-Key; plus `UNIQUE(client_request_id)` on the entity |
| A counter/aggregate (a balance, stock) | an atomic `UPDATE ... SET bal = bal + :d` in the same transaction as the posting row; a periodic reconciliation job Σpostings = the balance (an alert on a divergence) |
| A per-tenant document number | a counters table + `UPDATE counters SET n=n+1 ... RETURNING n` (FOR UPDATE inside), NOT a global sequence and NOT max()+1 |
| An invariant across services/steps | Saga + Outbox (`ASYNC_WORKERS_CANON` §3): consistency via a state machine, not via a distributed transaction |
| A queue on Postgres | `SELECT ... FOR UPDATE SKIP LOCKED` (AP-9 of the async canon) |

---

## §3. TRANSACTIONAL DISCIPLINE

```
□ One business operation = one SHORT transaction. Open → change → close; on the scale of milliseconds.
□ Inside a transaction the following are FORBIDDEN: HTTP calls, enqueue, sleep, reading files (AP-14 of the async canon).
  An outgoing event — via Outbox in the same transaction; enqueue — after commit.
□ Isolation: READ COMMITTED by default + the explicit mechanisms of §1-2. SERIALIZABLE — pointwise for
  inexpressible invariants, mandatorily with a retry on 40001/40P01 (this is routine, not an error).
□ Deadlock prevention: a single resource-acquisition order across the system (documented in the ADR);
  batch updates — sort the keys; a retry on deadlock once with jitter, then — a rejection.
□ Reports/exports — NOT in an OLTP transaction: a read-only replica or a cursor without long locks.
```

---

## §4. IDEMPOTENCY OF THE API BOUNDARY (the pair to workers' AW-4)

- Every mutating POST with an external effect (a payment, a booking, a send) accepts an **`Idempotency-Key`**
  (a client UUID). The table `idempotency(key PK, request_hash, response, status, expires_at)`:
  a repeat with the same key → the saved response; the same key with a DIFFERENT body → 422.
- The key is written in the same transaction as the effect (unique = level 1). TTL 24–72h, cleanup by a job.
- **Incoming webhooks** (payment providers, telephony): the signature is verified + a `UNIQUE(provider, event_id)` inbox —
  a duplicate from the provider = a no-op 200. Processing — as a task via the queue (not in the handler), see the async canon.
- Retries of our OUTGOING calls always carry the provider's idempotency key, if it supports one.

---

## §5. MONEY (the finance module — no exceptions)

```
□ Storage: INTEGER minor units (kopecks/cents) BIGINT — or NUMERIC(19,4) for rates/tariffs.
  float/double/Number for money — forbidden on all layers, including JS (there — integers + a formatter).
□ Currency — a column next to every amount; operations only in one currency, conversion — an explicit row with a rate and a date.
□ Rounding: one method (half-even) in ONE place (the domain module), at the last step; in between — no rounding.
□ The price is fixed in the deal as a copy (price_at_order) — the price list changes, the history does not.
□ Totals (total) = computable from the rows; storage is allowed with a reconciliation-job invariant Σrows = total (§2).
□ Postings are append-only (a reversal as a new record, not an UPDATE of the amount after the fact); audit §8.
```

---

## §6. TIME (the schedule module — no exceptions)

```
□ Storage: timestamptz (UTC) always; naive datetime is forbidden. "A date without a time" — DATE, deliberately.
□ Timezone — an attribute of the BRANCH/resource (not of the user globally); conversion at the edges (input/output).
□ Intervals — the half-interval [start, end): adjoining slots do not overlap; the overlap check — §2.
□ The report "business day" = the branch's local day, not the UTC day (compute the day boundaries in the branch TZ).
□ Recurring events: a rule (RRULE) is stored + windowed materialisation; DST transitions are tested
  (the 02:30 slot on the switch night); now() — server only, client time does not participate in logic.
```

---

## §7. TENANT ISOLATION (multi-tenant SaaS — our typical case)

```
□ tenant_id NOT NULL in every business table; a PK is allowed (id), but all children's FKs are COMPOSITE:
  FOREIGN KEY (tenant_id, parent_id) REFERENCES parent(tenant_id, id) — a child physically cannot
  reference another tenant.
□ All uniqueness — per-tenant: UNIQUE(tenant_id, email), etc.
□ Filtering — at the repository/ORM-scope layer: the tenant is injected from the auth context automatically,
  a "raw" query without a tenant filter does not pass review. RLS (row level security) — as a safety net
  on sensitive tables: SET app.tenant_id per-connection + a policy.
□ Background tasks carry tenant_id in the payload (AW-5) and work in its scope; cross-tenant jobs —
  an explicit list, one tenant per iteration.
□ Files/exports — paths with a tenant prefix; signed URLs with an ownership check.
□ An isolation test is mandatory (T-H5): tenant A's token across all endpoints with tenant B's entity ids → 404/403
  everywhere, including lists, files, websockets. The IDOR matrix — the @PENTEST zone.
```

---

## §8. DELETION, LIFECYCLE, AUDIT

- Business entities — **soft delete** (`deleted_at`) by default + a partial unique `WHERE deleted_at IS NULL`
  + a default query scope (the deleted does not "surface" in lists and counts).
- Hard delete — only a policy: retention / the right to be forgotten; cascades are described in the ADR explicitly.
  FKs on business relations — `RESTRICT` by default; `CASCADE` — a conscious decision as a line in the ledger.
- Audit of critical tables (money, bookings, access): an append-only journal (who/when/what/old→new),
  written in the same transaction; the audit is not edited and not deleted outside retention.

---

## §9. INVARIANT LEDGER — the epic's invariant map (an artifact)

Every epic that writes data contains a ledger table in the ADR (@ARCH approves before code):

```markdown
| Invariant | Protection level | Where in schema/code | Error out | Race test |
|-----------|------------------|----------------------|-----------|-----------|
| A resource slot without overlaps | 1: EXCLUSION | migrations/012_booking.sql | 409 SLOT_TAKEN | T-H1 |
| Stock ≥ 0 | 2: an atomic UPDATE guard (+CHECK qty>=0) | OrderService.reserve | 409 OUT_OF_STOCK | T-H2 |
| A payment per order once | 1: UNIQUE(order_id) + a §4 key | payments schema | 200 (idem replay) | T-H3 |
```

Rules: an invariant without a ledger line = unprotected (🔴 @QA_ARCH); @QA_ARCH reconciles ledger ↔ migrations
(the constraint actually exists); @PENTEST executes the "Race test" column.

---

## §10. RACE AND ISOLATION TESTS — the T-H series (acceptance of an epic that writes data)

```
T-H1 DOUBLE-BOOKING: 100 concurrent requests for one slot → exactly 1 success, 99×409, the DB without duplicates.
T-H2 OVERSELL: qty=5, 50 concurrent purchases → exactly 5 successes, qty=0, not −45.
T-H3 IDEMPOTENCY: one Idempotency-Key ×10 in parallel → 1 effect, 10 identical responses.
T-H4 LOST UPDATE: two clients with version=7 → the first 200 (v8), the second 409 with a fresh version.
T-H5 ISOLATION: the matrix "token A × resources B" across all endpoints → 0 leaks (403/404), lists are clean.
T-H6 DST/DAY BOUNDARY: a booking on the clock-change night and a "for the day" report at the TZ boundary → no shifts/duplicates.
T-H7 RECONCILIATION: after T-H1–H4 a reconciliation-job invariant (Σpostings=balance, Σrows=total) — 0 divergences.
```
Instrumentally: parallelism — k6/pgbench/a script with a barrier; the criterion is always a NUMBER, not "seems OK".

---

## §11. ANTI-PATTERNS (🔴 on @QA_ARCH review, the Data-Race vector)

check-then-act without level 1/2 (`if free → insert`, `if qty>0 → save(qty-1)`) · float/Number for money ·
naive datetime / logic on client time · uniqueness "by a check in code" without an index ·
a global max()+1 or a sequence as a per-tenant document number · CASCADE on business relations by default ·
a query in multi-tenant without a tenant scope · HTTP/enqueue inside a transaction · SERIALIZABLE without a retry on 40001 ·
total stored "on faith" without a reconciliation job · an UPDATE of a monetary posting instead of a reversal · deletion without
a soft policy and a default scope · an Idempotency-Key accepted but not in the transaction with the effect.

---

Reference: `roles/ASYNC_WORKERS_CANON.md` (Saga/Outbox, AW-4/5, AP-14) · `roles/SYSTEM_DESIGN_PROTOCOL.md` (sizing/isolation under load) · `roles/MIGRATIONS_PLAYBOOK.md` (constraints on live data) · `roles/DATA_STORE_SELECTION.md` · `roles/ROLE_ARCH.md` (the ledger in the ADR) · `roles/ROLE_QA_ARCH.md` (the Data-Race vector) · `roles/PENTEST_SCENARIOS.md` (T-H)
Version: 1.0 (system v6.24) | 2026-07-07
