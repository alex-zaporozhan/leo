# SECOND_PASS_PROTOCOL.md
# The clean-context audit pass. NOT a checklist and NOT an automation — a declared step of the process.
# Owner: @LEAD (schedules and selects roles) · executed by the human in a NEW chat · consumers: every role.
# Position: after a delivered unit of work — one prompt, one stage, one batch. Never inside a prompt.

> **Why this file exists.** Every gate in this system is run by the same agent that produced the work it
> gates. That agent has already justified its own output; a checklist handed to it verifies the justification,
> not the product. The second pass exists because **a fresh context window is an execution environment**: it
> does not remember the rationalisations of the first pass, so it disagrees with the code instead of agreeing
> with the report. This is the property CI buys with scripts. Here it is bought with process, and it is the
> strongest verification the system has.

---

## §1. THE MECHANISM — isolation, not wording

The pass works because of **what it does not carry**, not because of what it says:

| The first pass has | The second pass has |
|--------------------|---------------------|
| Its own plan, and a stake in it | No plan to defend |
| A memory of "why that was fine" | The code, and nothing else |
| A closed Done-when it wrote itself | An open question |
| Fatigue at the end of a long chat | A full window |

**Consequence — the instruction must say SEARCH, not VERIFY-THIS-LIST.** A narrow prompt ("check V1, V12 and the error contract") finds only what was already predicted, and everything unpredicted survives. A broad prompt ("find what was done formally, what will not work, what could be better, and deepen the criteria yourself") finds the class of defect nobody listed. **Narrowing this pass into a checklist destroys the only thing it does better than the first pass.** This is a normative rule, not a preference.

---

## §2. THE LEVELS — trigger, scope, owner

| Level | Trigger | Scope | Cost expectation |
|-------|---------|-------|------------------|
| **SP-0 · prerequisite** | Before pasting the next unit of a series | Only the *previous* unit's Done-when, verified against the workspace on disk (§3) | Minutes. It is a check, not an audit — and it is what stops a skip-cascade. |
| **SP-1 · per unit** | A prompt / task reported Done-when | Everything that prompt touched: code, artifacts, contracts, screen | The workhorse. Runs every time. Expect it to cost several times the unit itself — that is the correct price, not an overrun. |
| **SP-2 · per stage** | A stage closed (typically 5–7 units) | The stage as one object: does the sequence of units add up to a working path A→B; cross-unit contradictions; what each unit could not see | Once per stage. Finds what no single SP-1 could see. |
| **SP-3 · per batch / epic** | A batch or epic closed, before any release claim | The whole delivered surface against the product: the model, the routes, the screens, the promises | Once per batch. This is the pass that catches "every unit green, the product holed". |

**@LEAD's duty:** the slots are **planned into the series, not discovered afterwards**. A batch map that shows only production prompts is an incomplete batch map (`TC-18`). @LEAD writes the SP slots into the map and names, per slot, the role set from §4.

**The human's duty:** the pass runs in a **new chat**. Continuing in the producing chat is not a second pass — it is the first pass with a new question, and it inherits every rationalisation the isolation was there to drop.

---

## §3. THE INTERCEPTOR (SP-0) — before the next unit, not after the batch

Between unit N and unit N+1 the queue is exposed to one specific failure: unit N reported success it did not deliver, and unit N+1 either builds on absent ground or declares the whole tail blocked and skips it (the **skip-cascade**: an agent that meets a publish/deploy prerequisite mid-queue reports compliance and writes no code, for twenty units in a row).

**SP-0 is one short pass, in its own chat, naming the prior unit explicitly:**

```
Check whether prompt [ID] was actually finished — read its Done-when and verify each line against the
workspace on disk (paths exist, tests pass, the behaviour is present in code, not only in the report).
Anything unmet: finish it now. Do not skip forward, do not mark the batch blocked.
```

**Rules:** SP-0 verifies **disk**, never publication — "not committed" and "the stand is stale" are not failures of unit N and never a reason to skip N+1. A genuinely missing prerequisite stops **this** chat with named paths, and one chat only.

---

## §4. ROLE SELECTION — by task class, mechanically

The class is already resolved by `roles/RAG_CANON.md` §2, so the role set is a lookup, not a judgement call. **An extra role is not free:** it costs tokens and, worse, it dilutes attention across a surface it has nothing to say about.

**This table is a default, not a permission list.** It gives a starting set that is right most of the time; the developer composes the actual set for the situation — adding a role because this particular change touches money, dropping one because the change is trivial, calling a pair no table names. **No role is ever excluded by this table, and no set is complete merely because the table says so.** What it removes is the blank page, not the choice: a pass that starts from a default beats a pass that starts from "who should look at this?", and the composition stays the developer's instrument.

| Task class | Default roles | What each is there for |
|------------|--------------------|------------------------|
| **TC-01 · operational-screen** | @FRONTEND @DESIGN @QA_VISUAL @QA_ARCH | craft floor and primitive · pattern and composition · rendered geometry and states · business logic and the four states |
| **TC-02 / TC-03 · public / statement** | @DESIGN @MOTION @QA_VISUAL @SEO | world fidelity and timidity (Y1–Y12) · motion ambition delivered · geometry and rhythm · semantics not cut by design |
| **TC-04 · node-graph** | @FRONTEND @DESIGN @AI_ENGINEER @QA_VISUAL | node readability (G1–G10) · inspector density · graph semantics and run contour · overlay geometry |
| **TC-06 · backend-slice** | @DEV @QA_ARCH @PRINCIPLE | contract sheet honoured · vectors and error contract · invariants and reachable states |
| **TC-07 · async-pipeline** | @DEV @QA_ARCH @PENTEST @ARCH | the reflex greps · liveness vectors · crash tests T-A…T-I · passports match the code |
| **TC-08 · integrity / tenancy** | @PRINCIPLE @QA_ARCH @PENTEST | invariant ledger vs schema · data-race vector · isolation attacked, not asserted |
| **TC-09 · migration** | @ARCH @DEV @QA_ARCH | expand/contract order · reversibility · data left consistent |
| **TC-10 · security-surface** | @PENTEST @SEC @QA_ARCH | attacks run, red-green regression present · the 18 pillars · surface preflight |
| **TC-11 / TC-12 · model / architecture** | @PRINCIPLE @ARCH @LEAD @BIZ | twelve adversaries answered · structure derived from the model · fit to the product · undecided business rules surfaced |
| **TC-13 · ai-contour** | @AI_ENGINEER @PRINCIPLE @QA_ARCH | passports and eval thresholds · money/status effects · retrieval honesty |
| **TC-14 · visibility** | @SEO @DESIGN @ARCH | curl-without-JS · H-structure survived design · rendering decision honoured |
| **TC-18 · execution-planning** | @LEAD @ARCH @QA_ARCH | tier and code ratio · gaps between units · gates not embedded as mid-queue stops |
| **TC-19 · documentation** | @SCRIBE @LEAD | no invented facts, `[UNDOCUMENTED]` used honestly · fit to the audience |
| **TC-05 · visual-concept** | @CREATOR @DESIGN @MOTION | the world is coherent across its axes and survives the TASTE GATE · the passports are derived, not slot-filled · no `[hex]` placeholder left |
| **TC-15 / TC-16 · a QA pass itself** | @LEAD + the role that produced the report | **spot-check 2–3 of the report's own claims against the code** — a green that does not survive re-reading is a false green, and auditing the auditor is the only defence against it |
| **TC-17 · product-package** | @LEAD @BIZ @PRINCIPLE | the differentiator survives one sentence · the foundation is not tagged LATER · the first domain model exists |
| **TC-20 · system-evolution** | @LEAD + the role whose canon changed | the router knows about the change (§6 of the router) · no rule added that already lives elsewhere · no conflict with a decided winner |
| **TC-21 · operations** | @ARCH @DEV @QA_ARCH | the running artifact is the one that was built (Law 36) · config centralised, no magic values · the declared contour matches what actually runs |
| **TC-00 · trivial** | none | no pass. Law 43 forbids ceremony here; if a trivial change turns out to need one, the class was wrong |

**The audit set is not the class's reading list.** A class minimum names the canons the *work* needs; this table names the roles whose *judgement* the finished work needs. They legitimately differ — TC-17 reads `ROLE_DOMAIN_EXPERT` while its audit set does not call @DOMAIN_EXPERT, because mapping a domain and judging a delivered package are different acts. Where you want both, add the role: nothing here forbids it.

**Always in the set:** @LEAD at SP-2 and SP-3 — the only role whose subject is the product rather than a layer of it.

---

## §5. THE DERIVATION CHAIN — what turns re-reading into checking

**An audit without a reference finds only the internal inconsistency of the text it is reading.** That is why a specification can be excellent and still contradict the product: nothing states what the product *is*, so the contradiction has nothing to be measured against. Every artifact in this system is **derived from** something above it and is **checked against** it — and the pass walks the chain **upward**, not only at the level it was handed.

| Artifact | Derived from | Checked against, in the pass | A 🔴 in this cell means |
|----------|--------------|------------------------------|------------------------|
| Code | `DEV_PROMPTS` + the spine | the prompt's Done-when **and** the artifact above it | code that satisfies the prompt and violates the decision |
| `DEV_PROMPTS` | spine + `DOMAIN_MODEL` + Security Contract | the spine's numbers and the model's invariants | a prompt that instructs a violation — the most expensive defect class, because it is executed faithfully |
| `DESIGN_SPEC` | `VISUAL_CONCEPT` + `CAPABILITY_MAP` + the register canon | the world, the backend's real capabilities, the register | a beautiful spec for a product that cannot do that, or in the wrong register |
| `CAPABILITY_MAP` | the actual backend (schema · endpoints · passports · ledger) | the code, not the ticket | a map written from the ticket — the tablecloth is laid here |
| `ARCH_SPINE` / ADR | `DOMAIN_MODEL` | the model's layers 1–4 and 7 | structure that encodes a hole the model had already closed |
| `DOMAIN_MODEL` | `BUSINESS_ROUTES` + the twelve adversaries | the routes, and the twelve answered | a model that skipped an adversary — that adversary will arrive in production |
| A screen | the product surface (routes · roles · journeys) | every other screen carrying the same action | the same capability living in two places with no declared home |
| **The change itself** | the task and its Done-when | **the diff, before any prose** — what this change actually did, not what the file now contains (Law 12) | a report describing a change the diff does not contain |
| **Product invariants** (`PRODUCT_INVARIANTS_[PROJECT].md`) | `BUSINESS_ROUTES` + the declared surface set — the invariants say what the product's shape must stay, so they are derived from its routes and its surfaces, not from a screen | the whole product surface at once, never one screen | an invariant broken by a screen that is individually flawless — the defect no single-surface gate can see |
| `QA_REPORT` | the code | a spot-check of 2–3 of its own claims against the code | a report whose 🟢 does not survive re-reading — the false green |

**The upward rule:** when the pass finds a defect, it asks **one level up before fixing down**. A missing state in the UI is a UI bug *unless* the model never had that state — then fixing the UI hides the real hole and it will return through another screen. **Fixing a symptom whose cause lives one level up is a formal fix**, and it is reported as such.

**The ADR discipline this chain enforces:** an ADR is born only from a decision that has a **spine vertebra or a model layer behind it**. A note about an implementation choice is not an ADR — it belongs in the task report. Arbitrary ADRs are the visible symptom of a chain nobody walked.

---

## §6. KNOWN FALSE-GREEN PATTERNS — start the pass here

These are the shapes "done" takes when it is not done. They recur across projects and are cheap to look for first. **Add a row whenever a new one is met** — this catalogue is scar tissue, and its value is that it grows.

| # | Pattern | How to catch it in one move |
|---|---------|----------------------------|
| **FG-1** | Declared closed; the code does something adjacent instead | Read the named function, not the report. Compare the *behaviour* to the claim word by word. |
| **FG-2** | Wiring without the operating end — the field is read on the frontend, the admin has no input for it | Trace the value end to end: who writes it, who stores it, who reads it. |
| **FG-3** | A class, preset or token declared in code and absent from the stylesheet | grep the identifier in both layers. |
| **FG-4** | A link, card or CTA pointing at a route that does not exist | Enumerate every href/navigate target; resolve each against the router. |
| **FG-5** | A silent no-op — a guard returns unchanged state with no toast, no error, no reason shown | grep early `return` in handlers; any branch that ends without user-visible consequence. |
| **FG-6** | A promise in copy with no implementation behind it | Take the product's own promises and look for the code path of each. |
| **FG-7** | A cost/limit estimate that ignores a dimension it must count | grep the dimension's variable inside the estimator; absence is the finding. |
| **FG-8** | Polling with no terminal condition — keeps requesting after 404/completion | Find every interval/poll; each must have an exit and a ceiling. |
| **FG-9** | An entity that can be created and not deleted (or vice versa) | For each entity, list the operations actually reachable in the UI. |
| **FG-10** | Internal roles, tool names or process vocabulary leaking into product copy | grep `@`-role markers and tool names in user-facing strings (Law 20). |
| **FG-11** | A test asserting the old, wrong behaviour — the bug became the spec | When a fix "breaks a passing test", read the test before touching the fix. |
| **FG-12** | An artifact claiming a version/decision the sibling artifacts of the same batch never received | Diff the decision across every file that names it. |

**Count the hits — a rule that never fires is not a rule.** The catalogue above is the **canon** and lives here; the **tally is per project** and lives in `docs/artifacts/FALSE_GREEN_REGISTER.md` — **opened by @LEAD at the first pass that matches a shape** (it does not pre-exist; its absence is never a reason to skip the count) — one row per shape (FG-1…FG-12 plus any new ones), columns: **shape · hits · where each hit was found · promoted to which rule**. `roles/` is the universal layer and Law 16 keeps it out of per-project writing, so the count cannot live in this file.

**The trigger:** every pass that matches a shape increments its row — that is a line in §8 CLOSURE, not an intention. **Three hits of the same shape = an automatic `@EVOLVE` candidate**: the system gains a permanent check (a reflex grep, a gate line, a passport field) and the shape stops being caught by hand. A finding that is only ever fixed is a finding the system did not learn from — this counter is what closes **incident → rule**, the loop that made the async contour hold.

---

## §7. THE INSTRUCTION — the form is normative, the wording is versioned here

The pass is invoked with a **broad search instruction**, in a new chat, naming the role set from §4. Canonical form:

```
[role set from §4] — take everything the previous [prompt / stage / batch] produced and audit it:
functional completeness · precision of execution against the artifact above it (§5 chain) · architectural
coherence · design and craft in the declared register · the user's path A→B step by step, including the
narrow viewport · critical, medium and minor risks (queues, races, gaps) · what was done FORMALLY and will
not actually work · what could have been done better · outright bugs. Fix what you find, implement your own
recommendations within the platform's declared architecture. Do not stop at the first defect — go deep.
Extend the audit criteria yourself where mine are weaker than they should be.
```

**Two rules about this text.** (1) The invitation to *extend the criteria* stays — it is what lets the pass exceed the author's imagination. (2) It is **never** replaced by a list of specific checks; a narrowed pass is a different, weaker instrument (§1).

---

## §8. CLOSURE — what a pass leaves behind

```
□ Findings fixed in the same pass where the fix is E1–E2 (Law 43).
□ Findings that are E3–E4 are NOT quietly reconstructed — they are named, scoped and routed to @LEAD.
   A second pass that silently rebuilds the architecture is a bigger defect than the one it found.
□ A cause one level up (§5 upward rule) is reported at that level, not patched at this one.
□ One line into the **wave ledger** — `docs/artifacts/waves/[N]/WAVE_LEDGER.md`, owned by @LEAD, one line per closed unit: what was found, what was fixed, what was escalated, what remains open.
□ Every matched false-green shape → **increment its row** in `docs/artifacts/FALSE_GREEN_REGISTER.md`, with where it was found.
□ A new shape → a new row there, and a new row in §6 of this canon.
□ A shape reaching **three hits** → raise it to @LEAD as an `@EVOLVE` candidate: which permanent check would have caught it.
□ A recurring finding that is really a missing system rule → @EVOLVE, not a note.
   The pass is how scar tissue enters the system; that is its second job.
```

---

## §9. WHAT THIS IS NOT

```
✗ NOT automatable, and not a candidate for it. The value is the empty context window; an automated pass
  inside the producing chat has neither the isolation nor the freedom to look where nobody pointed.
✗ NOT a replacement for @QA_ARCH, @QA_VISUAL or @PENTEST. Those are gates with defined vectors and
  blocking verdicts. This pass looks for what no vector covers — and hands what it finds to them.
✗ NOT a checklist. See §1: the moment it becomes one, it stops finding the class of defect it exists for.
✗ NOT optional because the unit "reported green". A reported green is precisely its input.
✗ NOT a licence to reconstruct. §8 is the boundary.
```

---

Reference: `.cursorrules` (TASK ROUTING · Law 12 fact-or-admission · Law 14 no "practically good" · Law 42 · Law 43) · `roles/RAG_CANON.md` §2 (the class, hence the role set) · `roles/LEAD_ANTI_CHECKBOX_PROTOCOL.md` (forbidden phrasings) · `roles/LEAD_PRODUCT_GATE_PROTOCOL.md` (the formal gates this pass feeds) · `roles/LOGIC_MODELING_CANON.md` (the level above most UI findings) · `roles/ASYNC_AWAIT_REFLEX.md` (the reflex form — mechanical, self-run, complementary to this pass) · `roles/SYSTEM_EVOLUTION_PROTOCOL.md` (`@EVOLVE`, for a finding that is really a missing rule)
Version: 1.0 | 2026-09-03
