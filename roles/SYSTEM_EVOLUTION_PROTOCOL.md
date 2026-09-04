# SYSTEM_EVOLUTION_PROTOCOL.md
# How the role system grows without rotting. Manual trigger only — the system NEVER edits itself.
# Owner: @LEAD (runs it), the developer (triggers it, approves every change).
# Position: this is the meta-protocol. It governs how every other file in roles/ may be changed.

> **The dogma:** a system that only grows becomes a swamp. A system that grows **deliberately** becomes an asset.
> The difference is not discipline in adding — it is discipline in **deciding whether to add at all**, and in
> where the thing lands.
>
> **The iron rule:** the system NEVER edits itself. No incident, no gate failure, no clever idea rewrites a role
> without an explicit human command. An agent that quietly improves its own instructions will, one day, quietly
> improve away something that was load-bearing.

---

## §0. THE TRIGGER — one command, always manual

```
@EVOLVE: [what happened, in one line]
```

Examples:
```
@EVOLVE: worker hung with an open transaction, Cancel queued behind the lock, whole system stuck
@EVOLVE: fix didn't help on the stand — turned out CI was rebuilding a local image instead of pulling
@EVOLVE: DEV keeps writing 10 files at once and the contracts between them don't line up
@EVOLVE: the admin panel is technically correct but miserable to use
```

Optional narrowing: `@EVOLVE --dry` (analysis only, no file changes proposed) · `@EVOLVE --retire [rule]` (see §7).

**On this command and only on this command**, @LEAD runs the seven phases below. Nothing else in the system may
modify `roles/` or `.cursorrules`. Not @AUDITOR after a root cause. Not @QA_ARCH after a repeated 🔴. Not @LEAD's
own ⚡ REFLEX — REFLEX *proposes*; `@EVOLVE` *executes*, and only with the developer's hand on it.

---

## §1. PHASE 1 — EVIDENCE (facts, never hearsay)

No rule is written from a story. Gather what actually happened:

```
□ The artefacts: logs, screenshots, the failing test, the error, the production symptom
□ The code: the actual files involved — read them, do not recall them
□ The history: git log for the fix commits — what was tried, what finally worked
□ The config: CI, env, compose — the incident is often here, not in the code (see the artifact-identity lesson)
□ The cost: how long did this take to find? how much did it cost? (this number decides §5)
```

**If the evidence is thin, stop.** A rule invented from a hunch is a tax on every future request, forever, with
no proof it ever catches anything. Say so plainly: *"Not enough evidence to legislate. Here is what to collect."*

---

## §2. PHASE 2 — ROOT CAUSE AND CLASS

Answer three questions, in writing, before touching any file:

```
1. MECHANISM — what ACTUALLY happened, step by step, causally.
   Not "the worker hung" — but "the worker hung inside an open transaction → Postgres held the row locks
   indefinitely → Cancel's UPDATE queued behind them → the recovery path also queued → the system stood still."

2. WHY THE EXISTING LAWS DID NOT CATCH IT — name them and say why each was blind.
   This is the most important line in the whole protocol. Usually one of four:
     (a) NO RULE EXISTS for this class          → a genuine gap (rare and valuable)
     (b) A RULE EXISTS BUT WAS NOT FOLLOWED     → NOT a system gap. Do not add a law. Strengthen the CHECK (§3-B).
     (c) A RULE EXISTS BUT IS TOO WEAK/VAGUE    → amend it in place. Do not add a second one.
     (d) EVERY LAW ASSUMED SOMETHING THAT BROKE → the deepest kind. (E.g. every law silently assumed
         "the artifact under test is the artifact that was fixed.") These are worth a new law on their own.

3. CLASS — is this a one-off, or a CLASS of failure that will recur in different clothes?
   A rule earns its place only if it prevents the CLASS, not the instance.
```

**Present this analysis and STOP. Wait for the developer's confirmation before Phase 3.** The analysis is cheap;
editing eight files is not. A wrong diagnosis, applied, is worse than no change.

---

## §3. PHASE 3 — PLACEMENT (the decision tree that prevents bloat)

Walk it top to bottom. Stop at the first match. **Most `@EVOLVE` runs end at A or B — and that is a success,
not a disappointment.**

```
A. A RULE ALREADY COVERS THIS, IT WAS SIMPLY NOT FOLLOWED
   → Do NOT add anything. Add or sharpen the CHECK that would have caught the violation:
     a grep in @QA_ARCH · a line in @DEV's self-check · a test in the acceptance series.
   → The system does not need more knowledge. It needs the knowledge it has to be enforced.

B. A RULE IS ALMOST RIGHT — vague, or missing a number
   → AMEND IT IN PLACE. Replace "handle errors carefully" with a number, a grep, a test.
   → Never add a second rule that says the same thing more loudly. That is how canons become sermons.

C. A NEW RULE FITS AN EXISTING CANON'S SCOPE
   → Add it to that canon, IN THE BODY, in the section where it belongs.
   → NEVER as an "ADDENDUM" at the end of the file. An addendum is a rule nobody will read at the moment
     they need it. Put the rule where the role is standing when the mistake happens.

D. A GENUINELY NEW CLASS, owned by nobody
   → A new canon. This is RARE and must be justified against §5.
   → Requirements: a one-line dogma · explicit boundaries with its neighbours ("this file answers X; it does NOT
     answer Y — that is <other canon>") · an owner role · a mirror check · tests.
   → If you cannot state its boundary in one sentence, it belongs inside an existing canon (go back to C).

E. A NEW ABSOLUTE LAW in .cursorrules
   → The rarest. Reserved for the class (d) of §2: an assumption every other law silently depended on.
   → A law is a line every role reads on every task. It must be worth that.
```

---

## §4. PHASE 4 — INTEGRITY RULES (how to add without breaking)

Every change, regardless of where it lands, satisfies all seven:

```
1. VERIFIABLE. A number, a grep, or a test. "Be careful with X" is not a rule — it is a wish.
   If you cannot state how a violation is detected, you have not finished thinking.

2. OWNED. Exactly one role owns the rule (produces/decides), and exactly one role MIRRORS it (catches violations).
   A rule with no mirror is a rule that will be quietly skipped forever.
   Typical pairs: @ARCH decides / @QA_ARCH greps · @DEV self-checks / @QA_ARCH mirrors · @DESIGN specs / @QA_VISUAL measures.

3. PLACED WHERE THE HAND IS. The rule goes where the role is standing when the mistake happens — in the task type,
   the vector, the step — not in a distant appendix. (This is why ASYNC_AWAIT_REFLEX works and a canon alone did not.)

4. BOUNDED. State what this rule does NOT cover, and which file does. Every duplication is a future contradiction.

5. CROSS-LINKED BOTH WAYS. The new rule references its neighbours; the neighbours reference it. A one-way link is
   an orphan waiting to happen.

6. NO ADDENDUMS. Version stamps ("## v6.27 ADDENDUM") are forbidden. Merge into the body. The file must read as
   one document written by one mind, not as an archaeological dig.

7. TESTABLE AS A REGRESSION. The incident that produced the rule becomes a test in the acceptance series
   (T-A/T-H/T-I/T-D/...). The rule prevents; the test proves it prevents.
```

---

## §5. PHASE 5 — THE COST QUESTION (the anti-bloat gate)

Every rule is read by a model on every relevant request, forever. It is not free. Answer honestly:

```
□ WHAT DOES IT COST? Roughly how many tokens, on how many task classes, per request.
□ WHAT DOES IT SAVE? What did this incident cost — in hours, in money, in trust? Will the class recur?
□ THE VERDICT:
      A rule that prevents a class of expensive failure → it has earned its place, whatever it weighs.
      A rule that prevents one cheap annoyance, and is read on every request → it is a tax. Reject it.
      A rule that only makes a role "more thorough" without a check → reject; it will be ignored anyway.

□ THE HONEST TEST: "If I remove this rule in six months, will anything break — and will I be able to tell?"
      If the answer is no, do not add it.
```

**Rejecting an `@EVOLVE` is a valid outcome and a sign of health.** The right report is often:
*"This was an execution failure, not a knowledge gap. No new rule. Adding this grep to @QA_ARCH would have caught it."*

---

## §6. PHASE 6 — DRAFT, APPROVE, APPLY

```
1. DRAFT THE PLAN — not the files. One table:

   | # | File | Section | Change | Why here | Verified by |
   |---|------|---------|--------|----------|-------------|
   | 1 | DATABASE_RUNTIME_CANON.md | new canon | the DB has a clock | no owner for DB liveness | T-D1…T-D7 |
   | 2 | ROLE_QA_ARCH.md | Vector 17 | 12 greps AP-DB-* | the mirror check | grep |
   | 3 | ARCH_SPINE_PROTOCOL.md | vertebra 4 | guard-rail numbers | it is a timeout, and timeouts live here | passport reconciliation |

2. SHOW THE PLAN. WAIT. The developer approves, adjusts, or rejects. No file is touched before this.

3. APPLY — complete files, never diffs, never "insert this here". The developer copies whole files.

4. VERIFY MECHANICALLY, and report the numbers:
   □ 0 occurrences of "ADDENDUM" in the changed files
   □ every roles/*.md reference resolves to a file that exists
   □ codes and terms match the glossary (AW/AP/V/T-*/vertebra/Law)
   □ nothing was lost: the section counts of the touched files did not shrink unexpectedly
   □ the new rule is greppable — show the grep, show it firing on the original defect
```

---

## §7. RETIREMENT — the other direction (`@EVOLVE --retire`)

The system grows in value, not in weight. A rule that no longer earns its place is removed — deliberately,
with the same care used to add it.

```
CANDIDATES FOR REVIEW:
  · a rule that has not fired in the memory of the project (nobody can recall it catching anything)
  · a rule superseded by a stronger one (both are still there — the weak one is now noise)
  · a rule about a technology or a repo path the project no longer uses
  · a canon nobody has opened during any wave

THE RETIREMENT CHECK (all four, or it stays):
  □ Nothing references it (grep the whole roles/ tree — both ways)
  □ No test depends on it
  □ Its removal is stated in one sentence: "removed because [reason]" — recorded in §8
  □ The developer approves. Nothing is ever removed silently.

WHAT IS NEVER RETIRED WITHOUT DEEP CAUSE:
  a rule born from a real incident that cost real money. It is cheap to keep and expensive to relearn.
```

---

## §8. THE LEDGER — `docs/artifacts/SYSTEM_EVOLUTION_LOG.md`

Every applied `@EVOLVE` gets one line. This is the system's memory of **why** it is the way it is — and it is what
makes retirement (§7) possible years later, when nobody remembers.

```markdown
| Date | Trigger (what happened) | Class | Change | Where it lives | Verified by |
|------|------------------------|-------|--------|----------------|-------------|
| 2026-07-12 | worker hung in an open transaction; Cancel queued behind the lock; system stuck | (a) no rule existed | DATABASE_RUNTIME_CANON — the DB has a clock | new canon + QA_ARCH V17 + ARCH_SPINE vertebra 4 | T-D1…T-D7, 12 greps |
| 2026-07-12 | fix "didn't help" — CI rebuilt a local image instead of pulling | (d) every law assumed the artifact was current | Artifact identity: prove the running code is the fixed code | QA_ARCH Vector 18 | grep the CI config |
```

---

## §9. WHAT THIS PROTOCOL REFUSES TO DO

```
✗ It does not run automatically. No incident, no gate failure, no repeated 🔴 triggers it. Only the developer does.
✗ It does not add a rule for every mistake. Most mistakes are execution failures, and the fix is a check, not a law.
✗ It does not write addendums. Ever.
✗ It does not touch a file without showing the plan first.
✗ It does not delete a rule silently.
✗ It does not "improve" a role's wording for elegance. A change must prevent a named failure.
```

> **The measure of this protocol working is not how much the system grows.**
> It is that every rule in it can name the failure it prevents, the check that catches it, and the day it was born.

---

Reference: `roles/ROLE_LEAD.md` (⚡ REFLEX — proposes; `@EVOLVE` disposes) · `roles/ROLE_LEAD.md (§CRYSTALLIZATION)` (successful paths — a different memory: what worked, not what broke) · `roles/SYSTEM_UPGRADE_MANIFEST.md` (the record of waves) · `roles/INTEGRATION_PATCHES_TASTE.md` (the record of where integrations landed) · `roles/ASYNC_WORKERS_CANON.md` §0/§9 (two incidents that became laws — the model for Phase 2) · `roles/DATABASE_RUNTIME_CANON.md` §0 (the third) · `.cursorrules`
Version: 1.0 | 2026-07-12
