# MIRROR PROTOCOL — Mutual Role Reinforcement

> **What this is:** a managed cross-audit session of roles.
> **When to run:** manually — after adding a new role, after a major system refactor, or when recurring friction between roles appears in the work.
> **Do not run:** during active development, automatically, on a schedule.

---

## THE ONLY IMPROVEMENT CRITERION

Before reading the protocol — fix this filter. It applies to every observation found.

An improvement exists **only** if one of three conditions holds:

```
[VAC] VACUUM OF DECISION
      Role B makes decision X without knowing the context of role A,
      even though that context should change that decision.

[GAP] RESPONSIBILITY GAP
      Action Y belongs to no one between roles A and B.
      Each believes the other does it — or nobody believes so.

[CON] CONTRADICTION
      Role A says one thing, role B says another
      about the same situation, artifact, or decision.
```

Anything that does not fall into one of the three types — **is not an improvement**. Rejected without discussion.

**Forbidden "improvements" (automatic rejection):**
- Adding a reference from role A to role B without a substantive reason
- Copying a checklist from one role to another
- Adding mention of an artifact simply because it exists
- Rephrasing the same thing in different words
- Any change that increases role volume without changing its behaviour

---

## GRAPH OF SIGNIFICANT CONNECTIONS

Not all role pairs have intersection points. The matrix is built on the principle:
**"A→B is significant if A regularly hands an artifact to B, or B makes a decision based on A's work"**

```
Direction          Connection type
───────────────────────────────────────────────────────
@CREATOR   → @LEAD        delivers start package
@CREATOR   → @ARCH        business logic shapes stack
@BIZ       → @ARCH        business routes before DB schema
@DOMAIN    → @ARCH        domain rules before API
@PRINCIPLE → @ARCH        invariants before DEV_PROMPTS
@ARCH      → @DEV         contracts and DEV_PROMPTS
@ARCH      → @QA_ARCH     what to check and by what criteria
@ARCH      → @PRINCIPLE   draft for revision
@DEV       → @QA_ARCH     code for audit
@QA_ARCH   → @DEV         list of fixes
@QA_ARCH   → @DESIGN      systemic design problems
@DESIGN    → @DEV         DESIGN_SPEC as input
@AUDITOR   → @ARCH        systemic defect
@AUDITOR   → @LEAD        escalation
@QA        → @OPS         readiness for deploy
@SEC       → @ARCH        vulnerabilities requiring architectural solution
@SCRIBE    → @LEAD        UNDOCUMENTED list for archiving
@PRINCIPLE → @QA_ARCH     invariants as audit criterion
```

Total: **18 directed connections**. Each is one "lens" in a session.
Not all need to be covered in one session. Choose only those where there is real friction.

---

## SESSION PROTOCOL

### Step 1 — Select pairs (you do this)

Before launching, select 1–5 pairs from the graph. Selection criteria:

```
□ Was there friction at this junction in recent sessions?
□ Was a new role added recently that affects this connection?
□ Did @LEAD escalate a problem that came from this junction?
□ Was a role significantly changed — do neighbours know about it?
```

Record: `MIRROR_[DATE].md` — start the file with the list of selected pairs.

---

### Step 2 — Lens (run in Cursor)

For each selected pair A→B — one prompt:

```
MIRROR SESSION: @[A] looks at @[B]

Read docs/ROLE_[A].md and docs/ROLE_[B].md fully.

Ask yourself three questions from the perspective of role A:

1. VACUUM: Is there a decision that B makes without the context I (A) hold —
   and this decision would be different if B had that context?

2. GAP: Is there an action that should happen at the A→B junction,
   but belongs explicitly to neither me nor B?

3. CONTRADICTION: Is there a situation where A and B give different instructions
   about the same thing?

For each observation found:
- Type: [VAC / GAP / CON]
- Fact: what specifically happens now (with a quote from the role file)
- Effect: what goes wrong in real work because of this
- Patch: minimum change that eliminates the problem (1–3 sentences max)

If no observations — write: "Connection A→B: no gaps found."
Do not invent observations to have something to write.
```

---

### Step 3 — Filter (you do this)

On receiving the response — apply the filter manually to each observation:

```
□ Is this [VAC], [GAP] or [CON]? If not — reject.
□ Does the patch increase role volume without changing behaviour? Reject.
□ Is the patch copying text from one role to another? Reject.
□ Does the patch solve a problem you actually felt in the work?
  If not — postpone, do not reject.
□ Does the change affect a system file (.cursorrules, ENGINEERING_PLAN)?
  Requires separate confirmation — Law 16.
```

---

### Step 4 — Applying patches

Observations that pass the filter — apply pointwise to role files.
Recording format in `MIRROR_[DATE].md`:

```markdown
## Pair: @ARCH → @DEV

### [VAC-01] Name
**Fact:** "...quote from ROLE_ARCH.md..." shows that @ARCH passes X,
           but "...quote from ROLE_DEV.md..." does not mention X as input.
**Effect:** @DEV makes decision Y without X → specific error.
**Patch:** In ROLE_DEV.md, section "Execution Protocol", add:
          "[1–3 sentences max]"
**Status:** ✅ Applied / ⏸ Deferred / ❌ Rejected

---

### Connection @ARCH → @QA_ARCH: no gaps found.
```

---

### Step 5 — Session close

After applying patches — one line in `MIRROR_[DATE].md`:

```
Session closed: [date]
Pairs checked: N
Patches applied: M
Next session: [trigger or "as needed"]
```

File goes to `docs/artifacts/archive/`.

---

## SESSION ANTI-PATTERNS

These signals indicate the session went off track — stop:

**Textual proliferation**
Roles begin to reference each other in a circle. Each adds `Reference:` to its neighbour. Volume grows, behaviour does not change. Sign: not a single patch changed a single verb in a role.

**Architectural refactor instead of a patch**
An observation requires rewriting a role by 50%+. That is not a patch — it is a signal the role is conceptually obsolete. Right action: record in `MIRROR_[DATE].md` as `[REQUIRES_REDESIGN]` and hand off to @LEAD as a separate task.

**Patch without real friction**
"This would be better if..." without an example of a real situation where it broke. Defer, do not apply.

**Symmetric additions**
A adds mention of B, B adds mention of A. If both changes are only mentions, both are rejected. A true improvement is asymmetric: one side changes behaviour, the other does not.

---

## EXAMPLE OF A COMPLETE OBSERVATION

So it is clear what counts as a quality conclusion:

```
Pair: @PRINCIPLE → @QA_ARCH (lens: what QA_ARCH does not know from PRINCIPLE)

[GAP-01] Invariants do not reach the audit

Fact: ROLE_PRINCIPLE.md §"Handoff" says @PRINCIPLE passes
      invariants [INV-NN] to @ARCH. ROLE_QA_ARCH.md nowhere mentions
      that the audit should verify fulfilment of these invariants —
      only UI vectors and business logic.

Effect: @QA_ARCH issues 🟢 on Finance module. Invariant [INV-03]
        "on confirm_booking there is no second active record for the slot"
        is implemented incorrectly in code — but QA_ARCH did not know to check this,
        the invariant was nowhere in its checklist.

Patch: In ROLE_QA_ARCH.md, Vector 8 "Data Integrity", add item:
      "[ ] If PRINCIPLE_FINDINGS_*.md exists for the module —
           verify that each [INV-NN] from it is either reflected in code
           or explicitly marked N/A with a reason."

Status: ✅ Applied
```

---

## WHEN NOT TO RUN

- Active development is in progress (TUNNEL in state machine)
- A role was just added and has not worked in real sessions yet
- No specific friction — running "for prevention" produces noise
- Fewer than 3 real sessions with the system since the last MIRROR

---

Reference: `roles/ENGINEERING_PLAN.md` · `.cursorrules` Law 16
Version: 1.0 | 2026-04-02
