# SECURITY UPGRADE — INTEGRATION MANIFEST
# What changed, where, and how to install. Second pass (independent review) applied.

> **Delivery model.** The twelve files in this folder are **ready-to-use drop-ins** — the authoritative
> artifact. This manifest is the human-readable changelog: which files changed, what each gained, the
> second-pass review findings, and the few steps that are *not* inside any file and must be done by hand.
> No anchor-level patching is required; replace the files and do the three residual steps at the end.

---

## 1 — WHAT THIS UPGRADE DOES (one paragraph)

Security moves from a late, optional, non-blocking pass to a **mandatory gate with three checkpoints**.
A change on the **SECURITY SURFACE** (S1–S12, decided by a mechanical grep of the diff — not by feel) must pass:
**S-0** (@PENTEST threat-model at planning → a `## Security Contract` written into DEV_PROMPTS before @DEV),
**S-Wave** (@PENTEST adversarial gate inside GATE-4 — any 🔴 blocks deploy; every finding → an owner + a
red-green regression test), and **S-Global** (@PENTEST periodic full-product red-team before pilot/release and
on surface-widening). @PENTEST holds a **blocking verdict** peer to @QA_VISUAL; @LEAD cannot raise the gate on
words. In parallel, @QA stops shipping happy-path-only: it gains **risk tiers T0–T3**, **test-design techniques**,
a **mandatory negative baseline from the first wave**, and a **planning role**. Philosophy: not to hide holes but
to raise the reliability floor and account for residual risk honestly.

---

## 2 — SECOND-PASS REVIEW (independent re-review — findings folded into the shipped files)

The first draft was re-reviewed adversarially. Eleven substantive issues were found and fixed:

- **R1 — Gameable `none`.** The `[SECURITY SURFACE: none]` escape hatch was a judgement call. Now it is a
  **mechanical grep** of the diff (a concrete signal list); @QA_ARCH validates it by that same grep. (SECURITY_GATE §1)
- **R2 — No survival valve → the gate gets deleted.** A zero-give blocker is honoured or quietly removed. Added a
  **narrow, human-only, logged risk-acceptance** (`SECURITY_RISK_ACCEPTANCE`, §4A): a 🔴 is *never* acceptable; a
  🟠/🟡 only by the human owner, with owner + expiry, re-surfaced at S-Global. Merciless **and** durable.
- **R3 — Fake-green regressions.** "Add a regression test" didn't prove the test catches the bug. Now every
  regression must be **red-green** (observed RED on the vuln, GREEN after the fix). (SECURITY_GATE §6; GATE-4 blocker)
- **R4 — Contract location ambiguous.** It lived in two places. Resolved to **one home**: a `## Security Contract`
  section **inside** `DEV_PROMPTS_[NAME].md`. No separate file. (SECURITY_GATE §2/§3/§6; ROLE_PENTEST MODE 0/GLOBAL_AUDIT)
- **R5 — "Every N waves" wasn't actionable.** Made the **milestone triggers primary** (pilot / release tag /
  surface-widening — all unambiguous); the wave-count N is now only a **backstop**. (SECURITY_GATE §2/§10)
- **R6 — Two grading scales confusable.** QA tier (T0–T3, test intensity of a *path*, at planning) vs @PENTEST
  severity (🔴🟠🟡🟢, criticality of a *finding*, at the gate) are **orthogonal** — stated in both canons. (SECURITY_GATE §5; TESTING_CANON §7; ROLE_QA)
- **R7 — @SEC vs @PENTEST double-gate.** Clarified: at S-Wave @SEC is **advisory** and feeds @PENTEST's report;
  **@PENTEST alone holds the block**. One gate, not two. (SECURITY_GATE §8; ROLE_PENTEST)
- **R8 — Law 33 too long.** Tightened ~25%; the full S-list detail lives in the canon, not the constitution.
- **R9 — Self-check felt redundant with "four questions".** Reframed: the four questions are **design-time**; the
  self-pentest is the **handoff-time verification** that they held. (ROLE_DEV; ROLE_PENTEST)
- **R10 — S-0 could stall with no spine.** A small surface epic without an architectural trigger has no spine; S-0
  now **derives abuse cases from the S1–S12 list + DOMAIN_MODEL** and is never skipped. (SECURITY_GATE §2; ROLE_PENTEST MODE 0)
- **R11 — False T-series blocker.** GATE-4's T-series line is now explicitly **N/A** when the epic has no async /
  shared resource / pipeline. (SECURITY_GATE §4)

---

## 3 — FILES DELIVERED (drop-in) AND WHAT EACH GAINED

**New canon**
- `roles/SECURITY_GATE_PROTOCOL.md` — the whole contract: SECURITY SURFACE (S1–S12) + mechanical none-grep · the
  three checkpoints · the Security Contract template · GATE-4 blocker set · §4A narrow risk-acceptance · severity→action
  with tier-orthogonality · evidence artifacts + red-green + append-only · the "no hiding" principle · role table · release DoD R9/R10 · S-Global cadence.

**Rewritten (full replacements)**
- `roles/ROLE_PENTEST.md` → **v2.0** — merciless mindset (six axioms), blocking authority at three points, `THREAT_MODEL`
  (planning) and `GLOBAL_AUDIT` (red-team) modes, the vector tree A–G + T-series kept, the scourge contract with @ARCH/@DEV,
  the @DEV self-pentest, acceptance rules (🔴 never), @SEC-advisory clarity.
- `roles/ROLE_QA.md` → **v2.0** — three modes PLAN/BUILD/GATE, tiered suite table, the 21 Pillars kept as the final pass
  (P19 = the isolation/XSS floor), the @PENTEST relationship + tier↔severity orthogonality.
- `roles/TESTING_CANON.md` → **v2.0** — §2 pyramid & test types · §2A risk tiers T0–T3 · §2B test-design techniques ·
  §3.7 negative/adversarial baseline (GATE-4 QA blocker) · §5 A→Z lifecycle · §6 QA-at-planning · §7 @PENTEST relationship.
  **Anchors §3.1–§3.6 and §4 preserved verbatim** (other files depend on them).

**Patched (full drop-ins — replace in place)**
- `_cursorrules` → **v6.26** — ABSOLUTE **Law 33**; ROLE MAP rows for @PENTEST (blocking, 3 points) and @QA (tiered, planning);
  CHAIN PROTOCOL S-0 node + @PENTEST S-Wave/S-Global in the deploy node; QA_ARCH GATE security-surface line; Layer P registration; footer + version.
- `roles/ROLE_LEAD.md` → **v2.2** — chain **node 1.9** (S-0 before DEV_PROMPTS) + node 2 Security-Contract line + node 5 S-Wave/S-Global;
  **WORKING PRINCIPLE 15**; capstone chain; footer (Laws 23–33).
- `roles/LEAD_PRODUCT_GATE_PROTOCOL.md` — AUTO-TRIGGER; GATE-1 Security-Contract blocker; **GATE-4 @PENTEST blocker set** (red-green + §4A);
  GATE-4 checkbox symptoms; **release DoD R9/R10**; GATE-6 S-Global L0 cap.
- `roles/ROLE_ARCH.md` — PREVENTIVE CHECK security axis; TEST DESIGN security handoff (feeds S-0); footer (Laws 29–33).
- `roles/ROLE_DEV.md` → **v4.2** — SECURITY SELF-CHECK (handoff-time); task-protocol step 7; footer (Laws 26–33).
- `roles/ROLE_PRINCIPLE.md` → **v3.3** — authority → abuse-cases (feeds S-0); footer.
- `roles/ROLE_DESIGN.md` → **v2.1** — security-aware UX; footer (Laws 28, 33).
- `roles/ROLE_QA_ARCH.md` — LAUNCH PROTOCOL **preflight 3.3** (Security-Surface, mechanical grep, no final 🟢 without the gate); footer (Laws 26–33).

---

## 4 — INSTALL (order)

```
1. Place the new file:      roles/SECURITY_GATE_PROTOCOL.md
2. Replace the rewrites:    roles/ROLE_PENTEST.md · roles/ROLE_QA.md · roles/TESTING_CANON.md
3. Replace the patched:     _cursorrules · roles/ROLE_LEAD.md · roles/LEAD_PRODUCT_GATE_PROTOCOL.md
                            roles/ROLE_ARCH.md · roles/ROLE_DEV.md · roles/ROLE_PRINCIPLE.md
                            roles/ROLE_DESIGN.md · roles/ROLE_QA_ARCH.md
   (note: the file shipped as `_cursorrules`; put it where your system reads `.cursorrules`)
```

## 5 — RESIDUAL MANUAL STEPS (not inside any file — do these by hand)

```
□ Register roles/SECURITY_GATE_PROTOCOL.md in roles/SYSTEM_FILES_MASTER.md (Layer P inventory), next to ROLE_PENTEST / PENTEST_SCENARIOS
□ (Optional, low priority) roles/ROLE_SEC.md: add one line — "at S-Wave @SEC (18 pillars) is advisory and feeds @PENTEST; the gate is roles/SECURITY_GATE_PROTOCOL.md"
□ Verify the reference graph: grep -rl "SECURITY_GATE_PROTOCOL" . — every referrer resolves; run the link check per roles/SYSTEM_UPGRADE_MANIFEST.md
```

## 6 — A LEVER YOU CONTROL

Law 33 **and** a new canon slightly tension with `SYSTEM_EVOLUTION_PROTOCOL`'s minimalism ("a new absolute law is
rarest"). The justification: security-mandatory is a cross-cutting product property (that is what a Law is for), while
the operational detail is too large for a law line (that is what the canon is for). If you want it stricter, collapse
Law 33 into a reference inside Law 27/31/32 and keep the canon — the wiring in the other files still holds, since they
all point at `roles/SECURITY_GATE_PROTOCOL.md`, not at the law number.
