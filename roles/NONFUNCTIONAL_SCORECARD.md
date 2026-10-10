# NONFUNCTIONAL_SCORECARD — Live NFR Tracking
> Records facts and NFR maturity goals for a specific project.
> Norms and checklists: `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md`
> Update owner: @LEAD | Evidence verification: @QA_ARCH
> Update after measurements, releases, audits. Always: date + source of fact.

---

## 1. SUMMARY ASSESSMENT

| Category | Weight | Score 0–10 | Target | Date | Evidence source |
|-----------|-----|------------|------|------|------------------------|
| Reliability & Resilience | 20% | | 8 | | |
| Security & Compliance | 20% | | 9 | | |
| Performance & Scalability | 15% | | 8 | | |
| Data Integrity & Transactions | 10% | | 9 | | |
| Observability & Incident Response | 10% | | 7 | | |
| Architecture & Code Quality | 10% | | 8 | | |
| AI Integration Quality | 10% | | — | | N/A if no AI contour |
| Delivery Discipline | 5% | | 8 | | |

**Weighted total score:** ___ / 10 | Date: ___

> Calculation method: sum (Score × Weight). Example: 8×0.20 + 9×0.20 + ... = X.

---

## 2. KPI TARGETS — baseline and goals

| Metric | Target | Baseline (actual) | Baseline date | Goal | Measurement date |
|---------|----------|----------------|---------------|------|-------------|
| API availability (working window) | ≥ 99.5% | | | | |
| p95 critical APIs (without AI) | ≤ 300–500ms | | | | |
| p95 AI/RAG endpoint | ≤ 3000ms | | | N/A if absent | |
| 5xx rate on business-critical endpoints | < 1% | | | | |
| MTTR P1/P2 | ≤ 60 min | | | | |
| RPO | documented | | | | |
| RTO | documented | | | | |
| Restore drill | per schedule | | | | |
| Security critical (SCA/images) | 0 on release branch | | | | |
| RAG eval recall@3 | ≥ threshold from EVAL_PLAN | | | N/A if absent | |
| Queue consumer lag (critical) | < 30 sec | | | N/A if absent | |
| Mobile app startup (cold) | < 2 sec | | | N/A if absent | |

Add rows for your product.

---

## 3. DETAILED STATUS BY CATEGORY

### 3.1 Reliability & Resilience

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| Circuit breakers on external APIs | | | |
| Retry with exponential backoff + jitter | | | |
| DLQ for Celery / Kafka | | | |
| Graceful shutdown configured | | | |
| Health check verifies DB and Redis | | | |
| Restore drill conducted | | | |
| Failure modes documented in SYSTEM_DESIGN | | | |

### 3.2 Security & Compliance

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| Tenant isolation in every DB query | | | |
| IDOR protection (404 instead of 403) | | | |
| Webhook signature validation | | | |
| Secrets in env variables only | | | |
| SCA scan with no critical vulnerabilities | | | |
| PII not in logs and metrics | | | |
| FZ-152 / GDPR requirements documented | | | |

### 3.3 Performance & Scalability

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| SYSTEM_DESIGN_*.md created with Load Profile | | | |
| Latency Budget fixed for critical endpoints | | | |
| No N+1 queries (verified via logs/EXPLAIN) | | | |
| Cursor-based pagination on large lists | | | |
| Connection pool calculated (does not exceed max_connections) | | | |
| Indexes on all FK and filtered fields | | | |
| Caching strategy documented (CACHE_STRATEGY.md) | | | |

### 3.4 Data Integrity & Transactions

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| FOR UPDATE on competing resources | | | |
| Idempotency keys for payment operations | | | |
| Append-only journal for financial operations | | | |
| Soft delete with deleted_at IS NULL in SELECT | | | |
| Decimal for money (not float) | | | |
| Unique indexes on business invariants | | | |
| @PRINCIPLE ran check (if G1–G7 triggered) | | | |

### 3.5 Observability & Incident Response

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| Structured logging (JSON with tenant_id) | | | |
| Prometheus metrics configured | | | |
| Grafana dashboards created | | | |
| AlertManager alerts configured | | | |
| Distributed tracing (OTel/Jaeger) | | | |
| Runbook for P1/P2 incidents | | | |
| METRICS_REGISTRY.md is up to date | | | |

### 3.6 Architecture & Code Quality

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| ADR_REGISTRY.md is up to date | | | |
| SYSTEM_DESIGN_*.md exists | | | |
| Unified error contract (detail + code) | | | |
| TypeScript strict / Python type hints | | | |
| No magic numbers (constants/enum) | | | |
| Migrations: upgrade + downgrade | | | |

### 3.7 AI Integration Quality (N/A if no AI contour)

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| RAG_PASSPORT filled (all 12 items) | | | |
| Tenant isolation in vector search | | | |
| EVAL_PLAN with numeric threshold | | | |
| Golden-set exists (≥ 10 records) | | | |
| Last eval above blocking threshold | | | |
| LLM calls not in sync user-facing path | | | |
| Fallback when LLM is unavailable | | | |

### 3.8 Delivery Discipline

| Item | Status | Date | Evidence / ADR |
|-------|--------|------|----------------------|
| CI pipeline: lint + tests required | | | |
| Zero-downtime deployment configured | | | |
| Expand-deploy-contract for migrations | | | |
| DEV_EXECUTION_PASSPORT used | | | |
| @QA_ARCH gate passed before @QA | | | |

---

## 4. RELATED ADR (decisions affecting NFR)

| ID | Topic | NFR Category | Link |
|----|------|--------------|--------|
| | | | |

*Fill on each ADR that affects the metrics above.*

---

## 5. SNAPSHOT HISTORY

| Date | Weighted score | Main change | Who |
|------|-------------------|-------------------|-----|
| | | Scorecard created | @LEAD |

---

## 6. HOW TO UPDATE

```
After each release:
  □ Update Baseline for changed metrics (§2)
  □ Update statuses of affected items (§3)
  □ Add row to history (§5)

After a new ADR affecting NFR:
  □ Add row to §4

After @PERF audit:
  □ Update §3.3 and KPI p95 in §2

After @AI_ENGINEER audit:
  □ Update §3.7

After P1/P2 incident:
  □ Update MTTR in §2
  □ Update Reliability items in §3.1
```

---

Reference: `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` · `roles/ROLE_PERF.md` · `roles/ROLE_AI_ENGINEER.md` · `roles/ROLE_PRINCIPLE.md` · `roles/SYSTEM_DESIGN_PROTOCOL.md` · `docs/artifacts/ADR_REGISTRY.md` · `docs/artifacts/METRICS_REGISTRY.md` · `docs/artifacts/EVAL_PLAN.md`
Version: 2.0 | 2026-05-22
