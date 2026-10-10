# DATABASE_RUNTIME_CANON.md
# The database as a LIVE RESOURCE: time, locks, connections, a pulse. The third sister canon.
# ASYNC_WORKERS_CANON = the discipline of WORK (workers have time).
# DATA_INTEGRITY_CANON = the discipline of DATA (the data is correct).
# THIS FILE           = the discipline of the DB RUNTIME (the database has time, and it can die of a corpse).
# Owners: @ARCH (the numbers, before code), @DEV (the recipes), @QA_ARCH (the grep vector), @OPS (alerts + runbook),
# @PERF (query cost — references only, does not duplicate).

> **The canon's dogma:** **the database has time too.**
> A connection, a transaction and a lock are resources that **somebody is holding**. For each one there must
> always be an answer to three questions: **who holds it · for how long · and what happens if they die.**
> Every other layer of this system was given a deadline — the HTTP call, the worker, the lease, the pipeline stage.
> The database was the last layer with no clock. That is where the system finally fell over.

---

## §0. THE CANONICAL INCIDENT — "the queue behind a corpse" (a real case)

```
A worker hung mid-job WITHOUT COMMITTING its transaction. It did not crash; it did not release anything.
In Postgres, an abandoned transaction holds its row locks INDEFINITELY. Nothing in the system had an opinion
about that. Then:

1. CANCEL ("stop the job") issues an UPDATE on those same rows
      → it queues behind the dead lock → the spinner spins forever.
      The user literally cannot cancel the task, because the DATABASE IS WAITING FOR A CORPSE.
2. RECLAIM (which exists precisely to recover hung jobs) ran SELECT ... without SKIP LOCKED
      → it queued behind the same lock → the recovery mechanism itself hung.
3. DISPATCH and RECLAIM lived on ONE maintenance worker with concurrency=1
      → one frozen maintenance task froze EVERYTHING: new admissions, recovery, cancellation.

One hung worker → the whole system stands still, including the button meant to save it.
```

**Five root causes → five laws:**

| Cause | Law |
|-------|-----|
| An abandoned transaction holds locks forever | **DB-1:** the session has guard rails — as numbers (§1) |
| Cancel waited on a lock with no timeout | **DB-2:** nobody waits for someone else's lock forever (§1, §3) |
| Reclaim polled without `SKIP LOCKED` | **DB-3:** any queue-like read uses `SKIP LOCKED` (§3) |
| Maintenance shared a worker with dispatch | **DB-4:** the recovery path is isolated from the work path (§5) |
| Nobody could see who held the lock | **DB-5:** the DB has a pulse and a runbook (§6, §7) |

---

## §1. DB-1 — THE DATABASE HAS A CLOCK: guard rails as numbers

These are **per-session settings**, applied by the app engine on connect, configured via env. Not optional.
Without them, one hung process can hold a lock until someone notices — which, in practice, means until a human does.

```sql
-- applied on every application connection (the values below are defaults; the passport may override with a reason)
SET lock_timeout                        = '10s';   -- nobody waits for someone else's lock forever (DB-2)
SET idle_in_transaction_session_timeout = '5min';  -- Postgres KILLS an abandoned transaction and frees its locks
SET statement_timeout                   = '120s';  -- a query has a ceiling
```

**`idle_in_transaction_session_timeout` is the one that saves you.** It is the only mechanism that turns a corpse
back into a corpse instead of a lock-holder. Set it, and the incident above ends by itself in 5 minutes instead
of ending when a human wakes up.

**Profiles by path** — one size does not fit all; declare them in the passport:

| Path | `lock_timeout` | `statement_timeout` | `idle_in_transaction` | Note |
|------|----------------|---------------------|-----------------------|------|
| **OLTP (user request)** | 3–10s | 5–15s | 1–5min | the user is waiting; fail fast and say so |
| **Background job / worker** | 10s | 60–180s | 5min | may work longer, but never forever |
| **Maintenance (reclaim, dispatch, cron)** | **1–3s** | 30s | 1min | it must NEVER be the thing that hangs (§5) |
| **Reports / analytics** | 5s | 60s (a separate pool) | 5min | a separate pool so it cannot starve OLTP |
| **Migrations** | **3s** + retry | long, explicit | — | a migration that waits on a lock **blocks the whole deploy**; fail fast and retry |

**The migration rule (a deploy-killer):** `ALTER TABLE` needs an ACCESS EXCLUSIVE lock. If a long query is running,
the migration queues — and every subsequent query queues behind **it**. A migration without a short `lock_timeout`
can take the site down while "just adding a column". Always: `SET lock_timeout = '3s'` + retry with backoff.

**Env contract:** `DB_LOCK_TIMEOUT_MS` · `DB_IDLE_IN_TRANSACTION_TIMEOUT_MS` · `DB_STATEMENT_TIMEOUT_MS`
(+ per-profile overrides). Defaults ON. A code path that opens a session bypassing these = 🔴.

---

## §2. SESSION AND CONNECTION LIFECYCLE

```
□ EVERY SESSION HAS AN OWNER AND A SCOPE: request-scoped (opened and closed inside one HTTP request) or
  task-scoped (inside one job). A session held across a user's "thinking time" or across an external call
  does not exist. Long-lived sessions are how pools die.

□ THE CONNECTION BUDGET IS COUNTED ACROSS **ALL** CONSUMERS, not per service:
      API replicas × pool + worker replicas × pool + beat + migrations + admin tools  ≤  max_connections × 0.8
  This number goes in the ARCH_SPINE (vertebra 9, capacity). A pool sized per-service and never summed is the
  most common way to discover max_connections in production, at the worst possible moment.

□ ACQUIRE TIMEOUT IS MANDATORY (pool_timeout 3–5s). Waiting for a connection IS a call, and every call has a
  deadline (ARCH_SPINE T-rules). Pool exhaustion → a fast 503 + a metric — NEVER an unbounded waiting queue.

□ PgBouncer at > ~100 real connections. Mode: transaction pooling — and then session-level SET is unavailable,
  so the guard rails of §1 move into the connection string options / server_settings. Verify this. A team that
  adds PgBouncer and silently loses its lock_timeout has re-armed the incident.

□ LEAK DETECTION: a connection checked out longer than N seconds → a log line with the stack. Pool saturation
  is a metric with an alert (§6). A leak is not found by reading code; it is found by watching the pool.
```

---

## §3. LOCK DISCIPLINE

```
□ SKIP LOCKED IN ANY QUEUE-LIKE READ (DB-3). Reclaim, dispatch, outbox relay, any "grab the next N rows":
      SELECT ... FROM jobs WHERE ... FOR UPDATE SKIP LOCKED LIMIT n
  Without SKIP LOCKED, the recovery mechanism queues behind exactly the rows it is trying to recover.
  **The recovery path must never be able to block on the thing it is recovering.** This is the whole lesson of §0.

□ ONE RESOURCE-ACQUISITION ORDER, documented in the ADR. Multi-row updates sort their keys.
  Deadlocks are then rare; when they happen: retry once with jitter (40P01), then reject. A deadlock retry is
  routine, not an error — but an UNBOUNDED deadlock retry is a livelock.

□ NEVER WAIT FOR A LOCK WITHOUT A TIMEOUT (DB-2 — §1 already enforces it globally; do not override it locally
  with `SET LOCAL lock_timeout = 0` "just for this one").

□ THE CORPSE-LOCK PATTERN (know it by name):
      a process dies or hangs with an open transaction → its locks live on → every writer to those rows queues →
      the queue includes the CANCEL and the RECLAIM meant to fix it → the system is alive and completely stuck.
  Defences, in order: idle_in_transaction_session_timeout (§1) · lock_timeout on the waiters (§1) ·
  SKIP LOCKED on recovery (§3) · maintenance isolation (§5) · the alert on the longest transaction (§6).

□ ADVISORY LOCKS for cross-table/cross-process invariants (a singleton scheduler, a migration guard).
  Always with a timeout; always released in a `finally`. An advisory lock leaked by a crashed process is
  released when the session ends — which is why §2's session scoping matters here too.
```

---

## §4. THE SHAPE OF A TRANSACTION (what happens when the process dies inside one)

```
□ SHORT. Milliseconds. Open → change → close.
□ NO HTTP, NO ENQUEUE, NO SLEEP, NO FILE I/O INSIDE (ASYNC_WORKERS_CANON AP-14, DATA_INTEGRITY §3).
  An external call inside a transaction means the lock's lifetime is now controlled by SOMEONE ELSE'S SERVER.
  This is precisely how §0 begins.
□ THE DEATH TEST — ask it of every transaction you write:
      "If this process is SIGKILLed right here, what is held, and for how long?"
  If the answer is "row locks, until someone notices" — the transaction is wrong, or §1 is not configured.
  With §1 configured, the answer is always: "at most idle_in_transaction_session_timeout."
□ SERIALIZABLE only where the invariant is inexpressible otherwise — and always with a retry on 40001.
□ A transaction never spans a user's decision. "Open a transaction, show a form, wait for Save" is not a design.
```

---

## §5. DB-4 — THE RECOVERY PATH IS ISOLATED

```
□ MAINTENANCE (reclaim, stale-detection, dispatch, cron) RUNS ON ITS OWN WORKER / QUEUE — never sharing a
  process with the work it supervises. In §0, dispatch and reclaim shared one worker at concurrency=1: one
  frozen maintenance task froze admissions, recovery AND cancellation simultaneously.
  This is the SUPERVISION plane of ASYNC_WORKERS_CANON §1, expressed in the database's terms.

□ EVERY BACKGROUND TASK HAS A TIME LIMIT — **including maintenance.** Especially maintenance.
      soft_time_limit = <expected × 3>   ·   time_limit = soft + grace
  A maintenance task with no time limit is a single point of failure with root access to your uptime.

□ RECLAIM'S PERIOD ≤ TTL/2 (ASYNC_WORKERS_CANON AW-11) and its queries use SKIP LOCKED (§3) and a SHORT
  lock_timeout (§1). Recovery must be the fastest, most timid thing in the system: it never waits, it never
  blocks, it retries next tick.

□ THE CANCEL PATH IS SACRED. Cancelling must not require taking a lock that a hung job may hold. Prefer a
  control-plane flag (a separate row/key the executor polls — AW-2) over an UPDATE of the job row itself.
  **If your Cancel button can queue behind the thing it is cancelling, you do not have a Cancel button.**
```

---

## §6. DB-5 — THE PULSE (metrics and alerts; without these you are blind)

| Metric | Alert |
|--------|-------|
| `pool_in_use / pool_size` | > 80% for 2 min → 🟡 · 100% (queueing) → 🔴 |
| **longest transaction age** | > `idle_in_transaction_session_timeout / 2` → 🔴 **(this is the corpse alarm)** |
| **longest lock wait** | > `lock_timeout / 2` → 🔴 |
| `idle_in_transaction` sessions | count > 0 sustained → 🔴 |
| deadlocks / min | > 0 sustained → 🟡 (the acquisition order is wrong — §3) |
| slow queries (> statement_timeout / 2) | trend → @PERF |
| connections total vs `max_connections` | > 80% → 🔴 capacity (the §2 budget was wrong) |

The one alert that would have caught §0 on day one: **"a transaction has been open for longer than N."**
It costs one query and saves a production outage.

---

## §7. OPERATIONAL SURGERY (the runbook — @OPS owns it, @AUDITOR uses it)

**"Who holds the lock?" — the single most valuable query in an incident. Keep it in the runbook, not in your head.**

```sql
SELECT
  blocked.pid          AS blocked_pid,
  blocked.query        AS blocked_query,
  blocking.pid         AS blocking_pid,
  blocking.query       AS blocking_query,
  blocking.state       AS blocking_state,           -- 'idle in transaction' = A CORPSE
  now() - blocking.xact_start AS blocking_tx_age,   -- how long it has been holding
  now() - blocked.query_start AS blocked_wait
FROM pg_stat_activity blocked
JOIN LATERAL unnest(pg_blocking_pids(blocked.pid)) AS b(pid) ON TRUE
JOIN pg_stat_activity blocking ON blocking.pid = b.pid
WHERE cardinality(pg_blocking_pids(blocked.pid)) > 0
ORDER BY blocking_tx_age DESC;
```

```
KILLING, in order of violence:
  pg_cancel_backend(pid)     — cancels the QUERY. Try this first. The transaction stays open.
  pg_terminate_backend(pid)  — kills the SESSION. The transaction is rolled back, the locks are freed.
                               This is what you use on 'idle in transaction' corpses.

WHAT NOT TO DO:
  ✗ restart Postgres to clear a lock (you have a scalpel; do not use a hammer)
  ✗ kill blindly without reading the query — you may be killing the fix, not the problem
  ✗ "clear the locks and move on" — a corpse means §1 is not configured. Fix the cause, in code, today.
```

---

## §8. TESTS — the T-D series (part of acceptance for any epic with background work)

```
T-D1  KILL MID-TRANSACTION: SIGKILL a worker holding row locks with an open transaction
      → the locks are released within idle_in_transaction_session_timeout (measured, a number). No human involved.

T-D2  CANCEL BEHIND A CORPSE (the §0 regression): a hung job holds a lock; issue Cancel
      → Cancel RESPONDS within lock_timeout with an honest error — it does NOT hang forever. Ideally it does not
        take that lock at all (§5, control-plane flag).

T-D3  RECLAIM DOES NOT BLOCK: rows are locked by a live worker; run reclaim
      → reclaim SKIPS them (SKIP LOCKED) and completes. It never waits.

T-D4  POOL EXHAUSTION: saturate the pool
      → new requests get a fast 503 within pool_timeout. No unbounded queue, no hang.

T-D5  DEADLOCK: two transactions in opposite lock order
      → one victim, one retry, both complete, data intact.

T-D6  MAINTENANCE ISOLATION: freeze one maintenance task
      → dispatch, admissions and cancellation all keep working (they are on another worker/queue).

T-D7  MIGRATION UNDER LOAD: run a migration while a long query holds the table
      → the migration fails fast on lock_timeout and retries; it does NOT queue and take the app down.
```

---

## §9. ANTI-PATTERNS (🔴 at @QA_ARCH review)

```
AP-DB-1  No lock_timeout / idle_in_transaction_session_timeout / statement_timeout in the engine config
AP-DB-2  A queue-like SELECT (reclaim, dispatch, outbox) without SKIP LOCKED
AP-DB-3  An HTTP call, an enqueue, or a sleep inside an open transaction
AP-DB-4  A background/maintenance task without soft_time_limit / time_limit
AP-DB-5  Maintenance (reclaim/dispatch/cron) sharing a worker with the work it supervises
AP-DB-6  A pool with no acquire timeout; overflow that queues instead of rejecting
AP-DB-7  The connection budget computed per service and never summed across ALL consumers
AP-DB-8  A migration with no short lock_timeout (a deploy that can take the site down)
AP-DB-9  Cancel implemented as an UPDATE of the very row a hung job may be holding
AP-DB-10 SET LOCAL lock_timeout = 0 / statement_timeout = 0 "just for this one query"
AP-DB-11 No alert on the longest open transaction (the corpse alarm)
AP-DB-12 PgBouncer in transaction mode with the §1 settings still assumed to be session-level (silently lost)
```

---

## §10. WHAT GOES INTO THE PASSPORTS (numbers, before code)

**`ARCH_SPINE` vertebra 4 (timeouts) gains a DB row** — the guard rails are part of the deadline system:
```
DB guard rails: lock_timeout [Xs] · idle_in_transaction [Xmin] · statement_timeout [Xs]
                (profiles: OLTP / worker / maintenance / reports / migrations)
Connection budget: [API replicas × pool] + [workers × pool] + [beat] + [migrations] = [N] ≤ [max_connections × 0.8]
```

**`PIPELINE_PASSPORT` / `JOB_PASSPORTS` gain three lines:**
```
DB guard rails:        [the profile used]
Maintenance isolation: reclaim/dispatch on [their own worker/queue]; time_limit [Xs]
Cancel path:           [control-plane flag | row UPDATE] — must not be blockable by the job it cancels
```

---

## §11. BOUNDARIES (reference, do not duplicate)

```
DATA_INTEGRITY_CANON  → is the DATA correct? (invariants, races, idempotency, money, time, tenancy)
DATA_STORE_SELECTION  → WHICH store?
MIGRATIONS_PLAYBOOK   → how to CHANGE the schema safely (this file adds only: a migration needs a short lock_timeout)
ASYNC_WORKERS_CANON   → the discipline of WORK (workers, leases, limiters, retries)
ROLE_PERF             → is the query FAST? (N+1, indexes, EXPLAIN) — cost, not liveness
THIS FILE             → is the DATABASE ALIVE, and does it have TIME?
```

A system can satisfy every one of the above and still be taken down by a single hung transaction.
That is the gap this canon closes.

---

Reference: `roles/ASYNC_WORKERS_CANON.md` (§0/§1 the supervision plane · AW-2 cooperative cancellation · AW-11 lease and reclaim) · `roles/DATA_INTEGRITY_CANON.md` (§3 transactional discipline) · `roles/ARCH_SPINE_PROTOCOL.md` (§4 timeouts — the DB is a dependency like any other) · `roles/MIGRATIONS_PLAYBOOK.md` · `roles/ASYNC_AWAIT_REFLEX.md` (§1-C — the grep self-check) · `roles/ROLE_ARCH.md` · `roles/ROLE_DEV.md` · `roles/ROLE_QA_ARCH.md` · `roles/ROLE_OPS.md` (alerts, the runbook) · `roles/ROLE_AUDITOR.md` (§7 is step 0 of any "everything is stuck" investigation)
Version: 1.0 | 2026-07-12
