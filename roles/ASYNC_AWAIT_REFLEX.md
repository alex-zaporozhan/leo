# ASYNC_AWAIT_REFLEX.md
# Reflex map of async / await / queues for @DEV. NOT a canon (the canon — ASYNC_WORKERS_CANON.md — covers the "why").
# This is "what's under your fingers right now": a pattern in code → stop → fix. Run BEFORE handoff, over your own diff.
# Stack: Python (FastAPI/Celery/asyncio) is primary; the Java column (Spring/CompletableFuture) — a foundation.
# Mirror at @QA_ARCH: the same greps. If @DEV ran them already — @QA_ARCH finds zero and doesn't send things back.

> **Why this file exists.** The knowledge is already in `ASYNC_WORKERS_CANON`. Defects arise not from ignorance,
> but from the gap between "a rule in the system" and "the hand typing `await foo()`". This map closes the gap:
> it is built around what @DEV PHYSICALLY TYPES, not around topics. Three top pain points: **hangs · races/duplicates ·
> transactions+queues** — they hit together (hung → restart → duplicate → race in the DB), so they are fixed together.

---

## HOW TO USE (30 seconds before handoff)

```
1. Open your diff.
2. Run the GREP SELF-CHECK block (§1): each pattern is a `grep` over your changes.
   A match found → this is NOT automatically a bug, but you MUST go through the stop-questions next to it.
3. Check suspicious spots against §2 (bad/good signatures).
4. Async task or pipeline → §3 micro-tests — the handoff stop-condition (failure = do not hand off).
5. In the @LEAD report, one line: "ASYNC_REFLEX: ran, clean / found and fixed [what]".
```

If even one §1 pattern fired and was NOT consciously closed — the task is not ready, regardless of whether it "works in the demo".

---

## §1. GREP SELF-CHECK (trigger in code → stop-questions → fix)

### Block A — HANGS (await, event loop, infinite wait)

**A1. `await` of an external call without a timeout** — the most frequent and most costly.
```
grep:   await (client|http|session|requests|httpx|conn|provider|llm|redis|s3)\.
Python: await httpx_client.post(url)                  # ← how long do we wait? forever
Question: where is timeout=? what do we return to the user if the provider hangs for 5 minutes?
Fix:    await httpx_client.post(url, timeout=httpx.Timeout(10.0))
        # or a wrapper: async with asyncio.timeout(10): await call()
Law:    "no eternal await exists" (ASYNC_WORKERS_CANON §10, AW-7).
```

**A2. CPU work inside an async function (blocks the event loop)** — the entire service hangs, not just this task.
```
grep:   (for .+ in .+:|while ).+  inside async def without await   ·   json.loads(huge) · re.compile in loop · hashlib · PIL · pandas
Python: async def process(): for row in 100k_rows: heavy_cpu(row)   # ← loop is frozen
Question: does this section take >50ms without an await? then it holds the ENTIRE loop.
Fix:    await asyncio.get_event_loop().run_in_executor(None, heavy_cpu, row)
        # Celery prefork — fine for CPU; asyncio — move into executor/process
Law:    "the event loop is never blocked" (§10, AW-3). Monitor loop lag.
```

**A3. `time.sleep()` in async code** — synchronous sleep freezes the loop.
```
grep:   time\.sleep\(   (in an async context)
Fix:    await asyncio.sleep(n)          # never time.sleep inside async
```

**A4. Synchronous blocking I/O in async** (`requests`, `open()`, sync DB driver).
```
grep:   \brequests\.(get|post)   ·   \bopen\(   ·   psycopg2 (sync) inside async def
Fix:    httpx.AsyncClient / aiofiles / async driver (asyncpg, sqlalchemy async)
```

**A5. heartbeat/lease renewed without progress growth** — a zombie looks alive (incident #2, D1).
```
grep:   (extend|renew|touch|update).*(lease|heartbeat|lock)   without a nearby growing cursor/progress
Question: pulse without cursor growth = NOT life. What if the external await under the heartbeat hangs forever?
Fix:    renew lease ONLY alongside growth of a progress metric; every external await gets its own deadline.
Law:    "heartbeat = progress" (§10, AW-11).
```

### Block B — RACES AND DUPLICATES (idempotency, restart, cancellation)

**B1. check-then-act: `if free/enough → write`** — under concurrency two threads race in (double booking, oversell).
```
grep:   if .+(exists|free|available|count|qty|balance).+:\s*\n.+(insert|create|save|update|add)
Python: if slot.is_free: booking.create(slot)          # ← a second request passes the if at the same time
Question: will two parallel requests pass this if simultaneously?
Fix:    protect with SCHEMA or LOCK, not with if:
        · UNIQUE/EXCLUDE constraint (the DB itself forbids) → catch IntegrityError → 409
        · SELECT ... FOR UPDATE on the resource row
        · atomic guard: UPDATE stock SET qty=qty-1 WHERE id=? AND qty>=? → rowcount 0 = reject
Law:    "if in code — a UX hint, NOT protection" (DATA_INTEGRITY_CANON §1, Law 32).
```

**B2. Task/endpoint without idempotency** — delivery/network retry → duplicate effect.
```
grep:   @app.task / def task(  ·  @router.post   with an external effect (payment, email, creation)
Question: if this executes TWICE (network retry, at-least-once broker) — will there be a duplicate?
Fix:    · Idempotency-Key in a table + UNIQUE, in ONE transaction with the effect
        · UPSERT (ON CONFLICT DO NOTHING/UPDATE) instead of INSERT
        · dedup guard at the start of the task: if already_done(job_id): return
Law:    at-least-once delivery → idempotency is MANDATORY (AW-4, Law 32 §4).
```

**B3. Task payload contains data instead of references** — working with stale data after a restart.
```
grep:   \.delay(  /  .apply_async(  /  queue.add(   with an object/dict instead of id
Python: send_email.delay(user_dict)          # ← in 10 min the user has already changed
Fix:    send_email.delay(user_id)   # data is read FRESH at execution time
Law:    payload = only id+version (AW-5). Bonus: no PII in the broker.
```

**B4. Cancellation "killed from outside" instead of cooperative** — either doesn't cancel, or tears apart mid-way.
```
grep:   revoke(  /  terminate=True  /  a task that changes the fate of another task
Question: does the cancellable task check a cancellation flag at checkpoints? or is it being killed externally?
Fix:    cooperatively: SET cancel:{job_id} → the task at each chunk/≤5s: if cancelled: checkpoint+return
        AbortSignal/asyncio.CancelledError is propagated into I/O. UI: cancelling→cancelled ONLY after confirmation.
Law:    cancellation is a control-plane flag, not a task in the data queue (AW-2; incident §0).
```

**B5. Worker waits for / polls / kills another worker** — deadlock when slots are full (your first incident).
```
grep:   \.get(  /  \.result  /  waitUntilFinished  /  \.join(   INSIDE a task on its own queue's task
Python: result = other_task.delay(x).get()   # ← in Celery this is a documented deadlock
Fix:    compose via orchestration: chain/chord/group; the parent finishes, the continuation is a callback.
Law:    "a worker does not wait for a worker" (AW-5/§0). If a task is WAITING on another — the architecture is wrong, stop → @ARCH.
```

### Block C — TRANSACTIONS + QUEUES (enqueue inside a transaction, outbox, DB races)

**C1. `enqueue` / HTTP call INSIDE a DB transaction** — the task starts before commit (or after rollback) → "phantom" work.
```
grep:   (.delay(|.apply_async(|httpx|requests|publish)   between begin/commit of a transaction
Python: with session.begin():
            order = create_order()
            send_confirmation.delay(order.id)   # ← task reads order BEFORE commit → finds nothing
Question: if the transaction rolls back — has the task already left? if the task is faster than the commit — reads empty?
Fix:    · enqueue AFTER commit  ·  or OUTBOX: write the event into the outbox table IN THE SAME transaction,
          a relay worker publishes after commit (guarantee: "exactly what was committed")
Law:    HTTP/enqueue/sleep forbidden inside a transaction (AW-14; DATA_INTEGRITY §3).
```

**C2. Transaction open during an external call** — holds a connection from the pool, breaks limits.
```
grep:   await (httpx|provider|llm)   between begin and commit
Fix:    short transaction: read → close → external call → short transaction for write.
Law:    a transaction runs in milliseconds, without external calls inside (§3).
```

**C3. Event published "by hand" after commit** — instead of outbox → event lost on crash between commit and publish.
```
grep:   session.commit()  \n  .*(publish|delay|kafka|emit)
Question: the process crashed BETWEEN commit and publish — is the event lost forever?
Fix:    OUTBOX pattern: commit writes both the data and the outbox row atomically; relay publishes and marks it.
Law:    "commit first, then publish by hand" loses events (AW §4, pattern catalogue).
```

**C4. Postgres queue with polling without `SKIP LOCKED`** — workers grab the same task.
```
grep:   SELECT .+ FROM .*queue.* without FOR UPDATE SKIP LOCKED
Fix:    SELECT ... FOR UPDATE SKIP LOCKED LIMIT n  — each worker takes its own.
```

**C5. Mutations via `Promise.all`/`asyncio.gather` without result inspection** — some failed silently.
```
grep:   asyncio\.gather\(   /  Promise\.all\(   with mutations inside
Python: await asyncio.gather(*[save(x) for x in items])   # one failed → the others? status?
Fix:    gather(..., return_exceptions=True) + check EVERY result;
        or sequentially if there is a dependency/shared resource. Concurrency limit (Semaphore).
Law:    Promise.all/gather with mutations — only with checking each status (AW-10; Law 11).
```

**C6. Retry bypassing the shared limiter** — under parallel workers the provider gets a burst (incident #2, D3).
```
grep:   retry / backoff around a call, while the limiter — only before the FIRST attempt
Fix:    the limiter is taken INSIDE the provider before EACH HTTP attempt, including retries; check-and-consume atomically.
Law:    "limiter at the wire" (AW-13, §11). A limiter rejection does NOT spend capacity (test T-I1).
```

**C7. Broad `except` around a provider call** — a retryable error degrades to fatal, retries are "dead" (D4).
```
grep:   except (Exception|BaseException|AppError):   around an external call
Python: try: await provider.call()
        except Exception: mark_failed()      # ← timeout (retryable) became terminal failed
Fix:    catch NARROWLY by type; the retryable/fatal classification is propagated, not collapsed into failed.
Law:    end-to-end error taxonomy; broad catch = AP-18.
```

---

**C8. An abandoned transaction holds row locks — the "corpse lock" (the outage pattern).**
```
grep:   session/transaction opened, then a long external call or a loop with no timeout inside it
        · engine config WITHOUT lock_timeout / idle_in_transaction_session_timeout / statement_timeout
Python: async with session.begin():
            job.status = 'processing'
            await provider.call(...)      # ← the worker hangs here; the transaction stays OPEN
                                          #   Postgres holds the row locks INDEFINITELY
Question: if this process is SIGKILLed right here — what is held, and for how long?
Fix:    · per-session guard rails (DATABASE_RUNTIME_CANON §1): lock_timeout=10s,
          idle_in_transaction_session_timeout=5min, statement_timeout=120s — via env, defaults ON
        · never an external call inside an open transaction (C1/C2 above)
Law:    "the database has time too" (DB-1/DB-2). The corpse alarm: an alert on the longest open transaction.
```

**C9. A queue-like SELECT without SKIP LOCKED — the recovery path blocks on what it recovers.**
```
grep:   SELECT .+ FROM .*(job|queue|outbox|task).* FOR UPDATE   without   SKIP LOCKED
        · reclaim / dispatch / stale-detector / outbox relay
Fix:    SELECT ... FOR UPDATE SKIP LOCKED LIMIT n
Law:    DB-3. The recovery mechanism must never be able to queue behind the rows it is trying to recover.
        (This is how a hung job also took down Cancel and reclaim — DATABASE_RUNTIME_CANON §0.)
```

**C10. Cancel implemented as an UPDATE of the row a hung job may hold.**
```
grep:   cancel / stop endpoint doing UPDATE jobs SET ... WHERE id = ?
Question: if that row is locked by the hung job I am cancelling — does my Cancel just… queue?
Fix:    a control-plane flag the executor polls at checkpoints (AW-2), not an UPDATE of the job row.
Law:    DB-4. "If your Cancel button can queue behind the thing it is cancelling, you do not have a Cancel button."
```

---

## §2. SIGNATURE COMPARISON (quick recognition of bad → good)

**External call (Python):**
```python
# ❌ bad                                      # ✅ good
await client.post(url, json=data)            async with asyncio.timeout(10):
                                                 await client.post(url, json=data)
                                             # + retry(retryable only) + limiter inside provider on every attempt
```

**Entity creation with an external effect (Python/FastAPI):**
```python
# ❌ race + enqueue inside transaction        # ✅ constraint + enqueue after commit + idempotency
with session.begin():                        # UNIQUE(tenant_id, slot_id) in the schema
    if slot_free(slot):                      with session.begin():
        b = Booking(slot=slot)                   session.add(Booking(slot=slot, idem_key=key))
        session.add(b)                       # IntegrityError → 409 SLOT_TAKEN
        notify.delay(b.id)                   notify.delay(b.id)   # ← after commit
```

**Celery task (Python):**
```python
# ❌ bad                                      # ✅ good
@app.task                                    @app.task(bind=True, acks_late=True,
def process(user_dict):                                soft_time_limit=1800, time_limit=1860,
    charge(user_dict)                                  max_retries=3)
                                             def process(self, user_id, idem_key):
                                                 if already_done(idem_key): return       # dedup
                                                 for chunk in read_chunks(user_id):
                                                     if cancelled(self.request.id):      # cooperative cancel
                                                         checkpoint(); return
                                                     charge(chunk)                       # idempotent
                                                     self.update_state(...)              # progress
```

**Java foundation (Spring / CompletableFuture) — same laws, different syntax:**
```java
// ❌ await without timeout                   // ✅ timeout + result inspection
future.get();                                future.get(10, TimeUnit.SECONDS);   // TimeoutException
CompletableFuture.allOf(fs).join();          // + handle(ex) on each; @Retryable(retryFor=Transient.class)
// transaction + event                        // @TransactionalEventListener(phase=AFTER_COMMIT) — no publish inside transaction
// idempotency                               // @Idempotent / UNIQUE + ON CONFLICT; SELECT ... FOR UPDATE (JPA @Lock PESSIMISTIC_WRITE)
```
> Java column — a foundation; when you actively start using it, it will be expanded to Python-level depth. The laws (AW-1…13) are stack-independent.

---

## §3. MICRO-TESTS AS STOP-CONDITION (for async tasks and pipelines)

Not "write tests later" — assemble them from 5–8 ready lines and run BEFORE handing off. Failure = do not hand off.

```python
# T-A  KILL: task survives kill -9 mid-work (idempotency + acks_late)
def test_survives_restart():
    start_task(job_id="j1"); kill_worker_mid()      # SIGKILL
    restart_worker()
    assert effect_count(job_id="j1") == 1           # exactly one effect, not zero and not two

# T-B  DUPLICATE: one task delivered twice → one effect
def test_duplicate_delivery():
    run_task(idem_key="k1"); run_task(idem_key="k1")
    assert effect_count(idem_key="k1") == 1

# T-RACE  RACE: N parallel on one resource → exactly 1 success
def test_no_double_booking():
    results = run_parallel(book_slot, slot_id="s1", n=50)
    assert results.count(200) == 1 and results.count(409) == 49

# T-I1  LIMITER: a rejected attempt does NOT spend capacity (incident #2, D2)
def test_limiter_reject_no_spend():
    fill_bucket()
    before = token_count(); attempt_rejected(); after = token_count()
    assert before == after

# T-I5  HUNG PROVIDER: mock hangs forever → stage dies by deadline (not by heartbeat)
def test_hung_provider_dies_by_deadline():
    with mock_provider_hangs_forever():
        t0 = now(); run_stage()
        assert now() - t0 < deadline + margin        # died by timeout, did not hang forever
```

Which test is mandatory by task type:
```
Data write / booking / payment   → T-RACE + T-B (+ T-A if background)
Background task                  → T-A + T-B
Pipeline with external provider  → T-A + T-I1 + T-I5
```

---

## §4. REFERENCE MAP (where to go for the "why")

```
Writing async/await, event loop, timeouts      → this file §1-A + ASYNC_WORKERS_CANON §10
Writing a queue/worker/Celery task             → this file §1-B + JOB PASSPORT (ASYNC_WORKERS_CANON §4)
Writing a pipeline with an external provider   → this file §1-C6/C7 + PIPELINE PASSPORT (ASYNC_WORKERS_CANON PART II)
Writing data writes under concurrency          → this file §1-B1/B2 + DATA_INTEGRITY_CANON §1-§2
Transaction + event/queue                      → this file §1-C + DATA_INTEGRITY_CANON §3 (Outbox)
```

**@DEV rule:** this file is run BEFORE saying "done", for any Type F/H/B task
(and any task with `async`/a queue/an external call). The line in the report is mandatory.
**@QA_ARCH mirror:** the same §1 greps — a verification vector. A clean @DEV run = @QA_ARCH finds zero.

---

Reference: `roles/ASYNC_WORKERS_CANON.md` (AW laws, incidents, passports) · `roles/DATABASE_RUNTIME_CANON.md` (the DB's clock, locks, the corpse pattern — C8–C10) · `roles/DATA_INTEGRITY_CANON.md` (races, idempotency, Outbox) · `roles/ROLE_DEV.md` (Types F/H/B) · `roles/ROLE_QA_ARCH.md` (mirror vectors) · `roles/DEV_EXECUTION_PASSPORT.md`
Version: 1.0 | 2026-07-09
