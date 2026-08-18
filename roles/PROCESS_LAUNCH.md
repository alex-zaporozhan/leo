# PROCESS_LAUNCH — Launching Any Project

> Universal process. Run for every new product.
> Do not skip phases — each exists because someone skipped it.
>
> Session states, handoff protocol and quality gate — docs/ENGINEERING_PLAN.md.

---

## Phase 0: Idea (before any code)

**Goal:** Understand whether it is worth building.
```
[ ] Formulated the idea in one sentence ("We help [who] do [what] without [pain]")
[ ] @BIZ ran KILL SIGNAL check — all 6 points OK (or received verdict DO NOT BUILD)
[ ] @BIZ created MARKET_AUDIT.md — competitors with real prices and complaints, differentiator, channel
[ ] Launched @CREATOR → Pre-flight (LEGAL / MARKET / TECH) passed without 🔴
[ ] @CREATOR ran High-Tech Baseline — determined what goes into MVP
[ ] BUSINESS_LOGIC.md created and filled
```

**Output:** MARKET_AUDIT.md + BUSINESS_LOGIC.md filled. Without MARKET_AUDIT.md — @LEAD does not start the plan.

---

## Phase 1: MVP (minimum viable product)

**Goal:** First paying customer.
```
[ ] MVP from BUSINESS_LOGIC.md implemented (only items from there)
[ ] Hosting in the required region if PII is involved
[ ] Data in PostgreSQL, not in Google Sheets
[ ] Payment system integrated
[ ] Notifications via NotificationService (Telegram + Email fallback)
[ ] Basic error handling: try/except + logs
[ ] .env not in git (.gitignore verified)
[ ] First customer received a demo and gave feedback
```

**Output:** There is one paying customer or an explicit reason why not.

---

## Phase 2: Stability (after first revenue)

**Goal:** System does not crash under real load.
```
[ ] Retry with exponential backoff for external APIs
[ ] Circuit breaker for critical integrations (payments, external APIs)
[ ] Idempotency keys for operations that cannot be duplicated
[ ] DB transactions for atomic operations
[ ] Celery + Redis for background tasks
[ ] Automated DB backup (at least once a day)
[ ] Alembic migrations (do not change schema manually)
[ ] Sentry for errors
```

**Output:** 5 customers working without complaints about crashes for a week.

---

## Phase 3: Growth (after product-market fit)

**Goal:** System handles 10x growth without rewriting.
```
[ ] Prometheus + Grafana or equivalent (metrics)
[ ] Structured logs (logging with levels, not print())
[ ] OpenAPI documentation (from FastAPI automatically)
[ ] Tests: critical paths covered
[ ] Dead Letter Queue for failed tasks
[ ] Caching of frequent queries (Redis)
[ ] Horizontal scaling verified (stateless services)
[ ] Disaster recovery plan documented
```

**Output:** System handled peak load without manual intervention.

---

## Before release / deploy (Quality Gate)

**Goal:** Do not release critical defects and vulnerabilities to production.
```
[ ] @QA — final testing: all P0/P1 closed
[ ] @SEC — security audit: critical vulnerabilities closed
[ ] @LEAD — explicit decision: release approved
```

Full Quality Gate checklist — docs/ENGINEERING_PLAN.md.

**Output:** Release is executed after gate is passed.

---

## After deploy (before handover to client)
```
[ ] Licence and protection: contract/offer signed, LICENSE.txt in deploy folder
    (on request — @LAWYER: docs/ROLE_LAWYER.md)
[ ] Commercial package: COMMERCIAL_PACK_[Project].md filled
    (@BIZ: price, description, competitors, objections, ROI)
[ ] Sales card text ready (from COMMERCIAL_PACK_*.md)
```

**Output:** Client receives the folder only after accepting the terms.

---

## Process Rules

**Do not skip phases.** There is no point setting up monitoring without a first customer.

**Checklist — not checkbox-ticking.** Mark only what is actually done.

**BUSINESS_LOGIC.md — living document.** Every significant decision is appended there.

**NotificationService — build with abstraction:**
```python
class NotificationService:
    async def send(self, user_id: str, message: str, level: str = "info"):
        # Telegram → Email fallback → queue
        pass
```
30 minutes once. Protects against any blocking.

---

## Quick Start
```
1. "We help [who] do [what] without [pain]"
2. @CREATOR → Pre-flight + High-Tech Baseline + BUSINESS_LOGIC.md
3. @LEAD → architectural planning
4. Decision: build MVP or not
```

The entire process up to the decision — maximum 2-3 hours.

---

Reference: docs/ENGINEERING_PLAN.md · docs/ROLE_CREATOR.md · docs/STACK_SELECTION.md
