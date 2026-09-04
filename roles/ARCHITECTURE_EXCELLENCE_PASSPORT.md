# ARCHITECTURE_EXCELLENCE_PASSPORT — Target Architecture Passport (10/10)

> **Roles:** @ARCH (design and invariants) · @QA_ARCH (evidence, risks, DoD) · @LEAD (priority and acceptance).  
> **Purpose:** single **universal** reference for **non-functional** maturity: security, durability, multi-tenancy, operations, delivery discipline.  
> **Passport vs project facts:** here — norms and checklists for any product; **specific KPIs, baseline and targets** for a specific team are maintained in `roles/NONFUNCTIONAL_SCORECARD.md` (or equivalent per repo convention).

**How to use:** @ARCH verifies new decisions against sections **3.1, 4–12, 15** before committing to `docs/artifacts/ARCH_*.md`. @QA_ARCH uses **§3 (including 3.1), §13–§15** and checklist **§14** as an extension to `.cursorrules` and DOMAIN_STANDARDS.

---

## 1. Why This Document

**Problem without a shared language:** NFR and "commercial maturity" are easy to interpret differently if criteria are scattered across chat history and old notes.

**Risk of expectation vs. fact divergence:** actual delivery discipline (CI, images, migrations) changes over time — it must be verified against **`roles/DOCKER_INFRA_PASSPORT.md`**, **`roles/MIGRATIONS_PLAYBOOK.md`** and the current workflow in `.github/workflows/`, not stale assumptions.

This passport **fixes the criteria for target maturity (10/10 north star)** and a **gap matrix** so that @ARCH and @QA_ARCH check the same plane; measurable team targets — in `roles/NONFUNCTIONAL_SCORECARD.md`.

---

## 2. Maturity Scale (explicit, project-wide)

| Score | Meaning |
|------|--------|
| **0–3** | Prototype: unpredictability, manual workarounds, no provable SLOs. |
| **4–6** | Working product for a limited scope; load growth = high risk. |
| **7–8** | Production for moderate load; basic metrics and backups "exist", evidence is partial. |
| **8.5** | Managed system: measurable KPIs, DR practices, quality gates, tenant-safety is verifiable. |
| **10** | Target north star: all of §8.5 **plus** stable improvement discipline, regression barriers for perf/security/data, formalised schema and API evolution without "accidentally breaking prod". |

> **Current working team target** may be phased (e.g. 8.5 first), but the **passport describes the full 10/10** as north star.

---

## 3. Weighted Evaluation Matrix (scorecard)

Overall maturity score — weighted sum of categories; **live tracking** — in `roles/NONFUNCTIONAL_SCORECARD.md`:

| Category | Weight | What @QA_ARCH proves |
|-----------|-----|-------------------------|
| Reliability & Resilience | 20% | Failures, retries, idempotency, queues, no "silent" losses |
| Security & Compliance | 20% | Tenant isolation, secrets, supply chain, PII, negative tests |
| Performance & Scalability | 15% | p95/p99 budgets, capacity, bottlenecks measured |
| Data Integrity & Transactions | 10% | Transaction boundaries, DB constraints, anti double-booking |
| Observability & Incident Response | 10% | Traces, alerts, runbook, MTTR measurable |
| Architecture & Code Quality | 10% | Layers, domain boundaries, no hidden "magic" in critical chains |
| AI Integration Quality | 10% | Timeouts, fallback, metrics, prompt/PII safety |
| Delivery Discipline (CI/CD, tests, release) | 5% | CI gates, image tags, migration safety |

### 3.1. Approximate KPI targets (set by @LEAD, not dogma)

For @QA_ARCH to say "verified by number" rather than "seems fine", the team sets target values in `roles/NONFUNCTIONAL_SCORECARD.md` or records them in ADR / `docs/artifacts/ARCH_*.md`. **Below — a typical 8.5+ target** for B2B SaaS / commercial contour (product-specific adjustment mandatory):

| Area | Target |
|---------|-----------|
| API availability (working window) | ≥ 99.5% |
| p95 of critical APIs (excluding heavy AI) | ≤ 300–500 ms |
| 5xx share on business-critical endpoints | < 1% |
| MTTR P1/P2 | ≤ 60 min (with runbook) |
| RPO / RTO | Documented and confirmed by drill |
| Security | 0 critical in SCA/images on release branch |
| Restore | ≥ 1 successful verified restore per period (schedule by @LEAD) |

---

## 4. Database and Data Model (10/10)

**Multi-tenancy**

- Every business entity with clinic/tenant isolation: explicit `clinic_id` / `tenant_id`, composite indexes `(tenant_id, …)`.
- Ban on "forgotten filter": critical reads/writes pass through a layer where the tenant is mandatory (code convention + negative tests).

**Integrity**

- Unique and partial indexes where business invariant exists (slots, idempotency, "one active record").
- Foreign keys and cascades are deliberate; soft delete does not break referential integrity (or an explicit "archive" policy).

**Migrations**

- **Expand → deploy → contract** rule: add nullable/new columns and dual-read first, then switch code, then remove the old.
- Every migration with a meaningful `downgrade`; destructive steps — in a separate release after old field reads are stopped.
- Ideally: a **separate step** "alembic upgrade" in the release, not only "on container start" without control (for 10/10 — controlled job / runbook).

**Backup and Recovery**

- Regular backup (logical/physical), **verified restore** (RPO/RTO documented, drill recorded).
- Backup encryption at rest — if required by environment/client.

**Evolution**

- Index audit against top queries; slow query budget; on growth — read-replica / partitioning as a deliberate ADR, not "accidental".

---

## 5. Transactions, Events, Queues (10/10)

**Synchronous path**

- Explicit transaction boundaries for chains "booking → payment → completion → side effects".
- `SELECT … FOR UPDATE` (or equivalent) on contested resources: slots, cashboxes, package deductions, task claim.

**Asynchronous path**

- **Outbox / transactional outbox** pattern (or equivalent): DB write and queue enqueue are atomic by intent; no scenario "record exists — event missing" without compensation.
- **Idempotency** of consumers: `idempotency_key`, `source_event_id`, deduplication.
- **Dead-letter / poison queue**: isolation policy, manual triage, metrics.

**Celery**

- `autoretry_for`, retry limits, error classification (transient vs permanent).
- Metrics: lag, task age, retry rate, final failures.

**Money, audit, consistency**

- Chains with money/cashbox/package movement: where possible — **reconciliation** (reporting vs operational tables) or periodic reconciliation-job; discrepancies — alert.
- **Immutable or append-only trail** for sensitive admin actions (who, when, what; retention sufficient for investigation and compliance — level fixed by @LEAD).

---

## 6. External Integrations and AI (10/10)

- Unified policy: **timeout, retry with backoff+jitter, circuit breaker** where appropriate; do not block the event loop with infinite waits.
- Errors — in **unified envelope** (see contract in ARCH), no stack leakage outward.
- **AI:** token/cost limits, fallback, logging without raw PII, metrics for success/timeout/provider_error/retry.

---

## 7. Security and Multi-tenancy (10/10)

- RBAC matrix + **negative** tests for prohibited actions.
- **Cross-tenant** tests: cannot get another tenant's data via API, job, or "leaking" join.
- Webhooks (payments, messengers): signature, replay protection, idempotent handling.
- Supply chain: dependency and image scanning; secrets from secret store only; rotation.
- Security headers, CORS, CSRF — per admin/PWA profile (ADR).
- **Privileged access** (support / "act as user"): only with explicit policy, TTL, two-factor per regulation and **full audit**; no "silent" tenant bypasses.
- **Abuse:** baseline contour — rate limiting, bot protection on public forms, suspicious pattern tracking (ADR on growing threats).

---

## 8. Observability and Operations (10/10)

- Structured logs: `trace_id`, `clinic_id`, actor, operation, result; PII masking.
- Prometheus metrics + **alerts**: error budget, queue lag, DB/Redis saturation, external dependencies.
- Distributed tracing (OpenTelemetry) on critical chains — target state 10/10.
- Runbooks: P1/P2, escalation, **game day** (Redis, DB, AI failure).
- **Service and dependency catalogue** (who depends on what: DB, Redis, AI provider, payment, messengers) — so that during an incident the architecture is not being discovered from scratch.

---

## 9. Performance and Capacity (10/10)

- Baseline p50/p95/p99 for an **agreed** list of APIs; separate read / write / AI.
- Load scenarios (k6/Locust etc.) + **regression** threshold (at least nightly).
- Connection pool profiles, worker concurrency, Redis memory; degradation plan under overload.

---

## 10. CI/CD and Delivery Discipline (10/10)

- **PR / main:** lint + backend tests, build + frontend tests; block on critical SCA/secret scan findings.
- Images: tag with **`git sha`** (mandatory), policy for `latest`.
- No "release without CI tests"; flaky tests = process defect.
- CD: at least a **staging** with smoke + manual gate to prod (or full automation — ADR).

---

## 11. Frontend and API Contract (10/10)

- Four UI states (Loading / Empty / Error / Success) on all user-facing lists.
- Error and type contract agreed with backend; on API change — version or feature-flag + compatibility period (ADR).
- **Accessibility (a11y):** target level (e.g. WCAG 2.1 AA for key flows) set in ADR; critical actions — keyboard operable, visible focus, meaningful labels.
- **Client matrix:** which browsers/modes are officially supported (and what is best-effort) — recorded, so @QA_ARCH doesn't argue against the wind.

---

## 12. What Is Often Missed Even in Good Roadmaps (supplement to archived documents)

| Topic | Why |
|------|--------|
| **ADR registry** | Decisions don't get lost in chats; "why this DB decision" is linked. |
| **API versioning and deprecation policy** | How we announce breaking changes, timelines, client compatibility. |
| **Feature flags / kill switch** | Quickly disable risky branches without schema rollback. |
| **FinOps / AI limits** | Cost and tokens as a managed budget, not a surprise. |
| **Right to erasure / anonymisation (GDPR-like)** | Procedures and technical hooks if PII is in the system. |
| **API contract tests** | Response schema doesn't silently drift between services/client. |
| **DB extension versioning** | pg_trgm etc. — recorded in migrations/docs. |
| **Unified rate limiting** | On public and sensitive endpoints. |
| **Evidence, not intent** | KPIs and drill results stored with code (QA reports, artifacts). |
| **SLO vs external SLA** | Internal error budgets ≠ client promises; separate in documentation. |
| **Dependency update policy** | Renovate/Dependabot, pinning, licences — not "we never update". |
| **Localisation and time zones** | If product is multi-language/multi-region — unified date contract (UTC+IANA). |
| **Periodic pen-test / security review** | At least a regulation under commercial contour. |

---

## 13. Summary: "What Is Missing for 10/10" (system mapping)

Below — not a single-sprint TODO, but a **maturity checklist** (prioritisation by @LEAD).

1. **CI quality gates** — tests, lint, security scan; fail on critical.  
2. **Image tags** — reproducible releases.  
3. **CD / staging smoke** — minimal manual pipeline with checks.  
4. **Restore drill** — documented success, RPO/RTO.  
5. **Retry/backoff/circuit breaker** — policy library for external calls and AI.  
6. **Transactional correctness** — chain map, tests for rollback/races.  
7. **Outbox / idempotency** — event → job → write without duplicates or losses.  
8. **Anti double-booking** — DB + code + regression parallel test.  
9. **Observability 2.0** — trace + log correlation + SLO-based alerts.  
10. **Tenant safety** — path audit and negative tests.  
11. **Perf baseline + budgets** — measured before optimisations.  
12. **AI reliability metrics** — treated like any other first-class external service.  
13. **KPIs** — target numbers and baseline measurement date recorded in `roles/NONFUNCTIONAL_SCORECARD.md` (§3.1).  
14. **Financial consistency + audit** — reconciliation/alerts on money; trail of sensitive actions.  
15. **Test pyramid on critical paths** — see §15; flaky test policy (do not ignore).

---

## 14. Role Checklists

### 14.1. @ARCH — before committing a decision to `docs/artifacts/ARCH_*.md`

- [ ] Tenant model and indexes agreed with §4.  
- [ ] Critical chains: transactions, locks, idempotency — described (§5).  
- [ ] External calls and AI: timeouts/retries — in specification (§6).  
- [ ] Migrations: expand/contract and rollout order (§4).  
- [ ] Observability: which metrics/logs are mandatory for the new chain (§8).  
- [ ] Risks for @QA_ARCH listed explicitly (negative scenarios).
- [ ] Money/audit affected — reconciliation or trail and alerts described (§5).

### 14.2. @QA_ARCH — before 🟢

- [ ] Evidence exists for four UI states for affected screens.  
- [ ] Tenant: negative scenarios and IDOR verified in code.  
- [ ] Mutations: no double charges/duplicates on request replay (where applicable).  
- [ ] API errors conform to contract; no UUID "in the user's face".  
- [ ] For changes in critical chains — a test exists or an explicit gap with @LEAD escalation.  
- [ ] Cross-reference with §3–§15 of passport: do not issue 🟢 for a known target invariant violation without @LEAD decision.  
- [ ] If payments/cashbox/packages affected — traceability verified and no "silent" discrepancies (or gap recorded).

---

## 15. Quality Evidence: Tests and Regression (10/10)

- **Pyramid:** unit on domain logic; integration on DB/transactions/repositories; e2e or contract tests on critical user journeys (booking, payment, visit completion — per product profile).
- **Critical invariants** (double-booking, webhook idempotency, tenant isolation) — covered by **automated tests**, not only manual regression.
- **API contract** (OpenAPI/schemas + CI check or consumer-driven) — agreed with frontend.
- **Flaky tests:** zero-tolerance policy for persistently failing tests; quarantine only with owner and deadline.
- **Test data and isolation:** no accidental integration test runs against the production DB.

---

## 16. References

- **Live NFR tracking:** `roles/NONFUNCTIONAL_SCORECARD.md`  
- **Roles (this repository):** `roles/ROLE_ARCH.md`, `roles/ROLE_QA_ARCH.md`  
- **Product UI/module plane (this repository):** `roles/TEMPLATE_MODULE_DEV.md`, `roles/TPF_MASTER.md`, `docs/TPF_MODULE_*.md`  
- **Cache:** `roles/CACHE_STRATEGY.md`  
- **Migrations (operational runbook):** `roles/MIGRATIONS_PLAYBOOK.md`  

In another project, replace paths with the ones accepted by the team; passport and scorecard remain the "norms / facts" separation.

---

*Passport — living document: @LEAD initiates updates when the target bar or stack changes. Version in git — source of truth.*
