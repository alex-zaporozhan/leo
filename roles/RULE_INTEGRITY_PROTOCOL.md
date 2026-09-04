# RULE_INTEGRITY_PROTOCOL.md
# The seven tests a rule must pass before it enters this system — and the ladder that decides between two rules that are both true.
# Owner: @LEAD · run by: anyone adding, rewriting or auditing a rule (`@EVOLVE`, the second pass, any role writing a canon).
# Position: before a rule is written, and before an audit finding about a rule is accepted as a defect.

> **Why this exists.** Most damage to a rule system is not a wrong rule. It is a right rule that **lost its goal to a well-meant edit**, a **right rule placed on the wrong axis**, a right meaning **kept in two homes**, or a right metric **measuring a shadow of its claim**. Each of these passes an ordinary review, because each is locally correct. This protocol is the seven questions that catch them, and the ladder for the case where two rules are both true and pull apart.

---

## §1. THE SEVEN TESTS — a rule enters only by passing all seven

**T0 · GOAL — what is this law FOR? One sentence.**
The goal is the **core of meaning**. Everything else in the law — motivation, algorithm, owners, the worked case — exists to accumulate toward that one goal. **A law that smears its goal has lost it:** two goals in one law means neither is enforced, because the reader satisfies the cheaper one and reports the law as met.
*The test:* strike any clause and ask whether the goal still lands. A clause serving a **different** goal belongs to a different law, or it is decoration.
*Failure: smeared centre.* The most common way a good law is ruined by a well-meant edit — an audit adding "completeness" does this routinely.
*Case: Law 5's first rewrite fused two goals — "make the thought constructive" and "leave no dead end in the output" — and enforced neither. Its goal is the first; everything in it must serve that or leave.*

**T1 · AXIS — what is this rule about?**
Exactly one of: **register** (how a thing is said) · **action** (what is done) · **form** (the shape of an artifact) · **order** (what precedes what) · **fact** (what is true of the world).
**A rule is true only on its own axis.** Naming the axis first is not pedantry — it is what makes the other five tests answerable.
*Failure: category error.* The deepest of the seven, and the only one that survives every other check.
*Case: Law 5 is a register rule. Twice it was rewritten as a procedure with owners and a gate, and twice the filter was lost — a register rule cannot have a gate, and demanding one destroys it. Silence was then attached to Law 5 and belongs to Law 13: withholding a known improvement is an **action** failure, not a **register** failure.*

**T2 · HOME — is this a DECISION or a MEANING?** They obey opposite rules, and confusing them is where "single source of truth" advice goes wrong in a system like this one.
A **decision** — a resolved value, a threshold, an address, a winner, a name — has **exactly one home**; every other place points at it. Copies of a decision drift, and a drifted decision gives two confident answers instead of one honest gap.
A **meaning** — a principle, a priority, a way of thinking — **belongs in many homes on purpose.** Repetition across files is not redundancy, it is reinforcement: a reader who meets the same principle from three directions cannot slip past it, and laws that reference each other hold each other up. **Isolation is the failure mode of other systems, not the goal of this one** — this library is deliberately interwoven, and its stability comes from that.
*The test:* if this text changes, must the other copies change too? **Yes → decision → one home.** **No → meaning → repeat it**, in the local voice, wherever the reader needs it.
**A repeated meaning carries its link, or it will be consolidated away.** When a law reinforces the goal of another, or sharpens it by contrast, **the link is named in both places** — two kinds, and both are load-bearing:
> **REINFORCES:** — another law serves this goal from a different angle. Naming it turns a coincidence into a mesh: the reader who arrives from either side lands on the same goal.
> **CONTRASTS:** — another law sharpens this goal by holding up its anti-pattern. Contrast focuses a goal more sharply than repetition does, and a contrast nobody linked reads as a contradiction to the next auditor.
**This is what protects deliberate repetition from a well-meant consolidation.** An unlinked repeat looks like duplication, and duplication invites a sweep; a linked one announces that it is structural. *(The existing form of this is the `MIRROR OF:` marker indexed in `roles/CONFLICT_REGISTRY.md` — a link with a stated source, not a loose copy.)*

*Failure, in three directions: a decision kept in two homes (drift) · a meaning starved into one (a principle the reader meets once, and therefore skips) · a meaning repeated with no link (structural repetition that the next audit will delete as noise).*
*Case: `foundation` was a decision listed twice in different words (Laws 41 and 43) — collapsed to one home. The `instrument` / `statement` register is a meaning, and it is restated in six files on purpose.*

**T3 · NAME — is this word free in this vocabulary?**
A loaded word carries its old meaning in. Check before naming, not after.
*Failure: namespace collision.* *Case: "constructive routing" — `routing` already means task routing here. `C1` means six different things across canons; `T1` five.*

**T4 · REACH — can the reader act, and does what the rule names exist?**
A rule that names an artifact obliges someone to create it. A rule with no next step is half a rule.
*Failure: ghost.* *Case: `§Surfaces` was required by two roles before it existed in the template; `PRODUCT_INVARIANTS` had three readers and no author; `CAPABILITY_MAP` was a hard blocker on modules whose backend could not exist yet.*

**T5 · SIDES — if this binds two parties, do both know?**
An obligation written only in the file of the party that **demands** it is a wish.
*Failure: one-sided contract.* *Case: 17 of 20 role handoffs, before the `RECEIVES` / `RETURNS` pass.*

**T6 · MEASURE — does the metric measure the claim, or a shadow of it?**
A count that is mechanically correct and conceptually empty is worse than no count: it produces confident false findings.
*Failure: shadow metric.* *Case: "33 of 43 laws violate Law 5" counted negation words — in laws whose nature is prohibition, against a rule that is a priority and not an axiom. And a rewrite once defended by character count: a shorter version of the wrong thing is still the wrong thing.*

---

## §2. PRIORITY BETWEEN TWO TRUTHS

Both rules are true and they pull apart. Walk in order; stop at the first step that resolves it.

**P0 · Compare axes before choosing.** Different axes = **no conflict**, and the resolution is to name each axis, not to pick a winner.
*Most apparent conflicts are axis confusion.* Law 5 (register) and Law 23 (form) never competed; Law 13 (action) and Law 43 (budget) only appeared to.

**P1 · Narrower scope wins inside its scope — and only there.**
Valid **only where the broader rule names the exception**. If it does not, the broader rule is obeyed or amended (`@EVOLVE`) — never reinterpreted in the moment.
*Case: `statement` register narrows the geometry law for non-interactive overlap; it does not touch what never inverts.*

**P2 · Irreversible outranks reversible.** The truth whose violation cannot be undone wins over the truth whose violation can be repaired. Safety, publication, integrity, licence — over speed, scope, taste, effort.

**P3 · Verifiable outranks judged.** Not because it is more true, but because **a system cannot arbitrate the unverifiable one** — and an unarbitrable rule becomes whatever the tired agent says it is.

**P4 · Stop outranks proceed.** When one says *do* and the other says *stop*, stop wins, in the single objection form, and work continues only on what the stop does not touch, at the declared scope.

**P5 · Record once.** The tie is written into `roles/CONFLICT_REGISTRY.md` with one named winner. **A conflict re-argued per task is not resolved** — it is a recurring tax.

---

## §3. WHEN THIS RUNS

```
Writing or rewriting a rule        → all seven tests, then §2 against its nearest neighbours.
Auditing a rule                    → T1 FIRST. An audit finding that fails T1 or T6 is not a defect
                                     in the rule — it is a defect in the finding, and it is reported as one.
Two roles reading a rule differently → §2 P0. If the axes differ, the rule needs a boundary line, not a fix.
An @EVOLVE proposal                → all seven + §2, and the result is a router entry (`RAG_CANON` §6).
```

**Scope — read this before applying any of the above.** This protocol governs a rule that is **being written or changed**, and a **finding** about a rule. It is **not a conformance schema for the laws that already exist**, and it is never run as a sweep across all of them. **A law that works in practice and does not match this shape is evidence about the shape, not about the law** — the working house outranks the drawing of the house. Where the two disagree, the protocol is amended, not the law.

**The standing warning.** A mechanically correct audit of a rule system will confidently propose changes that destroy it. Two happened here in one day: *"Law 5 has no owner"* (it cannot have one — T1) and *"Law 23 cites the wrong law for silence"* (it cites exactly the right one — silence is an action failure, and Law 13 owns action). **Before accepting a finding about a rule, run T1 and T6 on the finding itself.**

---

Reference: `.cursorrules` (LAW PRECEDENCE · ABSOLUTE LAWS) · `roles/CONFLICT_REGISTRY.md` (where a resolved tie is recorded) · `roles/SYSTEM_EVOLUTION_PROTOCOL.md` (`@EVOLVE`) · `roles/RAG_CANON.md` §6 (a rule that is not routed does not exist) · `roles/SECOND_PASS_PROTOCOL.md` (where findings about rules arrive)
Version: 1.0 | 2026-09-04
