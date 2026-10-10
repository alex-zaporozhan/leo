# ASYNC_WORKERS_CANON.md
# The async-contour canon: background-job semantics, worker discipline, patterns under load.
# Position: SYSTEM_DESIGN_PROTOCOL Step 6 chooses the broker and sizing; DATA_STORE_SELECTION §2 — the store.
# HERE — what was missing: the CONTRACT of every task and the laws under which workers do not hang or lose work.
# Owners: @ARCH (the contour ADR + task passports), @DEV (execution), @PENTEST/@QA (crash-tests §7).
# Stack-agnostic; the primary mapping — Node/BullMQ (Redis), the second — Python/Celery.
# v2.0 (system v6.25): + PART II "Pipeline liveness" — a breakdown of incident #2 (RAG ingest), laws AW-11…13,
# the lease single-clock model, the limiter at the wire, the PIPELINE PASSPORT, tests T-I1…I6, anti-patterns AP-15…20.

> **The canon's dogma:** a queue is not "drop a function, it runs later". A queue is a distributed system
> with at-least-once delivery, concurrency and failures. Whoever writes a task without a contract (§4) is
> designing a hang — just with a delay.

---

## §0. THE CANONICAL INCIDENT — the "supervisor task" (a breakdown of a real deadlock)

```
How it was built:
  A queue (concurrency = 2 slots)
  ├─ Task A: long work; inside — a synchronous loop (the event loop is busy, never yields outward)
  └─ Task B: "stop task A" — placed IN THE SAME queue

What happened:
  A took a slot and locked the event loop → heartbeat/progress aren't sent, there is no one to check the cancel.
  B waits for / gets a slot, but has nothing to "stop" A with: a task has no lever over another process.
  Slots exhausted → the whole queue stands still. The system is alive but dead.
```

**Five root causes → five laws that make this impossible:**

| Cause | Canon law |
|-------|-----------|
| A control command was placed in the DATA queue | AW-1: control-plane ≠ data-plane |
| Cancellation was conceived as an external action | AW-2: cancellation is only cooperative |
| A synchronous loop locked the event loop | AW-3: the event loop is always free |
| No heartbeat → the hang is invisible | AW-8: lock + heartbeat + stalled-detector |
| The long and the controlling task in one pool | AW-9: bulkhead — task classes are separated |

---

## §1. THE MENTAL MODEL — THREE PLANES

```
DATA-PLANE   (the task queue): units of WORK. A worker pulls → executes → acknowledges. That's all.
CONTROL-PLANE (commands):      cancel/pause/priority. NOT tasks! Channels: a flag in the store
                              (cancel:{jobId} in Redis) / pub-sub / the broker API. The executor itself reads it.
SUPERVISION  (oversight):      the process manager (Docker/systemd/an orchestrator) + the broker's stalled-detector.
                              ONLY this plane may kill a process. A worker does not kill a worker.
```

**The iron corollary:** a worker never waits for, stops, or polls another worker.
Multi-step coordination — through state (§3 Saga/Flow), not through "I'll wait for my neighbour in the queue".

---

## §2. THE TEN LAWS OF THE ASYNC CONTOUR (AW-1…AW-10)

```
AW-1  ONE TASK = ONE UNIT OF WORK. Control (cancel/orchestration) is not framed as a data task.
      "A task that changes another task's fate" is a forbidden type (§0).

AW-2  CANCELLATION IS COOPERATIVE. The executor itself checks the signal at checkpoints: every chunk/iteration,
      no less than once every 5s for long ones. Node: AbortController, the signal in fetch/requests; a check
      `if (await isCancelled(jobId)) return job.discard()`. You cannot cancel one who does not check —
      so a task without checkpoints does not pass the passport review (§4).
      Escalation: did not acknowledge the cancel within 2× the checkpoint interval (min 30s) → the SUPERVISION
      plane kills the task process (a hard limit), status aborted; a repeat run is safe by AW-4.
      UI truth: a cancel request → "cancelling"; "cancelled" — ONLY after the executor confirms it.

AW-3  THE EVENT LOOP IS FREE. CPU work > 50ms — into worker_threads / a child process / a separate
      CPU service (Python: a separate pool, not a gevent worker). A long loop must yield:
      `await setImmediate()` every N iterations — otherwise the heartbeat is dead and AW-8 won't save you.

AW-4  AT-LEAST-ONCE + IDEMPOTENCY. "At least once" delivery is a given; exactly-once is marketing.
      Every task has an idempotency key (a natural one: `invoice:{id}:send`) and writes via
      UPSERT/a unique index/a status check. A repeat = a no-op, not a duplicate email/charge.

AW-5  PAYLOAD = REFERENCES, NOT SNAPSHOTS. The id and version go into the task, the data is read fresh at the
      moment of execution. An object snapshot in the payload = work on stale data + a bloated broker.

AW-6  RETRY WITH SENSE. exponential backoff + full jitter (1s→2→4→…, cap 5–15 min), attempts 3–5.
      Errors split: RETRYABLE (network, 429, DB deadlock) → retry; FATAL (validation, 4xx of logic,
      "no such entity") → straight to DLQ without retries. Retrying a fatal = DDoS-ing yourself.

AW-7  DLQ IS MANDATORY. Exhausted attempts / fatal → dead-letter with the full error context.
      A DLQ has: a size metric, an alert, a triage procedure (replay/write off), retention.
      A queue without a DLQ loses work silently — forbidden.

AW-8  LOCK + HEARTBEAT + STALLED. A long task holds a lock and renews it with a heartbeat
      (BullMQ: lockDuration + stalledInterval; Celery: visibility_timeout > the max duration!).
      A dead/locked-up worker → the stalled-detector returns the task to the queue (subject to AW-4).

AW-9  BULKHEAD. Task classes are isolated: fast (≤1s) / slow (minutes) / external (others' APIs) —
      DIFFERENT queues and DIFFERENT worker pools. One slow wave does not eat the fast ones' slots.
      Priority = a separate queue, not a priority field within a shared one (a field won't free busy slots).

AW-10 BACKPRESSURE AND LIMITS. Concurrency is always bounded (never Promise.all over 10k —
      p-limit/chunks); external APIs — a rate-limiter on the queue (groupKey), not "as it goes";
      queue depth and age — metrics with alerts (§6); on overload the producer lowers generation
      (reject/defer), rather than bloating the broker to OOM.
```

---

## §3. PATTERN CATALOG (which one when; the core; the mapping)

| Pattern | When | Implementation core | BullMQ / Celery |
|---------|------|---------------------|------------------|
| Fire-and-forget | an email, a recompute, an outgoing webhook | enqueue(id) after commit | `queue.add` / `delay()` |
| Scheduled (cron) | reports, cleanups, syncs | a SEPARATE scheduler process + a distributed lock (not setInterval in the API!) | Job Schedulers (`upsertJobScheduler`) / celery beat (one!) |
| Delayed | "remind in 24h", manual retries | a delayed task; cancellability via a key | `delay` opt / `eta` |
| Chain / Pipeline | steps strictly in order with result passing | the parent waits for children by state, not in a slot | Flows (parent-children) / chain |
| Fan-out / Fan-in | multiply into N, collect a total | children + an aggregator parent; a counter in the store | Flow / chord |
| Saga (orchestration) | a multi-step business process with compensations (booking→payment→notifications) | a saga table: a state machine in the DB; each step is a task; a failure → compensating tasks | a custom orchestrator over the queue (the state machine in the DB, not in memory) |
| **Outbox** | "write to the DB + send an event" atomically | in ONE transaction we write the entity + an outbox row; a relay worker publishes and marks it | a polling relay with `FOR UPDATE SKIP LOCKED` |
| Rate-limited external | SMS/payment/AI APIs with limits | one queue per provider + a limiter (rps/burst) | `limiter {max,duration,groupKey}` / rate_limit |
| Batch / Chunking | process 100k rows | cursor → chunks of N → progress in the job; checkpoint-resume | `job.updateProgress` + a loop with AW-2/3 |
| Debounce / Dedupe-window | "recompute the view" 50 times/sec | jobId = a deterministic key: a duplicate collapses | a fixed `jobId` / a lock key |

**Queue-store selection rule:** Redis (BullMQ/Streams) — the default (`DATA_STORE §2`); a queue ON Postgres
is allowed for small volumes ONLY via `SELECT … FOR UPDATE SKIP LOCKED` (polling without it = races and duplicates);
Kafka — at > 100k msg/s or for replay (see the DATA_STORE table). Redis under queues: `maxmemory-policy noeviction` —
eviction on a queue means silent task loss.

---

## §4. JOB PASSPORT — the contract of every task type (without it a task is not written)

Artifact: `docs/artifacts/JOB_PASSPORTS_[PROJECT].md` — a table of passports. @ARCH approves it when
designing the epic; @DEV implements it line by line; @QA/@PENTEST run §7 against the passport.

```markdown
### JOB: notifications.booking-confirm
Queue/class:     external (bulkhead: the SMS provider)          [AW-9]
Payload:         { bookingId, version } — references only        [AW-5]
Idempotency:     key = booking:{id}:confirm-sms; a status guard in the DB [AW-4]
Timeout:         15s hard                                        [AW-8]
Retry:           attempts 4, backoff exp+jitter 2s cap 5m; FATAL: an invalid phone → DLQ [AW-6/7]
Cancellation:    checkpoints: before the provider request        [AW-2]
Concurrency:     limiter 10 rps (the provider's limit)           [AW-10]
CPU profile:     I/O-bound; does not occupy the event loop        [AW-3]
Observability:   lifecycle logs with jobId; queue metrics         [§6]
Cleanup:         completed keep 1k/24h; failed → DLQ 14d
```

A mini version is allowed (fire-and-forget ≤1s): queue · payload · idempotency · retry/DLQ — 4 lines minimum.

---

## §5. TOPOLOGY AND SIZING

```
□ A worker = a SEPARATE process/container. Never inside the API process (deploy, memory and crashes — separate).
□ Concurrency per process by profile: I/O-bound 10–50 · CPU-bound = cores (in threads/procs) · memory-heavy 1–2.
□ Prefetch by class: slow/external = 1 + late-ack (Celery: acks_late+prefetch_multiplier=1) — a worker does not
  "pocket" tasks it can't handle; fast ≤ 4–8. High prefetch on long ones = an invisible queue in memory.
□ Scale = worker replicas, not concurrency=500 in one (GC, connections, blast radius).
□ Capacity: workers ≥ ceil(incoming_rate × avg_duration / concurrency) × 1.5 (a margin for retries and peaks).
□ Worker connection pools count into the shared DB budget (SYSTEM_DESIGN Step 4: total connections).
□ Docker: stop_grace_period ≥ the max fast-job time; a SIGTERM handler is mandatory (AW-9 shutdown below).
□ Graceful shutdown: SIGTERM → stop taking new ones → wait for the current ones within the grace → the ones that
  didn't finish return via stalled (AW-8) WITHOUT duplicates (AW-4). kill -9 is survived by construction — this is test T-A.
□ The scheduler (cron) — one instance logically: a distributed lock, even if there are two replicas.
```

---

## §6. OBSERVABILITY (the junction of LOGGING_OBSERVABILITY + METRICS)

Mandatory metrics per queue (M-ASYNC-*): `depth` (waiting) · `oldest_age` (how long the oldest has waited) ·
`processing_p95` · `fail_rate` · `stalled_count` · `dlq_size`. Alerts: depth grows for 10 min straight ·
oldest_age > 3× the class SLA · stalled > 0 repeatedly · dlq_size > 0 (for critical types).
Lifecycle logs with correlation: `enqueued/started/heartbeat/progress/completed/failed(reason)/moved-to-dlq`
+ jobId + attempt. A worker's health = the freshness of the last heartbeat, not "the process is alive".

---

## §7. CONTOUR CRASH-TESTS (@QA/@PENTEST; part of the acceptance of an epic with queues)

```
T-A  kill -9 of a worker mid-task → the task replayed, NO duplicates in the DB/emails (AW-4+8)
T-B  Duplicate delivery (enqueue a task twice with one key) → one effect (AW-4)
T-C  Cancel a long task → reaction ≤ 5s, resources freed, status cancelled (AW-2)
T-D  A task with a locked loop (deliberately) → the stalled-detector returned it, the queue did NOT stall (AW-3+8, §0)
T-E  SIGTERM under load → 0 lost, 0 doubled (graceful §5)
T-F  A wave of slow tasks → the fast queue lives (bulkhead AW-9)
T-G  The provider answers 429 → the limiter throttles, retries with jitter, the DLQ is empty (AW-6+10)
```

---

## §8. ANTI-PATTERNS (🔴 on review)

AP-1 the "supervisor task" — controlling another's task through the data queue (§0) · AP-2 sync-CPU/a loop without
a yield in a worker · AP-3 cron via setInterval in the API process · AP-4 one queue for all task classes ·
AP-5 an unbounded Promise.all / concurrency without a number · AP-6 an object-snapshot payload · AP-7 retry without
idempotency · AP-8 a priority field instead of a separate queue · AP-9 a Postgres queue polled without
SKIP LOCKED · AP-10 a worker inside the API process · AP-11 visibility_timeout/lock smaller than the task duration
(eternal "zombie retries") · AP-12 manually removing a lock / "let's clean Redis" as a cure (the passport is the cure) ·
AP-13 waiting for the result of a task of ITS OWN queue inside a task (`result.get()` / `waitUntilFinished` /
`.join()`) — a deadlock when slots are full, mechanics §0; composition only via §3 Flows/chord/Saga ·
AP-14 a DB transaction held open over an external call/enqueue (holds the connection pool; an event — via Outbox,
enqueue — after commit).

---

# PART II (v2.0) — PIPELINE LIVENESS: incident #2 → laws AW-11…13

## §9. THE CANONICAL INCIDENT #2 — the "living dead" (RAG ingest, a real case)

The pipeline: Celery ingest (concurrency=2) → embedding batches to an external API (8 RPS, a limiter in Valkey)
→ upsert into pgvector. Symptom: the first job reached `embed_cursor=64/190`, the second went silent; maintenance
saw `global_active=2`, the queue grew, `reclaimed:0` — the system counted zombies as alive. Eight defects:

| # | Defect (as it was) | Law |
|---|--------------------|-----|
| D1 | An external await hung forever, and the heartbeat kept renewing the lease → a "living" zombie | AW-11: heartbeat = PROGRESS; a deadline on every await |
| D2 | The limiter `zcard → zadd → if`: a token was written even on a REJECTION → the bucket kept itself full, waiting forever | AW-13: an atomic check-and-consume; a rejection does not spend capacity |
| D3 | Retries after a 429 went AROUND the shared limiter → 429 spikes with parallel workers | AW-13: the limiter at the wire, before EVERY attempt |
| D4 | A broad catch (AppError) inside the task turned a retryable error into a terminal failed → retry "nearly dead" | AW-12: the taxonomy is end-to-end; a catch does not degrade the class |
| D5 | Retry lived on TWO levels (Celery autoretry + service-requeue) → double attempts, bypassing the limiter | AW-12: a single retry owner per error class |
| D6 | Three sources of truth about life: lease 120s at dispatch, stale 5min at reclaim, a fresh `updated_at` "rejuvenated" an expired lease | AW-11: the lease is the only clock; one alive predicate |
| D7 | Reclaim scheduled once per 300s while the lease TTL is 120s → a slot held by a dead worker for up to 5 minutes | AW-11: reclaim period ≤ TTL/2; recovery window — a number |
| D8 | The batch ran SEQUENTIALLY under an 8 RPS budget → the budget was unused, throughput ~1 RPS | §11: parallelism = the budget; pacing instead of burst |
| D9 | A regression test PINNED a wrong invariant ("active until stale even with an expired lease") — the fix required changing the test | AP-20: a test invariant is derived from the passport, not from behaviour |

---

## §10. AW-11 — THE LEASE MODEL: THE SINGLE LIFE CLOCK

```
ONE SOURCE OF TRUTH: lease_expires_at. Expired → the job is reclaimable and the slot is free IMMEDIATELY.
  updated_at / any second timestamp — only a fallback for legacy records WITHOUT a lease;
  "rejuvenating" an expired lease with a fresh updated_at is forbidden (D6).

ONE PREDICATE: alive(job) := status='processing' AND lease_expires_at > now().
  Admission, dispatch, reclaim, metrics read ONE function/SQL view — not three copies of their own (D6).

NUMBERS AGREED (in the PIPELINE PASSPORT §13, not in people's heads):
  the lease renewal interval ≤ TTL/3 · the reclaim period ≤ TTL/2 (D7) ·
  RECOVERY WINDOW := TTL + the reclaim period — the declared maximum of "a slot held by a dead one", a number in the alert.

HEARTBEAT = PROGRESS, NOT A PULSE (D1): the lease renewal goes alongside the growth of the progress metric
  (cursor/stage). Stuck predicate: processing AND the lease is alive AND progress does not grow > stage_deadline
  → an alert + a forced fail of the stage. A pulse without progress does not count as life.

A DEADLINE ON EVERY EXTERNAL AWAIT (the hard form of AW-7): eternal awaits do not exist —
  a hung call dies by its own timeout BEFORE the heartbeat manages to mask it.
```

---

## §11. AW-13 — THE EXTERNAL CALL UNDER A BUDGET: THE LIMITER AT THE WIRE

```
POSITION: inside the provider, immediately before EVERY HTTP attempt — the first one, a retry,
  from any worker (D3). The limiter guards the WIRE, not the intention to call.

ATOMICITY: check-and-consume in one operation (Redis/Valkey Lua). Separate
  zcard→zadd / INCR→EXPIRE = a race and self-lock (D2). A rejected attempt does NOT consume capacity —
  the mandatory regression test T-I1: "with a full bucket, a rejection does not increment the counter".

THE FORM OF THE BUDGET — an explicit choice in the passport: burst (N per window) or PACING (1 request / interval;
  8 RPS → ~125ms). The default for others' APIs — pacing: even load, fewer 429s (the D8 fix).

PARALLELISM = THE BUDGET: a batch of N under a budget R runs in parallel (the shared limiter doses it itself);
  a sequential batch under a parallel budget = the budget is unused (D8).

The provider's 429/Retry-After is respected GLOBALLY (a pause in the limiter), not by each worker in its own way.
```

---

## §12. AW-12 — A SINGLE RETRY OWNER + AN END-TO-END TAXONOMY

```
THE LEVEL MATRIX (transport client · task autoretry · service-requeue · broker redelivery):
  for EACH error class in the passport, EXACTLY ONE owner is assigned; the other levels
  for that class are turned off EXPLICITLY (in a line), otherwise a multiplication of attempts and a bypass of the limiter (D5).

THE TAXONOMY IS END-TO-END: retryable/fatal is assigned at the point of origin and does NOT degrade along the way.
  A broad except (Exception/AppError) around the provider call, turning a retryable into a
  terminal failed, — AP-18 (D4). A catch is either narrow by type or propagates the classification.

REQUEUE ≠ RETRY: a requeue returns the job whole (with the checkpoint cursor, an honest status in the UI),
  a retry repeats the call attempt. One error class — one mechanism, not both.
```

---

## §13. PIPELINE PASSPORT — the pipeline contract (an artifact before code)

For any pipeline (multi-stage background processing with an external provider and progress:
ingest, import, sync, mass mailing, generation) @ARCH fills in
`docs/artifacts/PIPELINE_PASSPORT_[pipeline].md`. Without a passport a pipeline is not written; @DEV executes
it line by line; the passport numbers = the config numbers (reconciled by @QA_ARCH). Template:

```markdown
### PIPELINE: rag.ingest
Stages + progress: fetch → chunk → embed (cursor N/M) → upsert; metric: embed_cursor      [§10]
Stage deadlines:   embed-batch ≤ 120s · upsert ≤ 10s · the job as a whole ≤ 30m            [AW-7]
Budget/limiter:    provider 8 RPS · pacing 125ms · Lua check-and-consume · at the wire
                   (inside the provider, every attempt incl. retry) · test T-I1            [§11]
Parallelism:       batch=8 in parallel · workers ≤2 · the shared limiter doses it          [§11]
Retry matrix:      429/timeout → owner SERVICE-REQUEUE (with cursor); transport OFF,
                   task-autoretry OFF (in a line) · fatal (invalid input) → DLQ            [§12]
Lease numbers:     TTL 120s · renewal 30s (together with cursor) · reclaim 60s ·
                   RECOVERY WINDOW ≤ 180s (an alert on exceeding)                          [§10]
Alive predicate:   processing AND lease_expires_at > now() — SINGLE (admission=dispatch=reclaim)
Stuck predicate:   processing AND cursor does not grow > 120s → an alert + a stage fail     [§10]
Idempotency:       upsert ON CONFLICT (chunk_id) DO UPDATE                                  [Law 32]
Degradation:       the provider is down → the queue piles up to depth X, then a 429 to the producer [AW-10]
Acceptance tests:  T-I1…I6 + T-A (kill) + T-D (locked loop)                                 [§14]
```

**THE "FIVE QUESTIONS BEFORE A PIPELINE"** (@ARCH answers with a number before handing off to @DEV — the short form of the passport):
1) Who is the SINGLE retry owner for each error class (what is turned off)?
2) Where is the limiter (at the wire?) and is it atomic (a rejection does not spend capacity — T-I1)?
3) What is the source of truth about life (one alive predicate; TTL / heartbeat / reclaim / recovery window as numbers)?
4) What progress metric and stuck predicate as a number (a pulse without progress ≠ life)?
5) What is the deadline of every external await and the recovery window after a worker's death?

---

## §14. TESTS T-I1…I6 AND ANTI-PATTERNS AP-15…20

```
T-I1 FULL BUCKET: a rejected attempt does not increment the token counter (the before/after measurement is equal) (D2)
T-I2 RETRY THROUGH THE LIMITER: a retry storm under a budget R → the actual outgoing RPS ≤ R (measured) (D3)
T-I3 LEASE EXPIRED → the job is reclaimable and the slot is free immediately, updated_at does not "rejuvenate" (D6)
T-I4 KILL -9 of a worker mid-stage → recovery ≤ the passport's RECOVERY WINDOW (a number) (D7)
T-I5 A HUNG PROVIDER (mock-forever) → the stage falls by its deadline, the zombie heartbeat does not save it (D1)
T-I6 STUCK DETECTOR: progress frozen while the lease is alive → an alert/forced fail ≤ 2× the deadline
```

AP-15 the limiter spends capacity on a rejection / a non-atomic check-then-add (D2) · AP-16 retry bypassing the shared
limiter (D3) · AP-17 a second source of truth about life: updated_at against the lease, admission/dispatch/reclaim
have different predicates (D6) · AP-18 a broad catch degrades retryable → terminal failed (D4) · AP-19 the reclaim
period > the lease TTL / the recovery window is not declared as a number (D7) · AP-20 a test pins a defect — the test
invariant contradicts the passport/ledger; after fixing the invariant the TEST changes, and this is recorded in a line (D9).

---

Reference: `roles/SYSTEM_DESIGN_PROTOCOL.md` Step 6 (broker/sizing) · `roles/DATA_STORE_SELECTION.md` §2 (Redis/Streams/Kafka) · `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` (DLQ, degradation) · `roles/LOGGING_OBSERVABILITY_PROTOCOL.md` · `roles/METRICS_PROTOCOL.md` · `roles/TESTING_CANON.md` · `roles/DATA_INTEGRITY_CANON.md` · `roles/ASYNC_AWAIT_REFLEX.md` (@DEV grep self-check) · `roles/ROLE_ARCH.md` · `roles/ROLE_DEV.md`
Version: 2.0 (system v6.25) | 2026-07-08
