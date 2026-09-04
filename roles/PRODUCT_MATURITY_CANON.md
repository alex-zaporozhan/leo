# PRODUCT_MATURITY_CANON.md
# The canon that ends "it works, ship it". Absence of defects is NOT presence of quality.
# Owners: @LEAD (declares the class, holds the level gate), @DESIGN (designs TO the class), @FRONTEND (builds the
# class primitives), @QA_VISUAL (measures the level), @DEV (executes).
# Position: BEFORE DOMAIN_STANDARDS. That file answers "what must this PAGE TYPE contain".
# This file answers the question nobody was asking: "what KIND OF THING is this, and how good is it?"

> **The canon's dogma:** every gate in this system is binary — pass or fail. Nothing says
> **"this passes, and it is mediocre, and mediocre is not done."**
> So @LEAD says *"good enough"*, and it is a **rational verdict**: against what standard would it not be?
>
> **The failure this file prevents — SHIPPING AT LEVEL 1.** A screen that works, has four states, no 🔴,
> and is a plate of grey furniture. The client cannot use it. You find every flaw by hand, button by button,
> because nobody in the system was ever asked *"is this actually good?"* — only *"is it broken?"*
>
> **And the deeper one — CLASS BLINDNESS.** A page builder is a **canvas-class product** (Webflow, n8n, Figma).
> Built without naming its class, it becomes a form over a JSON blob with `block_type` and `schema_version`
> showing in the UI. Not because anyone chose that — because **nobody said what kind of thing it was**, and
> "card–text–button" is what a model produces when nothing tells it otherwise.

---

## §1. DECLARE THE PRODUCT CLASS — the question nobody was asking

**Before `DOMAIN_STANDARDS` (what the page type must contain), answer: what KIND of thing is this?**
The class carries **non-negotiables**. A screen built without its class is furniture.

| Class | What it IS | Non-negotiables (absent → it is NOT this class, it is a CRUD form pretending) | Canon |
|-------|-----------|------------------------------------------------------------------------------|-------|
| **LIST / TABLE** | comparing many peers | search · filters that persist · **bulk select** · sort · row actions on hover · sticky header · identifying column first | INTERFACE_CRAFT §3 |
| **RECORD / DETAIL** | one entity, deeply | inline edit · **lifecycle visible** (only legal transitions) · history/undo · related entities as **navigation**, not id-dropdowns | FRONTEND_CAPABILITY C1/C3/C6 |
| **CONSOLE / INSTRUMENT** | operating a system | the **I1–I12 inventory** · command palette · keyboard path · **undo instead of confirm** · live state, not "Refresh" | INTERFACE_CRAFT §1 |
| **BUILDER / CANVAS** | composing a structure | **the structure is VISIBLE, not a list** · direct manipulation · **live preview coupled to the edit** · typed slots refusing illegal content · undo/redo unlimited · duplicate/copy-paste of a selection · **no schema names in the UI** | CANVAS_CRAFT |
| **DASHBOARD** | understanding at a glance | every number has a **trend, a comparison, and the action it implies** · a bare number in a box is decoration | FRONTEND_CAPABILITY C12 |
| **WIZARD / FLOW** | a guided sequence | progress visible · back without loss · **validation before the step, not after** · resumable |  |
| **SHOWCASE** | making someone FEEL | scale, tension, one committed gesture — restraint here produces the banal page | EDITORIAL_CRAFT |
| **PIPELINE / MONITOR** | watching work happen | the **real cursor** (`64/190`), stage, ETA, a working **Cancel**, retry, the honest error | ASYNC + CANVAS §6 |

**@LEAD names the class in the handoff. @DESIGN designs TO the class.**
A page builder declared as `LIST` will be built as a list — and that is exactly what happened.

---

## §2. THE MATURITY LADDER — "it works" is level ONE

> Every level is **checkable**, not a matter of taste. **A client product does not ship below L3.**
> An internal tool may ship at L2 — deliberately, with the level written down.

```
L1 — IT WORKS.  CRUD exists. The happy path completes.
     ⚠ THIS IS WHERE MOST SCREENS DIE, because the gates are all binary and this passes them all.
     Shipping at L1 is the disease this canon exists to name.

L2 — IT DOES NOT HURT.  + all four states (loading/empty/error/success) render for real
     · no dead ends · no raw internal names in the UI (§4) · no 409/403 the UI could have predicted
     · keyboard basics (Tab order, Esc, Enter) · nothing blocks the whole screen.

L3 — IT IS GOOD.  + THE CLASS NON-NEGOTIABLES OF §1 ARE ALL PRESENT.
     A console has its inventory. A builder shows the structure and previews it live. A dashboard
     gives insight, not numbers. **This is the floor for anything a client pays for.**

L4 — IT IS EXCELLENT.  + the CAPABILITY_MAP is fully surfaced (the backend actually plays — C1–C12)
     · one thing on the screen is **genuinely better than the benchmark** (§3), and you can name it.
```

**The gate, and it is @LEAD's:**
```
@LEAD does not close a client-facing epic below L3. "It works" is a report of L1, not of done.
The level is DECLARED in the acceptance, with the evidence: "L3 — class CONSOLE, inventory I1/I3/I4/I6/I8
present, I11 N/A (7 records)". A level claimed without the checklist is not a level.
```

---

## §2A. PRODUCTION RELEASE (not «Absolutely Functional MVP»)

> Sister bar: `roles/PRODUCTION_READINESS_CANON.md`.  
> Planning discipline: `roles/PLANNING_MATURITY_CANON.md`.

**Absolutely Functional Production Release** = the intended product is designed with **foundation complete**; **delivery** is claimed only via named **Production Release Contours**; every Contour that ships to a paying client meets **this file's L3** for its declared class.

| Term | Definition | Anti-pattern |
|------|------------|--------------|
| **Production Release Contour** | A claimable set of operator outcomes + required slices + L3 classes (`PRODUCTION_READINESS` §5) | «MVP slice» that ships L1 CRUD and skips tenancy/credentials |
| **Absolutely Functional Production Release** | Contour(s) green **and** foundation not hollow **and** marketing claims only cite green Contours | «Absolutely Functional MVP» used as a quality floor |
| **Phased delivery** | Contour B after Contour A; WAVE-2 polish with owner+cost | Foundation deferred «until after MVP» |
| **Pilot** | Audience / WAIVE log with owner+expiry | Licence to ship furniture or silent platform fallback |

**@LEAD acceptance line (template):**
```
Contour: PR-[NAME]
Class/Level: [CONSOLE|BUILDER|…] at L3 — checklist: […]
Foundation: [PRODUCTION_READINESS §2 items for this Contour — closed / WAIVE id]
Forbidden claim until green: […]
```

A Contour closed at L1 «happy path works» is **not** a production release — it is a demo debt.

---

## §3. THE REFERENCE WALK — the mechanism that makes the model SEE

> **This is the most important section in this file.** The model does not fail to see mediocrity out of laziness —
> it fails because it has **no contrast**. "Is this good?" is a question with no checkable answer.
> "What does Webflow do in this exact screen that we do not?" is **mechanical**, and mechanical questions get answered.

**Mandatory before closing any screen above L2. Written, not felt.**

```
REFERENCE WALK — [screen]
Class:      [from §1]
Benchmark:  [the ONE product that does this class best. Not "Linear" in general —
             a CONCRETE product AND its CONCRETE screen.
             builder → Webflow/Framer/n8n · console → Linear/Vercel · table → Linear/Notion db ·
             dashboard → Stripe/Vercel · pipeline → n8n/Temporal · media library → Figma/Notion]

THREE THINGS THEY DO THAT WE DO NOT (be specific — a behaviour, not an adjective):
  1. [e.g. "the block list shows a THUMBNAIL of each block, so you see the page without clicking"]
  2. [e.g. "editing a field updates the preview instantly — the edit and the result are ONE surface"]
  3. [e.g. "no technical field names anywhere — 'Media Id' is called 'Image'"]

FOR EACH:  [we take it — how] / [we deliberately reject it — WHY, in one sentence]
"We reject it because it is hard" is not a reason. It is a confession.

THE ONE THING WE DO BETTER: [name it, or admit there isn't one]
If you cannot name one thing your screen does better than the benchmark, the screen is a copy at best
and furniture at worst. That is a legitimate finding — write it down and decide what to do about it.
```

**Why this works:** it converts an unanswerable question ("is it good?") into an answerable one
("what is missing versus a named thing?"). @LEAD stops saying *"good enough"* because the walk produces a
**list**, and a list is a thing you can be asked about.

---

## §4. NO INTERNAL NAMES IN THE UI (a generalisation of Law 8, and it is violated constantly)

Law 8 forbids UUIDs. That was too narrow. **The database must not appear in the interface at all.**

```
🔴 FORBIDDEN in any user-visible label, field, badge, tooltip or heading:
   · schema/table/column names:  block_type · schema_version · media_id · carousel_media_ids · tenant_id
   · enum values as-is:          video_block · draft_v1 · PENDING · is_enabled
   · type/version badges:        v1 · schema 1 · payload
   · developer-facing status:    "Design lint: ready for preview" — the user did not ask, and does not care

✅ REQUIRED:
   Every field the user sees has a HUMAN name and, where it is not obvious, a one-line explanation of what
   it does — written for the person doing the job, not for the person who wrote the migration.
   `Carousel Media Ids` → `Carousel images` · `Media Id` → `Image` · `block_type: video_block` → the
   block is simply CALLED “Video”, and its type is shown by its ICON, not by a string.
   (Write these in the **product's own user language**; the examples here are in English because the canon is.)

THE TEST: show the screen to someone who has never seen the codebase. Every word they cannot explain is a defect.
```

**Why this matters more than it looks:** internal names in the UI are the single clearest signal that the frontend
was laid **over** the backend rather than built **for the user** — the tablecloth (`FRONTEND_CAPABILITY` T1),
made visible in one glance.

---

## §5. THE LAZY-FRONTEND DETECTOR — 10 signs of "card–text–button"

| # | Symptom | What it really means |
|---|---------|----------------------|
| **L1** | Every screen is a stack of cards, each: title + text + button | The default output of a model with no class and no reference |
| **L2** | The UI library is used at its defaults; nothing is composed from its primitives | Mantine (or any library) is a **blank**, not a design. Using `<Card>` is not designing a card |
| **L3** | No panel slides, expands, docks, splits, or reveals — everything is a page or a modal | No spatial thinking. Real tools have **layers**: canvas + inspector + preview, coexisting |
| **L4** | Internal field names visible (§4) | The backend leaked through |
| **L5** | The edit and its result live on different screens | Direct manipulation was never considered (builder/canvas classes: 🔴) |
| **L6** | Two mechanisms for the same action (↑↓ arrows **and** a drag handle) | Neither was finished; the second was added because the first felt wrong |
| **L7** | A structure (page, tree, flow) is shown as a flat list of names | The structure is invisible. This is the builder failure in one line |
| **L8** | The refactor "for a revolutionary UI" changed only colours | The concept was applied to tokens and never to **behaviour or composition** |
| **L9** | No state on the screen is deep-linkable | Nothing can be shared, resumed, or reported |
| **L10** | The screen has no keyboard path at all | It was never imagined being used by someone fast |

**3+ hits → the screen is L1 furniture regardless of what the gates said.** Not a styling problem: a **class and
level** problem. Go back to §1.

---

## §6. WHERE THIS BINDS IN THE CHAIN

```
@LEAD    — declares the CLASS (§1) and the Contour (§2A) in the handoff to @DESIGN.
           Names the TARGET LEVEL (L3 for client-facing). Does not close below L3.
           Does not call Contours «MVP quality». "It works" is a report of L1.
@DESIGN  — designs TO the class; the SPEC names each class non-negotiable as present or N/A+why.
           Runs the REFERENCE WALK (§3) before the SPEC is final. Three things, written down.
@FRONTEND— builds the class primitives ONCE (the canvas, the inspector, the palette, the bulk machinery),
           so the second screen of a class is cheap and consistent.
@DEV     — executes; refuses to render an internal name in the UI (§4).
@QA_VISUAL— measures the level: the §5 detector (L1–L10) + the class checklist. The level is a NUMBER in the report.
@QA_ARCH — an internal name in the UI is 🔴 (§4), on par with a UUID.
```

---

## §7. THE HARD TRUTH THIS FILE EXISTS TO SAY

> **Working is level one.** Every other role in this system is optimised to remove defects, and removing defects
> produces a screen that is not broken. **Not broken is not good.**
>
> Nobody in the chain was ever given the job of asking *"is this actually any good?"* — so nobody asked, and the
> honest answer to *"is it good enough?"* was always **yes**, because "enough" was never defined.
>
> Now it is: **the class (§1), the level (§2), and three things the benchmark does that we do not (§3).**
> A screen that cannot answer all three is not finished. It is furniture, and the client will feel it in the
> first thirty seconds — which is exactly where you have been finding it, by hand, button by button.

---

Reference: `roles/PRODUCTION_READINESS_CANON.md` (foundation · Contours) · `roles/PLANNING_MATURITY_CANON.md` (ledger) · `roles/INTERFACE_CRAFT_CANON.md` (CONSOLE I1–I12) · `roles/CANVAS_CRAFT_CANON.md` (BUILDER) · `roles/EDITORIAL_CRAFT_CANON.md` · `roles/VISUAL_CRAFT_CANON.md` · `roles/FRONTEND_CAPABILITY_CANON.md` · `roles/DOMAIN_STANDARDS.md` · `roles/ROLE_LEAD.md` · `roles/ROLE_DESIGN.md` · `roles/ROLE_QA_VISUAL.md`
Version: 1.1 | 2026-07-26
