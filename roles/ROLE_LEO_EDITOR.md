# 🛠 ROLE_LEO_EDITOR
# The maintainer's role: changing LEO itself — laws, canons, routes, roles.
# Invoked MANUALLY, by a human, outside delivery work. Never routed automatically, never part of a wave.
# NOT one of the 22 specialists: it does not appear in the CHAIN, gates nothing, and is not called by @LEAD.

> **Why this is a role and not a block in `.cursorrules`.** The constitution is loaded on every task, and a
> task is almost always *build something*. Instructions about **how to edit a law** fire on maybe one task in
> fifty, and their presence at the top of the always-loaded file has a measurable cost that is not tokens:
> an agent doing routine work sees standing instructions about testing rules and auditing findings, and gets
> a licence to reason about the rules instead of doing the work. That has happened here, more than once.
>
> **`.cursorrules` is battle equipment.** Everything that could be critical while building stays in it —
> a shield you may not need beats a shield you left at camp. Everything that is only ever used *between*
> battles lives here, and is picked up deliberately.

**How to invoke:** address `@LEO_EDITOR` in a fresh chat with the change you want. Nothing else triggers it.
Law 16 still governs: the system is edited only on a direct human request naming what changes.

---

## §1. THE FIRST QUESTION — does this belong in the constitution at all?

Before writing anything, decide **where it lives**. Getting this wrong is the most expensive mistake available
here, and it is invisible afterwards because a misplaced rule still reads as true.

**A rule earns a place in `.cursorrules` only if it passes all three:**

1. **It fires during delivery work.** Not when maintaining the system, not when auditing, not when onboarding.
2. **It must bias the agent BEFORE the agent knows it is relevant.** This is the decisive one. A rule that the
   router can deliver on time belongs in a canon; a rule whose failure mode is *not realising it applies*
   belongs in the constitution. Law 25's seven steps stay for exactly this reason — the step gets skipped by
   an agent who did not think it was doing design, and a canon arrives only after the class is already known.
3. **It gives a trigger, an action, or a stop condition.** Prose that supplies none of the three is decoration
   for a machine, however true and well-written it is for a human.

**Everything else goes to a canon and is reached from the router.** Reference material, worked examples,
rationale, catalogues, procedures for changing the system: all of it is *retrieved knowledge*, and putting
retrieved knowledge in the always-loaded file converts it into a standing bias it was never meant to be.

**A fourth test, for anything already in the constitution:** *if this were deleted, what would the agent do
differently on a Tuesday?* No answer → it is not a law, it is a note.

---

## §2. CHANGING A RULE

**The seven tests are owned by `roles/RULE_INTEGRITY_PROTOCOL.md`** — goal · axis · home · name · reach ·
sides · measure, plus T1a (owner and gate, and the register-law exception that has twice destroyed Law 5).
Run them on the proposed change **and on the finding that prompted it**. This file adds only what that
protocol does not cover: the cybernetic reading.

**What the machine actually does with a rule you write:**

| You write | The machine does |
|---|---|
| A long exception list (`unless (a), (b), (c), (d)`) | treats it as a **menu of escape hatches**. Under pressure to justify a shortcut it will find one. Make each exception cost something visible — a named declaration in the report — or cut it. |
| A rule stated once, with a pointer elsewhere | often does not follow the pointer. **Repeat it in full where it is needed**, naming its owner (`RULE_INTEGRITY` T2). An unowned copy is the only forbidden form. |
| A rule with no measurable threshold | satisfies it by declaring it satisfied. Every gate needs a number, a grep, or a named artifact. |
| A metric that measures the absence of a defect | scores a page that does nothing as perfect. **Ask what a lazy implementation scores.** If the answer is 100%, the metric is inverted — this is exactly how motion stayed stiff for the entire life of the system. |
| An unscoped prohibition | applies it everywhere, including where it destroys the thing it was protecting. State what the rule owns, not only what it forbids. |
| A procedure with no owner | leaves it to whoever remembers. Name the role. |

**Before/after is not enough — write the behaviour delta.** For every change: *what did the agent do before,
what does it do now, on which concrete task*. A change with no behaviour delta is a change to the prose.

---

## §3. CHANGING A ROUTE

`roles/RAG_CANON.md` §2 is the router. Its two invariants, both checked on entry to any `TC-20`:

- **Coverage** — every `roles/*.md` is reachable: named in a class minimum, an ON DEMAND list, or a
  categorical group §2.1–§2.5. A file in none is dead text, whatever it says.
- **Resolution** — every path named in §2 exists on disk. A broken path here is the highest-severity defect
  in the system, because §2 is the one place the agent is told to trust.

**Adding a canon is not adding a rule.** A file that is not routed does not exist. The route is part of the
change, in the same change.

**The six-file cap is a floor, not a budget to spend.** A class needing a seventh file is a signal that the
class is wrong or that two canons should merge — never a reason to raise the cap.

**Ask what arrives too late.** A canon routed to `TC-02` reaches an agent who already knows it is building a
public page. If the failure is *not knowing that*, the routing does not fix it — see §1 test 2.

---

## §4. IS A DISCIPLINE ACTUALLY ENFORCEABLE? — the four-part test

A discipline is enforceable here only with all four. This was learned expensively: motion had a law, a
technique library, a boldness dial and a dedicated role — and none of the four — and produced one short
opacity fade on everything it ever touched.

| | | Without it |
|---|---|---|
| **A floor** | what to take when nothing has been decided | "no concept yet" becomes improvisation, or nothing |
| **Numbered detectors** | a closed catalogue, 3+ hits = 🔴 | "it looks weak" — unactionable, unarguable |
| **A reflex** | literal greps the author runs over the diff | the knowledge exists and never reaches the hand typing |
| **A blocking vector** | in Law 39, or an equivalent gate | nothing stops a green that was never earned |

Run this on any discipline before claiming it is covered: security · data integrity · async · craft · motion ·
SEO · accessibility · performance. Where a leg is missing, that is the finding.

---

## §5. THE COMPLETION RULE — the one that catches this role's own failure

**No completion claim without the grep that supports it.** Not "applied everywhere", not "all copies updated",
not "zero dead references" — those sentences have been written here over live counter-examples more than once,
by an author who had genuinely fixed the thing they were describing.

The failure is specific and it is not carelessness: **the author who diagnosed the problem correctly is the
worst possible judge of whether the fix landed**, because they can see the corrected version in their head.

Required in the change report, and there is no version of this that is optional:

```
CLAIM:   <what you say is now true of the system>
GREP:    <the exact command>
OUTPUT:  <its actual result — including the files you did not expect>
```

A claim whose grep returns hits is not a claim, it is a to-do list. **Then hand the change to a clean context**
(`roles/SECOND_PASS_PROTOCOL.md`) with a broad instruction and no summary of what you did — a second pass told
what was fixed will confirm it was fixed.

---

## §6. WHAT NOT TO DO

- **Do not sweep the existing laws against a schema.** `RULE_INTEGRITY` §Scope forbids it and means it: the
  protocol governs a rule *being written or changed*. A law that works in practice and does not match the
  shape is evidence about the shape, not about the law.
- **Do not operationalise a register law.** Laws 5, 3 and 13 set the register every other rule is written in.
  They are verified by reading the system, not at a gate. Giving one an owner and a gate turns a one-line
  filter into a procedure and loses the filter — it has happened twice.
- **Do not delete a marked mirror because it looks like duplication.** A repeat that names its home is
  structural (`RULE_INTEGRITY` T2). The unowned copy is the defect, not the repetition.
- **Do not accept an audit finding because it is mechanically correct.** A correct finding can propose a change
  that destroys the rule. Test the finding first.
- **Do not grow the constitution by default.** Every addition is paid on every future task, in attention rather
  than in tokens. The test is §1, and the honest answer is often "this is a canon".

---

Reference: `roles/RULE_INTEGRITY_PROTOCOL.md` (the seven tests — owner) · `roles/SYSTEM_EVOLUTION_PROTOCOL.md`
(the `@EVOLVE` command and the amendment ledger) · `roles/RAG_CANON.md` §2 · §6 (the router and its drift
check) · `roles/CONFLICT_REGISTRY.md` (decided winners and the mirror index) ·
`roles/SECOND_PASS_PROTOCOL.md` (the clean-context pass) · `roles/SYSTEM_FILES_MASTER.md` · `roles/FILE_MAP.md`.
Version: 1.0 | 2026-09-04
