# Case Studies

LEO's 44 Laws are not theoretical. Most of them exist because of a specific defect in one of the three systems below.

All three repositories were built with a coding agent operating under the LEO rule system as the sole author of the code — `@DEV` is the only role permitted to write code; every other role writes documents, specs and prompts. The author directed the process; the agent executed it.

Figures below are rounded and dated. Nothing here is offered as proof — one of the three systems is public, and anyone who wants to check rather than take a claim on faith can clone it and measure it directly.

```bash
git clone https://github.com/alex-zaporozhan/medCore && cd medCore
git log --oneline | wc -l                                          # commits
git ls-files 'src/**/*.py' | tr '\n' '\0' | xargs -0 cat | wc -l   # application lines
grep -rh '^\s*\(async \)\?def test_' tests --include='*.py' | wc -l # test functions
grep -c 'postgresql' tests/conftest.py                             # real Postgres, not SQLite
grep -ric 'UniqueConstraint\|postgresql_where\|CheckConstraint' src alembic
```

---

## Case Study 1 — MedCore · multi-tenant clinic operating system

**Public: [github.com/alex-zaporozhan/medCore](https://github.com/alex-zaporozhan/medCore)**

A full clinic-operations platform — booking, CRM, ERP, omnichannel messaging, tasks, finance — as a modular monolith. Tenancy is `Organization → Clinic`, with a separate platform contour for the vendor side. Licensed PolyForm Shield 1.0.0.

MedCore was the author's first end-to-end run of this process and reads that way in places. It is included with its flaws intact, because a debut that survived large, messy real-world data at build time is more informative than a polished demo.

| | |
|---|---|
| **Scale** | ~180,000+ lines total — ~90,000+ application, ~29,000+ tests, ~59,000+ frontend |
| **History** | 100+ commits · March → August 2026 |
| **Tests** | 770+ test functions · 140+ Alembic migrations · full suite runs ~7h against a live PostgreSQL, ~95% passing |
| **Stack** | Python 3.11, FastAPI, Pydantic v2, SQLAlchemy 2 (async), PostgreSQL 16, Redis, Celery · React 18, TypeScript, Vite, Mantine, TanStack Query |
| **Integrity** | 120+ unique constraints, partial-unique indexes and check constraints in the schema; `SELECT … FOR UPDATE`, advisory locks and idempotency keys where two writers can collide |
| **Access control** | 49 permission codes mapped to base roles, with a CI test that diffs every router's declared permissions against the matrix — a permission added to a route without updating the matrix fails CI instead of shipping as a silent privilege gap |
| **Operations** | Jenkins → GHCR, GitHub Actions PR gates including a hard `critical_path` merge block; Prometheus metrics, Grafana dashboards, an on-demand backup-restore drill |

**Known gap — where tenant isolation lives.** Every query carries its own `organization_id` filter in application code, backed by cross-tenant regression tests. The database is not the enforcer: PostgreSQL row-level security is used only in places like entitlements, not as the primary control. The distinction is the whole point — application-layer scoping guarantees *we tested that no query forgot the filter*; row-level security guarantees *a query cannot forget it*, because the database refuses out-of-tenant rows no matter what the query asked for. One forgotten `WHERE` is the difference between a bug and a data leak, and this design still depends on nobody writing that line wrong.

**What LEO's gates caught.** Law 32 (integrity under concurrency) exists largely because of this domain's signature failure — two staff booking the same slot at once. The fix is a database constraint plus row locking with a dedicated race test, not check-then-insert. Law 8 (no UUIDs in the UI) is a recurring `@QA_ARCH` finding class in multi-role admin panels. The Async Workers Canon governs the transactional outbox that delivers webhooks reliably even when a worker dies mid-delivery.

---

## Case Study 2 — Commercial engagement under client NDA

**Class:** a large B2B product with an AI contour — RAG, agent graphs, real-time voice and video.

*Under a client NDA. Published here: the scale of the work and the basic technology layer. Withheld: source code, architecture, business logic, client identity, product specifics, third-party provider names and the details of any incident. **None of the figures below can be checked by anyone but the author** — weigh them as self-reported.*

| | |
|---|---|
| **Scale** | 550,000+ lines of code · 5,000+ tests · 500+ API operations (author's count at hand-over) |
| **History** | June → October 2026 (4 months) · one engineer |
| **Stack** | Python, PostgreSQL with `pgvector`, RAG, LangGraph · React, TypeScript |
| **Acceptance** | Every feature checked by the client under parallel load on a deliberately weak machine, so that races and queue defects surfaced before sign-off rather than after |

**Why this system stressed LEO's canons hardest.** It is the reason `@AI_ENGINEER` routes separately from `@ARCH`: retrieval and agent-graph state have failure modes — stale embeddings after a corpus change, duplicate generation on a retried task, an external side effect produced twice — that a generic backend review does not catch. And real-time voice and video leave no room for a feature that only looks finished: a reply either arrives within its latency budget or it does not.

---

## Case Study 3 — Continuing-education platform · public site + block-based CMS

**Class:** public marketing site plus content-management platform, where search performance and regulatory disclosure are product requirements rather than afterthoughts.

The admin side is not a thin CMS form over a blob: it is a block-based page builder with schema-versioned block types, undo/redo, a version-history timeline, stale-draft conflict detection on concurrent edits, and a publish audit trail — 20 distinct block types, each schema-versioned with a frontend/backend default-parity test.

| | |
|---|---|
| **Scale** | ~93,000+ lines total — ~29,000+ application, ~24,000+ tests, ~39,000+ frontend and admin |
| **History** | 35 commits · July → August 2026 |
| **Tests** | 930+ backend test functions · 20+ Alembic migrations · full suite runs ~1h against a live PostgreSQL, >99% passing · plus Playwright visual-regression and accessibility specs |
| **Stack** | Python 3.12, FastAPI, SQLAlchemy 2 (async), PostgreSQL 16, Valkey · Next.js 15 (App Router, SSR/ISR) for the public site · Vite + React admin with React Hook Form, Zod, TipTap and `@dnd-kit` · S3-compatible object storage |
| **Access control** | 3 roles, 2 permission codes — intentionally sized to an actual small content-ops team rather than a speculative enterprise RBAC model nobody would use |
| **Governance** | 10 ADRs, an architecture spine, a stack manifest with a full license allowlist, a pentest meta-audit, and wave-by-wave QA and design-spec artifacts |

**Known gap — a committed virtual environment.** `backend/.venv311` is tracked in git: several thousand third-party Python files that belong in a lockfile, not in the repository. Beyond bloating every clone and every diff, it pins one machine's interpreter build — useless on another OS or architecture — and, more seriously, it drags those vendored dependencies into the delivered repository's own licence surface, which is precisely the exposure Law 27 exists to prevent. Fix is two commands: untrack it and add it to `.gitignore`.

**What this system demonstrates.** Law 29 (search visibility by design) drove an explicit architecture decision — server-side rendering rather than a client-only SPA — *before* any admin or design work started, because an SPA on indexable pages is an SEO liability discovered too late to fix cheaply. Law 27 (license purity) is not decorative: the stack manifest denies Redis 7.4+ in favour of Valkey and CKEditor/TinyMCE/Quill in favour of TipTap, with the reasoning recorded per choice. Law 39's craft floor leaves the constitution here and lands in an actual pipeline stage — a machine-checkable visual floor (button-primitive usage, contrast in every interactive state, equal-height control rows) that runs on every build instead of living as a code-review convention someone forgets under deadline.

---

## Where the bar goes next

These are the checks that would raise the standard of evidence beyond what is on this page. None has been run yet, and they are listed because a page that reports only what went well is a brochure:

- **Mutation testing** on the core domain modules ([mutmut](https://mutmut.readthedocs.io/), [Stryker](https://stryker-mutator.io/)) — the direct answer to whether a test suite detects anything. Google's published position is that a whole-repository mutation score is [infeasibly expensive](https://research.google/pubs/state-of-mutation-testing-at-google/); the realistic target is a handful of modules with exclusions stated.
- **An invariant attack test** — bypass the service layer, attempt to write a row violating a business invariant directly, show the constraint rejecting it. An hour of work, and it turns "invariants live in the schema" into a demonstration.
- **Isolation-anomaly tests** in the style of [Hermitage](https://github.com/ept/hermitage), run against the actual configured isolation level rather than the assumed one.
- **An OWASP ASVS 5.0 Level 2 self-assessment** with unmet requirements listed by ID.

Coverage percentages are deliberately absent: coverage correlates weakly with suite effectiveness once suite size is controlled for ([Inozemtseva & Holmes, ICSE 2014](https://www.cs.ubc.ca/~rtholmes/papers/icse_2014_inozemtseva.pdf)).

---

## What these three systems have in common

Three different *shapes* of software — a transactional multi-tenant SaaS, an AI and async-heavy platform, and a content-and-SEO-driven public site — and LEO ran the same 22-role, 44-law process across all three without being rewritten per project. The **stack-specific canons** changed per project; the **process canons** — gates, laws, artifact contracts, the security and data-integrity disciplines — did not.

That is the claim: not "an AI wrote some code," but that a written engineering process, enforced by a rule system instead of a human reviewer's memory, produced systems whose invariants are enforced in the schema, whose tests run against real infrastructure, and whose scale was reached in months rather than years.
