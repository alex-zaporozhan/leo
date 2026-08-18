# PRODUCTION_READINESS_CANON — foundation complete; delivery phased

> **What this is:** the quality bar for a **healthy full production product** — not a pilot excuse, not an «MVP quality» licence.
> **Anchor:** planning must reach this bar (`PLANNING_MATURITY_CANON`); shipped client surfaces must meet **L3** (`PRODUCT_MATURITY_CANON`).
> **Read by:** @LEAD · @ARCH · @CREATOR · @BIZ · @QA_ARCH · @OPS

---

## §0. THE DOGMA

**We ship a production release.** Delivery may be **phased** (contours / waves). Phasing never means:

- lowering security, tenancy, or invariants;
- shipping client furniture at L1 («it works»);
- hiding foundation gaps under «MVP / Pilot / post-pilot»;
- claiming the whole product done when only one contour is green.

> **Anti-phrase:** «Absolutely Functional MVP» is **not** a product bar.  
> **Correct phrase:** **Production Release Contour** — a named, claimable slice of the intended product, delivered at production quality (foundation intact + L3 surfaces).

---

## §1. FOUNDATION vs DELIVERY

| Layer | Meaning | May be phased? |
|-------|---------|----------------|
| **Foundation** | Tenancy/RLS · authz · error contract · idempotency · allowlisted egress · secrets · audit · soft-delete · timeouts · JOB/PIPELINE passports · credential model · RF/compliance filters that apply to day-one providers | **No** — must be designed and built for the intended product, not deferred as «MVP» |
| **Delivery** | Which operator journeys / surfaces / providers are *claimable today* | **Yes** — tagged Contour / WAVE-1 / WAVE-2 / LATER / OUT |

A foundation gap labeled «MVP» = **🔴** planning failure (`PLANNING_MATURITY` completeness).

---

## §2. FOUNDATION MUST NOT BE `LATER`

These classes are **forbidden** from `LATER` when the product already exposes the related surface:

1. Identity / RBAC / tenant boundary  
2. Mutation idempotency + unique invariants  
3. Credential / secret resolution (no silent platform fallback after org disable)  
4. Allowlisted outbound integrations (no arbitrary URL)  
5. Structured errors + observability on money/AI/job paths  
6. Webhook tenancy when multi-tenant BYOK is claimed  
7. Soft-delete / RESTRICT where entities are shared  
8. Session/DB guard rails (timeouts) on new write paths  
9. License allowlist for every new provider/adapter  
10. Security Contract on any SECURITY SURFACE slice  

Delivery features (extra providers, Instant Avatar, Template API) may be WAVE-2 / Scale / OUT — with owner + trigger.

---

## §3. PRODUCTION-READY LAYERS (checkable)

For each Contour being claimed, all applicable layers are green — not «demo-happy»:

| Layer | Production-ready means |
|-------|------------------------|
| **Data** | Schema invariants hold under concurrency; additive contracts; no stub tables pretending to be product |
| **Logic** | Happy path + failure edges; real ports (not stub on prod path); single retry owner; lease/heartbeat where async |
| **UX** | Declared **product class** at **L3** (`PRODUCT_MATURITY`); no internal names; EmptyState; mutation disable |
| **Security** | S1–S12 grepped; Contract in DEV_PROMPTS; tenant isolation evidenced |
| **Design** | Class non-negotiables in SPEC; Reference Walk written; craft floor V15–V20 |
| **Ops** | Versioned health; runbooks for stuck jobs; Law 36 artifact identity on env claims |

---

## §4. MVP / PILOT LANGUAGE — WHEN ALLOWED

| Allowed | Forbidden |
|---------|-----------|
| «Contour PR-VIDEO is the first *claimable* production release» | «MVP quality is enough for Integrations» |
| «WAVE-2: live GET-by-id validation (declared-open, owner, cost)» | «We'll harden tenancy after pilot» when claiming multi-tenant BYOK |
| Human **WAIVE** with owner + expiry on a Contour checklist item | Silent WAIVE that unlocks marketing claims |
| Internal tool at L2 with level written down | Client product below L3 |

**Pilot** = a *deployment audience* or a Contour WAIVE log — never a quality downgrade of foundation or L3.

---

## §5. PRODUCTION RELEASE CONTOURS

A Contour is a **claim gate**, not a planning shortcut.

```
Contour = named operator outcomes
        + required delivery slices (AND)
        + product classes at L3
        + foundation decisions that must already be closed
        + forbidden claims until green
```

**Program example (generic, illustrative)** — source of truth for IDs lives in the project's `docs/artifacts/LEAD_DECISIONS_*.md`, not in this canon:

| Contour | Claim unlocked when green |
|---------|---------------------------|
| **PR-CORE** | Core operator path works without customer-side secrets in `.env` |
| **PR-GRAPH** | The advertised node/graph surface is actually in production |
| **PR-SHARE** | Primary share / publish path is live |
| **PR-LIVE** | Real-time / live surface may be marketed |
| **PR-CLAIM** | External narrative / pilot story |

**Rules:**

1. Contours nest: later claims require earlier Contours (unless LEAD explicitly splits).  
2. Closing an early planning pack ≠ closing the first production contour (later contours still need their own evidence).  
3. Marketing / sales claim language may only cite Contours that are 🟢 with evidence.

---

## §6. BINDING IN THE CHAIN

```
@LEAD     — names Contours; forbids MVP-as-quality; does not raise claim gates early
@ARCH     — foundation items in spine; Contour membership of each slice
@CREATOR/@BIZ — rollup tagged Contour/WAVE; foundation ≠ LATER
@QA_ARCH  — Vector: MVP language on foundation = 🔴; Contour claim vs evidence
@OPS      — Law 36 before any Contour claimed on an environment
```

---

## §7. RELATION TO SISTER CANONS

| Canon | Role |
|-------|------|
| `PLANNING_MATURITY_CANON` | Plan to this bar in one run; Completeness Ledger |
| `PRODUCT_MATURITY_CANON` | Every client Contour ships at **L3** for its class |
| `SECURITY_GATE_PROTOCOL` | Surface Contours still pass S-0 / S-Wave |
| LEAD decisions / NEXT_STEPS | Contour IDs + OUT with owner |

---

Reference: `roles/PLANNING_MATURITY_CANON.md` · `roles/PRODUCT_MATURITY_CANON.md` · `docs/artifacts/LEAD_DECISIONS_PP_PROD_2026-07-26.md` §8  
Version: 1.0 | 2026-07-26
