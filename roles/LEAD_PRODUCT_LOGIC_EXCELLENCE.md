# Product Logic Reference (business coherence)

> **Path:** `docs/LEAD_PRODUCT_LOGIC_EXCELLENCE.md`  
> **Role:** @CREATOR (package at start) · @DOMAIN_EXPERT (process map) · @LEAD (acceptance "does it make sense") · @QA_ARCH (verification of screen–process alignment).  
> **Does not replace:** `DOMAIN_STANDARDS.md` (page types), `LEAD_PRODUCT_GATE_PROTOCOL.md` (gates), `ROLE_QA_ARCH.md` (audit vectors). **Supplements:** the answer to the question *"why does this screen exist in the real clinic process and what must the operator accomplish in 30 seconds"*.

---

## 1. Problem this file solves

A machine does modals, grids, and "features" well. It does **not** do well independently seeing the **process**: who is a participant, in what order decisions are made, which actions are mandatory on any given day and which are a rare edge case. Without explicit logic the result is a product of "many buttons, little money": decorative UI, dead actions, missing critical steps.

This document is a **logic reference template**: how to record the process so that a screen and code can be checked against it, not evaluated by "like/dislike".

---

## 2. "Module logic" artifact (minimum)

For each significant module (or group of screens) in `docs/artifacts/` — a separate file or section in `BUSINESS_ROUTES.md`:

| Block | Content |
|-------|---------|
| **Role in the process** | Which staff member uses it (administrator, doctor, owner). One primary role per screen. |
| **Trigger** | What happened in reality before the person opened the screen (a call, no-show, debt, incoming message). |
| **Session goal** | One sentence: what must be **true** after the screen is closed (booking created, status agreed, reply sent). |
| **Mandatory chain** | 3–7 steps in order: what **cannot be skipped** for a legally/commercially significant result. |
| **Quick actions** | What must be accessible **without extra clicks** (shortcuts, primary CTA, context menu). |
| **Deliberate "noes"** | What we **do not do** on this screen (to avoid bloating the UI); if a button exists — it either leads to a result or is removed. |

Without this block @LEAD does not consider the module aligned with the business — regardless of passing technical gates.

---

## 3. "Dead buttons vs gaps" audit

Conducted by @QA_ARCH or @LEAD on the ready UI (after GATE-2):

| Question | FAIL if |
|----------|---------|
| Does every visible button/menu item lead to a completed scenario with a backend? | There is a stub, 501, empty response, "coming soon" without a @LEAD-agreed stub note |
| Is every step from §2 "mandatory chain" reachable from this screen or an explicit parent? | Requires a workaround through a developer or console |
| Number of modal windows per one scenario | More than two without justification in ARCH — a smell of "theatre" |
| Duplicate contours (two "chats", two schedules) | No source of truth in `ARCH_*.md` |

Result — table in `QA_REPORT` or a separate sub-item: **Logic coherence: 🟢 / 🟡 / 🔴**.

---

## 4. Connection with @CREATOR

@CREATOR at start is required not only to compile MARKET_AUDIT and BUSINESS_ROUTES, but also to **draft the first logic reference** for one critical journey (booking / payment / communication — whichever is declared the differentiator): §2 table filled for at least one module. Otherwise the subsequent UI inevitably becomes a set of screens without a process.

---

## 5. Connection with gates

- **GATE-1:** in `ARCH_*.md` or `DEV_PROMPTS` — reference to the logic section in `BUSINESS_ROUTES.md` / module reference.  
- **GATE-3:** @QA_ARCH checks §3 of this file where applicable.  
- **GATE-6:** on L-assessment "expectation deception" (UI without process) — P0 in the "Product" layer, see `docs/LEAD_PRODUCT_GATE_PROTOCOL.md` (GATE-6).

---

## 6. Reference for insertion in ROLE_LEAD / prompt

```
When designing or auditing a screen: cross-reference docs/LEAD_PRODUCT_LOGIC_EXCELLENCE.md — process, role, mandatory chain, dead buttons.
```
