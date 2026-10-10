# LOGIC_MODELING_CANON.md
# The modeling core. The system had VALIDATION of a model (@PRINCIPLE checks what @ARCH drafted)
# and no CONSTRUCTION of one. This file builds the model — before there is any structure to check.
# Owner: @PRINCIPLE (mode MODEL). Consumers: @ARCH (structure derives FROM the model), @DEV, @QA_ARCH, @DESIGN.
# Position: after @DOMAIN_EXPERT (what the business does) and BEFORE @ARCH (how it will be built).

> **The canon's dogma:** software is holed by default not because anyone was careless, but because
> **the model never existed** — the structure was built first, and the logic was inferred from it afterwards.
> A schema is not a model. An endpoint list is not a model. **A model is the set of things that can HAPPEN,
> the states they can leave the world in, and the things that must remain true regardless.**
>
> **And the second law of this file: a model is not made better by re-reading it.**
> It is made better by **confronting it with adversaries** — and the adversaries are a FINITE, known list (§2).
> That is what makes the cycle terminate instead of becoming analysis paralysis.

---

## §1. WHAT A MODEL IS — seven layers, in this order

Each layer is answered **before** the next. Skipping one is where the holes come from — and every hole below has
a name you have already met in production.

```
1. ENTITIES AND IDENTITY
   What things exist? And for each: WHAT MAKES TWO OF THEM THE SAME THING?
   (a booking is identified by (resource, slot) — not by its row id. Get this wrong and every dedup,
    every idempotency key and every "why do I have two of these" bug is born here.)

2. LIFECYCLES
   For each entity with a status: the states, and the LEGAL TRANSITIONS between them.
   For every transition:  WHO may trigger it · WHAT must be true before · WHAT is true after ·
                          WHAT ELSE happens (side effects, events, money).
   The states nobody drew are the states you will find in production. Draw them all — including
   the ugly ones: paid-but-cancelled, published-but-edited, deleted-but-referenced.

3. OPERATIONS (the verbs — what can HAPPEN)
   Not endpoints. VERBS. "Confirm a booking", "Refund an order", "Publish a page", "Cancel an ingest".
   For each:  precondition · postcondition · who may · what it emits · IS IT REVERSIBLE, and if so, HOW.
   ⚠ The most common hole in all of software: an operation that can be DONE but not UNDONE,
     because nobody asked. Publish → can you unpublish? Pay → can you refund? Merge → can you split?

4. INVARIANTS (what must ALWAYS be true — the things reality is not allowed to violate)
   "No two bookings for one slot." · "Stock is never negative." · "Exactly one active plan per tenant."
   · "A published page always has a rendered snapshot."
   Each one gets a PROTECTION LEVEL here, not later: schema constraint · lock/version · atomic guard.
   → this becomes the INVARIANT LEDGER (`DATA_INTEGRITY_CANON` §9). It is BORN here, not discovered by QA.

5. EVENTS (what the system tells the world)
   What does this domain EMIT? (created, confirmed, failed, progressed, expired.)
   → this becomes the frontend's live layer (`FRONTEND_CAPABILITY` C2). An event nobody modelled is a
     "Refresh" button somebody will build later.

6. TIME
   What expires? What goes stale? What must be retained, and for how long? What happens to a draft that
   sat for six months, a lease that outlived its worker, a lock nobody released?
   ⚠ Time is the layer skipped most often and the one that produces the most expensive incidents.

7. AUTHORITY
   Who may do what — and, crucially, WHAT DOES EACH ROLE SEE? (Not "we'll 403 them." What do they SEE?)
   → this becomes `FRONTEND_CAPABILITY` C9: show what you CAN do, do not error after the click.
```

**The artifact:** `docs/artifacts/DOMAIN_MODEL_[MODULE].md` — the seven layers, filled. Half a page to three pages.
**It is not UML. It is not a diagram exercise.** It is the set of sentences that must be true, written down before
anybody types.

---

## §2. THE ADVERSARY CATALOGUE — twelve, and only twelve

> **This is the engine of the cycle, and its termination condition.** You do not "think harder" about the model.
> You march it past twelve named adversaries. When all twelve have been answered, the model is done — not because
> it is perfect, but because **you have exhausted the known ways reality attacks software**, and further staring
> yields nothing.

| # | The adversary | The question it asks the model | Where it bites if unanswered |
|---|---------------|--------------------------------|------------------------------|
| **A1 THE DOUBLE** | It happens twice. Retry, redelivery, a double click, a network blip. | duplicate charges, duplicate emails, duplicate rows |
| **A2 THE RACE** | Two actors, one object, same instant. | double-booking, overselling, lost update |
| **A3 THE DEATH** | The process dies **mid-way**. What is half-done? What is held? | the corpse lock, the orphaned job, the half-paid order |
| **A4 THE REVERSAL** | It must be UNDONE after the fact. Refund, unpublish, un-merge, restore. | the operation with no inverse — discovered by a customer |
| **A5 THE STALE** | It sat for six months. Is it still valid? Whose price? Whose rules? | the draft that references a deleted thing; the expired-but-active |
| **A6 THE PARTIAL** | Step 3 of 5 failed. What is the world now? Who tells the user? | silent inconsistency; "it says paid but nothing happened" |
| **A7 THE IMPOSTOR** | Wrong tenant, wrong role, right id. | IDOR, cross-tenant leak |
| **A8 THE OUTLIER** | 0 items. 10,000 items. An empty string. A 400-character name. An emoji. | the crash, the timeout, the broken layout |
| **A9 THE WRONG ORDER** | They do B before A. They skip step 2. They go Back and resubmit. | the unreachable state; the state nobody drew |
| **A10 THE ABANDONMENT** | They close the tab mid-flow. They never come back. | the zombie draft; the held resource; the lock nobody released |
| **A11 THE LIAR** | The external system says OK and did nothing. Or says nothing and did it. | the payment that "failed" and went through |
| **A12 THE SCALE** | It is a hundred times more. Ten thousand tenants, a million rows, a fat customer. | the query that dies; the report that OOMs |

**How to run it:** take each layer of §1 and each of the twelve. Not every cell is meaningful — most are not.
The ones that ARE meaningful produce either **a rule you did not have** or **a hole you must close**.

---

## §3. THE CYCLE — and why it terminates

```
        ┌────────────────────────────────────────────┐
        │                                            │
   BUILD THE MODEL (§1, seven layers)                │
        │                                            │
        ▼                                            │
   STRESS IT (§2, twelve adversaries)                │
        │                                            │
        ▼                                            │
   HOLES?  ── yes ──► CLOSE THEM (a new rule, a new  │
        │             state, a new invariant, a new  │
        │             operation) ────────────────────┘
        │
        no
        ▼
   THE MODEL IS DONE.  → @ARCH derives the structure FROM it.
```

**It terminates because the catalogue is finite.** You are not chasing perfection — you are exhausting a known
list. Two or three passes, and the twelve are answered. That is the whole discipline.

**A hole that cannot be closed inside the model** is not an engineering question. It is a **business decision**
nobody has made ("what SHOULD happen when a client cancels after the course started?") → @LEAD/@BIZ decides,
and the decision is written into the model. **Never guessed by the code.** This is the single largest source of
"silent policies" that nobody chose and everybody later has to live with.

---

## §4. WHERE IT SITS — and why the current order is backwards

```
NOW (produces holed software):
  @DOMAIN_EXPERT → @ARCH drafts a structure → @PRINCIPLE looks for holes in it → @DEV
                          ▲
                   the model is INFERRED from the structure, backwards, by whoever reads the code later

CORRECT (for a new module or a changed domain):
  @DOMAIN_EXPERT (what the business does)
        ▼
  @PRINCIPLE — MODE: MODEL  →  DOMAIN_MODEL_[MODULE].md, stressed against the twelve (§2)
        ▼
  @ARCH — derives the STRUCTURE from the model (schema, contracts, spine).
          The invariant ledger is a COPY of model layer 4. The job passports come from layer 3+6.
        ▼
  @PRINCIPLE — MODE: VERIFY  →  did the structure preserve the model? (this is the role's existing job)
        ▼
  @DEV — the contract sheet (Law 37) is now a LOCAL restatement of a model that already exists,
         instead of an attempt to invent one at the keyboard.
```

**Two passes of @PRINCIPLE, not infinite.** MODEL builds; VERIFY checks that the structure did not lose anything.

**When to skip modeling:** a change inside a domain already modelled, that touches no state, no invariant, no
operation, no authority. (A new column for display. A copy change. A styling fix.) **If you are unsure whether
to skip it, you are not skipping it.**

---

## §5. THE ARTIFACT — `docs/artifacts/DOMAIN_MODEL_[MODULE].md`

```markdown
# DOMAIN MODEL — [module]
> Built by @PRINCIPLE (MODE: MODEL) · stressed against the 12 · consumed by @ARCH · [date]

## 1. Entities and identity
| Entity | Identified by (what makes two the same) | Notes |

## 2. Lifecycles
| Entity | From → To | Who may | Precondition | Postcondition | Emits |
(and: the states we deliberately DO NOT allow, and what prevents them)

## 3. Operations
| Verb | Pre | Post | Who may | Emits | REVERSIBLE? how |

## 4. Invariants  → becomes the INVARIANT LEDGER
| Invariant | Protection level (schema / lock / guard) | Error out |

## 5. Events → becomes the frontend's live layer
| Event | When | Who cares |

## 6. Time
| What | Expires / goes stale / is retained | After what | Then what happens |

## 7. Authority
| Role | May | Sees (not "gets a 403" — SEES) |

## 8. THE TWELVE — the stress result
| # | Adversary | Answer / the rule it forced | Status |
|A1 | The double | Idempotency-Key on confirm; UNIQUE(request_id) | ✅ |
|A4 | The reversal | Publish has NO inverse → **hole**. @LEAD decided: unpublish = new draft version | ✅ decided |
|A9 | The wrong order | Editing a published page → forks a draft; the live version is untouched | ✅ |
| … | | | |
(Every one of the twelve is answered or explicitly marked "not applicable, because —")

## 9. Business decisions this model FORCED (holes that were not engineering questions)
| Question | Decided by | Decision | Date |
```

---

## §6. THE GATE

```
@LEAD    — a new module / a changed domain does not reach @ARCH without a stressed DOMAIN_MODEL.
           "We'll figure out the edge cases in QA" is the sentence that produces holed software, and it is
           how every one of them was produced.
@ARCH    — derives structure FROM the model. The invariant ledger is a copy of layer 4, not a fresh invention.
           A constraint in the schema that is not in the model, or an invariant in the model that is not in the
           schema → one of the two is wrong, and it is caught HERE, for free.
@PRINCIPLE— MODE: MODEL builds it; MODE: VERIFY checks the structure preserved it.
@QA_ARCH — an invariant in the model with no protection in the code → 🔴.
           A state reachable in the code that the model forbids → 🔴 (this is the bug class that has no
           other detector in the entire system).
@DEV     — the contract sheet restates the local slice of an EXISTING model. It does not invent one.
@DESIGN  — layer 2 (lifecycles) is the source of "show only the legal transitions" (FRONTEND_CAPABILITY C1);
           layer 7 (authority) is the source of "show what you CAN do" (C9). **The model is what the UI shows.**
```

---

## §7. WHAT THIS IS NOT

```
✗ NOT UML, not diagrams, not ceremony. Seven tables and twelve questions. Half a page to three pages.
✗ NOT analysis paralysis. The catalogue is FINITE — that is the entire design of it. Exhaust the twelve and stop.
✗ NOT a replacement for @ARCH. The model says WHAT MUST BE TRUE. @ARCH says HOW IT IS BUILT. Different jobs.
✗ NOT for every task. A styling fix does not get a domain model. A new module always does.
✗ NOT written after the code, from the code. A model derived from an implementation is a description, not a model,
  and it will faithfully describe every hole the implementation has.
```

> **The one sentence:** the holes in your software are not the holes the developers left.
> They are the holes **the model never had a chance to close, because there was no model.**

---

Reference: `roles/ROLE_PRINCIPLE.md` (owner — MODE: MODEL builds, MODE: VERIFY checks) · `roles/ROLE_DOMAIN_EXPERT.md` (the input: what the business actually does) · `roles/ROLE_ARCH.md` (derives structure FROM the model) · `roles/DATA_INTEGRITY_CANON.md` (§9 the INVARIANT LEDGER — a copy of layer 4; §2 the race recipes answer A2) · `roles/ASYNC_WORKERS_CANON.md` (A3 the death · A1 the double — the passports come from layers 3 and 6) · `roles/DATABASE_RUNTIME_CANON.md` (A3, A10 — what is held when a process dies) · `roles/FRONTEND_CAPABILITY_CANON.md` (C1 from layer 2 · C2 from layer 5 · C9 from layer 7 — **the model is what the UI shows**) · `roles/PRODUCT_MATURITY_CANON.md` (a class cannot be designed over a domain nobody modelled) · `.cursorrules` Law 37 (the contract sheet — the local restatement)
Version: 1.0 | 2026-07-13
