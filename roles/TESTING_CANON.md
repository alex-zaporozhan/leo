# Testing and Quality Assurance Canon

**Purpose:** the common working base of *how to think about tests* — not only "what to check" at the end, but **what to test, at which risk tier, by which technique, and starting when**. @ARCH uses it to design tests (ARCH_TESTS) and to demand testability; @QA uses it to build a **designed, multi-tier suite** (not a happy-path smoke) and to run the final gate; @DEV uses it for task-level tests; @AUDITOR uses it for typical failure causes; @QA_ARCH cross-references the check categories; @PENTEST breaks the floor this canon builds.

> **The problem this version fixes.** The suite used to start as a happy-path smoke and grow only *reactively* — a new test per discovered bug. That is testing as archaeology. This canon makes the suite **designed up front**: the risk of each path is classed before code (§2A), the negative/edge cases are mandatory on the first wave (§3.7), and QA influences the architecture so the thing is testable at all (§6). @PENTEST comes at the end regardless (`roles/SECURITY_GATE_PROTOCOL.md`) — the point is not to hide holes from it but to raise the floor so it finds only the genuinely non-obvious (§7).

---

## 1. Who uses what as base

| Role | Usage |
|------|-------|
| **@QA** | Builds the designed suite (§2A tiers · §2B techniques · §3 categories · §3.7 negative baseline) and runs the final gate. Participates in planning (§6). |
| **@ARCH** | ARCH_TESTS from these categories, extended per project (endpoints, fixtures, E2E). Demands **testability** as an architectural property (§6). |
| **@DEV** | Task-level tests follow the project ARCH_TESTS + the DEV role's Level 1/2/3. A test without an assertion on a concrete value is not a test. |
| **@AUDITOR** | Typical failure causes during diagnosis (§4). |
| **@QA_ARCH** | Cross-references §3.1 (empty state), §3.2 (validation), §3.3 (API contract) as a supplement to its audit vectors. AI contour → Vector 11. |
| **@PENTEST** | Attacks the reliability floor this canon builds; a finding @QA "should" have caught permanently expands the QA suite (§7 · `roles/SECURITY_GATE_PROTOCOL.md`). |

---

## 2. Key test types (and the pyramid)

Design the suite as a **pyramid**, not an ice-cream cone: many fast tests low, few slow tests high.

- **Unit** — pure business logic, calculations, state machines, validators. Fast, deterministic, many. Not required for trivial CRUD.
- **Contract / API** — request → service → DB → response; the response contract (fields, types, status codes); boundary and empty data. The seam between client and server.
- **Integration** — a real DB / broker / cache; migrations apply; transactions and isolation behave.
- **E2E** — one or two end-to-end critical journeys (booking → payment; entity create → appears in list). Few, expensive, on the critical paths only.
- **Smoke** — the thinnest subset: health, login, one main flow — runs after every deploy.
- **Regression** — the golden path plus **every previously-found bug**, kept forever (append-only). Green CI must fail when a critical path breaks.
- **Property-based** (where logic is rich) — assert invariants over generated inputs (e.g. "reversing a posting returns the balance to its prior value for any amount").
- **Load / crash** — resilience, not throughput (owned with @PENTEST CRASH_TEST).

---

## 2A. Risk tiers — T0 / T1 / T2 / T3 (class every path BEFORE writing tests)

Not every path deserves the same rigour, and none deserves *none*. Class each user-facing path and each endpoint by **blast radius** — what breaks, and for whom, if this fails. The tier sets the mandatory coverage. This classing happens **at planning** (§6), recorded in QA_TEST_STRATEGY, so the suite is designed, not discovered.

| Tier | Blast radius — "if this breaks…" | Mandatory coverage (from the FIRST wave) |
|------|----------------------------------|------------------------------------------|
| **T0 CRITICAL** | data loss · money wrong · auth/tenant boundary broken · irreversible destructive action · cannot log in | Happy path **+ all negative/edge (§3.7) + concurrency (2+ actors) + idempotency + rollback + isolation (other tenant)**. E2E on the journey. A **regression** test locking each fixed defect. No release with a red T0. |
| **T1 HIGH** | a core scenario fails (save/submit/list) · a form loses data · a status desync · a report shows a wrong number | Happy path **+ negative + boundary + empty-state**. API contract asserted on the body, not just the code. Regression on fixes. |
| **T2 MEDIUM** | partial breakage with a workaround · a non-core screen degraded · a rare edge | Happy path + the highest-risk 1–2 negatives. Covered if it touches shared code. |
| **T3 LOW** | cosmetic · rare · easily reversible | Smoke / manual check. Not gold-plated; not zero. |

**Rules:**
- **Anything on the SECURITY SURFACE (S1–S12, `roles/SECURITY_GATE_PROTOCOL.md`) is at least T1**, usually T0. Security paths do not get happy-path-only treatment.
- **Money, auth, tenant isolation, and any irreversible action are T0 by definition** — no debate.
- A path's tier is recorded once (QA_TEST_STRATEGY) and challenged only with a reason. "It's probably fine" does not lower a tier.

---

## 2B. Test-design techniques (how to derive cases instead of guessing)

Happy-path-only happens when nobody applies a technique to *derive* the cases. Pick the technique from the shape of the input/logic:

| Technique | Use it when | It forces you to test |
|-----------|-------------|-----------------------|
| **Equivalence partitioning** | an input has classes that behave the same | one representative per class (valid / invalid / boundary class) — not ten from the same class |
| **Boundary-value analysis** | numeric / length / date ranges | min−1, min, min+1, max−1, max, max+1; empty; overlong; zero; negative |
| **Decision table** | output depends on a combination of conditions (role × status × flag) | every meaningful combination, not just the common one — reveals the "impossible" state that is reachable |
| **State-transition** | an entity has a lifecycle (FSM) | every legal transition **and** the illegal ones (jump to a forbidden status; act on a cancelled entity; double-transition) |
| **Pairwise** | many independent parameters (combinatorial explosion) | all pairs of values with far fewer cases than the full cross-product |
| **Negative / adversarial** | any input crossing a trust boundary | empty, null, wrong type, overlong, special chars, injection strings, negative numbers, the other tenant's id, the double click, the request in the wrong order |
| **Property-based** | rich logic with an invariant | the invariant holds over generated inputs (round-trips, reversals, ordering, idempotency) |
| **Concurrency** | a shared mutable resource | 2..N actors at once → exactly-once outcome (no double-book, no double-spend, no double-account) |

**The floor:** for every T0/T1 path, at minimum apply **boundary-value + negative + state-transition** (and concurrency where a shared resource exists). This is the anti-happy-path mandate, made concrete.

---

## 3. Check categories (base list)

The minimum categories to account for. Specific steps and priorities are set in roles (@QA Pillars, @ARCH ARCH_TESTS).

### 3.1 Empty state

- **Essence:** errors that naturally arise when the DB lacks required data (no clinic, patients, doctors) must not return 500.
- **Rule:** API returns **4xx** (e.g. 404) with **clear text** in `detail`; user-facing messages in one place (e.g. `user_messages.py`); the frontend shows `detail` + a **universal plain-language hint** with no code.
- **No technical jargon for the end user.** Messages must not contain commands/paths/script names (docker, seed_demo_data). Required: **why** it happened and **what to do**, in plain words ("Add the first doctor"; "The report needs doctors, patients and at least one completed appointment"; "Once you add the data, the error will disappear"). Seed commands live in developer docs/logs only.
- **Checks:** empty DB after deploy, missing clinic/doctors/patients — no 500; a clear message and hint without code.

### 3.2 Empty fields and validation

- Required fields validated on backend **and** in forms; invalid input → clear error, not a crash.
- Boundary values: empty string, too-long text, special characters — handled without 500 and without a blank screen.
- Forms: cancel/back does not save garbage; on save — explicit success or error.

### 3.3 API and contracts

- Health-check and critical endpoints respond without 500 on a valid request.
- Invalid request (bad params, no permission) → 4xx with clear `detail`, not 500.
- request → service → DB → response chain does not lose data on boundary values (empty list, single record, limits).

### 3.4 Authorisation and access

- Protected endpoints/pages without a token/session → 401/403 or redirect to login.
- After login only permitted sections accessible; logout invalidates the session.
- No information leakage in the error body ("user not found" vs "wrong password" must not differ).

### 3.5 Data and volumes

- Empty lists/calendar → empty state or message, not a crash.
- Large lists → pagination or limits; no N+1 on typical screens.
- Transactions: create/update atomic; on conflict — clear error, not a "corrupt" record.

### 3.6 Environment and configuration

- Missing required env variable → explicit error at startup, not a runtime error in an obscure place.
- Migrations apply and roll back without breaking the application.

### 3.7 Negative & adversarial baseline (the anti-happy-path mandate)

**Mandatory from the FIRST wave for every T0/T1 path — not deferred until a bug appears.** For each such path, the suite includes, at minimum:

```
□ Empty / null / missing required field           → 4xx with detail, no crash
□ Wrong type / overlong / special chars / unicode  → handled, no 500, no blank screen
□ Boundary values (min−1, min, max, max+1, zero, negative) per §2B
□ Illegal state transition (act on cancelled / jump a step / double-submit) per §2B
□ Unauthorised: no token → 401/403; other tenant's id → 404 (not 403) per §3.4
□ Concurrency (if a shared resource): 2 requests at once → exactly-once outcome
□ Idempotency (if a mutating external effect): repeat → same result, one effect
□ Failure of a dependency (DB/cache/external) → graceful message, not a stack trace
```
A first-wave suite that covers only the happy path for a T0/T1 path is **incomplete** — @QA does not sign off, and this is a GATE-4 QA blocker (`roles/LEAD_PRODUCT_GATE_PROTOCOL.md`). New tests are added when bugs appear **as well** — but the baseline above exists **before** the bugs do.

---

## 4. For @AUDITOR: typical causes during diagnosis

When searching for a failure root cause by layers (browser → network → server → DB), include among hypotheses:

- **Empty DB state** — no clinic/patients/doctors; endpoint expects data and crashes. Fix: 4xx + message per §3.1 (no code in user text).
- **Validation** — invalid request body/params; server must return 4xx with `detail`, not 500.
- **Authorisation** — 302 to login or 401/403 instead of expected 200.
- **API contract** — client expects a field/format absent from the response; check OpenAPI/docs and the actual response.
- **Logs** — first step: server logs and browser console (`roles/LOGGING_OBSERVABILITY_PROTOCOL.md`).

---

## 5. The testing lifecycle — A→Z (test is a phase of the work, not a coda)

```
A. PLAN (at planning, with @ARCH — §6)
   → risk-class every path/endpoint (§2A) → QA_TEST_STRATEGY_[MODULE].md
   → name the testability the architecture must provide (seams, ids, clock, isolation, observability)
   → for the SECURITY SURFACE: the Level-1 security tests are named in the Security Contract (S-0)
B. DESIGN (before/with code)
   → derive cases with §2B techniques per tier; write the negative baseline (§3.7) as intent, not afterthought
C. IMPLEMENT
   → @DEV writes Level-1 (+2/3) task tests; @QA/@ARCH grow the tiered suite; a test asserts on a concrete value
D. EXECUTE
   → run per tier; T0 red = stop; record status, not "tested"
E. REGRESSION (append-only)
   → every fixed defect leaves a permanent test; CI fails when a critical path breaks
F. ADVERSARIAL (the gate)
   → @QA final pass (Pillars) → @PENTEST S-Wave breaks the floor (`roles/SECURITY_GATE_PROTOCOL.md`)
G. MAINTAIN
   → the suite is a living asset; flaky tests are fixed, not muted; coverage only grows with the surface
```

---

## 6. QA at planning — testing shapes the product, not just verifies it

QA and @ARCH decide **testability as an architectural property**, before code. A thing that cannot be tested cheaply will not be tested, and will rot. QA has standing to influence product/architecture decisions here:

- **Seams for testing** — inject the clock, the external client, the random source; do not hard-wire `datetime.now()` or a live provider into business logic (otherwise time and failure paths are untestable).
- **Deterministic identity** — stable ids/keys so a test can assert the exact record; idempotency keys make "did it run twice?" testable at all.
- **Test-data isolation** — a way to seed and tear down without touching production; a tenant boundary that a test can cross to prove it holds.
- **Observability for assertions** — the events/logs/metrics a test needs to assert an outcome (not just a 200) exist by design.
- **Product influence** — QA may say: "this flow is not testable without an idempotency key / an audit event / a status field — add it." That is a legitimate architectural ask, not scope creep. A flow whose correctness cannot be observed is a flow whose correctness is unknown.

Output: `docs/artifacts/QA_TEST_STRATEGY_[MODULE].md` — the risk map (§2A), the technique plan (§2B), the negative baseline scope (§3.7), the testability asks, the regression plan. Feeds DEV_PROMPTS alongside the Domain Checklist.

---

## 7. Relationship to @PENTEST — the floor and the break

@QA builds the **reliability floor**: functional correctness, the negative/edge baseline, exactly-once behaviour, the regression net. @PENTEST then **tries to break that floor** adversarially (`roles/ROLE_PENTEST.md`, `roles/SECURITY_GATE_PROTOCOL.md`). They are complementary layers, not duplicates:

- QA's negative tests ask "does it fail *gracefully*?"; @PENTEST asks "can I *make* it fail in my favour?".
- The point of knowing @PENTEST comes is **not** to hide holes — it is to build the floor honestly so the gate finds only the genuinely non-obvious. A pentest finding that a negative/isolation test *should* have caught is a signal the floor was too low: that class of test is **added to the QA baseline permanently** (§3.7), and if it recurs, it is a REFLEX/@EVOLVE candidate.
- Security-surface paths (§2A rule) are T0/T1 with the full negative + isolation + concurrency baseline **before** they ever reach @PENTEST.
- **Tier ≠ severity.** The QA tier (T0–T3) sets how hard a *path* is tested — by blast radius, decided at planning. @PENTEST's 🔴🟠🟡🟢 rates how critical a *finding* is — decided at the gate. They do not map onto each other: a T0 path may yield only a 🟢, a T2 path a 🔴.

---

## 8. Connection to other documents

- **`roles/ROLE_QA.md`** — the operational role: builds the tiered suite, participates in planning, runs the Pillars gate.
- **`roles/ROLE_ARCH.md`** — ARCH_TESTS + testability as an architectural property (§6).
- **`roles/ROLE_QA_ARCH.md`** — §3.1/§3.2/§3.3 as audit supplements; Vector 11 for the AI contour.
- **`roles/ROLE_AUDITOR.md`** — typical failure causes (§4).
- **`roles/ROLE_PENTEST.md`** · **`roles/SECURITY_GATE_PROTOCOL.md`** — the adversarial layer above the floor; the security-test baseline for surface paths.
- **`roles/DEV_EXECUTION_PASSPORT.md`** — detailed testing patterns by task type (@DEV).
- **`roles/DATA_INTEGRITY_CANON.md`** §10 (T-H) · **`roles/ASYNC_WORKERS_CANON.md`** §7/§14 (T-A…G, T-I) — the concurrency/resilience test series.
- Implementation: user-message constants in one place (e.g. `user_messages.py`); "no data" handling in routers per §3.1; QA_TEST_STRATEGY per §6.

Version: 2.0 | 2026-07-18
