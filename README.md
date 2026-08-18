<div align="center">

# LEO
### Lead Engineering Orchestrator

**A Context-Driven Agentic SDLC Framework.**
A written constitution that turns a general-purpose LLM coding agent into a disciplined engineering organization — with a Tech Lead, 22 specialist roles, 41 codified engineering laws, and a gate protocol that refuses to let "it works on my machine" pass for "done."

[![License: PolyForm Shield 1.0.0](https://img.shields.io/badge/license-PolyForm%20Shield%201.0.0-blue)](./LICENSE)
[![Status: Production-tested](https://img.shields.io/badge/status-production--tested-brightgreen)](./CASE_STUDIES.md)
[![Roles](https://img.shields.io/badge/roles-22-orange)](./ARCHITECTURE.md)
[![Laws](https://img.shields.io/badge/absolute%20laws-41-red)](#3-forty-one-laws-forged-from-incidents-not-opinions)
[![Agent-agnostic](https://img.shields.io/badge/works%20with-Cursor%20%C2%B7%20Claude%20Code%C2%B7%20Windsurf%20%C2%B7%20Copilot-black)](#compatibility)

[What it is](#what-leo-actually-is) · [Why it exists](#why-this-exists) · [How it works](#how-it-works) · [Proof at scale](#proof-at-scale) · [Get started](#get-started) · [The Manifesto](./MANIFESTO.md) · [License](#license)

</div>

---

## TL;DR

LEO is not a Python package and there is nothing to `pip install`. It is a **rule system**: a `.cursorrules` constitution plus a **127-file, ~26,000-line, ~254,000-word role library** (`roles/*.md`, including 5 niche-bootstrap packages under `roles/niches/`) that you hand to a coding agent — Cursor, Claude Code, Windsurf, or anything else with file-system/tool access that reads a system-prompt / project-rules file — instead of a one-line "you are a helpful senior engineer" prompt.

Where a raw LLM agent free-improvises architecture, skips edge cases under time pressure, and silently forgets a decision it made 40 messages ago, LEO gives it:

- **A single entry point** (`@LEAD`) that routes every request to the right specialist instead of one model trying to be architect, developer, and QA simultaneously in the same breath.
- **22 specialist roles** with narrow, named jurisdictions — `@ARCH`, `@DEV`, `@PRINCIPLE`, `@QA_ARCH`, `@QA_VISUAL`, `@PENTEST`, `@SEO`, `@DESIGN`, `@AI_ENGINEER`, and 13 more — so "who decides this" is never a coin flip.
- **41 Absolute Laws** distilled from real production incidents (double-booked appointments, zombie Celery workers, leaked UUIDs in a UI, a `Promise.all` that silently ate an error) — so the same class of bug cannot recur, because the rule that would have caught it is now permanent.
- **A gate protocol (GATE-0 → GATE-6)** that blocks the chain from advancing without a concrete artifact as proof — never on the agent's word alone.
- **Artifacts instead of chat history** — every architectural decision, security threat model, and QA verdict lives in a versioned markdown file the *next* agent session reads before doing anything, closing the single biggest failure mode of long-running agentic work: **context drift**.

LEO has been directing real, shipped engineering work — not toy demos — across a multi-tenant healthcare SaaS, an AI training platform with RAG and executable agent graphs, and a public-facing marketing + CMS platform. See [Proof at scale](#proof-at-scale).

---

## Why this exists

Autonomous coding agents fail in a very specific, very boring way. Not by writing bad syntax — modern LLMs write syntactically fine code all day. They fail by:

- **Forgetting the decision they made an hour ago** and quietly re-deciding it differently three files later (context drift).
- **Skipping the boring 20%** — the empty state, the error contract, the race condition, the timeout on the outgoing call — because nothing in the prompt made skipping it *expensive*.
- **Hallucinating confidence.** "Most likely implemented," "should work now," "practically done" — phrases that mean *I did not check*, delivered with the same tone as a verified fact.
- **Never being told no.** A single-agent chat has no adversary, no auditor, no separate pair of eyes — so a hole in the logic ships exactly as fast as the happy path does.

None of this is a model-capability problem. It is a **process** problem — the same one software engineering solved decades ago with code review, QA, and architecture sign-off, and then re-broke the moment "just ask the AI" became a viable way to skip all three.

LEO is that process, written down as a constitution the agent cannot talk itself out of, because it is loaded as its operating rules, not as a suggestion in a chat bubble.

---

## What LEO actually is

| LEO is | LEO is not |
|---|---|
| A **rules + role-prompt library** your coding agent loads as its system prompt / project rules | A Python/Node package, a CLI, or a hosted service |
| A **process framework** — in the sense that Scrum, TOGAF, or the C4 model are frameworks: a way of organizing work, not a runtime you execute | A finished product, an IDE plugin, or "AutoGPT with extra steps" |
| **Agent-agnostic.** The constitution (`.cursorrules`) works as a system prompt anywhere; the full on-demand role library needs an agent with file-read/tool access (Cursor, Claude Code, Windsurf, Copilot Workspace agent mode) — see [Compatibility](#compatibility) | Tied to one vendor or one model |
| **Opinionated on purpose.** 41 Laws, not 4 — because "use your judgment" is exactly the instruction that produces context drift at scale | A generic "be a good assistant" prompt |
| **Text you read, edit, and own.** Every rule is a markdown file in this repo. You can delete a canon you don't need in five minutes | A black box, a fine-tuned model, or a magic prompt nobody can inspect |

If you came here expecting `npm install leo-orchestrator`, you'll be disappointed. If you came here because your AI pair programmer keeps forgetting what it decided two files ago and shipping a UUID in the UI, keep reading.

---

## How it works

### 1. One entry point, not one model wearing every hat

`@LEAD` is the Tech Lead. It never writes code. It reads the request, decides which specialist owns it, and — critically — runs a **Pre-Plan Gate** and a **Leverage Point Analysis** *before* anything gets built, so architecture-scale decisions don't get made accidentally inside a "quick fix."

### 2. Twenty-two roles with a real jurisdiction, not a personality

| Role | Owns | Typical veto power |
|---|---|---|
| `@ARCH` | Stack, DB, API contracts, the architecture spine | No epic reaches `@DEV` without 12 numbered architectural decisions on record |
| `@PRINCIPLE` | Invariants, state reachability, causality, concurrency | Blocks a feature that is *technically buildable* but *logically unsound* (e.g., a state the domain should never allow) |
| `@DEV` | The only role allowed to write code | Can raise a **MODEL BLOCKER** — refuses to type over a hole in the spec instead of guessing |
| `@QA_ARCH` | Business-logic audit: state matrix, UUID-in-UI, error contract, async safety | Nothing reaches release without a 🟢 verdict here first |
| `@QA_VISUAL` | Renders the UI and *measures* it — overflow, layout shift, hover states — under adversarial content | Geometry claims are verified by render, never by reading code and hoping |
| `@QA` | The final risk-tiered reliability floor (T0–T3) and negative-path baseline | Nothing reaches `@SEC`/`@PENTEST`'s S-Wave gate without this pass first |
| `@PENTEST` | Adversarial security — a **blocking** gate, peer to QA, not an afterthought | Any 🔴 finding stops the deploy; risk-acceptance requires a named human owner |
| `@FRONTEND`, `@SEO`, `@DESIGN`, `@MOTION`, `@AI_ENGINEER`, `@MEDIA_ENGINEER` | Capability mapping, search visibility, UI craft, motion/interaction, RAG & agent graphs, generative media | Each owns a mandatory artifact before the adjacent role can proceed |
| `@BIZ`, `@DOMAIN_EXPERT`, `@CREATOR` | Market fit, domain routes, product vision | Gate the chain before any code gets written on an unvalidated idea |
| `@SEC`, `@AUDITOR`, `@PERF`, `@OPS`, `@LAWYER`, `@SCRIBE` | Advisory security, root-cause diagnosis, profiling, deploy, legal, documentation | Called on trigger, not on a fixed schedule |

Full map, jurisdictions, and hand-off contracts: [`ARCHITECTURE.md`](./ARCHITECTURE.md).

### 3. Forty-one laws, forged from incidents, not opinions

A sample — the full list lives in [`.cursorrules`](./.cursorrules):

- **Law 8 — No UUIDs in the UI.** Displayed names are always resolved. A raw `entity_id` on screen is a 🔴 blocker.
- **Law 11 — Async safety.** Every `async` block has a real `try/catch` with structured logging; `Promise.all` with mutations is banned in favor of `Promise.allSettled` + a per-result status check. A forgotten `await` is treated as "a silent bomb," not a style nit.
- **Law 12 — Fact or an admission of not knowing.** "Probably implemented," "most likely present" are banned phrases. The only allowed answers are *"Verified: [file, line, evidence]"* or *"Could not determine: [reason]."*
- **Law 27 — License purity.** Every dependency is checked against an allowlist before it ships; GPL/AGPL/SSPL/unknown-license is a blocking 🔴, no exceptions, no "we'll swap it later."
- **Law 32 — Integrity under concurrency.** "No double-booking," "no overselling," "pay once" are protected at the database level (unique constraints, `SELECT FOR UPDATE`, idempotency keys) — an `if` check in application code is explicitly declared *not* protection.
- **Law 35 — The database has time too.** `lock_timeout`, `idle_in_transaction_session_timeout`, and `statement_timeout` are numbers in the architecture spine, not vibes. *"If your Cancel button can queue behind the thing it is cancelling, you do not have a Cancel button."*
- **Law 38 — Security is a gate, not a phase.** A threat model is written *before* the first line of code touching a security-relevant surface, not audited in afterward.
- **Law 40 — The human publish gate.** The agent prepares everything up to a commit-ready state and **never** runs `git commit`, `git push`, or `git merge` — not even if the user pastes the exact commands and asks twice. Publishing history is a human action, always.
- **Law 41 — Production-readiness by default.** *"MVP" is a delivery schedule, never a quality bar.* The foundation — data model, RBAC, security surface, money routes — is designed for the whole product on day one; only *features* ship in waves on top of it.

These laws did not come from a whiteboard. Several were written the week a specific defect happened in production — a Celery worker held a slot open past its lease, a rate limiter let a retry storm through, a race condition double-booked a clinic appointment slot. Each incident became a permanent, greppable rule instead of a lesson someone had to remember. That upgrade trail is preserved in `roles/SYSTEM_UPGRADE_MANIFEST.md`.

### 4. Artifacts, not vibes — "state, not history"

Every role writes to a file, not just to the chat. `docs/artifacts/SAAS_ARCHITECTURE_SPINE_2026.md`, `QA_REPORT_*.md`, `PRINCIPLE_FINDINGS_*.md`, `ARCH_SPINE_*.md` — these are the actual interface between roles. A new agent session (or a different model entirely) reads the artifact, not 40 pages of scrollback, and picks up exactly where the last one stopped. This is the single mechanism that makes long-horizon agentic work survive a context window.

### 5. Gates, not steps

```mermaid
flowchart TD
    A["Task"] --> B["@LEAD\nPre-Plan Gate + LPA"]
    B --> C["@ARCH / @FRONTEND\nSpine draft"]
    C --> D{"Model / AI /\nSecurity triggers?"}
    D -->|"yes"| E["@PRINCIPLE · @AI_ENGINEER · @PENTEST S-0"]
    D -->|"no"| F["DEV_PROMPTS finalized"]
    E --> F
    F --> G["@DEV\nexecutes to-dos, writes code"]
    G --> H["@QA_ARCH\nbusiness-logic audit"]
    H -->|"red flag"| G
    H -->|"green + UI"| I["@QA_VISUAL\nrender & measure"]
    I -->|"red flag"| G
    H -->|"green, no UI"| J["@QA"]
    I -->|"green"| J
    J --> K["@SEC + @PENTEST S-Wave\n+ @SEO TECH"]
    K -->|"red flag"| G
    K -->|"green"| L["Human reviews\nHuman publishes\n(Law 40)"]
```

A phase transition is never "the agent said it's done." Every gate needs a file as proof. `roles/LEAD_ANTI_CHECKBOX_PROTOCOL.md` exists specifically to catch the agent asserting completion without evidence.

### 6. The system can evolve — but only a human pulls the trigger

`@EVOLVE` lets the system amend its own rules after a real incident — but **only on an explicit human command**, never automatically. No repeated failure, no clever idea, silently rewrites a role. This is intentional: a self-modifying agent constitution without a human hand on the amendment process is exactly the failure mode LEO exists to prevent.

---

## Proof at scale

LEO is not a thought experiment. It has directed real, shipped engineering work across three systems of meaningfully different shape — a regulated multi-tenant SaaS, an AI/agent platform, and a public marketing + CMS platform. Full write-up with stack, scale, and what LEO's gates actually caught: **[`CASE_STUDIES.md`](./CASE_STUDIES.md)**.

| | MedCore | Enterprise AI Training Platform | Public Education Platform |
|---|---|---|---|
| **Class** | Multi-tenant B2B clinic OS | AI content/agent SaaS | Public site + CMS |
| **Backend** | FastAPI, SQLAlchemy 2 async, PostgreSQL 16, Celery/Redis | FastAPI, SQLAlchemy 2 async, PostgreSQL + pgvector, LangGraph agent graphs, Celery/Redis | FastAPI, SQLAlchemy 2 async, PostgreSQL 16, Valkey |
| **Frontend** | React 18, Vite, Mantine, TanStack Query | React 18, node-graph pipeline builder (XYFlow-class), TanStack Query | Next.js 15 (SSR/SSG), React admin SPA |
| **Notable engineering** | Tenant isolation, advisory locks, transactional outbox | RAG (pgvector), executable agent graphs, generative-media pipeline | SEO-gated SSR, licensed-content compliance, WCAG AA |
| **Test surface** | 187 test modules (pytest + Playwright) | 300+ test modules (pytest + frontend harnesses) | Full ADR + QA_REPORT + PENTEST audit trail |
| **Status** | Shipped; source-available (PolyForm Shield), going public at [github.com/alex-zaporozhan/medCore](https://github.com/alex-zaporozhan/medCore) | Client engagement (NDA — architecture disclosed, business logic withheld) | Shipped |

These aren't demo apps. They are the reason most of LEO's 41 Laws exist in the first place — each one is a scar from something that actually broke.

---

## Get started

LEO is a file, not a build step.

1. **Copy the constitution.** Drop [`.cursorrules`](./.cursorrules) into the root of your project (or translate it to `CLAUDE.md`/`AGENTS.md` if your agent uses that convention).
2. **Copy the role library.** Copy `roles/` alongside it. Your agent reads these on demand — they are not all loaded into context at once; `@LEAD` routes to the specific file a task needs. Twelve of these files (`TPF_MASTER.md`, `TPF_MODULE_*.md`) are a real, filled-in reference passport from one shipped admin panel, not a generic template — they say so in their own first line, and you can safely delete them if you don't want dental-SaaS-flavored UI examples in an unrelated project.
3. **Seed the artifact skeleton.** Copy the (empty, `.gitkeep`-only) `docs/` tree — `docs/artifacts/`, `docs/product_state/`, `docs/decisions/` — so the roles have somewhere to write.
4. **Talk to `@LEAD` first.** Open your agent, address `@LEAD`, and describe the task. Let it route.
5. **Trim to your stack.** LEO ships with canons for a specific opinionated stack (Python/FastAPI, PostgreSQL, React/TypeScript, Celery/Redis) because *specificity is what makes a rule enforceable*. Swap the stack-specific canons (`roles/STACK_SELECTION.md`, `roles/DATA_STORE_SELECTION.md`, `roles/TEMPLATE_ADMIN_UI_UX.md`, …) for your own; keep the *process* canons (gates, laws, artifact contracts) as-is.

**Minimum viable adoption:** even using just `.cursorrules` (the 41 Laws + Chain Protocol) without the full 127-file role library already fixes the most common agentic-coding failure modes — context drift and unearned confidence.

### Compatibility

LEO has no dependency on any specific vendor, but the two tiers below need to be kept distinct — the full system assumes the agent can read files on its own:

- **Tier 1 — any chat model, no tool access.** Paste `.cursorrules` into the system prompt. You get the 41 Laws and the role map as reference text the model reasons from. It cannot fetch a specific `roles/*.md` canon on demand, because it has no file-system access — but this alone already fixes the "hallucinated confidence" and "no adversary" failure modes.
- **Tier 2 — an agent with file-read / tool-use access** (Cursor, Claude Code, Windsurf, Copilot Workspace agent mode, or a custom harness wired to a file-read tool). This is the reference setup: `.cursorrules` is always loaded, and the agent opens the specific `roles/*.md` file a task needs, exactly the way this repository's own documentation was produced. Without tool access, "on-demand loading of the role library" is not something a plain system prompt can do by itself.

---

## Repository structure

```
LEO/
├── .cursorrules                    # The constitution: 41 Absolute Laws, role map, chain protocol
├── README.md                       # You are here
├── ARCHITECTURE.md                 # Deep dive: role jurisdictions, gate protocol, artifact layers
├── CASE_STUDIES.md                 # Real shipped systems built under LEO
├── MANIFESTO.md                    # Long-form: why context drift kills agentic dev, and how LEO stops it
├── LICENSE                         # PolyForm Shield 1.0.0 (source-available)
├── LICENSING.md                    # Why this license, in plain language, with a comparison table
├── roles/                          # 121 top-level files + niches/ — 127 files total, the role library
│   ├── ROLE_LEAD.md                #   The orchestrator: routing, gates, REFLEX, TRANSMISSION PROTOCOL
│   ├── ROLE_ARCH.md ROLE_DEV.md …  #   One constitution per specialist role
│   ├── SECURITY_GATE_PROTOCOL.md   #   S-0 / S-Wave / S-Global adversarial gates
│   ├── DATA_INTEGRITY_CANON.md     #   Race conditions, idempotency, money-as-integers
│   ├── DATABASE_RUNTIME_CANON.md   #   Lock discipline, connection budgets, the "corpse-lock" pattern
│   ├── ASYNC_WORKERS_CANON.md      #   Queue design, lease clocks, retry ownership
│   ├── ARCH_SPINE_PROTOCOL.md      #   The 12-vertebra architectural decision record
│   ├── SYSTEM_EVOLUTION_PROTOCOL.md#   The @EVOLVE command — how rules may change (human-gated)
│   ├── SYSTEM_UPGRADE_MANIFEST.md  #   The changelog of every rule the system learned the hard way
│   ├── niches/                     #   5 niche-bootstrap packages (CRM/ERP, marketplace, mobile-consumer,
│   │                               #   content/social, AI-assistant) selected once at project start
│   ├── TPF_MASTER.md, TPF_MODULE_*.md #12 files — a filled-in reference passport from one real admin
│   │                               #   panel (MedCore), self-labeled "project example, not a universal
│   │                               #   canon" in their own header; skip these on an unrelated project
│   └── … (visual craft, motion, SEO, RAG/agent-graph, testing, PENTEST scenarios, …)
└── docs/                          # Empty skeleton — artifacts/ · product_state/ · decisions/ · knowledge/ ·
                                    # execution/ · archive/ · commercial/ · operations/
    └── */.gitkeep                 #   Populated by the roles once you start using LEO on a real project
```

---

## FAQ

**Is this "vibe coding with extra Markdown"?**
No — the entire point is the opposite. Vibe coding is "describe what you want, accept what comes back." LEO forces every non-trivial decision through a named owner, a written artifact, and a gate that a *different* pass of the agent (or a human) has to actually check. The 41 Laws exist because the boring, unglamorous 20% of software (error contracts, race conditions, empty states) is exactly what an unconstrained agent skips first.

**Do I need all 127 files?**
No. Start with `.cursorrules` alone. Add canons as you hit the problem they solve. The library is intentionally modular — `roles/CONFLICT_REGISTRY.md` exists precisely to keep a large rule set internally consistent as it grows.

**Does this only work for Python/FastAPI/React shops?**
The *process* (roles, gates, laws, artifact contracts) is stack-agnostic. The *canons* (`STACK_SELECTION.md`, `DATA_STORE_SELECTION.md`, the admin/design templates) are opinionated toward the author's production stack on purpose — an enforceable rule has to be specific. Swap them for your own stack's equivalents; keep the skeleton.

**Is LEO "open source"?**
Not in the strict OSI sense — see [License](#license) below. It is free to read, use, modify, and build on for essentially any purpose, including commercial software you build with it. What you may not do is repackage and sell LEO itself (or a directly competing framework built from it) as a product.

---

## License

**Source-available**, not OSI Open Source. SPDX identifier: [`LicenseRef-PolyForm-Shield-1.0.0`](./LICENSE) — Shield is not (yet) on the official SPDX license list, unlike its Noncommercial/Strict/Small-Business siblings, hence the `LicenseRef-` prefix.

- You **may** read, run, copy, modify, and use LEO for any purpose — including inside a commercial company, on paid client work, or as the process backbone of your own product. That is the overwhelming majority of what anyone wants to do with it, and it costs you nothing and requires no permission.
- You **may not** use LEO to offer a competing product — a paid framework, hosted service, course, or template pack that is a practical substitute for LEO itself. That business stays with **Alexandr Zaporozhan**.

Full reasoning, a comparison against MIT / Apache-2.0 / CC-BY-NC-SA / PolyForm-Noncommercial, and how to request a commercial exception: **[`LICENSING.md`](./LICENSING.md)**.

---

## About the author

**Alexandr Zaporozhan** — AI-Native Systems Engineer and solo builder bridging computer-science fundamentals with high-velocity agentic orchestration.

- 5 years in Emergency ICU — zero-error tolerance, algorithmic discipline, and rapid crisis execution, carried directly into how LEO treats a production defect (a permanent rule, not a one-off fix).
- A rigorous CS foundation (algorithms & data structures in C++, OOP design, Java Core) built specifically because production SDLC cannot be learned from toy katas.
- Facing the junior-hiring freeze, instead of waiting for a team, engineered one: LEO directs 22 specialized agent roles through 41 codified engineering laws and a closed-loop verification chain — from S-0 threat modeling to Playwright visual-render tests and adversarial security checks.
- Directed 14B+ tokens across iterative verification loops in real client work and independent products, shipping a multi-tenant healthcare SaaS with row-level security and 800+ tests, an AI platform with executable LangGraph agent graphs and pgvector RAG, and a custom high-throughput CMS/PWA.
- **Core stack:** Python 3.11 (FastAPI, SQLAlchemy 2 async, Alembic) · PostgreSQL 16 (pgvector, RLS) · Celery · Redis/Valkey · TypeScript, React 18, Vite, TanStack Query, Mantine, Tailwind · Agentic orchestration, context engineering, LangGraph, RAG, spec-driven development.

Open to Founding Engineer roles, AI-native full-stack positions, and strategic AI-SDLC architecture contracts.

[LinkedIn — Alexandr Zaporozhan](https://www.linkedin.com/in/alex-zaporozhan/)

---

<div align="center">

*If your agent just shipped a UUID to a production UI, you needed this yesterday.*

[Read the full manifesto →](./MANIFESTO.md)

</div>
