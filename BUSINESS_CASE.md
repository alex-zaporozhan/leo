# The Business Case

**What LEO changes about risk, cost and timeline — written for the person paying for the software, not for the person writing it.**

If you are evaluating whether an AI-directed engineering process can build something you will still own in three years, this page is the short version.

Two things about the evidence, before anything else. Every claim about **the system itself** — laws, files, gates, mechanisms — is checkable against the rest of this repository, and pointers are at the bottom. Every claim about **the three systems built with it** — the timelines, the running cost, what shipped — is mine and unaudited; treat it the way you treat any founder's own numbers until you can ask about them. One exception worth knowing: the clinic SaaS goes public with its full commit history, and the commit dates settle its timeline without needing my word for it. And it is one person's work: not a team, not a firm with a bench. That is the material fact about who you would be dealing with, and it is the point rather than the caveat — a process built so that one operator can hold three production systems is the whole claim. How I got here, without the polish, is in [`MANIFESTO.md`](./MANIFESTO.md).

---

## The claim, stated narrowly

**LEO does not make an AI coding agent faster.** Saying otherwise would be the easiest sentence to write here and the first one a competent engineer would discount.

What it does is make the agent's output **survivable** — able to be extended in month nine by someone who was not there in month one.

**The thinnest part of the evidence.** All three systems below were built inside the last year, and none has yet been handed to a different engineering team to carry on. Nobody has yet taken one of them over cold and shipped a change from the written record alone. What is demonstrated is that the record exists, that it was written before the code rather than after, and that fresh sessions with no chat history have repeatedly picked the work back up from it — which is the same mechanism at a shorter horizon, not a substitute for the proof. The commercial value is almost entirely in things that did *not* happen: the double-booking that never reached production, the GPL dependency that never entered the stack, the rewrite that was not needed because the data model was designed for the whole product rather than for the demo.

That is an unglamorous claim. It is also the one that decides whether a codebase is an asset or a liability on your balance sheet.

---

## What was actually built, and in how long

Three production systems of deliberately different shape, each directed end-to-end through this process by **one engineer working full-time and single-handed**. Elapsed calendar time, not effort estimates:

| System | Elapsed | What shipped |
|---|---|---|
| **Clinic operations SaaS** — appointments, patient records, staff scheduling, billing, notifications, across many independent clinics on one system. Source-available, going public at [github.com/alex-zaporozhan/medCore](https://github.com/alex-zaporozhan/medCore) | **2 months** | Data separation between clinics enforced at the database, protection against two staff booking the same slot at once, reliable delivery of notifications even when a worker dies mid-send, a 49-permission access matrix checked by CI against the actual endpoints · 189 test modules, 816 test cases (collector-verified) |
| **Enterprise AI training platform** — client engagement, under NDA | **3 months** | A visual builder where a non-programmer assembles an AI pipeline as a graph and runs it, retrieval over the client's own corpus, generated media produced on a reproducible pipeline · 273 endpoints across 30 router modules, 124 migrations, 61 numbered architectural decisions · 366 test modules, 3,888 test functions (static count, Sept 2026) |
| **Public-sector vocational education platform** | **1.5 months** | A public site that search engines can actually read, accessible to WCAG AA, licensed-content compliance, and **a page builder with 20 block types written from scratch** so staff publish without a developer · 1,124 backend + 1,027 frontend tests, 17 Playwright visual and accessibility specs (all collector-verified) |

**"Shipped" here means running and in use, not launched to a public user count** — two are in service with their owners, one is a public-sector platform in production. There are no user, uptime or incident figures in this repository, so do not read any into the table. This page also prints no comparison against a hypothetical team: that number would be invented, and you would be right to discount it.

**Does this lock you in?** No, and the reason is structural rather than contractual. **LEO never ships.** It is a set of rules an AI reads while the code is being written; nothing in it is imported, linked, or present at runtime. What you receive is an ordinary codebase in ordinary frameworks — Python/FastAPI, PostgreSQL, React — that any competent team can read without ever having heard of this system.

What LEO leaves behind in the repository is markdown: the architectural decision records, the domain model, the invariant ledger, the threat models. Those *help* a team that arrives later — they are the written answer to "why is it like this," which is normally the most expensive thing to reconstruct — and they bind no one. If you never touch the framework again, you lose the process, not the product.

And if you do want it: the rules are source-available. You may keep, read, modify and run them inside anything you build, commercially, without permission or payment. The only withheld right is repackaging the framework itself as a competing product ([`LICENSING.md`](./LICENSING.md)). Two ways to use this, then — hire the person who operates the system, or take the system into your own team. Neither requires the other.

**And the dependency you would actually be signing is on one person, not on a framework.** That is the honest shape of it, and the artifacts are the answer to it: the architectural decision records, the domain model, the invariant ledger and the threat models exist *because* the process assumes whoever reads them next was not there. They are written for a stranger by design, since the agent itself is a stranger in every new session. A handover is the case this was built for — it is the normal operating condition, not a contingency plan bolted on. What is untested is the long version of it, and that is said plainly above.

**One caveat that is load-bearing.** The clinic SaaS was the first system directed this way, on early versions of LEO, and it is rougher than the two that came after it. It is published with those flaws intact rather than curated, because a debut that shows the process holding up against genuinely messy real data is better evidence than a polished demo that hides how it actually went.

---

## Where the money actually is

Every row is a failure mode of an unconstrained coding agent, what it costs when it reaches production, and the mechanism that stops it. Most of these rules exist because the corresponding defect already happened once, in one of the three systems above.

One thing to be exact about, because it is where this kind of page usually cheats. **Every row below is ultimately a written rule that an AI is required to read.** LEO ships no runnable code — it cannot, it is text. What differs is where the rule *ends up*: some end in a machine that refuses to cooperate (a database constraint that rejects the second booking; a licence scan wired into your CI), and once written, those hold whether anyone is paying attention or not. The rest end in a second pass that has to catch it.

That is a weaker guarantee than a compiler and a stronger one than a code review, and it is the honest description. What it buys is coverage: the defect classes below have no automated detector anywhere in the industry, and the alternative to a rule plus an auditor is not a machine — it is nothing.

| What goes wrong with an unsupervised agent | What it costs you | What stops it here |
|---|---|---|
| It forgets a decision it made forty messages ago and quietly re-decides it differently | The slow, invisible one: a codebase with two contradictory answers to the same question, which nobody notices until a feature has to cross both | Decisions live in versioned files, not in chat history. A new session reads the file and inherits the decision instead of re-guessing it |
| Two requests race and both succeed — a double-booking, a double charge, an oversell | Direct revenue loss and a support incident per occurrence, in a class of bug that is nearly impossible to reproduce on demand | A rule that **ends in a machine**: the invariant goes into a database constraint or a lock, never an `if` in application code — after which the database refuses the second write, unsupervised, forever. An `if` is explicitly declared *not* protection |
| A dependency with a GPL/AGPL/SSPL or unknown licence ships in the product | Found at acquisition or enterprise due diligence, when the cost of removing it is highest and the leverage is entirely theirs | A rule that **ends in a machine**: a licence scan wired into your CI as a merge blocker, plus a project-level decision record. A 50/50 doubt blocks the merge |
| The foundation — data model, permissions, tenancy, money paths — was built as an "MVP slice" | The month-six rewrite. The single most expensive event in a young product, and the one most reliably caused by a reasonable-sounding early decision | "MVP" is treated as a delivery schedule, never a quality bar. The foundation is designed for the whole product on day one; only *features* ship in waves |
| Security is reviewed after the feature works | A breach, or a security review that blocks a launch you already sold | A threat model is written *before* the first line of code on any security-relevant surface, and the security verdict blocks deployment rather than advising it |
| The agent reports success without having checked | You find out from a customer | "Probably implemented" and "should work now" are banned phrases. The only permitted answers are *verified, with file and line* or *could not determine, with the reason* |
| The session that built it is the same one grading it | Defects that pass every gate because the reviewer already agreed with the author | Every delivered unit is re-audited in a **fresh context that never saw it being built**, with a catalogued list of the specific ways an audit reports a false pass |

---

## "Doesn't all that process make it slow and expensive?"

**The short answer, in real numbers.** Agent tokens for the clinic SaaS cost **$60** — not per month, for the entire project. The AI pipeline platform, by far the hardest of the three, cost about **$1,000** across its three months. The public education platform, built in parallel with it, about **$200**. On a turnkey enterprise build with a high design bar, budget **$1,000–$2,000 in tokens for the whole project** — roughly $200–$400 a month across three to four months. The longer answer is the rest of this section, and it starts by conceding the premise: **yes, this costs tokens.** A constitution of ~110 KB is resident on every single task, before any work begins — that is a fixed toll on every request, paid all day, and no amount of routing removes it.

And the toll is bigger than that one file. A task's reading set can add up to roughly another 100 KB of canons on top, and a role may open more when the work needs it — the routing decides *which* rules load, not *whether* rules load. Call the real floor two hundred kilobytes of instructions before the first line of your code is read.

What that toll buys is the thing that decides whether a codebase can grow: **every task starts from the same decisions.** An agent with no constitution is not cheaper, it is cheaper *per request* and then pays it back with interest — it re-derives the tenancy model on Tuesday having agreed a different one on Monday, and the divergence surfaces two months later as a feature that cannot be built without unpicking both. That is the real cost curve of unconstrained agentic work: the first hundred tasks are fast and the next hundred are archaeology.

**The 131-file library, by contrast, is not resident at all.**

Every task is classified first — one of 22 task classes — and the class names **at most six files** to read, in order. A one-line copy fix resolves to a class that explicitly *forbids* ceremony; a payments change resolves to a class that pulls the concurrency and integrity canons whether the developer thought of them or not. The size of the library affects what is *available*, not what any one task carries. That is the property that lets the rule set keep growing — the two-hundredth canon costs nothing on a task that does not route to it — and it is the difference between a rule set that scales and a hand-written instructions file, which stops being read somewhere around thirty rules because everything is always in the prompt and nothing is emphasised.

The same mechanism is why this is not only for large systems:

- **Effort is declared before the work, and in a unit you can audit** — the number of existing decisions a task will reopen. Four tiers, from "nothing reopened" to "the decision set is rewritten." The rule is to take the lowest tier that reaches the declared result, and spending 80% of the effort for the last 10% of the outcome is refused by default — with named exceptions for the cases where it is genuinely correct, such as money, medical and legal domains, where an almost-right number is worthless rather than nearly right.
- **Escalation is not silent.** Arriving at a full reconstruction from a task that was scoped as a small extension means the estimate was wrong: the work stops and the decision comes back to you. You do not discover a rewrite by receiving it.
- **The expensive part of agentic development is the re-attempt, not the attempt** — building the wrong thing and rebuilding it. Everything above exists to make the wrong thing visible before it is built.

**Which brings back the figures at the top of this section, because the gaps between them are the argument.** $60 and $1,000 is a factor of sixteen, and none of it is process: the same laws and the same gates ran on both. What separates them is the domain — the expensive one is a multi-tenant retrieval platform indexed by chapter as well as by meaning, a twelve-node visual pipeline runner, several third-party AI adapters that each had to be made to behave atomically, three separate authentication contours and per-tenant billing. **Cost tracked the difficulty of the problem, which is what you want it to track.** A process tax would have shown up as a flat surcharge on all three, and it did not.

**And the cheapest of the three is the one worth pausing on, because it makes the sharper point.** That system was built entirely on a mid-tier coding model — not a frontier one — and it still arrived with tenant separation enforced in the database, a 49-permission matrix checked by CI against the live endpoints, and 816 tests. It is also the roughest of the three and stopped at pre-production, and both halves of that belong together. What the $60 shows is where the quality came from: **the written process did work the model tier did not have to do.** That is the difference between an approach whose output moves with whatever model you can afford this quarter, and one that does not.

Set any of these figures against one month of one engineer's time, and then against the rewrite this is designed to prevent. **The expensive resource here has never been tokens.**

---

## What it costs

The four that matter, in the order you will meet them:

- **The first week is slower.** Writing the domain model and the architectural decisions before the code feels like overhead precisely until the first time something changes underneath them.
- **Artifacts have to be maintained.** They are the memory of the project. A stale artifact is worse than none, and the process treats a mismatch between a document and the code as a defect in the document — the code wins, always.
- **Every non-trivial task carries a declared scope and effort tier.** That is a small tax on each task and the entire mechanism by which scope creep becomes visible instead of ambient.
- **It is opinionated about a stack.** The *process* is stack-agnostic; the specific engineering canons are written against Python/FastAPI/PostgreSQL/React, because a rule vague enough to fit every stack is a rule nobody can enforce. The skeleton — roles, gates, artifact contracts, the effort tiers — transfers unchanged. The stack canons do not, and replacing them is real work; nobody has yet done it for a stack other than this one, so treat any estimate you are given for it, including from me, as a guess.

---

## What it does not do

- It does not remove the need for a human to review and publish. The rules forbid the agent from writing to your git history at all: it prepares a commit-ready state, hands over the exact commands, and refuses to run them — including when asked directly, and including when the commands are pasted in for it. That is a rule with a written refusal script, not a permissions boundary; if you want it to be a permissions boundary, that is your repository's settings, and you should set them.
- It does not decide business rules. When the model reaches a hole that engineering cannot close — what happens to a refund after a partial delivery, who may override a lock — it stops and asks a person, rather than letting the code invent a silent policy. That is the intended behaviour, and it means you will be asked questions.
- It does not make an inexperienced operator into a senior engineer. It makes the *process* a senior engineer would follow non-optional, which is a different and more transferable thing.

---

## How to check any of this

Nothing here asks to be taken on trust:

- **[`CASE_STUDIES.md`](./CASE_STUDIES.md)** — the three systems in detail: stack, scale, and which specific defect class each one is responsible for turning into a permanent rule.
- **[`.cursorrules`](./.cursorrules)** — all 44 laws, in full, with the precedence ladder that decides between them. It is a text file; you can read the actual rule instead of a description of it.
- **[`roles/SYSTEM_UPGRADE_MANIFEST.md`](./roles/SYSTEM_UPGRADE_MANIFEST.md)** — the amendment history. Read the entry on Law 5 in particular: it records two rewrites of that law that were written, tried and **thrown away**, and why each one was wrong. A changelog that only lists wins is a marketing document; that entry is the fastest way to judge which kind this is.
- **[`README.md`](./README.md)** and **[`ARCHITECTURE.md`](./ARCHITECTURE.md)** — the mechanism, for whoever you forward this to.

---

## What is not on this page

A rate, a start date, and what the first two weeks of an engagement look like. Those depend on what you are building and they belong in a conversation, not in a document that cannot ask you a question.

---

**Alexandr Zaporozhan** — author of LEO, and the one person who operates it. [LinkedIn](https://www.linkedin.com/in/alex-zaporozhan/)

Engagements are contracted through **LEAD ENGINEERING ORCHESTRATION S.R.L.** (Moldova).
