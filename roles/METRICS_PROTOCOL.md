# 📊 METRICS_PROTOCOL — Metric Correctness Control

> **Purpose:** a unified protocol for verifying that metrics in the system not only exist but measure exactly what they should — correctly, consistently, and provably.  
> **Does not duplicate:** `ARCHITECTURE_EXCELLENCE_PASSPORT.md` (maturity norms) and `NONFUNCTIONAL_SCORECARD.md` (target KPIs). This document is about **how to verify**, not **what should be**.

**Roles and ownership:**

| Role | Zone |
|------|------|
| @PRINCIPLE | Correctness of metric definition before code: grain, formula, time boundaries |
| @QA_ARCH | Correctness of implementation: instrumentation, UI/backend consistency, absence of double-counting |
| @ARCH | Collection architecture: where and how the metric is written, which observability stack |
| @LEAD | Prioritisation: which metrics are critical for the current product stage |

---

## 1. FOUR LAYERS OF METRICS

Each layer has its own risks and its own verification protocol.

### Layer 1 — Business metrics (Business KPIs)
**What:** business outcome indicators. Revenue, conversion, LTV, churn, NPS.  
**Owner:** @BIZ formulates, @ARCH designs the data source, @PRINCIPLE verifies the formula.  
**Main risk:** the metric is calculated but measures the wrong thing — e.g. "revenue" includes cancelled payments.

### Layer 2 — Product metrics (Product Events)
**What:** events inside the product. Number of bookings, task statuses, feature usage, funnels.  
**Owner:** @ARCH designs the event schema, @DEV instruments, @QA_ARCH verifies.  
**Main risk:** the event is written at the wrong moment (before commit, not after), or duplicated on retry.

### Layer 3 — Technical metrics (Observability)
**What:** latency, error rate, availability, queue lag, DB connections.  
**Owner:** @ARCH selects the stack (Prometheus/OTel), @DEV instruments, @OPS configures alerts.  
**Main risk:** the metric exists but the alert is misconfigured or the threshold is not aligned with the SLO.

### Layer 4 — Data quality metrics (Data Quality)
**What:** consistency of views with operational tables, absence of "lost" records, correctness of aggregates.  
**Owner:** @PRINCIPLE verifies before code, @QA_ARCH verifies in implementation.  
**Main risk:** the report and the operational screen show different numbers for the same period.

---

## 2. METRIC DEFINITION PROTOCOL (before code)

Triggered by @PRINCIPLE when Gate G4 is present (aggregate / report / KPI).  
Every new metric must pass this checklist **before** it appears in `ARCH_*.md`.

### 2.1. Metric card (mandatory artifact)

One card per metric. Without a filled card, the metric does not go into development.

```markdown
## METRIC: [Name]

**Layer:** [Business / Product / Technical / Data]
**Owner:** [role or module]

**Definition:**
- What we count: [noun — what exactly is the unit of measurement]
- Inclusion condition: [which records go into the numerator]
- Exclusion condition: [what is explicitly not counted — cancelled, soft-deleted, test records]
- Denominator (if ratio): [what is in the base]

**Time boundaries:**
- Period: [day / week / month / arbitrary range]
- Boundary: [[start, end) or inclusive on both sides]
- Timezone: [UTC / tenant locale — and where conversion happens]

**Data source:**
- Table(s): [list]
- Source row grain: [one row = what]
- JOINs: [if any — risk of row multiplication]
- Tenant filter: [mandatory filter by clinic_id / tenant_id]

**Consistency:**
- Matches operational screen: [yes / no / partially — explanation]
- On discrepancy — source of truth: [operational screen / view / both synced]

**Invariants:**
- [INV-NN] statement — violation → effect
```

### 2.2. @PRINCIPLE checklist before handoff to @ARCH

- [ ] Definition contains no words "usually", "as a rule", "approximately"
- [ ] Inclusion and exclusion conditions explicitly listed (including soft-deleted, cancelled, test records)
- [ ] Time boundaries are unambiguous: boundary type is fixed
- [ ] Timezone: either everything in UTC, or explicit conversion point — not "somewhere in the UI"
- [ ] No risk of row multiplication on JOIN
- [ ] Tenant filter mandatory for all queries in a multi-tenant system
- [ ] For money metrics: refunds, reversals, partial payments — explicitly accounted for
- [ ] Metric aligned with operational screen or discrepancy is documented

---

## 3. IMPLEMENTATION VERIFICATION PROTOCOL (after code)

Triggered by @QA_ARCH as **Vector 10 — Metrics Integrity** (addition to the 9 existing vectors).

### 3.1. Vector 10 — Metrics Integrity

**Applied when:** module contains aggregates, reports, dashboards, event counters, or technical metrics.

**Counting correctness:**
- [ ] Metric matches the card from ARCH_*.md: inclusion/exclusion conditions observed in query
- [ ] Time boundaries implemented exactly as defined: `>=` and `<` or `<=` — per card
- [ ] Timezone: conversion in one place, not spread between backend and frontend
- [ ] JOIN does not multiply rows: GROUP BY or DISTINCT where needed
- [ ] Soft-deleted records excluded by explicit filter (`deleted_at IS NULL`)
- [ ] Tenant filter present in every aggregating query

**Layer consistency:**
- [ ] Number on dashboard matches number on operational screen for the same period — verified on a test dataset
- [ ] If view — verified against raw table for the same period
- [ ] With caching — invalidation occurs when source data changes

**Technical metrics:**
- [ ] Counter increments exactly once per event (no duplication on retry)
- [ ] Event is written after successful commit, not before
- [ ] tenant/clinic_id label present in metric labels
- [ ] Alert configured and threshold aligned with NONFUNCTIONAL_SCORECARD or ADR

**Product events:**
- [ ] Event written at the correct lifecycle moment (not on form creation, but on save)
- [ ] No event duplication on idempotent retry
- [ ] Event contains sufficient context: tenant_id, actor_id, timestamp, entity_id

### 3.2. Metric red flags (automatic 🔴)

- Metric on screen and in report diverge for the same period without documented reason
- `COUNT(*)` without `deleted_at IS NULL` filter on soft-delete table
- Money aggregate without accounting for refunds and reversals
- Date filtered without timezone consideration
- JOIN without GROUP BY on a one-to-many table
- Metric written before `session.commit()` — data may not be saved but counter already incremented
- Counter without tenant label in a multi-tenant system

---

## 4. REGULAR AUDIT PROTOCOL

Metrics degrade when business logic changes. Mandatory review triggers:

| Event | Metrics to review |
|---------|----------------------|
| New entity status added | All aggregates for that entity |
| Refund / reversal / cancellation added | All financial metrics |
| Source table schema changed | All views and reports using that table |
| New tenant type or role added | All metrics with tenant filter |
| Storage timezone changed | All metrics with time boundaries |

On trigger: @LEAD calls @PRINCIPLE to revise cards for affected metrics.

---

## 5. PROJECT METRICS REGISTRY

Stored in `docs/artifacts/METRICS_REGISTRY.md`. Created by @ARCH when metrics first appear, updated on every change.

**Structure:**

```markdown
# METRICS_REGISTRY — [Project Name]
> Version: [date]

| ID | Name | Layer | Source | Owner | Status | Card |
|----|----------|------|----------|----------|--------|---------|
| M-01 | Revenue for period | Business | finance_transactions | @BIZ | ✅ Verified | ARCH_*.md §... |
| M-02 | Bookings created | Product | bookings | @DOMAIN | ✅ Verified | ARCH_*.md §... |
| M-03 | p95 latency /bookings | Technical | Prometheus | @OPS | 🟡 Alert not configured | - |
| M-04 | Active tenants | Business | clinics | @BIZ | 🔴 No card | - |
```

**Registry statuses:**
- ✅ Verified — card exists, Vector 10 passed, aligned with operational screen
- 🟡 Partial — card exists, implementation not verified or known gap
- 🔴 No card — metric used without formal definition (blocker)
- ⚠️ Outdated — revision trigger fired, review required

---

## 6. WHO READS AND WHEN

| Role | When | Section |
|------|-------|--------|
| @PRINCIPLE | At Gate G4 (aggregate / report) | §2 — definition |
| @QA_ARCH | After @DEV if module contains metrics | §3 — Vector 10 |
| @ARCH | When designing the reporting layer | §2.1, §5 |
| @LEAD | When adding KPIs or changing business logic | §4 — triggers |
| @BIZ | When formulating KPIs | §2.1 — card |

---

Reference: `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` §3–§5 · `roles/NONFUNCTIONAL_SCORECARD.md` · `roles/ROLE_PRINCIPLE.md` · `roles/ROLE_QA_ARCH.md` · `roles/ROLE_ARCH.md`  
Version: 1.0 | 2026-04-02
