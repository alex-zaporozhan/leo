# FRONTEND_CAPABILITY_CANON

> **Greenfield clause — the map is written against what will exist, not only against what does.** On a module whose backend has not been built yet, "read the actual backend" has nothing to read, and the blocker "no map → no handoff to @DESIGN" would stop every new module forever. In that case the map is produced from the **`DOMAIN_MODEL_[MODULE].md`** (layers 2 lifecycles · 4 invariants · 5 events · 7 authority) and the ARCH spine, and every row is marked `PLANNED` instead of citing a file and line. It converts to `VERIFIED` — with real paths — at the first @QA_ARCH pass after the backend lands, and a row still `PLANNED` at that point is a finding, not a formality. What is never allowed is writing the map from the ticket.
# The canon that closes the "tablecloth frontend": a rich backend covered by a thin film of CRUD forms.
# Position: between the backend contract (@ARCH) and the screen (@DESIGN SPEC). Nothing else in the system asks
# the question this file asks. Owners: @FRONTEND (reads the backend, produces the map), @ARCH (confirms it),
# @DESIGN (must answer it in the SPEC), @DEV (executes), @QA_ARCH (the gate).

> **The canon's dogma:** the backend has **capabilities**. The frontend offers **affordances**.
> A mediocre frontend maps `endpoint → form`. A great one maps `capability → experience`.
> Between those two sentences lies the entire difference between a product that works and a product that sings.
>
> **The failure this file prevents — "the tablecloth":** the backend is rich — state machines, events, invariants,
> versioning, background pipelines, aggregates — and the UI is a set of tables and forms laid over it like a
> cloth over furniture. Every endpoint has a screen. Nothing has an **experience**. The system's power is
> invisible to the person paying for it. This is not a design failure and not a backend failure — it is a
> **missing step**, and until now no role owned it.

---

## §1. THE CAPABILITY INVENTORY — read the backend, not the ticket

Before designing screens for a module, @FRONTEND (with @ARCH) walks the backend and answers all twelve.
Each capability is either **SURFACED** (with the pattern named) or **NOT SURFACED + why**.
Silence is not an answer — silence is how the tablecloth gets laid.

| # | Backend capability | Where to find it | The CRUD answer (cheap) | The affordance (what it should be) |
|---|--------------------|------------------|--------------------------|-------------------------------------|
| **C1** | **State machine** — an entity has statuses and legal transitions | the status enum, the service's transition guards, `PRINCIPLE_FINDINGS` | a dropdown listing all statuses; a 422 when it's illegal | **The UI shows only the transitions that are legal RIGHT NOW**, with the reason the others are not. A visible lifecycle (a stepper/timeline), not a select. The user learns the process by using it. |
| **C2** | **Events / streams** — the system emits things (webhooks, job progress, log lines, outbox) | the event table, WS/SSE endpoints, the outbox, worker logs | a "Refresh" button | **A live feed.** Progress that actually moves. A counter that ticks. Presence ("Anna is editing"). If the backend knows something changed, the user should not have to ask. |
| **C3** | **Relationships** — real FKs and ownership | the schema, composite FKs, M2M tables | a `select` of ids; a "parent_id" field | **Navigation.** Backlinks: "used in 3 pages" — clickable. Related rails. A breadcrumb that comes from real ownership, not from the route string. The graph the backend already knows becomes the way the user moves. |
| **C4** | **Derivations / computation** — the backend can compute a result before it is committed | pricing, scheduling, validation services, dry-run endpoints | "Save and see what happens" | **Live preview / what-if.** The number updates as you type. The consequence is shown **before** the commit. If a dry-run endpoint does not exist but the logic does — **ask @ARCH for it**; that request is this canon's job. |
| **C5** | **Invariants / constraints** — the DB will refuse certain things (`DATA_INTEGRITY_CANON` ledger) | the INVARIANT LEDGER, unique/exclusion constraints | a 409 toast after the click | **Proactive guidance.** The taken slot is not clickable — it is shown as taken. The impossible action is disabled **with the reason**. A 409 that reaches the user is a UI that failed to know what the DB already knew. |
| **C6** | **History / audit / versioning** | the audit table, `version` columns, soft delete | a "Log" tab nobody opens | **Time travel.** Diff between versions. Restore. "Changed by Anna, 2h ago" inline on the field. **Undo** (`INTERFACE_CRAFT` I4) is powered by exactly this. |
| **C7** | **Background pipelines** — jobs with progress, cancel, retry (`ASYNC_WORKERS_CANON`) | JOB_PASSPORTS, PIPELINE_PASSPORT, the progress cursor | a spinner and hope | **The instrument panel.** The real cursor (`64/190`), the stage, the ETA, a working **Cancel** (cooperative — the backend supports it), **Retry** on the failed item only, and the honest error when it fails. The passport already defines all of this — the UI is obliged to show it. |
| **C8** | **Search / indexes / facets** | search endpoints, aggregations, indexed columns | a text field that filters client-side | **Instant, forgiving, faceted search.** The facets come from real aggregates. `/` focuses it. Results are keyboard-navigable. |
| **C9** | **Permissions / roles** | the authz layer, role checks | a 403 after the click | **Show what you CAN do.** Hide or disable-with-reason what you cannot. A 403 the user could have predicted is a UI defect. |
| **C10** | **Bulk / batch endpoints** | batch handlers, transactional multi-writes | one-by-one clicking | **Bulk select + one action + one undo** (`INTERFACE_CRAFT` I3). If the backend has a batch endpoint and the UI clicks 50 times, the frontend threw the capability away. |
| **C11** | **Idempotency** (`DATA_INTEGRITY` §4) | Idempotency-Key handling, inbox dedup | a confirm dialog to protect against double-submit | **Safe retry and Undo.** Because the backend is idempotent, the UI can be brave: act immediately, offer undo, retry silently on a network blip. Idempotency is what BUYS the fearless interface. |
| **C12** | **Aggregates / derived data** | materialised views, metrics registry (M-XX) | a number in a box | **An insight.** The number plus its trend, its comparison, and the one action it implies. A KPI card with no delta and no action is a decoration. |

---

## §2. THE ARTIFACT — `docs/artifacts/CAPABILITY_MAP_[MODULE].md`

Produced by **@FRONTEND** by reading the actual backend (schema, endpoints, services, passports — not the ticket
description), confirmed by **@ARCH**, consumed by **@DESIGN** as SPEC input. One page per module.

```markdown
# CAPABILITY MAP — [module]
> Source read: [schema files] · [endpoints/OpenAPI] · [services] · [JOB/PIPELINE_PASSPORT] · [INVARIANT LEDGER]
> Date: [ ] · @FRONTEND: [ ] · confirmed @ARCH: [ ]

| # | Capability found in the backend | Surfaced as | Owner screen | Status |
|---|--------------------------------|-------------|--------------|--------|
| C1 | booking: draft→confirmed→cancelled; cancel illegal after start | a lifecycle stepper; only legal actions rendered | BookingDrawer | ✅ |
| C2 | ingest emits progress (cursor N/M) via job status | live progress bar + stage + cancel | IngestPanel | ✅ |
| C5 | EXCLUSION on (resource, time-range) — "no double booking" | taken slots are visually taken and unclickable; no 409 reaches the user | Calendar | ✅ |
| C6 | audit table on courses (who/when/old→new) | version history drawer + diff + restore | CourseEditor | 🟡 P2 |
| C4 | pricing service can compute total without committing | — | — | ❌ NOT SURFACED — **no dry-run endpoint. REQUESTED from @ARCH (ADR-0xx).** |
| C10 | POST /leads/bulk-status exists | — | — | ❌ NOT SURFACED — deliberate: bulk is P3 |

## Capabilities the backend does NOT have, but the experience needs
> This is the reverse direction, and it is the most valuable row in this document:
> the frontend discovers what the backend must grow.
| Need | Why the experience requires it | Requested from @ARCH |
|------|-------------------------------|----------------------|
| a dry-run price endpoint | the user must see the total before committing (C4) | ADR-0xx |
| an SSE progress channel | polling a job every 2s is a fake live feed (C2) | ADR-0xx |
```

**The reverse direction is the point.** A frontend that only consumes what exists is a tablecloth. A frontend
that reads the backend and says *"give me a dry-run endpoint and this becomes a different product"* — that is
the frontend making the backend play. This request is legitimate, expected, and goes through @ARCH by ADR.

---

## §3. THE GATE

```
@FRONTEND: no CAPABILITY_MAP for a module → no handoff to @DESIGN. (The map costs an hour and changes the product.)
@DESIGN:   the SPEC must reference the map. Every SURFACED capability has a pattern named in the SPEC.
           Every NOT-SURFACED one is a conscious line, not an omission.
@ARCH:     confirms the map is honest (nothing invented, nothing missed) and answers the reverse-direction requests.
@QA_ARCH:  a capability marked SURFACED in the map but absent from the code → 🔴.
           A 409/403 that reaches the user for a condition the UI could have known → 🔴 (C5/C9 violated).
@LEAD:     an epic with a rich backend and a CRUD-only UI is not "done" — it is unexploited.
```

---

## §4. THE TABLECLOTH DETECTOR — 10 signs the frontend is a film over the backend

| # | Symptom | What it means | Fix |
|---|---------|---------------|-----|
| **T1** | Every endpoint has exactly one screen; every screen is a table or a form | The UI is a mirror of the API, not a product | Read the capability map: an experience often spans 3 endpoints, or 1 endpoint yields 3 experiences |
| **T2** | A 409 / 422 / 403 regularly reaches the user | The UI does not know what the DB and the authz layer already know (C5, C9) | Surface the constraint **before** the click |
| **T3** | A "Refresh" button exists on a screen whose data the backend knows has changed | The system has events; the UI ignores them (C2) | A live channel, or at minimum a smart poll with a change indicator |
| **T4** | Related entities are chosen from a `select` of ids | The backend has a graph; the UI has a dropdown (C3) | Navigation, backlinks, "used in N places" |
| **T5** | Any long operation shows an indeterminate spinner | The job passport defines a real cursor; the UI shows a lie (C7) | The real progress, the stage, cancel, retry |
| **T6** | The user must save to find out what happens | The backend can compute; the UI does not ask (C4) | Live preview, or request a dry-run endpoint |
| **T7** | The audit/version data exists and no screen uses it | History is stored and thrown away (C6) | Time travel, diff, restore, undo |
| **T8** | A batch endpoint exists; the UI loops one-by-one | The capability was thrown away (C10) | Bulk UI |
| **T9** | A KPI is a bare number in a box | The aggregate exists; the insight does not (C12) | Number + trend + comparison + the action it implies |
| **T10** | The frontend team never asked the backend team for anything | Nobody looked at the backend as an instrument to be played | The reverse-direction table in §2 is empty = the map was not really done |

**Verdict:** 3+ hits → the module's frontend is a tablecloth. It is not fixed by restyling. Redo the capability map.

---

## §5. THE PRINCIPLE BEHIND ALL OF IT

> **A rich backend is an instrument. A frontend is how it is played.**
> The same violin, in two pairs of hands, is two different objects.
>
> The frontend's job is not to *expose* the backend — exposing is what OpenAPI already does, for free.
> The frontend's job is to make the backend's power **felt**: to turn a state machine into a sense of progress,
> a constraint into guidance, an event into presence, a computation into confidence, a history into safety.
> Every one of those is already paid for in the backend. Not surfacing it is throwing money away.

---

Reference: `roles/INTERFACE_CRAFT_CANON.md` (the affordances themselves: I1–I12, bulk, undo, keyboard) · `roles/VISUAL_CRAFT_CANON.md` (how it looks) · `roles/DATA_INTEGRITY_CANON.md` (§9 the INVARIANT LEDGER — the source of C5) · `roles/ASYNC_WORKERS_CANON.md` (JOB/PIPELINE_PASSPORT — the source of C7) · `roles/ARCH_SPINE_PROTOCOL.md` (the decision spine — the source of C1/C3) · `roles/METRICS_PROTOCOL.md` (M-XX — the source of C12) · `roles/ROLE_FRONTEND.md` (owns the map) · `roles/ROLE_ARCH.md` (confirms it; answers the reverse direction) · `roles/ROLE_DESIGN.md` (consumes it) · `roles/ROLE_QA_ARCH.md` (the gate)
Version: 1.0 | 2026-07-12
