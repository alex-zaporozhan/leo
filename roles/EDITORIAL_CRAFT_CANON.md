# EDITORIAL_CRAFT_CANON.md
# The craft of the STATEMENT. The missing half of VISUAL_CRAFT_CANON, which is the craft of the TOOL.
# Scope: showcases, landings, hero sections, brand pages, portfolios, campaign pages, anything meant to be FELT.
# NOT for: /admin, /app, consoles, dashboards, forms — there, restraint IS the craft (VISUAL_CRAFT_CANON).
# Owners: @MOTION (the metaphor and the gesture), @DESIGN (the composition), @CREATOR (the world), @FRONTEND (the type system).

> **The canon's dogma:** restraint is the craft of the **instrument**. Expression is the craft of the **statement**.
> Both are craft. Both have laws. **They are not the same laws, and in places they are opposite laws.**
> A Vogue spread is not "90% quiet". A poster does not "whisper its chrome". A page that must make someone FEEL
> something cannot be built by subtraction alone.
>
> **The failure this file prevents — TIMIDITY.** A system that punishes bad boldness (cliché ban-lists, taste gates,
> cheapness detectors, reduction protocols) and never teaches good boldness produces a designer — human or model —
> who rationally retreats to safe micro-UI. Nothing is *wrong*. Nothing is *anything*. The page is competent,
> forgettable, and indistinguishable from a template. **Cowardice is a craft failure, and until now the system
> had no name for it and no detector to catch it.**

---

## §1. WHICH CRAFT AM I DOING? (answer before the first decision)

| | **Instrument** (`VISUAL_CRAFT_CANON`) | **Statement** (this file) |
|---|---|---|
| Where | `/admin`, `/app`, tools, dashboards, forms | landing, hero, brand page, campaign, portfolio |
| Judged by | speed of thought, hours of use without fatigue | **the three seconds before the scroll** |
| Decoration | ONE object per view; the rest is structure | **the page IS the object** |
| Chrome | must disappear | **may be an event** (a nav that is part of the composition, not furniture) |
| Type | 5–6 sizes, display 1.6–2× body | **display 6–15× body. A word that fills the screen.** |
| Colour | large area → low chroma | **a full-bleed field of colour can BE the idea** |
| Method | reduce until removal hurts | **commit to one gesture completely, without apology** |
| Silence | the default | **a weapon — but only if something else is loud** |

**The rule:** name the register in the SPEC's first line. `REGISTER: instrument` or `REGISTER: statement`.
A page that tries to be both is neither — that is how a landing ends up looking like a settings screen with a big
button on it.

**What does NOT invert** (craft is craft — §7): one light source · ink-tinted shadows · tinted neutrals · concentric
radii · `tabular-nums` · zero-shift interactions · reduced-motion. Boldness is never an excuse for sloppiness.

---

## §2. SCALE IS THE PRIMARY INSTRUMENT

The single biggest reason a showcase looks like a blog: **timid display type.**

```
THE NUMBER:  hero display on a statement page = clamp(3rem, 8–14vw, 16rem).
             Not 48px. Not 64px. A word that OWNS the viewport.
             If the heading fits comfortably beside other things, it is not a heading — it is a label.

THE CONTRAST: display / body ratio
             instrument   1.6–2×   ← calm, correct, and the reason your landings look like admin panels
             statement    6–15×    ← this is what reads as "designed"
             A 2× ratio on a hero is the visual equivalent of mumbling.

THE COROLLARY: if the display is enormous, EVERYTHING ELSE MUST GET SMALLER AND QUIETER.
             Big type does not mean big everything. It means one enormous thing surrounded by whispers.
             (This is §1 restraint, redeployed as ammunition rather than as a rule.)

MEDIA: an image is full-bleed, or it is a small deliberate object. There is NOTHING in between.
       A 60%-width image with rounded corners and a shadow is the universal signature of a page with nothing to say.
```

---

## §3. TENSION AND ASYMMETRY — the composition of a statement

```
□ NOTHING IS CENTRED BY DEFAULT. Centre is the composition of someone who did not choose.
  Centre is legitimate ONLY as a deliberate monument (an object on a pedestal, §1 of CONCEPT_ANATOMY axis 6).
  Ask: "did I centre this because it is right, or because I did not decide?" Usually the second.

□ OFF-GRID. The grid is the instrument's discipline; editorial breaks it ON PURPOSE, at one or two points,
  and holds it everywhere else. A break with no discipline around it is chaos; discipline with no break is a form.

□ BLEED. Something crosses the edge of the screen — type, an image, a rule, a field of colour.
  A page where every element politely stops inside the container has already told you it is afraid.

□ OVERLAP AND COLLISION. Text over image. An element straddling two zones. A number bleeding behind a photo.
  The collision IS the design — it says these two things belong to one another. (Watch LAYOUT_INVARIANTS §12:
  overlap is composition, but interactive elements must never intersect. Beauty does not license a broken tap target.)

□ OPTICAL BALANCE, NOT SYMMETRY. Mass, not measurement. A huge word on the left balances a tiny caption on the
  right — that is a composition. Two equal columns is a table.

□ THE UNCOMFORTABLE MARGIN. Editorial space is not "air" — it is TENSION. An enormous empty left column next to a
  dense right column is a decision. Even margins everywhere is wallpaper.
```

---

## §4. ONE GESTURE, COMMITTED TO COMPLETELY

> **This is the law that fixes timidity, and it is the hardest to obey.**

```
BOLDNESS IS NOT "SEVERAL BOLD THINGS". It is ONE thing, done without apology, and everything else made quiet
to serve it.

A TIMID BOLD MOVE IS WORSE THAN NO BOLD MOVE.
  a slightly large heading   → reads as a mistake in the type scale
  a slightly bright colour   → reads as an accident in the palette
  a slightly odd layout      → reads as a bug
  a slightly weird font      → reads as a licence problem
The viewer does not think "how daring". They think "something is off". HALF-COMMITTED IS THE UGLIEST PLACE ON
THE SPECTRUM — uglier than safe, uglier than wild.

THE TEST: "would a competitor be SURPRISED by any single decision on this page?"
  If no → the ambition dial says bold and the page says confident-at-best. You did not do it.
  This is the smell test that catches the model retreating to safety while claiming boldness.

WHERE TO SPEND IT (one, not several):
  scale (§2) · a colour field · a typographic gesture (§5) · a compositional break (§3) · one motion moment ·
  the signature element (CONCEPT_ANATOMY axis 8)
Choose ONE. The Chanel rule still applies — but here it means: remove the accessories so THE ONE THING can be seen,
not remove the one thing.
```

---

## §5. EDITORIAL TYPOGRAPHY — the craft the system does not have at all

> Grep says: `drop cap` 0 files · `baseline grid` 0 · `pull quote` 0 · `hanging punctuation` 0 · `optical kerning` 0.
> The system knows how to *animate* text (MOTION_LIBRARY T1–T6) and not how to *set* it. These are different crafts,
> and the second is the one that makes a page look like a magazine instead of a slide.

```
□ TEXT AS IMAGE. The word is not a label for the visual — the word IS the visual. Set a single word at 14vw,
  crop it, let it bleed, put an image inside its counters. This is the cheapest "expensive" move in existence:
  it costs one heading and no assets.

□ OPTICAL KERNING ON DISPLAY TYPE. Above ~48px, metric kerning lies. Tighten by hand:
      display  letter-spacing: -0.02em … -0.04em     (large type looks loose; close it)
      caps/labels                +0.06em … +0.12em   (small caps look strangled; open them)
  Untracked display type is the most common tell of a developer setting type instead of a designer.

□ MIXED WEIGHTS INSIDE ONE HEADLINE. `<span class="light">School of</span> <span class="black">Design</span>`
  — one line, two voices. Fashion and editorial do this constantly; SaaS never does. It costs nothing and it
  instantly reads as art-directed.

□ BASELINE GRID. All text sits on a shared vertical rhythm (`line-height` a multiple of a base unit, e.g. 8px).
  It is invisible and it is the difference between "laid out" and "typeset".

□ DROP CAP for the opening paragraph of an editorial block: `initial-letter: 3` (or a float fallback).
  Two lines of CSS. It says "this is a publication, not a page".

□ PULL QUOTE — a sentence lifted out of the body at 2–3× size, set in the display voice, breaking the column.
  The oldest trick in magazines and it still works, because it gives the eye a place to land.

□ HANGING PUNCTUATION: `hanging-punctuation: first last;` — quotes and dashes hang outside the measure so the
  optical edge stays straight. Nobody notices it. Everybody feels it.

□ VARIED MEASURE ON PURPOSE. A 35-character lede beside a 70-character body is a composition. Uniform 65ch
  everywhere is a document.

□ VERTICAL / ROTATED TYPE as structure — a section label running up the left margin (`writing-mode: vertical-rl`).
  Editorial furniture. Costs nothing. Reads as intent.

□ REAL HYPHENATION AND JUSTIFICATION where the world calls for it (`hyphens: auto; text-wrap: pretty;`).
  A ragged-right column with orphans is the default; a properly set column is a decision.

□ ONE FONT CAN BE ENOUGH — if you use its full range (100 → 900, italic, optical sizes). A variable font at three
  weights and two sizes IS a type system. "Two fonts" is not automatically richer than one used with conviction.
```

---

## §6. THE FASHION / LUXURY REGISTER (specifically)

```
□ NEGATIVE SPACE IS WEALTH. The most expensive pages say the least per screen. A hero with six words and one image
  outranks a hero with a value proposition, three bullets, two CTAs and a trust badge. Confidence is what you can
  afford NOT to say.

□ THE EDITORIAL PAUSE. A full viewport that contains almost nothing — a word, a rule, an image — between two dense
  sections. It is the rest between two notes, and it makes both of them louder.

□ PHOTOGRAPHY IS TREATED, NOT PLACED. Duotone, grain, a crop that cuts the subject, an unexpected scale.
  A stock photo dropped in at 100% with rounded corners is worse than no photo.

□ THE SERIF IS THE BRAND (when the world calls for it). A high-contrast display serif (didone/antiqua — CONCEPT_ANATOMY
  axis 4) at enormous size, set tight, is the single strongest luxury signal in typography, and it costs one font file.

□ COLOUR: often one, or none. A monochrome page with one photographic accent is a fashion page. A five-colour page
  is a promo.

□ NO BADGES, NO PILLS, NO ICON-TITLE-TEXT CARDS. These are SaaS furniture. In this register they read as a discount
  rack in a boutique. (This is X12 and C6 in the ban-lists — and here it is not just cheap, it is off-register.)
```

---

## §7. WHAT DOES NOT INVERT — craft is still craft

Boldness is never a licence for sloppiness. Every one of these still holds, from `VISUAL_CRAFT_CANON`:

```
□ ONE LIGHT SOURCE — shadows ink-tinted, never #000, opacity ≤ 10% (§3)
□ NEUTRALS ARE TINTED — a pure grey belongs to no world (§4.2)
□ CONCENTRIC NESTED RADII (§6)          □ tabular-nums on every number (§5.4)
□ ONE SEPARATION METHOD per surface (§2) — a bleeding, overlapping card still does not get border+shadow+tint
□ ZERO-SHIFT interactions; prefers-reduced-motion respected (LAYOUT_INVARIANTS §7, §10)
□ THE CLICHÉ BAN-LIST C1–C10 still applies — a cliché executed boldly is a bold cliché
□ THE CHEAPNESS DETECTOR X1–X12 still applies — this file adds a detector, it does not disable one
```

**A bold page that violates these is not brave. It is careless, and everyone can tell the difference.**

---

## §8. THE TIMIDITY DETECTOR — 12 signs the design chickened out

> The system already detects EXCESS (X1–X12 cheapness, C1–C10 clichés). It had no way to detect **cowardice**.
> Run this on any page whose `REGISTER: statement`. Owner: @DESIGN (AUDIT), mirrored by @QA_VISUAL.

| # | Symptom | What it means |
|---|---------|---------------|
| **Y1** | Hero display type below ~6vw on desktop | The page is mumbling (§2) |
| **Y2** | display/body contrast below 3× | The hierarchy is a suggestion, not a statement |
| **Y3** | Everything is centred | Nobody decided; centre is the default of the undecided (§3) |
| **Y4** | Nothing bleeds off any edge; every element stops politely inside the container | The page is afraid of its own frame (§3) |
| **Y5** | No overlap, no collision, no element straddling two zones anywhere | Nothing on this page belongs to anything else (§3) |
| **Y6** | The boldest thing on the page is a coloured button | The ambition dial says bold; the page says form (§4) |
| **Y7** | A symmetric row of three icon-title-text cards carries the main argument | The universal template of a product with nothing to say (X12) |
| **Y8** | Media is 50–80% width with rounded corners and a shadow | The "in-between" image — neither a statement nor an object (§2) |
| **Y9** | No typographic craft at all: no drop cap, no pull quote, no mixed weights, no tracked display, no varied measure | Text was *placed*, not *set* (§5) |
| **Y10** | Remove the logo → the page could belong to any competitor | The T1 test of CONCEPT_ANATOMY, failed |
| **Y11** | No competitor would be **surprised** by any single decision on the page | The commitment test of §4, failed. **This is the master sign.** |
| **Y12** | The ambition was declared `bold`/`experimental` and no artefact of the page reflects it | The dial was set and ignored — the most common failure of all |

**Verdict:** on a `statement` page — **3+ hits → 🔴.** The page is not "clean", it is **timid**, and timid is a
craft failure exactly as much as gaudy is. It is not fixed by polishing. **Something must become brave.**

> **Say this out loud to the model, because it is the whole point of this file:**
> *You are not being asked to be safe. You are being asked to be GOOD — and safe is not the same thing.
> A forgettable page is a failed page, even if every rule was obeyed.*

---

Reference: `roles/VISUAL_CRAFT_CANON.md` (the craft of the instrument — the other half; §7 here lists what does NOT invert) · `roles/CONCEPT_ANATOMY.md` (axis 4 typographic character · axis 6 composition · axis 8 the signature — the gesture this file asks you to commit to) · `roles/CONCEPT_DNA_LIBRARY.md` (the worlds — expression is executed INSIDE a world, never instead of one) · `roles/MOTION_AMBITION_DIAL.md` (bold/experimental — the dial grants permission; **this file supplies the craft**) · `roles/MOTION_LIBRARY.md` (how text MOVES — §5 here is how text is SET; different crafts) · `roles/HERO_ARCHETYPES.md` · `roles/VISUAL_CONCEPT_PROTOCOL.md` (TASTE GATE, C1–C10 — still binding) · `roles/LAYOUT_INVARIANTS.md` (§12 — overlap is composition; intersecting interactive elements are still a bug) · `roles/ROLE_DESIGN.md` · `roles/ROLE_MOTION.md` · `roles/ROLE_QA_VISUAL.md`
Version: 1.0 | 2026-07-12
