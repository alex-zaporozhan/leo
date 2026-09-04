# CACHE_STRATEGY — when and how to cache (@ARCH decision)

> **Purpose:** uniform criteria for introducing and changing cache in the system, to avoid **stale money/slots**, data leaks between clinics, and "silent" incidents without metrics.  
> **Decision is made by @ARCH** and recorded in `docs/artifacts/ARCH_*.md` / `docs/artifacts/DEV_PROMPTS_*.md`; **@DEV** implements only the described contract.  
> **Connection:** `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` (observability, data integrity, performance).

This is **not** a replacement for Redis as a Celery broker — this is about **response and computation caching**.

---

## 1. Where the decision is recorded

- In the architectural artifact or in the task for @DEV: **what** is cached, **key**, **TTL**, **invalidation**, **degradation** when Redis is unavailable.
- Any new use of server-side cache in a critical path — with an explicit line in the handoff (see `roles/ROLE_ARCH.md`, @DEV handoff section).

---

## 2. When cache **is appropriate**

| Criterion | Question |
|----------|--------|
| Read frequency | Is data read **significantly more often** than it changes? |
| Tolerable staleness | Does the user/business tolerate **X seconds/minutes** of stale data? |
| Source cost | Is the DB or external API query **expensive** (latency, $)? |
| Determinism | For the same key, is the answer **identical** until data changes? |

Typical candidates: reference data, dashboard aggregates (with explicit TTL), heavy read-only reports, idempotent GETs with no strict realtime requirement.

---

## 3. When cache **is inappropriate** (or only with special rules)

- **Source of truth for money and balances** — do not cache as the "only" answer without event-based invalidation.
- **Access and authorisation** — do not rely solely on cache to decide "can this entity be viewed".
- **Strong consistency** — slot booking, final check: cache does not replace locks and DB constraints (see passport §4–§5).
- **Personal/PII-heavy data** — only with TTL, tenant-keyed keys, and masking policy in logs.

---

## 4. Keys and multi-tenancy

- Key **always** includes the tenant identifier (**`clinic_id`** in this project) if data is isolated per clinic.
- Naming: predictable prefix (`v1:report:daily:{clinic_id}:{date}` etc.) to eliminate collisions and simplify debugging.

---

## 5. TTL and invalidation

- **TTL** — required; "no expiry" only for rare cases with an explicit ADR.
- **Invalidation:** prefer **event-based** (after a mutation on an entity, drop affected keys). TTL-only — when the business allows eventual consistency and the risk is assessed.
- When the **schema** of a cached object changes — version in the key prefix or full layer flush per release procedure.

---

## 6. Redis vs in-process

| Option | When |
|---------|--------|
| **Redis** (as currently in compose) | Multiple API workers/replicas, shared cache, Celery already uses Redis. |
| **In-process (LRU)** | Single instance, small data, loss on restart is acceptable. |

Do not duplicate the same meaning at two levels without need; if two levels — who is the "source of truth" on discrepancy must be described.

---

## 7. Degradation and stampede

- On Redis unavailability: **transparent fallback** to a direct DB query (or feature failure with a clear error) — defined in the task upfront.
- **Cache stampede:** for hot keys — computation lock, single-flight, or short TTL + background refresh (per @ARCH decision).

---

## 8. Frontend (React Query etc.)

- Server-side cache and **client-side** query cache are different levels: after backend mutations, the frontend must **invalidate** the relevant queries (see `.cursorrules` / DOMAIN_STANDARDS). @ARCH specifies in the task which frontend lists/keys are affected.

---

## 9. Observability

- For any introduced cache: **hit/miss** counters are desirable; optionally latency; with a feature-flag cache — ability to disable and compare metrics (passport §8).

---

*This document is extended as new scenarios arise; disputed cases — ADR.*
