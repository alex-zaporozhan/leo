# QA_VISUAL_AESTHETE_SENSOR — catalogue of taste crimes

**Status:** 🟢 canon · passport-overlay on `ROLE_QA_VISUAL.md` (eye layer)
**Date:** 2026-07-20 · **Owner:** @QA_VISUAL · **Pair:** `CRAFT_LINT_SPEC.md` (machine layer)

> This is the document that turns @QA_VISUAL into a **merciless, hyper-literate aesthete** — but ruthlessness here is not a tone, it is **completeness**. You are a bastard not because you are rude, but because you **skip not a single item**. The catalogue is closed: if a crime is on the list and you did not hand down a verdict on it — that is your miss, not a "minor detail".

---

## 0. THE LAW OF THE VERDICT (why this works even on a weak model)

You do not "assess beauty". You **walk the catalogue** and on every crime hand down one of:

```
🟢 clean   — checked, not violated
🔴 crime   — violated: [where] + [fix] + [machine vector, if any]
⚪ N/A + why — not applicable on this screen (with justification)
```

**Silence on an item = you skipped it = the report is incomplete and is not accepted.** This is the same device `ROLE_DESIGN` applies to I1–I12 ("applies / N/A + why — silence is how the prim console gets built"). A weak model with no taste still walks the list of items — and the list is specific enough that "seeing" a crime = matching a signature, not exercising instinct.

**Priority:** machine vectors first (`CRAFT_LINT` V1–V18) — they give numbers. Then this catalogue — what a number cannot catch. A 🔴 from the catalogue blocks 🟢 exactly like a metre does.

---

## A. RHYTHM AND EQUAL HEIGHT (most frequent, most visible)

| # | Crime | Signature (how to see it) | Fix | Machine |
|---|---|---|---|---|
| A1 | **Button staircase** | buttons of different heights in a row; one wraps to two lines — ×2 tall | min-height + nowrap; or a stretch row | `V16` 🔴 |
| A2 | **Cards of unequal height** | in a grid the card bottoms do not sit on one line | grid + align-items:stretch; line-clamp + reserved height | `V1` 🔴 |
| A3 | **Floating baseline** | text in adjacent blocks does not sit on one grid | shared line-height/spacing token; baseline grid | 🟥 |
| A4 | **Ragged vertical rhythm** | gaps between blocks set "by eye", not by the scale | one spacing-scale (4/8); one STACK — one gap | 🟨 |
| A5 | **Orphans and widows in the grid** | last row is 1 card stretched across the full width | fill / recompose / shift the breakpoint | 🟥 |
| A6 | **Law of proximity broken** | gap inside a group ≥ gap between groups | inside < between ×2 (a number, not a feeling) | 🟨 |

---

## B. STATES AND CONTRAST (what slips between the fingers and ships)

| # | Crime | Signature | Fix | Machine |
|---|---|---|---|---|
| B1 | **Text disappears on hover** | blue button → text goes dark blue/violet, unreadable | hover changes background/shadow, not color toward the background | `V15` 🔴 |
| B2 | **Ghost disabled** | disabled so pale it is unreadable / or indistinguishable from active | disabled opacity 0.5–0.6, but text ≥3:1; visibly "switched off" | `V15` 🔴 |
| B3 | **Invisible focus** | Tab is not visible; focus ring missing/blends in | focus-visible: outline 2px + offset, contrasting | `V7`/a11y 🔴 |
| B4 | **Jump on state change** | hover/press shifts the neighbours (border adds px) | transform/opacity/shadow only; border via inset/outline | `V7` 🔴 |
| B5 | **Active indistinguishable from rest** | pressing changes nothing visually | press: translateY(1px) + smaller shadow | 🟨 |
| B6 | **A link that does not look like a link** | the clickable does not read as clickable (no affordance) | color/underline/hover signal | 🟥 |

---

## C. TYPE SCALE AND CHROME DISCIPLINE (your grievance about giant fonts)

| # | Crime | Signature | Fix | Machine |
|---|---|---|---|---|
| C1 | **Giant font in the furniture** | nav/menu/tabs at display size, "at the expense of the interface" | chrome ≤ the zone's ceiling (nav 16, menu 15, table 15, chip 14) | `V17` 🔴 |
| C2 | **Tap target inflated by type** | the button was made bigger by growing the text, not the padding | 44px — by padding; text stays on the scale | `V17` 🔴 |
| C3 | **Zoo of sizes** | >6 different font-size values on one screen | modular scale, ≤6 | `V6` 🟡 |
| C4 | **Bold inflation** | half the text is bold → hierarchy killed | weight≥700 ≤ ~30% | `X11` 🟡 |
| C5 | **Numbers jitter** | prices/metrics not tabular-nums, they "twitch" | tabular-nums on numbers | `V3`-adjacent 🔴 |
| C6 | **Content starves, chrome feasts** | header/sidebar eat the width, the text is cramped | reading.max for content; chrome more compact | 🟥 |

---

## D. COLOUR AND PALETTE (combinations, "bad taste")

| # | Crime | Signature | Fix | Machine |
|---|---|---|---|---|
| D1 | **More than one accent hue family** | blue + green + orange accents at once, with no role | one accent-family; status colours are separate and meaning-driven | `X4` 🔴 |
| D2 | **Muddy tint** | a "dirty" semi-transparent colour over a coloured background | tint on a neutral, or go solid | 🟨 |
| D3 | **Pure grey / pure black text** | `#888`, `#000`, shadows `rgba(0,0,0)` | greys carrying a trace of the brand; text is ink, not #000 | `X9`/`X2` 🔴 |
| D4 | **Gradient as paint** | gradient on buttons/headings/borders everywhere | gradient only as signature/seal; budget ≤2/section | `X5` 🔴 |
| D5 | **Colour as decor, not information** | coloured card background with no meaning (Refactoring UI) | colour carries status/hierarchy, otherwise neutral | 🟥 |
| D6 | **Saturation over a large area** | a screaming background across half the screen | large area = low chroma (the inverse law) | `X3` 🔴 |
| D7 | **Body contrast failure** | grey text on light grey < 4.5:1 | WCAG AA | `V15`-adjacent 🔴 |

---

## E. COMPOSITION AND BALANCE (asymmetry that breaks)

| # | Crime | Signature | Fix | Machine |
|---|---|---|---|---|
| E1 | **Accidental asymmetry** | an offset "just because", with no optical counterweight | asymmetry = deliberate balance of masses, not a hole | 🟥 |
| E2 | **Dead zone** | a big empty corner for no reason, the screen does not hold | recompose; emptiness works, it does not gape | 🟥 |
| E3 | **Axes not aligned** | left edges of text/icons/buttons are not on one line | one vertical axis; optical alignment | `X7` 🟥 |
| E4 | **Everything equally important** | there is no lead; the eye has nothing to catch on | 90% quiet, 10% loud; one hero per screen | 🟥 |
| E5 | **A row of clones as an argument** | 3–4 identical title-text cards carry the whole meaning of the section | hierarchy: one weighs more; not a template-row | `X12` 🟥 |
| E6 | **Density ignores the context** | landing-page air in an operational tool / operational cramping on a landing page | density by register (statement vs instrument) | 🟨 |

---

## F. LAYOUT SEMANTICS (the interface's logic for the user)

> Your grievance: there is no "distribution of meaning across the monitor". Here are the rules.

| # | Crime | Signature | Fix | Machine |
|---|---|---|---|---|
| F1 | **Primary is not where the hand is** | the main action is hidden on the left / in the middle | primary — by the F/Z pattern (bottom-right in forms, top-right in chrome) | 🟥 |
| F2 | **The frequent action is far away** | the most frequent one costs extra clicks/scrolling | by frequency → by reachability; the hot thing sits closer | 🟥 |
| F3 | **Nav bloated, content squeezed** | the menu takes more attention than the screen's task | chrome serves, content rules | `V17`+🟥 |
| F4 | **No visual grouping of the logic** | related controls scattered, unrelated ones side by side | group by meaning (proximity = a number, A6) | 🟨 |
| F5 | **One layout for every width** | the desktop grid crammed into mobile / the reverse | real reflow: 360/768/1280/1920, not scaling | `V2` 🔴 |
| F6 | **The scan path breaks** | the eye jumps, reading order does not match importance | order in the flow = order of importance | 🟥 |

---

## G. PAGE RHYTHM AND SCREENS (fixing the "attic": blocks piled up unevenly)

> The page-level axis, separate from A (rhythm INSIDE a section). It answers "the site is like an attic, everything in a heap, no usable screens".

| # | Crime | Signature | Fix | Machine |
|---|---|---|---|---|
| P1 | **A dump of equal-weight sections** | 10+ sections of the same weight in a row with no pauses | ~6–9 sections; weight varies — the hero anchors, secondaries are lighter/denser | `V20.5` 🟡 |
| P2 | **Empty sections as filler** | "coming soon" / "Loading…" occupying a full-size section | no content → the section is HIDDEN on prod, not "padding made of placeholder" | `V20.3/4` 🔴 |
| P3 | **No clean first screen** | the hero does not fit / the next section peeks out crookedly | the hero holds the first viewport; peek ≤~15vh as a deliberate scroll hint | `V20.6` 🟡 |
| P4 | **Ragged vertical rhythm of sections** | sections have different vertical padding set "by eye" | one section-spacing token; distinct ≤2 | `V20.1` 🔴 |
| P5 | **Sections collide** | one section's CTA/panel rides over the next | pairwiseIntersection of sections == 0 | `V20.2` 🔴 |
| P6 | **Section boundaries do not read** | everything in one tone, no pauses — the eye never sees "a new chapter" | alternate tone/air; eyebrow→heading→content like a chapter | 🟥 |

---

## H. DETECTORS FROM THE CANON (folded in here as mandatory)

- **Cheapness X1–X12** (`VISUAL_CRAFT §9`) — walk it in full; the reducible ones → `CRAFT_LINT`, the irreducible ones (X7/X12) → verdict here.
- **Stiffness ST1–ST12** (`INTERFACE_CRAFT §7`) — for operational tools; the behavioural ones → verdict here.
- **Timidity Y1–Y12** (`EDITORIAL_CRAFT §8`) — for statement pages: not "cheap" but "cowardly" (a small hero, no gesture, timid typography).

---

## SEVERITY SCALE

| Level | What it is | Action |
|---|---|---|
| 🔴 **CRIME** | readability/equal height/contrast/palette-family/gradient-as-paint | blocks 🟢; the fix is mandatory |
| 🟠 **UGLY** | rhythm, axes, density, compositional imbalance | fix within the same iteration |
| 🟡 **WEAK** | timidity, under-pushed hierarchy, "could have been stronger" | verdict + proposal; not a block |

Class B (a formal deviation where the screen may be right) → one line to @DESIGN per verdict, as in the current `ROLE_QA_VISUAL Pillar 11`. Do not blindly repair a living decision.

---

## I. MANDATORY TABLE IN THE REPORT (crime-verdict)

Every visual report by @QA_VISUAL carries this table. There are no empty rows — only 🟢/🔴/⚪.

```markdown
## 🎯 Aesthete verdict
| Block | Items | Result |
|------|--------|------|
| A Rhythm/equal height | A1..A6 | 🟢/🔴 [if 🔴 — where+fix+vector] |
| B States/contrast   | B1..B6 | ... |
| C Type scale/chrome     | C1..C6 | ... |
| D Colour/palette          | D1..D7 | ... |
| E Composition/balance     | E1..E6 | ... |
| F Layout semantics   | F1..F6 | ... |
| G Page rhythm / screens | P1..P6 | ... |
| H Canon detectors      | X/I/Y  | ... |
```

**Acceptance rule:** a report without the table filled in = incomplete = not 🟢. This is exactly what makes @QA_VISUAL merciless: he physically cannot "let something slip between his fingers", because every cell demands an explicit verdict.

---

## J. WHAT THIS OVERLAY CHANGES

Before: `V1–V12` (geometry) + soft "V13 cheapness / V14 stiffness" judged by eye → things slip through.
After: `V1–V18` (geometry + states/rows/fonts/primitiveness measured) + **a closed catalogue A–H with a mandatory verdict** → nothing left to slip: either a number dropped, or a cell is unfilled (which is itself a 🔴 against the report).

---

*Version 1.0 — 2026-07-20 — the aesthete's eye: a catalogue of crimes with a mandatory verdict. Pair to CRAFT_LINT_SPEC.*
