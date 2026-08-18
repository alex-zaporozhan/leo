# SECURITY_GATE_PROTOCOL — security is a gate, not a phase

> **Path:** `roles/SECURITY_GATE_PROTOCOL.md`
> **Owner of the verdict:** @PENTEST (blocking) · with @SEC (checklist) · enforced by @LEAD (gate).
> **Connection:** `.cursorrules` Law 38 · `roles/ROLE_PENTEST.md` (arsenal + mindset) · `roles/ROLE_SEC.md` · `roles/LEAD_PRODUCT_GATE_PROTOCOL.md` GATE-1/GATE-4/GATE-6 · `roles/ROLE_ARCH.md` (STRIDE sketch) · `roles/ROLE_PRINCIPLE.md` (authority layer) · `roles/TESTING_CANON.md` (the reliability floor).
> **Strictness:** the gate does not open on words. Only an artifact + a reproduction + a passing regression test. "Practically secure" is not a state.

**Why this canon exists.** In the old flow @PENTEST ran once, late, and only if someone remembered — and it was a blocker in **no** gate. Security therefore depended on luck. This protocol makes the security of the shipped product **strictly mandatory**: it defines *which* changes must be secured (the surface), *when* they are checked (three points), *who* holds the blocking verdict (@PENTEST), and *what* counts as proof (artifacts + tests). The aim is not to pass the gate — it is to raise the reliability floor so high that the gate finds little.

---

## §1 — THE SECURITY SURFACE (the objective trigger)

A change is **on the security surface** if it touches **any** of S1–S12. This list is deliberately concrete and greppable so no role decides it "by feel". Touching the surface makes **S-0** (planning) and **S-Wave** (gate) mandatory.

```
S1  Identity        login / signup / password reset / session / token / JWT / MFA / SSO / OAuth
S2  Authorization   any permission, role, ownership or tenant-scope check; admin-only action; RLS policy
S3  Money           charge / refund / credit / ledger / price / coupon / quota / balance
S4  Object access   any endpoint taking an id/uuid/slug that maps to a stored record  (the IDOR surface)
S5  Untrusted sink  raw/dynamic SQL · ORM .raw / text() · shell/subprocess/eval/exec · template render ·
                    deserialization (pickle / yaml.load / unsafe JSON) · path or filename built from input
S6  Outbound        any HTTP/gRPC request to a URL/host derived from input (SSRF) · webhook callback URLs
S7  Files           upload · download · storage-key generation · archive extraction
S8  Integrations    inbound webhooks · signature verification · external API tokens
S9  Secrets & PII   read/write of secrets, tokens, or personal data · logging/metrics of the same
S10 Public exposure any endpoint/page reachable without auth · a new public route · CORS config ·
                    debug/admin endpoints · OpenAPI/Swagger exposure
S11 Background jobs any async / worker / cron task that touches S1–S9
S12 Infra as code   Docker USER/capabilities · exposed ports · reverse-proxy rules · env/secrets · CI credentials
```

**The "none" mark — a mechanical test, not a judgement.** If a wave touches none of S1–S12, it records in the wave artifact:
`[SECURITY SURFACE: none — <reason: pure UI copy / style / display-only change>]`.
"none" is **not** decided by feel — it is the claim that the diff contains **none** of the signals below. @QA_ARCH verifies it by grepping the actual diff; any single hit falsifies "none":
```
auth · login · logout · session · token · jwt · password · permission · role · is_admin · tenant · owner
.filter( · .where( · raw( · execute( · text( · f"…SELECT · subprocess · eval( · exec( · pickle · yaml.load
requests. · httpx · aiohttp · fetch(<host-from-input> · upload · download · open(<path-from-input> · zipfile
@app.route · @router. · new public route · price · amount · balance · charge · refund · coupon · quota
migration · alembic · Dockerfile · docker-compose · CORS · secret · os.environ · api_key · webhook
```
Zero hits across the whole diff → "none" is admissible. A "none" later shown wrong is itself a **finding** (and a REFLEX candidate — how did the surface get touched unnoticed, and why did the grep miss it?).

**Scope rule.** S-Wave and S-Global judge the **cumulative** surface of the wave/product, not each line in isolation. A sequence of "small" changes that together expose S2+S4 is a surface change even if no single diff looked like one.

---

## §2 — THE THREE CHECKPOINTS

```
S-0  THREAT MODEL   PLANNING   → before final DEV_PROMPTS, before @DEV   (shift-left)
S-Wave ADVERSARIAL  END OF WAVE → inside GATE-4, before deploy            (the blocking gate)
S-Global RED-TEAM   PERIODIC   → milestones + cadence                    (the repeated global sweep)
```

### S-0 — THREAT MODEL (planning)

**When:** the surface trigger fires during planning of a module/epic, after @ARCH's draft spine and @PRINCIPLE's model, **before** @LEAD marks DEV_PROMPTS final (peer to the @PRINCIPLE and @AI_ENGINEER planning nodes).

**Who:** @PENTEST `MODE: THREAT_MODEL`. Inputs, best-effort in this order: @ARCH's STRIDE sketch (spine vertebra 12), @PRINCIPLE's **authority** layer and abuse-cases, the INVARIANT LEDGER. **S-0 is never blocked on a missing input:** if the epic did not trip an architectural trigger and has no spine, @PENTEST derives the abuse cases directly from the touched S1–S12 elements and the DOMAIN_MODEL. A small surface still gets a contract.

**Output:** a single **`## Security Contract`** block written **inside** `DEV_PROMPTS_[NAME].md` (§3 below) — its one canonical home, so @DEV cannot miss it and it travels with the to-dos. There is no separate contract file; the S-Wave report references the contract by the DEV_PROMPTS path.

**Block condition (feeds GATE-1):** DEV_PROMPTS on the security surface **without** a Security Contract is an incomplete artifact — @LEAD does not release it to @DEV (Law 38; GATE-1 blocker).

**Purpose:** security requirements become an **input to the code**, not a finding after it. The most expensive vulnerabilities are the ones designed in; S-0 removes their class before a line is written.

### S-Wave — ADVERSARIAL GATE (end of wave)

**When:** at **GATE-4**, after @QA_ARCH 🟢 and @QA_VISUAL 🟢, alongside @QA and @SEC, before any deploy.

**Who:** @PENTEST runs `CODE_RECON → CHAIN_ANALYSIS → ATTACK_PLAN → CRASH_TEST` against the wave just built, and verifies the **Security Contract held** (each S-0 line satisfied with evidence). @SEC runs its 18 pillars in parallel.

**Output:** `docs/artifacts/PENTEST_REPORT_[WAVE].md` with the VERDICT format (`roles/ROLE_PENTEST.md`).

**Block condition:** any **🔴 blocks GATE-4** — it does not close, no deploy. Every 🔴/🟠 maps to a code owner and becomes a **permanent regression test** that must be added and passing **before** re-gate. 🟠 without a chain → fixed next release with a deadline recorded by @LEAD. This is the gate that "smacks" @DEV/@ARCH — by design.

### S-Global — RED-TEAM (periodic)

**When — the milestone triggers are the spine (each is unambiguous); the wave-count is only a backstop:**
- before the **first client pilot** — mandatory;
- before **every release tag** — mandatory;
- on **any change that widens the surface**: a new public route, a new tenant boundary, a new external integration, a new file-upload path, a new privileged action;
- **backstop:** @LEAD keeps a wave counter; after **N** surface-touching waves with no release-tag sweep in between (default N = 3 surface-heavy / 5 otherwise), run one anyway so a long unreleased streak is never left unswept.

**Who:** @PENTEST `MODE: GLOBAL_AUDIT` — attacks the **whole product as it has grown**, not the last diff. Re-runs the full surface and, critically, hunts the **seams between features** (a new module reusing an old endpoint; a new role widening an old query; two features that are each safe but unsafe combined). Also **regression-verifies** that every past 🔴 still has a passing test guarding it.

**Output:** `docs/artifacts/PENTEST_REPORT_GLOBAL_[DATE].md`.

**Block condition (feeds GATE-6):** an open 🔴 from S-Global caps commercial maturity at **L0** (`LEAD_PRODUCT_GATE_PROTOCOL.md` GATE-6). No pilot/sale on an open critical.

---

## §3 — THE SECURITY CONTRACT (the S-0 artifact, injected into DEV_PROMPTS)

The peer of the **Domain Checklist** and the **INVARIANT LEDGER**. @PENTEST authors it at S-0; @DEV satisfies each line with evidence; @PENTEST re-tests it at S-Wave; @QA_ARCH checks its presence.

<!-- MIRROR SOURCE (SoT): Security Contract template | ROLE_PENTEST.md MODE 0 carries a semantic (compact) echo — sync the requirement set, not the text | index: CONFLICT_REGISTRY.md -->
```markdown
## Security Contract   (surface: [S1–S12])   owner: @DEV verifies · @PENTEST re-tests at S-Wave
- Abuse cases — each MUST be impossible, with the guarantee NAMED (not "the service checks it"):
    · [actor] does [action] → would reach [impact]   → impossible by [constraint / lock / scope / authz layer]
- Authorization: every object read/write scoped by [tenant_id + ownership], enforced at [server layer]; IDOR → 404
    · Nested/related objects are scoped too — the INNER id is validated, not only the outer path segment (leaf C1.nested)
    · Function-level: a `role × endpoint × method` matrix exists; a role not allowed → 403/404 (leaf C2.BFLA)
- Idempotency / race: [effect] guarded by [unique constraint / FOR UPDATE / idempotency key] — not an `if`
- Input → sink: [field] reaches [sink] only via [parameterised query / allowlist / no shell / safe deserializer]
- Outbound (if S6): every URL/host from input passes a DNS-resolved ALLOWLIST; link-local/metadata/`file://`/`gopher://` denied; encodings + DNS-rebinding covered (leaves S6.metadata/S6.bypass)
- Secrets & PII: [field] never appears in [logs / metric labels / response body / URL] (leaf S9.piiurl)
- Identity (if S1): passwords/reset flows are enumeration-uniform (same message + timing); secret/HMAC compares are constant-time; refresh tokens rotate with reuse-detection (family invalidation); session id rotates on login (no fixation) (leaves B1.enum/B1.timing/B2.refresh/B3.fixation)
- CSRF: every state-changing request (incl. any state-changing GET) is CSRF-protected; cookies carry HttpOnly+SameSite+Secure (leaves B4.csrf-get/B3.cookie)
- CORS (if S10): allowed origins are an explicit allowlist; no reflected/`null` Origin with credentials (leaf S10.cors)
- Webhooks (if S8): signature verified (reject alg=none/empty-secret), timestamp window enforced (replay), inbox-dedup by event_id (leaf F1.sigbypass)
- Files (if S7): upload validated by magic bytes not extension, stored off web-root non-executable; archive extraction and export paths reject `../` (leaves E3.polyglot/E3.zipslip/E3.pathwrite)
- Abuse limits: [endpoint] rate-limited at [layer] and the limit is not bypassable by header/case/path/batch variation (leaf B1.ratelimit-bypass)
- Exposure: new endpoints are auth-required unless [explicitly public + does nothing privileged + recorded]
- Infra (if S12): container non-root, caps dropped, no exposed debug, TLS verified, lockfile pinned
- Level-1 security tests @DEV writes THIS epic: [tests keyed 1:1 to the abuse cases above]
```

> The conditional lines fire **only** when their surface element is touched (S1/S6/S7/S8/S10). Each names the `roles/ROLE_PENTEST.md` **named leaf** it defends and its `roles/PENTEST_SCENARIOS.md` §§10–15 scenario, so @DEV knows exactly what @PENTEST will run at S-Wave. This block is the **canonical, full** template; `roles/ROLE_PENTEST.md` MODE 0 carries a **compact semantic echo** of it — keep the *requirement set* in sync, but the two are intentionally not verbatim (do not force identical text).

---

## §4 — GATE-4 INTEGRATION (the mandatory blocker)

`LEAD_PRODUCT_GATE_PROTOCOL.md` GATE-4 "Quality and Security" gains a **@PENTEST blocker set** alongside @QA / @SEC / @QA_VISUAL. GATE-4 does not close while any is open:

```
### Blockers @PENTEST (S-Wave) — only when the SECURITY SURFACE was touched:
□ PENTEST_REPORT_[WAVE].md exists; verdict recorded; surface (S1–S12) listed
□ Security Contract from DEV_PROMPTS: every line verified HELD in code (not "should be")
□ No open 🔴 (tenant escape / auth bypass / injection / money manipulation / secret-PII leak / live T-series failure / working chain)
□ Every 🔴/🟠 has a permanent regression test — added and PASSING — before re-gate
□ CHAIN_ANALYSIS run on all 🟡 (chains proven or ruled out — not left implicit)
□ T-series green for the epic's applicable classes (T-H isolation / T-A…G async / T-I pipeline) — N/A when the epic has no async, no shared mutable resource and no pipeline
□ Every accepted 🟠/🟡 (if any) has a logged SECURITY_RISK_ACCEPTANCE record (§4A) with an owner and an expiry
□ 🟠 without a chain and without acceptance: deadline recorded by @LEAD; not silently deferred
```
If the surface was `none` (validated by the §1 grep), this set is marked `N/A — no security surface` and GATE-4 proceeds on @QA/@SEC/@QA_VISUAL.

---

## §4A — RISK ACCEPTANCE (narrow, logged, human-only — the gate survives without being disabled)

A blocking gate with zero give is either honoured or quietly deleted. To stay merciless **and** durable, the gate has one narrow, explicit exit — and it is nothing like hiding a hole (that is the point of §7): an accepted risk is **written down, owned, time-boxed and re-surfaced**, which is the opposite of concealment.

- **🔴 is never acceptable.** Tenant escape · auth bypass · RCE/SQLi · money manipulation · secret/PII leak · a live T-series failure on a real invariant · a working exploitation chain — these **cannot** be risk-accepted by anyone. They are fixed or the product does not ship. No agent, no @LEAD override, no deadline.
- **Only 🟠/🟡 may be accepted**, and **only by the human developer/owner** — never by @LEAD, never by any agent, never inferred from silence. @PENTEST states the residual risk plainly; the human decides.
- **An acceptance is an artifact, not a shrug.** `docs/artifacts/SECURITY_RISK_ACCEPTANCE_[DATE].md`: the finding ID + vector · why it is accepted now · the compensating control (if any) · the **owner** · an **expiry date** · a tracking issue. Absent any field → not accepted, still blocking.
- **Accepted risk is re-surfaced at every S-Global** and re-evaluated (has the surface grown around it into a 🔴 chain?). An expired acceptance re-blocks at the next gate. The acceptance log is append-only.

This is what keeps @PENTEST's verdict real: the team is never forced to choose between "ship a known-critical" and "disable the gate". The only door is narrow, logged, and closed to criticals.

---

## §5 — SEVERITY → ACTION

| Grade | Examples | Action |
|-------|----------|--------|
| 🔴 CRITICAL | tenant escape · auth bypass · RCE/SQLi · money manipulation · secret/PII leak · live T-series failure · working chain | **Blocks GATE-4 (and caps GATE-6 at L0).** Fix + regression test before re-gate. No deploy. |
| 🟠 HIGH | vuln with a precondition · missing rate limit on a sensitive endpoint · enumeration via message diff | Fix next release, deadline recorded; or block if it composes into a 🔴 chain. |
| 🟡 MEDIUM | hardening gap · info disclosure with no exploit yet · a control lacking defence-in-depth | Backlog with an owner; **always** fed to CHAIN_ANALYSIS — never dismissed silently. |
| 🟢 INFO | observed weakness, no path today | Recorded; re-checked at the next S-Global as the surface grows. |

**Severity ≠ QA risk-tier.** This grade rates the criticality of a *finding* (decided here, at the gate). The QA tier T0–T3 (`roles/TESTING_CANON.md` §2A) rates the required test *intensity* of a *path* (decided at planning, by blast radius). They are orthogonal: a T0 path can yield only a 🟢 finding; a T2 path can yield a 🔴. Do not map one onto the other.

---

## §6 — EVIDENCE ARTIFACTS (what proves the gate, not words)

| Artifact | Point | Author | Home |
|----------|-------|--------|------|
| `## Security Contract` — a section **inside** `DEV_PROMPTS_[NAME].md` (no separate file) | S-0 | @PENTEST | in the DEV_PROMPTS |
| `PENTEST_REPORT_[WAVE].md` (verdict + chains + findings + owners) | S-Wave | @PENTEST | `docs/artifacts/` |
| `PENTEST_REPORT_GLOBAL_[DATE].md` (full re-run + cross-feature + regression check) | S-Global | @PENTEST | `docs/artifacts/` |
| Regression tests `tests/pentest/test_[vector].py` — one per past 🔴/🟠 | S-Wave/S-Global | @DEV (from @PENTEST PoC) | `tests/pentest/` |
| `SEC Report` (18 pillars) | S-Wave | @SEC | `docs/artifacts/` |

**Regression permanence rule.** A finding, once fixed, leaves behind a test that runs forever. The security test suite is append-only: coverage grows with the surface and never shrinks. A silently deleted pentest regression = a Law-14 violation.

**Red-green proof rule.** A regression test is only accepted if it was **observed to FAIL against the vulnerable code** (red) and to **pass after the fix** (green). A test that was green from the start proves nothing — it may not exercise the vuln at all. @PENTEST (or @DEV, with @PENTEST confirming) records the red run alongside the green one. An unproven "regression test" does not close a 🔴 (mirrors Law 14: a control is verified or it is a finding).

---

## §7 — THE "NO HIDING" PRINCIPLE (raise the floor, don't dodge the gate)

The gate exists to make the product **honestly** reliable, not to be evaded. Two consequences:

1. **Findings feed the source, not just the instance.** A 🔴 is patched *and* its class is removed: a missing tenant scope → not one patch but a repository-scope pattern + a @QA_ARCH grep; a race → a constraint pattern in the INVARIANT LEDGER; a recurring class → **⚡ REFLEX**, and if it is a knowledge gap, an **`@EVOLVE`** candidate (`roles/SYSTEM_EVOLUTION_PROTOCOL.md`). "Passed pentest" over a patched instance while the class survives is a failure.
2. **@ARCH and @DEV build knowing @PENTEST will come — to build higher, not to hide lower.** The self-pentest before handoff (`roles/ROLE_PENTEST.md`, "Бич contract") is not concealment; it is the floor. The measure of success is that S-Wave finds only genuinely non-obvious issues — because the obvious ones were designed out at S-0 and self-caught before handoff.

---

## §8 — ROLE RESPONSIBILITIES

| Role | Duty in this protocol |
|------|-----------------------|
| **@LEAD** | Detects the surface trigger; routes S-0 before final DEV_PROMPTS; enforces the GATE-4 @PENTEST blockers; records 🟠 deadlines; runs REFLEX on repeated same-class findings; schedules S-Global by cadence. Cannot raise the gate on words. |
| **@PENTEST** | Owns the blocking verdict. Authors the Security Contract (S-0), attacks the wave (S-Wave), red-teams the product (S-Global). |
| **@ARCH** | STRIDE sketch is real and feeds S-0; architectural findings return to the spine + ADR, not a @DEV patch. |
| **@PRINCIPLE** | The authority layer + the twelve adversaries produce abuse-cases captured for S-0, so @PENTEST does not discover them as holes. |
| **@DEV** | Satisfies each Security Contract line with evidence; runs the self-pentest before handoff; writes Level-1 security tests + the regression test for every finding. |
| **@DESIGN** | Safe affordances (destructive confirms, no internal ids/secrets in UI, safe defaults, clear session/logout); a UX-level finding names @DESIGN. |
| **@QA** | Builds the reliability FLOOR (functional + negative + regression, `roles/TESTING_CANON.md`); a finding @QA "should" have caught permanently expands the QA floor. |
| **@QA_ARCH** | Runs the Security-Surface preflight; does not issue final 🟢 on a surface change until the Security Gate is scheduled/passed. |
| **@SEC** | The 18-pillar checklist runs in parallel at S-Wave but is **advisory**: it feeds @PENTEST's report. An exploitable pillar gap becomes a @PENTEST finding (graded, possibly blocking); a non-exploitable gap is a 🟡. **@PENTEST alone holds the blocking verdict** — one gate, not two competing ones. |

---

## §9 — RELEASE DoD (add to `LEAD_PRODUCT_GATE_PROTOCOL.md` §gate-6-release-dod)

```
| R9 | Security surface: S-0 Security Contract existed for every surface epic in this release; S-Wave PENTEST_REPORT closed (no 🔴); regression tests present | SECURITY_GATE_PROTOCOL §4/§6 |
| R10| For a release tag / first pilot: S-Global PENTEST_REPORT_GLOBAL_[DATE] run, no open 🔴 | SECURITY_GATE_PROTOCOL §2 (S-Global) |
```

---

## §10 — CADENCE SUMMARY (S-Global)

| Trigger | S-Global? |
|---------|-----------|
| Before first client pilot | **Yes** — mandatory |
| Before every release tag | **Yes** |
| Backstop: N surface-touching waves with no release sweep between (N = 3 surface-heavy / 5 otherwise) | **Yes** — a catch-net, not the main trigger |
| A new public route / tenant boundary / external integration / upload path / privileged action | **Yes** — surface widened |
| A pure UI copy/style wave | No |

---

Reference: `.cursorrules` Law 38 · `roles/ROLE_PENTEST.md` (§ NAMED LEAVES) · `roles/PENTEST_SCENARIOS.md` §§10–15 · `roles/ROLE_SEC.md` · `roles/LEAD_PRODUCT_GATE_PROTOCOL.md` · `roles/ROLE_ARCH.md` · `roles/ROLE_PRINCIPLE.md` · `roles/ROLE_DESIGN.md` · `roles/ROLE_QA.md` · `roles/ROLE_QA_ARCH.md` · `roles/TESTING_CANON.md` · `roles/DATA_INTEGRITY_CANON.md` §10 · `roles/ASYNC_WORKERS_CANON.md` §7/§14 · `roles/SYSTEM_EVOLUTION_PROTOCOL.md` (REFLEX/@EVOLVE) · OWASP WSTG
Version: 1.1 | 2026-07-23
