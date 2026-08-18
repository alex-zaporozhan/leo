# PLANNING_MATURITY_CANON — planning to production depth, in one run

> **What this is:** the discipline that makes planning approach production-readiness before code — via a self-audit loop inside a single agent run, proven by a ledger.
> **Anchor:** `.cursorrules` Law 41. Extends Laws 2 (no `# TODO`), 12 (fact or admission — no third state), 14 (no "practically good"), 13 (quality stops speed).
> **Read by (deciding roles):** @CREATOR, @BIZ, @ARCH, @PRINCIPLE, @DESIGN — and @LEAD (who verifies via the Completeness gate).
> **The bar it enforces:** `roles/PRODUCTION_READINESS_CANON.md` (full production release — **not** «MVP quality»).
> **Client UX floor when a Contour ships:** `roles/PRODUCT_MATURITY_CANON.md` **L3**.

---

## §0. THE PRINCIPLE — raw planning is a `# TODO` in the foundation

Incomplete planning is not "faster" — it is **debt in the foundation**. The more under-developed the design detail, the more expensive (or impossible) it is to reinforce the collapsing building later. Planning cost is **front-loaded on purpose**: it is cheaper to plan deeply once than to re-run planning on every implementation step.

> **Anti-Goodhart (critical):** we never instruct a role to "plan in N passes". A target on the pass-count makes the model optimize the counter and close its eyes to consequences. The target is the **bar** (production depth) + the **loop** + the **proof** (ledger). Fewer downstream reworks emerge as a *consequence*, never as an instruction.

> **Anti-MVP (critical):** «Absolutely Functional MVP» is **not** the planning target. The target is an **Absolutely Functional Production Release** delivered as **Production Release Contours** (`PRODUCTION_READINESS_CANON` §5): foundation complete for the intended product; delivery phased; every shipped Contour at **L3**. Phasing never excuses foundation gaps or L1 furniture.

---

## §1. THE SELF-AUDIT LOOP — three stages inside ONE agent run

A planning role does **not** hand off after its first draft. Within one run it performs:

1. **DRAFT** — design the whole product/module to production depth (`PRODUCTION_READINESS_CANON`), not an MVP-quality slice. Name **Production Release Contours** (what becomes claimable when green) separately from foundation (never `LATER`).
2. **RED-TEAM SELF-PASS** — the role switches stance and attacks its own draft with the **closed catalogs it already owns** (§3). It looks for two things: **gaps** (what is missing) and **formal answers** (placeholders dressed as decisions — "we'll sync later", "generally", "TBD", a status with no transition owner, a service-level `if` called an invariant).
3. **REFINE** — close every finding, **or** convert it to a typed deferral with an owner and a cost (§2 DECLARED-OPEN).

The loop repeats until the SELF-PASS finds nothing new that the three criteria (§2) require. It is one continuous run, not a handoff-and-wait.

---

## §2. THE THREE FORMAL CRITERIA (checkable, not felt)

Every planning artifact is graded on three axes. A miss on any is not "practically fine" (Law 14) — it is unfinished.

### Criterion 1 — RESPONSIBILITY (ответственность)
Every claim in the plan is in exactly **one of two states**. There is no third (silent vagueness):
- **DECIDED** — backed by a number, a rule, or a reference.
- **DECLARED-OPEN** — marked `[CLARIFY]` / `[STUB]` / `[ASSUMED]` **+ owner + cost of leaving it open**.

A claim that is neither (a soft "probably", "generally", "later") = a Law 12 violation.

### Criterion 2 — FORESIGHT (дальновидность)
Every entity / flow / screen answers the fixed **forward-question set**:
- Will the schema/contract survive waves N+1 and N+2 without a breaking migration?
- Are **all** lifecycle states and forbidden transitions enumerated?
- What happens on failure / at the edges (empty, huge, hostile, concurrent)?
- Who sees what / may do what (authority)?
- What breaks at 10× load/data?

A design that does not answer these is **sketched, not planned**.

### Criterion 3 — COMPLETENESS (полнота)
Coverage is measured against the **product-need surface**, not intuition:
- Every item of the **production completeness rollup** (§3 below) + the niche map (`roles/niches/*`) + the page-type minimum (`DOMAIN_STANDARDS.md`) is tagged **`Contour / WAVE-1 | WAVE-2 | LATER | OUT`** (delivery), with **foundation items forbidden from `LATER`** (`PRODUCTION_READINESS_CANON.md` §2).
- Prefer Contour IDs when the program has them (e.g. `PR-VIDEO`, `PR-NODES`) — a Contour is a **claim gate**, not a quality downgrade.
- Uncovered surface = a gap, not an omission-by-choice, unless explicitly `OUT` with a reason + owner + trigger.
- Forbidden completeness cheat: tagging a foundation row `WAVE-2` because «MVP first».

---

## §3. THE ATTACK CATALOGS (reused, not invented)

The RED-TEAM SELF-PASS attacks with catalogs that already exist — no new detector is created:

- **The 12 adversaries** (`ROLE_PRINCIPLE.md` MODE: MODEL): double · race · death · reversal · stale · partial · impostor · outlier · wrong-order · abandonment · liar · scale.
- **The forbidden phrases** (`LEAD_ANTI_CHECKBOX_PROTOCOL.md` §3) — detects formal answers.
- **The production completeness rollup** — 15 classes: roles/RBAC · lifecycles · money routes (refund/partial/tax) · notifications matrix · audit trail · export/GDPR/erasure · admin-vs-client surfaces · reporting/aggregates · integrations (mandatory vs stub) · onboarding/first-value · i18n/timezone · niche edge features · billing tiers · out-of-scope + stubs registry · data lifecycle. (Cross-checked against `DOMAIN_STANDARDS.md`.)
- **The foresight classes** — tenancy day-one · state machine vs enum-in-code · idempotency/webhook-inbox · money minor-units + currency · time timestamptz + `[start,end)` + DST · soft-delete + RESTRICT-not-CASCADE · additive-only contracts · outbox · pagination/index plan · PK strategy · invariant at schema level (not service `if`) · report grain / aggregate store.
- **The TASTE GATE** (`VISUAL_CONCEPT_PROTOCOL.md` §4, C1–C10) — for concept/design planning.

Each role attacks with the catalogs relevant to its layer (declared in its `ACTIVATES_CANONS:`).

---

## §4. THE COMPLETENESS LEDGER — proof of the loop

The role emits a ledger as evidence that the loop actually ran. **No ledger = the plan is incomplete = handoff blocked** (same discipline as Law 12: you cannot merely claim "done").

```markdown
## COMPLETENESS LEDGER — [artifact]

### Self-pass catalogs run: [PRINCIPLE-12 | rollup | foresight | anti-checkbox | TASTE GATE]

### Gaps found on RED-TEAM SELF-PASS and closed:
- [GAP] <what was missing> → CLOSED: <the decision now in the artifact>
- [FORMAL] <placeholder that pretended to be a decision> → CLOSED: <real decision>

### Declared-open (with owner + cost):
- [CLARIFY|STUB|ASSUMED] <item> → owner: <role/human> · cost of leaving open: <concrete>

### Criteria self-grade:
- Responsibility: every claim DECIDED or DECLARED-OPEN? [yes/no]
- Foresight: forward-question set answered for each entity/flow/screen? [yes/no]
- Completeness: every product-need-surface item tagged Contour/WAVE/OUT? [yes/no]
- Production bar: no foundation item deferred as MVP/Pilot? [yes/no]
- Contours: claim language matches Contour membership (not «PP-1+PP-2 = whole product»)? [yes/no]
```

### Anti-Goodhart lock — external spot-check
A pure self-report ledger can be written "plausibly but shallow". So **@LEAD (or @AUDITOR) spot-checks 2–3 random ledger lines against the actual artifact** — not the whole ledger (that would re-run the loop), just a sample. This is the same move as a red-green test instead of a narrative claim (@PENTEST): cheap, but it makes a fake ledger materially harder. A spot-check miss (a "CLOSED" line that is not actually closed in the artifact) → the ledger is rejected, handoff blocked.

---

## §5. HOW THIS SAVES COST (the mechanism, no invented numbers)

Re-planning mid-implementation compounds: every downstream layer built on a planning gap must also be rebuilt (DB migration + data backfill + contract change + the code and UX on top). Front-loading planning to production depth turns the implementation roadmap into a runway — the expensive cascade never starts. The cost is paid once, at the cheapest layer to change (a document), instead of repeatedly at the most expensive (a live schema under load).

*(Do not quote pseudo-precise multipliers as if measured — state the mechanism.)*

---

## §6. OWNERS

- **@CREATOR / @BIZ** — the rollup + niche completeness map; foundation items not `LATER`.
- **@ARCH / @PRINCIPLE** — foresight criterion on schema/contracts/invariants; the ledger for the data/logic foundation.
- **@DESIGN** — the concept-lock ledger before layout (`VISUAL_CONCEPT_PROTOCOL.md`; concept + tokens + section skeleton fixed before any wireframe).
- **@LEAD** — the FORESIGHT / Completeness gate: verifies the ledger exists, criteria are met, and runs the external spot-check (§4). No gate is raised on a missing or shallow ledger.

---

Reference: `.cursorrules` Law 41 (+ Laws 2/12/13/14) · `roles/PRODUCTION_READINESS_CANON.md` (the bar · Contours §5) · `roles/PRODUCT_MATURITY_CANON.md` (L3 floor on Contour ship) · `roles/ROLE_PRINCIPLE.md` (12 adversaries, MODE: MODEL) · `roles/LEAD_ANTI_CHECKBOX_PROTOCOL.md` (forbidden phrases) · `roles/VISUAL_CONCEPT_PROTOCOL.md` (TASTE GATE) · `roles/ROLE_LEAD.md` (the gate) · `roles/DOMAIN_STANDARDS.md`
Version: 1.1 | 2026-07-26
