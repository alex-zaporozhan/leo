# SYSTEM_DESIGN_PROTOCOL.md
# System design under load protocol
# Invoked by @ARCH — mandatory: at project start and when designing critical modules
# Output: SYSTEM_DESIGN_[PROJECT].md in docs/artifacts/

> **Principle:** architecture without load analysis is design for one user.
> Measure before code, not after an incident.

---

## When invoked

**These "minimums" are floors on top of `roles/ROLE_ARCH.md` STEP 0B, never reductions of it.** 0B asks for the load profile, the latency budget, the bottleneck, the failure modes and the store/queue choice on every module it fires for; a row below that names fewer steps adds specificity, it does not licence dropping one 0B already required. Where the two differ, **the larger set wins.**

**WHEN, exactly: before the structure is drafted, not after it is drawn.** The order is
`DOMAIN_MODEL` (Law 42 — what is true) → **this protocol** (what the load is) → `ARCH_SPINE` (what the structure
is). A spine drafted before the load profile encodes an assumption nobody stated; the twelve vertebrae then get
answered against a scale nobody chose.

| Trigger | Mandatory | The action it produces |
|---------|-----------|------------------------|
| New project start (any mode) | yes | Steps 1–7 in full → `docs/artifacts/SYSTEM_DESIGN_[PROJECT].md`; Steps 8–9 answered or marked `N/A + reason`. The load profile takes the floor below if the business has no figures |
| New module with financial operations | yes | Steps 1, 2, 5 minimum. Money paths get an explicit peak-concurrency row and a named behaviour under contention |
| New module with real-time (WebSocket, SSE, push) | yes | Steps 1, 2, 4 **and Step 8 (real-time architecture)**. Connection count is a capacity number, not a feature |
| New module with large lists or search | yes | Steps 1, 3 minimum. Pagination, index coverage and the N+1 answer are written before the endpoint exists |
| Change of storage or queue | yes | Steps **1**, 3, 4, 6 + `DATA_STORE_SELECTION` — Step 1 *is* the load profile, and this row's own point is that a store change without one is a preference, not a decision |
| Adding AI/LLM to a critical path | yes | Steps 1, 2, 5 minimum. Provider latency and rate limits are the budget; the fallback on provider failure is named |
| Typical CRUD without concurrency or growth | no — mark N/A in artifact | **`N/A + the reason` is a written answer, not silence.** "No load analysis" and "load analysis says this is fine" look identical afterwards, and only one of them is a decision |

---

> **[v6.23] Step results are pulled into the spine:** Steps 1–4 → vertebra 9 (capacity), Step 5 → vertebra 10
> (failure/radius), Step 6 → vertebra 8 (JOB_PASSPORTS). Artifact `docs/artifacts/ARCH_SPINE_*` per
> `roles/ARCH_SPINE_PROTOCOL.md` — mandatory companion to ADR (Law 31); without it the steps remain an investigation.

## Step 1: LOAD PROFILE

**THE LOAD FLOOR — take this verbatim when nothing is declared.** The absence of a number is not a licence to
design for one user, and it is not a licence to stop and wait for the business. It is a licence to take the floor,
exactly as `VISUAL_CRAFT_CANON` §11 works for colour and `MOTION_CRAFT_CANON` §1 for movement. **Write the floor
into the artifact, marked `FLOOR — not measured`, and continue.** A figure that later arrives from the business
replaces it; a figure that never arrives has still been designed against.

```
--- THE FLOOR (internal / B2B tool, unless the project says otherwise) ---
Concurrent active users      50          peak 200          growth ×10 in 12 months
Requests/sec                 20 avg      100 peak          read/write 80/20
Main table                   100k rows now, ×5 per year
p95 latency target           read 300ms · write 800ms · report 3s (and a report over 3s is a job, not a request)
Payload                      ≤200KB per response; anything larger is paginated or streamed
--- FOR A PUBLIC-FACING PRODUCT, raise to ---
Concurrent active users      500         peak 5000 (a campaign, a mention, a seasonal spike)
Requests/sec                 200 avg     2000 peak
Main table                   1M rows now, ×10 per year

--- WHAT THE FLOOR ALREADY DECIDES, so that nobody re-decides it per module ---
Every list endpoint is paginated. There is no unbounded SELECT in a request path.
Every filter and sort column a UI exposes has an index, or the UI does not expose it.
Every outgoing call has a timeout and a declared behaviour on timeout (Law 31).
Anything over the p95 write target moves to a queue with a JOB_PASSPORT (Law 30), not to a longer timeout.
The connection budget is summed across API + workers + cron + migrations, not per process.
```

**The floor is enforced at the keyboard by `roles/LOAD_REFLEX.md`** — LD1–LD12, literal greps @DEV runs over the diff before handoff. Each of the five decisions above has a check that fires on it; a floor with no reflex is a number in an artifact that never reaches the hand typing `.all()`.

**Growth ×10 is the floor's own stress test:** multiply the numbers by ten and name what breaks first. That one
sentence is the whole point of the step — a design that has never been asked what breaks first has not been designed.

If real numbers exist, use them. "We don't know" is not an answer; use domain data from ROLE_DOMAIN_EXPERT.md.

```
## Load Profile

### Users
Concurrent active:          [number or range]
Peak load:                  [when and how many — Monday morning, month-end, sale]
Growth in 12 months:        [×N from current]
Geographic distribution:    [single region / multi-region]

### Traffic
Requests/sec (average):  [RPS]
Requests/sec (peak):     [RPS peak]
Read/write ratio:        [e.g. 80/20]
Payload size (average):  [KB]

### Data
Rows in main table now:     [N]
Row growth per year:        [N/year]
DB size in 2 years:         [GB]
Hot data (read frequently): [%] of all data

### Critical operations
| Operation | RPS | Latency target p95 | Peak concurrency |
|----------|-----|-------------------|------------------|
| [operation] | [N] | [ms] | [N users simultaneously] |
```

---

## Step 2: LATENCY BUDGET

For each critical endpoint, distribute the permitted latency across layers.
If the sum of layers exceeds the target — revise the architecture before coding.

```
## Latency Budget

| Endpoint | Target p95 | Network | API logic | Cache | DB query | External |
|---------|-----------|---------|-----------|-------|----------|---------|
| POST /bookings | 300ms | 20ms | 30ms | — | 200ms | 50ms |
| GET /schedule | 200ms | 20ms | 10ms | 150ms | 20ms | — |

### Layer benchmarks
- Network (client → server): 10–30ms within region; 50–150ms cross-region
- API logic (business logic without IO): ≤ 20ms; if more — profile the code
- Cache hit (Redis): 1–5ms; miss: add DB query cost
- DB query (simple): 5–50ms; complex JOIN / aggregate: 50–500ms
- External API (payment, SMS, AI): 100–10000ms — always async or with timeout
- AI/LLM generation: 500–10000ms — never in synchronous user-facing path

### Rule: if External > 10% of target → async
Any external call more expensive than 10% of target latency is moved to a queue.
The user receives 202 Accepted; the result comes via webhook or polling.
```

---

## Step 3: BOTTLENECK ANALYSIS

Back-of-envelope calculation: for each critical path, find the slowest component.

```
## Bottleneck Analysis

### Throughput reference

PostgreSQL:
- One connection: ~1,000 simple queries/sec
- Pool of 20 connections: ~20,000 queries/sec
- Complex JOIN on 10M rows: 50–500ms → max 2–20 RPS per connection
- Conclusion: at > 500 RPS for complex queries → cache is mandatory or read replica

Redis (single-threaded):
- ~100,000 operations/sec; with pipeline — ~1,000,000
- Bottleneck is memory, not RPS
- Conclusion: Redis is rarely a bottleneck for RPS; more often for memory

FastAPI/uvicorn (Python async):
- One worker: ~1,000–5,000 async RPS (without event loop blocking)
- CPU-bound operation blocks the event loop → move to threadpool/Celery
- Conclusion: CPU-bound = enemy of async; profile before scaling

Celery:
- IO-bound tasks with concurrency=10: 50–200 tasks/sec
- CPU-bound (ML, processing): concurrency = number of CPU cores
- Conclusion: heavy tasks (AI, import) → separate pool with low concurrency

Kafka (one partition):
- ~100,000 msg/sec or ~50MB/sec
- Consumer lag — primary health metric
- Conclusion: Kafka is chosen not for speed, but for guarantees and replay

Go / Java Spring Boot:
- Go: ~50,000–100,000 RPS (HTTP) on 4 cores; goroutines are cheap (~8KB vs 1MB thread)
- Go memory: ~5MB per 1,000 goroutines → easily holding 100K concurrent connections
- Spring Boot (WebFlux/reactive): ~20,000–50,000 RPS on 4 cores
- Spring Boot (traditional MVC + virtual threads Java 21): ~15,000–40,000 RPS
- Conclusion: at > 10,000 RPS Python → Go for simple high-throughput; Java for complex business logic

NestJS / Node.js:
- Single-threaded event loop: ~10,000–30,000 async RPS
- CPU-bound blocks the same as Python → worker_threads for CPU
- Conclusion: comparable with FastAPI; preferable for full-stack TypeScript teams

GraphQL (specifics):
- N+1 problem on resolvers is critical: one GraphQL request → N SQL queries
- Solution: DataLoader (batching) — mandatory for any nested resolvers
- Query complexity: limit the maximum query depth (depth limit ≤ 10)
- Query cost analysis: calculate query cost before execution (apollo-server)
- Conclusion: GraphQL without DataLoader = guaranteed N+1; verify in @QA_ARCH

### Project bottleneck table
| Component | Current RPS | Ceiling | At 10x load | Solution |
|-----------|------------|---------|-------------|---------|
| [component] | [N] | [N] | [bottleneck?] | [solution] |
```

---

## Step 4: SCALING STRATEGY

```
## Scaling Strategy

### Stateless (horizontal scaling)
- API servers (FastAPI, Spring Boot, Go, NestJS)
- Celery workers
- gRPC services

### Stateful (requires a special strategy)
- PostgreSQL: primary/replica; connection pooling (PgBouncer at > 100 connections)
- Redis: Sentinel for HA; Cluster at > 100GB or > 100K RPS
- Kafka: partitioning by entity_id for ordered processing

### Horizontal scaling (Kubernetes HPA)
| Component | Metric | Min replicas | Trigger |
|-----------|--------|-------------|---------|
| API | CPU / RPS | 2 | CPU > 70% or RPS > 80% of calculated |
| Celery | Queue lag | 2 | Lag > 30 sec |
| WebSocket | Connections | 2 | Connections > 500 per pod |

### Connection Pool Planning
Total connections = (API_workers × per_worker) + (celery × 2) + overhead
Rule: total ≤ max_connections × 0.8 (default: 100 × 0.8 = 80)
If exceeded → PgBouncer (transaction pooling mode) is mandatory

### Kubernetes Resource Requests/Limits (benchmarks)
| Service | CPU request | CPU limit | Memory request | Memory limit |
|---------|------------|---------|---------------|-------------|
| FastAPI | 100m | 500m | 256Mi | 512Mi |
| Celery worker | 250m | 1000m | 512Mi | 1Gi |
| Spring Boot | 500m | 2000m | 512Mi | 2Gi |
| Go service | 50m | 200m | 64Mi | 128Mi |
| NestJS | 100m | 500m | 256Mi | 512Mi |
```

---

## Step 5: FAILURE MODES

Mandatory analysis for each critical component.

```
## Failure Modes

| Component | What happens on failure | System behaviour | Solution |
|-----------|------------------------|-----------------|---------|
| PostgreSQL primary | Write is impossible | 503 | Read replica; circuit breaker |
| Redis | Cache unavailable | Increased latency; fallback to DB | Graceful degradation |
| Kafka | Events not delivered | Loss / accumulation | Retry buffer; DLT |
| Celery broker | Tasks not enqueued | Async operations not executed | Sync fallback for critical ones |
| Payment system | Payment fails | User sees error | Idempotency key; retry; webhook |
| AI/LLM provider | Generation unavailable | Feature degradation | Fallback provider; cached response |

### Checklist for every external call
- [ ] Timeout is explicit (not infinite)
- [ ] Retry with exponential backoff + jitter
- [ ] Fallback (stub / cached / degraded mode)
- [ ] Circuit breaker on systemic provider failure
- [ ] Alert when error rate > threshold
- [ ] Degraded behaviour documented and clear to the user
```

---

## Step 6: QUEUE DESIGN

> **[v6.22] Execution discipline — `roles/ASYNC_WORKERS_CANON.md`.** This step selects the broker, topology
> and sizes; the BEHAVIOUR CONTRACT of tasks (laws AW-1…10: cooperative cancellation, idempotency, bulkhead,
> heartbeat/stalled, backpressure), pattern catalogue and crash tests T-A…T-G — in the canon. Step 6 output
> is supplemented by the artifact: `docs/artifacts/JOB_PASSPORTS_[PROJECT].md` — passport of each task type
> (§4 of the canon; without a passport a task is not written).

```
## Queue Design

### Solution selection

| Criterion | Celery + Redis | Redis Streams | Kafka |
|----------|--------------|--------------|-------|
| Setup complexity | low | low | high |
| Throughput | ~10K tasks/sec | ~100K msg/sec | ~1M msg/sec |
| Guarantees | at-least-once | at-least-once | exactly-once (idempotent) |
| Event replay | no | yes (retention) | yes (any period) |
| Ordering | no | per stream | per partition |
| Monitoring | Flower | Redis CLI | Kafka UI / Confluent |
| When to choose | Background jobs, periodic | < 100K msg/sec, replay needed | > 100K msg/sec, audit log, event sourcing |

### Celery Queue Priority
```python
# Priority queues
CELERY_TASK_ROUTES = {
    'app.tasks.payments.*': {'queue': 'critical'},
    'app.tasks.notifications.*': {'queue': 'default'},
    'app.tasks.reports.*': {'queue': 'low'},
}
# critical: concurrency=4, prefetch=1 (finance — do not parallelise)
# default:  concurrency=8, prefetch=4
# low:      concurrency=2, prefetch=8
```

### Naming
Celery queues:  [priority].[domain].[operation]
  critical.payments.process
  default.notifications.send
  low.reports.generate

Kafka topics:   [domain].[entity].[event]
  payments.invoice.created
  bookings.appointment.cancelled

Redis keys:     v[N]:[domain]:[entity]:[id]
  v1:clinic:schedule:123
  v1:report:daily:456:2026-05-22

### Dead Letter Queue / Topic
- Task failed N times → to DLQ (N configurable, default: 3)
- DLQ of critical queues: alert on every message
- DLQ of standard queues: alert when > 10 messages
- Manual replay — via admin endpoint or Flower/Kafka UI
```

---

## Step 7: CACHING ARCHITECTURE

Detailed policy — in CACHE_STRATEGY.md. Here — the architectural level.

```
## Caching Architecture

### Caching levels
L1: In-process LRU — reference data, configs
    TTL: 5–60 min | Size: ≤ 100MB per process | Invalidation: TTL only

L2: Redis — sessions, aggregates, results of expensive queries
    TTL: seconds–hours | Invalidation: event + TTL

L3: CDN — statics, public GET (if applicable)
    TTL: hours–days | Invalidation: cache-busting

### Redis Memory Budget
Total budget = total_keys × avg_key_size × 1.5 (safety factor)

| Category | Expected keys | Avg size | Total |
|----------|--------------|---------|-------|
| Sessions | [N] | 2KB | [...] |
| Schedule cache | [N] | 10KB | [...] |
| Report cache | [N] | 50KB | [...] |
| Celery broker | [depth × msg] | 1KB | [...] |
| TOTAL | | | [...] |

Rule: if total > 70% of maxmemory → two Redis instances
  (separate for cache, separate for Celery broker/sessions)

### Redis Data Structures — what to use when
| Use case | Structure | Why |
|---------|-----------|-----|
| Session data | Hash | Partial field updates |
| Online users | Set | O(1) add/remove/member |
| Leaderboard / ranking | Sorted Set | Ranking by score |
| Rate limiting, counters | String + INCR | Atomic increment |
| Queue (simple) | List | LPUSH/RPOP |
| Event stream | Stream | Persistent, consumer groups |
| Distributed lock | String + SET NX PX | Atomic set with TTL |
| Real-time pub/sub | Pub/Sub | Fire-and-forget notifications |

### Eviction Policy
- Response cache: allkeys-lru
- Celery broker: noeviction (task loss is critical)
- Sessions: volatile-lru (evict only keys with TTL)
```

---

## Step 8: REAL-TIME ARCHITECTURE

For modules with WebSocket, SSE, push notifications.

```
## Real-time Architecture (N/A if not applicable)

### Protocol selection
| Protocol | When | Limitations |
|---------|-------|-------------|
| WebSocket | Bidirectional exchange (chat, collaboration) | Stateful; scaling via Redis Pub/Sub |
| SSE | One-directional stream (notifications, progress) | Easier to scale; text only |
| Long Polling | Fallback | High server load |
| FCM/APNs Push | Mobile outside active session | Latency; platform dependency |

### WebSocket + Horizontal Scaling via Redis Pub/Sub
User A → WebSocket → Instance 1 (subscribes to "user:A" channel)
Event → Instance 2 → Redis PUBLISH "user:A" payload
Redis → Instance 1 → WebSocket → User A

Rule: WebSocket service — a separate Deployment in Kubernetes
  (stateful connections; cannot HPA like stateless API)

### Mobile Push Architecture
Trigger → NotificationService → determine channel (FCM/APNs/SMS/Email)
  → rate limiting (anti-spam)
  → send via provider
  → save to notification_log (inbox in app)
  → update delivery status via callback

Rule: push is a signal, not data.
  Data is obtained by a pull request after receiving the push.
```

---

## Step 9: MOBILE API DESIGN

For projects with iOS/Android clients.

```
## Mobile API Design (N/A if not applicable)

### Key principles
- Offline-first: critical data cached locally (SQLite/Room/CoreData)
- Cursor-based pagination: not offset (breaks with parallel inserts)
- PATCH instead of PUT: mobile saves traffic; partial updates
- Compression: gzip/brotli mandatory
- Versioning: X-App-Version header in every request

### API Versioning for mobile
Problem: users update the app slowly; an old version lives a year or more.

Rules:
1. X-App-Version: [version] header in every request
2. API supports N-1 versions minimum
3. Deprecation: Sunset: [date] header 90 days before removal
4. Force update: 426 Upgrade Required on critical mismatch

### Authentication
- Access token: 15 min TTL
- Refresh token: 30 days; stored in Secure Enclave (iOS) / Keystore (Android)
- Biometric: local unlock without transmitting data to the server
- Certificate pinning: for finance and medicine

### Offline Sync Strategy
Choose one approach before development starts:

Last-Write-Wins: simplest; suitable when conflicts are rare
Operational Transform: for collaborative editing (Google Docs-like)
CRDT: for distributed state without conflicts (complex to implement)
Server-authoritative: mobile only reads; writes only while online (simplest)
```

---

## OUTPUT ARTIFACT

Save to: `docs/artifacts/SYSTEM_DESIGN_[PROJECT].md`

```markdown
# System Design: [Project / Module]
> Date: [date] | Mode: [SAAS/ENTERPRISE/HIGHLOAD] | @ARCH

## 1. Load Profile         [Step 1]
## 2. Latency Budget       [Step 2]
## 3. Bottleneck Analysis  [Step 3]
## 4. Scaling Strategy     [Step 4]
## 5. Failure Modes        [Step 5]
## 6. Queue Design         [Step 6 — N/A if no queues]
## 7. Caching Architecture [Step 7]
## 8. Real-time Architecture [Step 8 — N/A if no real-time]
## 9. Mobile API Design    [Step 9 — N/A if no mobile]

## ADR: key decisions
| Decision | Alternative | Reason for choice |
|---------|-------------|-------------------|
| Redis Streams instead of Kafka | Kafka | Throughput < 10K/sec; simpler to operate |

## Open questions (resolve before DEV_PROMPTS)
- [ ] [question]: needed for [why]
```

---

## Role system connections

| Role | How they use it |
|------|----------------|
| @ARCH | Creates the artifact; incorporates results into spine and DEV_PROMPTS |
| @LEAD | Approves Load Profile numbers; makes trade-off decisions |
| @PERF | Uses Latency Budget as baseline for audit |
| @QA_ARCH | Verifies presence of Failure Modes for critical components |
| @AI_ENGINEER | Supplements steps 3 and 5 with data from RAG_PASSPORT |
| @OPS | Receives Scaling Strategy for Kubernetes configuration |

---

Reference: roles/DATA_STORE_SELECTION.md · roles/CACHE_STRATEGY.md · roles/ROLE_ARCH.md · roles/ROLE_PERF.md · roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md §9 · roles/STACK_SELECTION.md
Version: 1.2 | 2026-07-07
