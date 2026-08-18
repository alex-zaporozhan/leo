# Licensing — the reasoning, in plain language

This document exists because the license choice here is deliberate, and the goal it serves is a specific, narrow one: **maximum freedom to use, minimum freedom to resell.** This page explains why that goal does not map onto "MIT" or "open source" the way it might sound like it should, what license LEO actually uses, and what that means for you concretely.

## The goal, stated precisely

> People should be able to use this freely — including at work, including commercially, including as the backbone of a product they charge money for. What they should **not** be able to do is take LEO itself and sell it: package it as a paid template, a paid course that is substantially LEO, or a competing hosted "agentic SDLC" product.

That is a real, coherent goal. It is just not the goal the term "open source" was built to describe.

## Why this isn't "open source" in the strict sense

The Open Source Definition (maintained by the OSI) has a specific clause — **field-of-use neutrality** — that bans exactly this kind of restriction. A license that says "you can't use this to compete with me" is not OSI-approved, by construction, regardless of how permissive it is otherwise. MIT, Apache-2.0, and the GPL family all satisfy field-of-use neutrality; that is *why* Red Hat is allowed to sell Linux support contracts, and why anyone could legally fork MIT-licensed code into a paid competitor tomorrow.

**Linux is the wrong analogy for what you're describing.** The GPL explicitly permits commercial resale — its only real teeth are *copyleft* (if you distribute modified code, you must share the modification under GPL too). It has no "you can't sell this" clause at all. If what you actually want is "Linux-style," that means: free to use and resell, just can't make derivatives proprietary. That is **not** what you asked for.

What you described — free to use, not free to resell as a competing product — has an established name in licensing circles: **"source-available"** or **"fair source,"** not open source. This is not a lesser or lesser-known category; it's the same category MedCore's own `LICENSE` already uses (PolyForm Shield), and the same category several well-known companies have used for exactly this reason: Sentry and CockroachDB ship under the Business Source License; Elastic ran on a source-available license (SSPL / Elastic License v2) from 2021 until it added AGPLv3 back in 2024; HashiCorp moved Terraform from the open-source MPL to the source-available BSL in 2023 — the move that prompted the community to fork the last MPL version into OpenTofu.

Calling LEO "open source" on the README while shipping a noncompete clause would be a real, checkable misrepresentation the moment a lawyer or a pedantic commenter reads the LICENSE file. LEO's own `README.md` says **"source-available, not OSI Open Source"** for exactly this reason — precision here costs nothing and prevents an avoidable credibility hit.

## The options actually on the table

| License | Free personal/hobby use | Free **commercial** use (build your own product with it) | Can be **resold as-is / repackaged** | OSI "Open Source" | Fits the stated goal? |
|---|---|---|---|---|---|
| **MIT / Apache-2.0** | ✅ | ✅ | ✅ — no restriction at all | ✅ | ❌ — explicitly allows the one thing you want to prevent |
| **GPL-3.0** | ✅ | ✅ | ✅ (with copyleft: derivative source must be shared) | ✅ | ❌ — same problem; resale is legal, only the *source* is protected, not the *business* |
| **CC BY-NC-SA 4.0** | ✅ | ❌ — "noncommercial" bans using it to build *your own* commercial software too, which is the opposite of what you want | ❌ | ❌ | ❌ — too restrictive on legitimate use |
| **PolyForm Noncommercial 1.0.0** | ✅ | ❌ — same problem as CC-NC: a for-profit company cannot use it at all without a separate commercial license | ❌ | ❌ | ❌ — blocks the exact "people should freely use it at work" case you want to enable |
| **PolyForm Shield 1.0.0** ← **chosen** | ✅ | ✅ — any use is permitted *except* competing with LEO itself | ❌ — the one carve-out is precisely "don't resell/repackage this as a competing product" | ❌ (source-available) | ✅ — matches the goal exactly |

The two most "obvious"-sounding choices — a Creative-Commons NonCommercial variant, or PolyForm's own Noncommercial license — actually fail the goal, because their "noncommercial" clause is broader than it sounds: it does not mean *"you can't resell this file"*, it means *"you (or your employer) cannot use this at all if the use has any commercial purpose."* That would stop a startup, a freelancer, or literally any for-profit company from adopting LEO for their own internal engineering process — the exact adoption you're trying to encourage.

**PolyForm Shield** solves this with a different, narrower mechanism: a **noncompete** clause instead of a **noncommercial** clause. Its text is blunt about it: *"Any purpose is a permitted purpose, except for providing any product that competes with the software."* That is: use it for anything, including running a commercial business — just don't turn around and sell LEO itself, or a rebrand of it, as a rival product.

## Why this is also the *consistent* choice

MedCore — one of the three real systems built under LEO (see [`CASE_STUDIES.md`](./CASE_STUDIES.md)) — already ships under **PolyForm Shield 1.0.0**. Licensing LEO the same way means:

- One license family across the author's public engineering portfolio, not a new legal instrument invented per repository.
- A track record: this is not a hypothetical license someone picked off a list — it is already running in production on a shipped product.

## What this means for you, concretely

**You may, without asking anyone:**
- Clone this repo, read every file, and use `.cursorrules` + `roles/` on your own projects — personal, freelance, or inside a company.
- Modify any rule, delete canons you don't need, translate it, extend it with your own laws.
- Use LEO as the process backbone for software you sell — a SaaS, a client project, an internal tool at your employer. That software is yours; nothing here reaches into its IP.
- Publish a blog post, a talk, or a case study about using LEO, including critical ones.

**You may not, without a separate agreement:**
- Package LEO's rules/roles (verbatim or lightly reskinned) and sell it as your own framework, template pack, or paid course.
- Launch a hosted/paid service whose product **is** "run your coding agent through this rule system," positioned as a substitute for LEO.

**If you want to do one of the "may not" things** — for example, white-labeling LEO for an internal enterprise rollout, or building a commercial product *around* LEO with the author's involvement — that's a conversation, not a rejection. Open an issue or reach out via [LinkedIn](https://www.linkedin.com/in/alex-zaporozhan/).

## SPDX / machine-readable identifier

```
LicenseRef-PolyForm-Shield-1.0.0
```

Full license text: [`LICENSE`](./LICENSE). Canonical upstream text: <https://polyformproject.org/licenses/shield/1.0.0>.
