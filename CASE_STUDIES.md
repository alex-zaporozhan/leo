# Case Studies — proof at scale

LEO's 41 Laws are not theoretical. Most of them exist because of a specific defect in one of the three systems below. This page describes what each system is, what it's built from, and — where disclosable — what a LEO gate actually caught before it reached production.

One note on scope: all three repositories were built with a coding agent operating under the LEO rule system as the sole author of the code (`@DEV` is the only role permitted to write code; every other role writes documents and prompts). The author directed the process; the agent executed it.

---

## Case Study 1 — MedCore (source-available; going public at [github.com/alex-zaporozhan/medCore](https://github.com/alex-zaporozhan/medCore))

**Class:** Multi-tenant B2B clinic operating system.

MedCore is a full clinic-operations platform — booking, CRM, ERP, omnichannel messaging, tasks, finance — shipped as a modular monolith, not a slide deck. Tenancy is `Organization → Clinic`, with a separate platform/founder contour for the vendor side. It ships its own `README.md`, `SECURITY.md`, `CONTRIBUTING.md`, and a `LICENSE` (PolyForm Shield 1.0.0 — the same license family this repository uses, for the same reason: use it freely, don't resell it as a competing clinic-OS/booking SaaS).

Full disclosure: MedCore was the author's first end-to-end run of this process, and it reads that way in places — rougher than the two engagements below it. It's included with its flaws intact rather than cleaned up for the pitch, because a debut that survived large, messy real-world data at build time is more informative than a polished demo that hides how the process actually went.

| | |
|---|---|
| **Backend** | Python 3.11, FastAPI, Pydantic v2, SQLAlchemy 2 (async), asyncpg, Alembic, PostgreSQL 16 |
| **Jobs / cache** | Redis 7, Celery (broker, rate limits, report cache, transactional-outbox delivery) |
| **Frontend** | React 18, TypeScript, Vite 6, Mantine 7, TanStack Query 5, Tabler icons |
| **Observability** | Prometheus `/metrics`, Grafana dashboards in `deploy/` |
| **CI/CD** | Jenkins → GHCR (primary), Docker Hub (single-VPS path), GitHub Actions (PR gates) |
| **Test surface** | 187 test modules — pytest (API, services, security, tenancy isolation) + Playwright |
| **Isolation model** | Application-layer tenant scoping + cross-tenant regression tests (ADR-007); PostgreSQL RLS used selectively (e.g., entitlements), not as the sole control |

**What LEO's gates caught here, concretely:**
- **Law 32 (integrity under concurrency)** exists in large part because of exactly the failure class this domain invites: two staff members booking the same chair-slot at once. The fix isn't "check first, then insert" — it's a database-level constraint plus `SELECT … FOR UPDATE`, with a dedicated race test.
- **Law 8 (no UUIDs in the UI)** is a recurring `@QA_ARCH` finding class in multi-role admin panels — a raw `clinic_id` or `entity_id` leaking into a table column is exactly the kind of thing that looks "fine" in a quick demo and unprofessional the moment a real clinic manager sees it.
- The **Async Workers Canon** (queue design, lease clocks, retry ownership) governs the transactional outbox that delivers webhooks and notifications reliably even if a worker dies mid-delivery.

---

## Case Study 2 — Enterprise AI Training Platform (client engagement, NDA)

**Class:** AI-native content and agent-orchestration SaaS.

*This engagement is under a client NDA. What follows is architecture and stack — publicly observable engineering shape — with all business logic, client identity, and product specifics withheld.*

The platform is a modular SaaS for building and running AI-driven training/content pipelines: a knowledge base with retrieval, an admin console for constructing multi-step generation pipelines visually (a node-graph / pipeline-builder interface), and integrations with third-party generative-media providers for automated video/asset production.

| | |
|---|---|
| **Backend** | Python 3.11+, FastAPI, SQLAlchemy 2 (async), asyncpg, Alembic, PostgreSQL + **pgvector** |
| **AI orchestration** | **LangGraph** — executable, stateful agent graphs with PostgreSQL-backed checkpointing |
| **Jobs / cache** | Celery, Redis, Prometheus metrics |
| **Frontend** | React 18 + TypeScript, a node-graph pipeline builder (XYFlow-class canvas UI), TanStack Query |
| **Test surface** | 300+ test files across backend (pytest) and frontend (custom unit/integration harnesses + Playwright-based visual/E2E checks) |

**Why this system stressed LEO's canons the hardest:**
- It is the primary reason `roles/CANVAS_CRAFT_CANON.md` exists at all — a node-graph pipeline builder is explicitly *not* a CRUD list, and the system calls that out as a **naming failure**, not a modeling one, if the product class is mis-declared upfront: *"A page builder is a CANVAS-class product; declared as a 'list,' it will be built as a list — a form over a JSON blob."*
- It's the reason `@AI_ENGINEER`'s routing exists as a separate lane from `@ARCH`: RAG corpus/chunking/embedding decisions and agent-graph state design have their own failure modes (stale embeddings after a corpus change, duplicate generation on a retried task, an agent producing a real-world side effect twice) that a generic backend architecture review does not catch.
- `ASYNC_WORKERS_CANON.md` §9–13 (lease-as-single-life-clock, "heartbeat = progress," the rate-limiter-at-the-wire pattern) documents, per `roles/SYSTEM_UPGRADE_MANIFEST.md` v6.25, a real production incident with exactly this shape: a rate limiter that held itself full, retries that bypassed it, and a heartbeat that revived a task that was actually stuck. This platform's generation pipeline is precisely the kind of background-job surface that canon exists to protect — a queue with retries, external calls, and a lease clock.

---

## Case Study 3 — Public-Sector Vocational Education Platform (shipped)

**Class:** Public marketing site + headless CMS, licensed-institution compliance requirements.

A public-facing site and content-management platform for a licensed continuing-education institution (vocational retraining / professional certification), where SEO performance and regulatory trust signals (license numbers, accreditation documents, accessibility) are core product requirements, not afterthoughts.

| | |
|---|---|
| **Backend** | Python 3.12, FastAPI, SQLAlchemy 2 (async), asyncpg, Alembic, PostgreSQL 16 |
| **Cache** | Valkey 8 (BSD-3 — chosen explicitly over Redis 7.4+'s commercial license, see Law 27) |
| **Marketing frontend** | Next.js 15 (App Router, SSR/ISR for SEO), React, TanStack Query |
| **Admin frontend** | Vite + React SPA, React Hook Form + Zod, TipTap rich-text editor, `@dnd-kit` for block-based page composition |
| **Media** | Yandex Object Storage (S3-compatible) |
| **Governance artifacts on file** | 10 ADRs, a dedicated `ARCHITECTURE_SPINE.md`, `STACK_MANIFEST` with a full license allowlist, a `PENTEST_META_AUDIT`, dozens of wave-by-wave `QA_REPORT_*` and `DESIGN_SPEC_*` files under `docs/artifacts/` |

**What this system demonstrates about LEO's SEO and license-purity discipline:**
- **Law 29 (search visibility by design)** drove an explicit architecture decision — SSR via Next.js, not a client-only SPA — *before* any admin or design work started, specifically because an SPA on the indexable public pages would have been an SEO liability discovered too late to cheaply fix.
- **Law 27 (license purity)** is not decorative here: the stack manifest explicitly denies Redis in favor of Valkey, and CKEditor/TinyMCE/Quill in favor of TipTap, with the reasoning for each recorded in the manifest rather than assumed.
- The accessibility requirement (WCAG AA) and the "trust is the product" positioning (license numbers, accreditation documents, a `svedenya`/legal-disclosure section required by Russian education law) show up as first-class product theses in `BUSINESS_LOGIC.md`, not retrofitted after launch — an example of Law 41 ("production-readiness by default") applied to a genuinely regulated content domain.

---

## What these three systems have in common

They are three different *shapes* of software — a transactional multi-tenant SaaS, an AI/agent platform, and a content-and-SEO-driven public site — and LEO ran the same 22-role, 41-law process across all three without being rewritten per project. The **stack-specific canons** (`STACK_SELECTION.md`, `DATA_STORE_SELECTION.md`, the admin/design templates) changed per project's needs; the **process canons** (gates, laws, artifact contracts, the security and data-integrity disciplines) did not.

That is the actual claim this repository is making: not "an AI wrote some code," but "a written engineering process, enforced by a rule system instead of a human reviewer's memory, produced systems that survived multi-tenancy, concurrency, AI-agent nondeterminism, and public-facing SEO/compliance scrutiny — repeatably, across genuinely different domains."
