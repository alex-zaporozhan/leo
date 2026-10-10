# 📋 BUSINESS_LOGIC: [Project Name]

> **[v6.16] This is the SCHEMA of the artifact `docs/artifacts/BUSINESS_LOGIC.md`, filled from the project specification + @BIZ/@CREATOR.** Once filled — the single source of truth on project business rules (sections 5–9). The template = structure; the filled BUSINESS_LOGIC.md = truth. TPF files are NO LONGER the source of business logic.

> Single source of truth for project business logic.
> Created by @CREATOR, extended during development.
> Version: v1.0 | Date: [date]
>
> Sections 1–4 are generated automatically by @CREATOR from INDUSTRY INTELLIGENCE.
> Sections 5–14 are filled manually — these are rules recorded nowhere else.

---

## 1. Product essence
[We help [whom] do [what] without [which pain]]

## 2. Target audience and monetisation
- **Who pays:** [title, company size, specific portrait]
- **Who uses:** [may differ from who pays]
- **Main pain:** [loss of money / time / risk]
- **Monetisation model:** [subscription / one-time / % of transaction]
- **Price:** $[X]/mo — rationale: [comparison with competitors]
- **ROI for client:** pays off in [X months] given [Y]
- **Differentiator:** [one sentence — why they will choose us]
- **KILL SIGNAL:** ✅ Verified by @BIZ / ⛔ [what triggered]

## 3. WAVE-1 scope (first paying client)
<!-- MIRROR OF: PRODUCTION_READINESS_CANON.md + PLANNING_MATURITY_CANON.md (Law 41) | index: CONFLICT_REGISTRY.md -->
> **Law 41 anchor — production-readiness by default.** MVP / WAVE-1 / Roadmap sequence **which features ship when** —
> a delivery schedule, never a licence to cut the foundation. The foundation set (§5 business rules · §7 domain rules ·
> §8 constraints · §9 dependencies + their Fallbacks) is decided **now, at production grade** — it is never left as a
> blanket `[FILL IN]` "to decide later". A raw foundation is a `# TODO` in load-bearing concrete: every deferred
> foundation decision is cheaper to make here than to retrofit under a live product (mirrors PLANNING_MATURITY_CANON).

- [ ] [feature 1]
- [ ] [feature 2]
- [ ] [feature 3]

## 4. Roadmap
> Sequences **features** across delivery waves. Foundation quality does not appear here — it is uniform from WAVE-1.

| Wave | What's included | Launch criterion |
|--------|-----------|-----------------|
| WAVE-1 | [list] | first 3 paying clients |
| V1 | [list] | after first 10 clients |
| V2 | [list] | [condition] |

---
> Below — rules and decisions that are NOT generated automatically.
> This is a living document: every significant decision is recorded here.
---

## 5. Business rules (NEVER changed without an explicit team decision)

> These are not technical constraints — this is domain logic.
> Violation = bug level 🔴 regardless of how the code is written.

### Rule 1: [name]
**Condition:** [when it applies]
**Action:** [what happens]
**Why it cannot be violated:** [business consequence]
**Exceptions:** [if any — explicitly stated]

### Rule 2: ...

---

## 6. Business rules (configuration — changes frequently)

> This is not hardcode — these are parameters changed by an administrator.
> @DEV implements as settings, not as constants in the code.

| Parameter | Default value | Who changes | Where in UI |
|----------|-----------------------|------------|---------|
| [parameter] | [value] | Owner / Admin | [settings section] |

---

## 7. Critical domain business rules

> Industry-specific rules that cannot be violated.
> Source: BUSINESS_ROUTES.md (Data axis + Money Routes) from @DOMAIN_EXPERT.

- [rule] — consequence of violation: [what breaks]

---

## 8. Domain constraints

> Regulator, licences, legal specifics.
> Affects architecture — @ARCH reads this section before designing.

- **Licences:** [required / not required — rationale]
- **Personal data:** [how stored, local data-protection law if applicable]
- **Industry norms:** [what restricts]
- **Geography:** [local / regional / global — what that entails]

---

## 9. External dependencies (critical)

> Without these the product physically does not work. Every dependency has a Fallback.

| Service | Why | Risk | Fallback |
|--------|-------|------|---------|
| [service] | [why] | 🟢/🟡/🔴 | [what we do if it goes down] |

---

## 10. A-B decisions (recorded choices)

> Every architectural or product choice is recorded here.
> Goal: not to revisit closed questions a month later.

| Question | Chosen | Reason | Condition to revisit |
|--------|---------|---------|-------------------|
| [question] | [A or B] | [one sentence] | [under what condition we change] |

---

## 11. Stubs (agreed)

> Features intentionally implemented as stubs. @QA_ARCH does not block on them.

| Stub | Date agreed | When to implement | Owner |
|----------|-------------------|-------------------|---------------|
| [what] | [date] | [version / condition] | [@role] |

---

## 12. Closed objections (top 5 for sales)

> Filled by @BIZ. Used during demos and negotiations.

| Objection (client's words) | Answer | Evidence |
|---------------------------|-------|---------------|
| [objection] | [answer] | [what to show] |

---

## 13. Success metrics

- **Technical:** uptime ≥ 99.5%, response < 500ms, 0 critical bugs
- **Business:** [MRR in 6 months], [churn < X%], [N clients]
- **User:** [time to first value < X min], [NPS > X]

---

## 14. Explicitly out of scope (not doing in this version)

- [feature] — reason: [why not now]

---

*Sections 5–9 change only through explicit decision with recorded date.*
*Sections 1–4 updated by @CREATOR when strategy changes.*
*Sources: BUSINESS_ROUTES.md · MARKET_AUDIT.md · roles/TEMPLATE_BIZ_LOGIC.md*
