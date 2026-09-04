# LOAD_REFLEX.md
# Reflex map of load for @DEV. NOT a canon (the canon — SYSTEM_DESIGN_PROTOCOL.md — covers the "why" and the numbers).
# This is "what's under your fingers right now": a pattern in the diff → stop → fix. Run BEFORE handoff, over your own diff.
# Stack: Python (FastAPI / SQLAlchemy 2 async / Postgres / Celery) primary; the client-side block is React/TanStack.
# Mirror at @QA_ARCH: **Vector 20 (Load)** — the same greps. If @DEV ran them, that vector passes quickly.

> **Why this file exists.** `SYSTEM_DESIGN_PROTOCOL` already decides the numbers, and its **LOAD FLOOR** already
> made five decisions so that nobody re-decides them per module. But a decision in an artifact does not reach the
> hand typing `.all()`. This map closes that gap: every check below enforces one line of the floor, and every
> check fires on a **literal string an implementer types**, not on a topic.
>
> **What makes load different from every other reflex here, and why it needs one most.** An async bug hangs. A
> race double-books. **A load defect works perfectly** — on your machine, on staging, on the first two hundred
> rows. It is the only failure class in this system whose symptom is *success*, right up until the table grows,
> and by then it is in production and inside a page a customer uses. There is no test that fails. That is the
> whole argument for greps.

**Scope note — what this file does NOT own.** Timeouts on outgoing calls, event-loop blocking, retries, queue
liveness and cancellation belong to `roles/ASYNC_AWAIT_REFLEX.md` (A1–A5, C1–C9). Lock and session guard rails
belong to `roles/DATABASE_RUNTIME_CANON.md`. This file owns **volume**: what has no ceiling, what multiplies, and
what does not belong in a request.

---

## HOW TO USE (30 seconds before handoff)

**Use `rg` (ripgrep), not `grep`.** Several patterns below are multiline (`rg -U`) and several need PCRE
(`rg -P`). Plain `grep -E` silently returns nothing on the multiline ones and errors on the lookaheads — a
reflex that silently finds nothing is worse than no reflex.

1. Run the greps in §1 **over your own diff**, not over the repository as it stands.
2. **A match is NOT automatically a bug** — it is a place where the question below it must be answered. Several
   of these patterns match correct code as readily as broken code (a properly paginated endpoint still contains
   `.all()`). What is not optional is *answering*: fix it, or say why it is correct. An unanswered hit is a stop.
3. One line in the report, required even at zero: `LOAD REFLEX: <n> triggers, <n> fixed, <n> N/A + reason`.
   Silence reads as "not run" (Law 12).
4. A trigger you consciously leave goes in `NOT DONE:` with the reason (Law 13).

**Fires on** any diff touching a query, a list endpoint, a serializer, a loop over rows, a report, an export,
a dashboard aggregate, or a component that renders a collection.

---

## §1. GREP SELF-CHECK (trigger in the diff → stop-questions → fix)

### Block A — UNBOUNDED (things with no ceiling)

**LD1. A query with no limit** — the floor's first decision, and the most common breach of it.
```
rg:     '\.(scalars\(\)\.)?all\(\)|fetchall\(\)'          then check each hit for .limit( on the same statement
rg -P:  'select\((?!.*\.limit\()[^)]*\)'                  a select( whose chain never reaches .limit(
SQL:    rg -Pi 'SELECT(?!.*\bLIMIT\b).*FROM'  (raw SQL / text() only)
Python: rows = (await session.execute(select(Client))).scalars().all()
Note:   a CORRECT keyset endpoint also contains `.all()` — the pattern finds the place, the question decides.
Question: how many rows is this on the biggest tenant, in two years, at the floor's ×5 growth?
          if the answer is "I don't know" — the answer is "unbounded".
Fix:    .limit(n) with an explicit page, or a keyset cursor for anything a user scrolls.
        A deliberate full read (a migration, an export job) is fine OUTSIDE a request path — say so in the report.
Floor:  "There is no unbounded SELECT in a request path."
```

**LD2. A list endpoint with no pagination in its signature** — the ceiling has to be in the contract, not in a habit.
```
rg -U:  '@router\.(get|post)[^\n]*\n[^\n]*def [^\n]*(list|search|index|all|export)'
rg:     'response_model=(list|List)\[|-> (list|List)\['      both spellings: py3.9+ writes list[X]
Python: @router.get("/clients", response_model=list[ClientOut])
        async def list_clients(...):            # ← no limit/offset/cursor anywhere
Question: what does this return on a tenant with 200k rows? what does the client do with it?
Fix:    limit + cursor in the signature, a hard server-side max (the floor's ≤200KB payload), and the max
        applies even when the caller asks for more. A caller cannot opt out of the ceiling.
Floor:  "Every list endpoint is paginated · payload ≤200KB per response."
```

**LD3. A filter or sort the UI offers, on a column with no index** — the silent one.
```
rg:     'order_by\(|\.where\(|ilike\(|ORDER BY'   (SQLAlchemy 2 writes select().where(); `.filter(` is the
        1.x Query API and also matches JS arrays — scope this one to the backend path)
        → then open the migration and look for an index on every column named
Python: .order_by(Client.last_visit_at.desc())     # ← is there an index on last_visit_at?
Question: does a migration create an index for every column this endpoint sorts or filters by?
          composite, in the order the query uses them? partial, if the query always carries a status?
Fix:    add the index in the same change as the endpoint — not "later, if it is slow".
        Cannot index it (a computed expression, a JSON path)? then the UI does not offer it as a filter.
Floor:  "Every filter and sort column a UI exposes has an index, or the UI does not expose it."
```

**LD4. `count()` on a growing table for a UI element** — the most expensive number nobody looks at.
```
grep:   func\.count\(  ·  \.count\(\)  ·  SELECT COUNT\(\*\)
Python: total = await session.scalar(select(func.count()).select_from(Client))
Question: is this count rendered, or is it only feeding "Page 1 of N"? does a full scan happen per request?
Fix:    keyset pagination with "has more" instead of a total · an estimate from `pg_class.reltuples` where an
        approximation is honest · a maintained counter where the exact number is a product requirement.
        A count is a product decision, not a pagination detail.
```

### Block B — MULTIPLICATION (one becomes N)

**LD5. A query inside a loop — N+1.** Passes every test with three rows in the fixture.
```
grep:   for .+ in .+:\n(\s+).*(await session|\.execute\(|\.get\(|\.filter\(|requests\.|httpx)
Python: for order in orders:
            client = await session.get(Client, order.client_id)   # ← one query per row
Question: how many queries does this endpoint issue for a page of 50? for the floor's peak RPS?
Fix:    selectinload / joinedload for relations · one IN-query keyed by id · a dict built before the loop.
        SQLAlchemy 2: state the loading strategy on the relationship — lazy loading in async code is
        a latent N+1 that only appears under real data.
```

**LD6. A serializer that walks a relationship** — an N+1 hiding one layer below the query.
```
grep:   class .*Out\(BaseModel\)  → fields that are lists or nested models
        @property  ·  computed_field  — anything that touches the session
Python: class OrderOut(BaseModel):
            items: list[ItemOut]        # ← where did items come from? a lazy load per order?
Question: is every nested collection eager-loaded by the query that produced the parent?
Fix:    load it in the query, or drop it from the response and give it its own endpoint.
        A response shape is a load decision — this is where "just add one field" becomes an outage.
```

**LD7. Fan-out with no ceiling** — one request becomes N calls, one job becomes N jobs.
```
grep:   for .+ in .+:\n\s+.*(\.delay\(|\.apply_async\(|send_|publish|notify)
Python: for user in tenant.users: send_email.delay(user.id)      # ← 40k users = 40k tasks
Question: how large can the collection be? what does the queue look like at that size? what is the
          provider's rate limit, and who owns it (ASYNC_AWAIT_REFLEX C6)?
Fix:    batch into chunks with a declared size · a single job that iterates with its own lease and progress
        (JOB_PASSPORT, Law 30) · the limiter at the wire, not per call.
```

### Block C — THE HOT PATH (what does not belong in a request)

**LD8. Heavy synchronous work inside a request handler.**
```
rg -U:  '(async )?def [a-z_]+\([^)]*\)[^\n]*:(?s:.{0,800}?)(pandas|openpyxl|PIL|Image\.|reportlab|weasyprint|
        csv\.writer|zipfile|subprocess)'                     — then keep only the hits inside a router module
rg:     '@router\.'  -A 40  | rg 'pandas|openpyxl|PIL|reportlab|csv\.writer'   (the cheaper version)
Python: @router.post("/reports")     async def build(...):  df = pandas.read_sql(...)  # ← in the request
Question: what is the p95 of this at the floor's target (read 300ms · write 800ms)? what happens at peak
          concurrency, when N of these run at once against the same connection pool?
Fix:    anything past the write target becomes a queued job with a JOB_PASSPORT and a progress cursor —
        NOT a longer timeout. A report over 3s is a job, not a request.
Floor:  "Anything over the p95 write target moves to a queue, not to a longer timeout."
```

**LD9. A pool size that was chosen per process, not per system.**
```
grep:   pool_size  ·  max_overflow  ·  create_async_engine  ·  DATABASE_POOL  ·  max_connections
Config: pool_size=20, max_overflow=10          # ← × how many API workers? × how many Celery workers?
Question: API replicas × pool + Celery workers × pool + beat + migrations + a human with psql —
          does the sum fit inside Postgres `max_connections`, with headroom for a deploy overlap?
Fix:    budget the total first, divide second. Put the number in spine vertebra 9. A deploy that briefly
        doubles the API replicas must still fit.
Floor:  "The connection budget is summed across API + workers + cron + migrations, not per process."
```

**LD10. A read that is hot, identical and uncached** — and a cache with no invalidation, which is worse.
```
grep:   (settings|config|catalog|dictionary|permissions|tariff|rate|currency).*await session
        cache\.set\(  ·  @cache  ·  lru_cache  — then look for the invalidation
Question: how often is this read, and how often does it change? if it is cached, WHO invalidates it and on
          which write path? a cache nobody invalidates is a bug with a delay fuse.
Fix:    cache read-mostly reference data with an explicit TTL **and**, where the write path is known,
        an invalidation on it. **TTL-only is legitimate** where the business accepts eventual consistency and
        the staleness window has been assessed — that is `roles/CACHE_STRATEGY.md`'s own rule and it wins here.
        What is never acceptable is a cache whose staleness nobody has decided about: **write the TTL and the
        acceptable staleness in the report, or remove the cache.**
```

### Block D — THE CLIENT SIDE (the load the browser carries)

**LD11. The client fetches everything and filters in memory.**
```
rg -t ts -t tsx: '\.(filter|sort|slice)\(' — then keep the hits applied to a fetched array
rg -U -t ts -t tsx: 'useQuery\([^\n]*\)[\s\S]{0,200}?\.(filter|sort)\('
TS:     const rows = data.filter(r => r.status === tab)     # ← the server returned all of them
Question: is the server-side filter missing, or was it just easier here? what is this at 50k rows —
          transfer, parse, and the main thread?
Fix:    the filter and the sort belong in the query string and in the index (LD3). Client-side filtering is
        legitimate only over a page that is already bounded.
```

**LD12. A long list with no virtualisation, and a growing collection with no ceiling in the UI.**
```
grep:   \.map\(  rendering rows  ·  <table  ·  infinite scroll without a windowing library
TS:     {rows.map(r => <Row key={r.id} .../>)}    # ← how many rows can `rows` be?
Question: what does this render at the floor's table size? does the page keep accumulating on scroll with
          nothing ever released?
Fix:    virtualise beyond ~100 rows (TanStack Virtual) · a page size the API enforces · and an empty/overflow
        state that is designed rather than discovered (`INTERFACE_CRAFT_CANON` I12 for the empty case).
```

---

## §2. SIGNATURE COMPARISON (quick recognition of bad → good)

| ✗ what gets typed | ✓ what it should be | Check |
|---|---|---|
| `(await session.execute(select(X))).scalars().all()` | `.limit(page_size)` + cursor, server-side max | LD1 |
| `response_model=list[XOut]` with no `limit` in the signature | `limit`, `cursor`, and a hard max the caller cannot raise | LD2 |
| `.order_by(X.some_column)` | + the index migration **in the same change** | LD3 |
| `select(func.count()).select_from(BigTable)` per request | keyset "has more", or an estimate, or a maintained counter | LD4 |
| `for row in rows: await session.get(...)` | `selectinload` / one IN-query / a dict before the loop | LD5 |
| a nested `list[ItemOut]` on a response model | eager-loaded in the parent query, or its own endpoint | LD6 |
| `for u in users: task.delay(u.id)` | chunked batches, or one job with a lease and progress | LD7 |
| `pandas` / PDF / image work inside a handler | a queued job with a JOB_PASSPORT and a progress cursor | LD8 |
| `pool_size=20` chosen per service | a total budget divided across every consumer, in vertebra 9 | LD9 |
| `cache.set(...)` with no invalidation path | TTL **and** invalidation on the write path, or no cache | LD10 |
| `data.filter(...)` over a fetched array | the filter in the query string and in the index | LD11 |
| `rows.map(...)` over an unbounded collection | virtualisation + an API-enforced page size | LD12 |

---

## §3. THE STOP-CONDITION

A change is not ready for handoff while **any** of these is true:

- a query in a request path has no ceiling (LD1) or a list endpoint has no pagination in its signature (LD2);
- a column the UI sorts or filters by has no index created in the same change (LD3);
- a query sits inside a loop, in code or in a serializer (LD5, LD6);
- work past the floor's p95 write target runs inside the request instead of a queue (LD8);
- a fan-out has no declared ceiling (LD7).

**And the question that stands above all twelve:** *what does this do at ten times the current data?* The load
floor's own stress test. If you cannot answer it for the code you just wrote, you have not finished writing it —
this failure class is invisible until it is expensive, and the answer is cheap only now.

---

## §4. REFERENCE MAP (where to go for the "why")

`roles/SYSTEM_DESIGN_PROTOCOL.md` — **THE LOAD FLOOR** (the five decisions these greps enforce) · Step 1 load
profile · Step 2 latency budget · Step 3 bottleneck · Step 6 queue design · Step 7 caching ·
`roles/DATABASE_RUNTIME_CANON.md` — locks, session guard rails, the connection budget as a number ·
`roles/ASYNC_AWAIT_REFLEX.md` — timeouts, the event loop, retries, queue liveness (this file does not duplicate them) ·
`roles/ASYNC_WORKERS_CANON.md` — JOB_PASSPORT and PIPELINE_PASSPORT for anything that leaves the request ·
`roles/CACHE_STRATEGY.md` · `roles/ARCH_SPINE_PROTOCOL.md` vertebra 9 (capacity as a number) · `roles/ROLE_PERF.md`
— the measured-symptom path, which is what this file exists to make unnecessary.
Version: 1.0 | 2026-09-04
