# VISUAL_CRAFT_CANON.md
# The physics of expensive. The craft layer that separates premium execution from gaudy execution — inside ANY world.
# Position: CONCEPT_ANATOMY answers "which world" (genetics) · this file answers "how well is it made" (craft)
# · LAYOUT_INVARIANTS answers "does it hold under content" (geometry). Three different questions. No overlap.
# Owners: @DESIGN (mandatory before SPEC and AUDIT), @FRONTEND (owns the scales and the floor tokens),
# @QA_VISUAL (the cheapness detector as a checkable vector), @CREATOR (craft applies to the concept too).

> **The canon's dogma:** the world answers WHAT. Craft answers HOW EXPENSIVE.
> The same world — the same palette, the same fonts — can be executed like an Hermès window or like a 2008 website.
> The difference is never the colour. It is restraint, one light, tonal depth, optical care and the courage to remove.
>
> **⚠ THE SCOPE OF THIS FILE — read this before applying it to a landing.**
> This is the craft of the **INSTRUMENT**: restraint, quiet, subtraction. It is the physics of expensive
> *operational* software (Linear, Stripe, a console) — and it is exactly right there.
> **It is NOT the craft of a statement.** A hero, a landing, a brand page, a fashion or editorial surface obeys
> partly OPPOSITE laws (scale as a weapon, deliberate asymmetry, one gesture committed to completely) —
> those live in `roles/EDITORIAL_CRAFT_CANON.md`. Applying §1's restraint to a showcase is precisely how a
> landing ends up looking like a settings screen with a big button on it.
> **Name the register first:** `REGISTER: instrument` → this file. `REGISTER: statement` → EDITORIAL_CRAFT.
> §1–§10 of this file still bind a statement page in the parts that are craft, not temperament
> (one light, tinted neutrals, concentric radii, tabular-nums, the cheapness detector) — see EDITORIAL_CRAFT §7.

> **The sister canon:** `roles/INTERFACE_CRAFT_CANON.md` — this file is how a screen **looks** expensive;
> that one is how it **feels** expensive to work in. A console can be visually flawless and still be stiff
> (every action a modal, no bulk, no keyboard, filters lost on reload). Both are craft; both are checkable.
>
> **The failure this file prevents:** a technically correct screen that reads as cheap. Every token filled,
> every invariant satisfied, TASTE GATE passed — and it still looks like a template. That is a craft failure,
> and until now the system had no name for it.

---

## §1. THE LAW OF RESTRAINT — the first and the hardest

```
CHEAP:     every element is decorated. Everything has a border, a shadow, a tint, an icon, a gradient.
           The screen shouts from every corner, so nothing is heard.
EXPENSIVE: 90% of the screen is quiet. 10% speaks. The quiet is what makes the 10% audible.
```

**The decoration budget — a number, not a feeling:**
- **ONE** decorated object per view. It is the signature, the hero, or the primary action — never all three.
- Everything else is **structure**: it exists to be read, not to be admired.
- Count the elements competing for first attention. **More than 1 = the screen is cheap**, regardless of the palette.

**The corollary that hurts:** if you have a beautiful effect and a beautiful hero, you must kill one.
An expensive screen is not a collection of good decisions — it is one good decision protected by silence.

---

## §2. TONAL DEPTH INSTEAD OF DECORATION

```
CHEAP:     a card with a border AND a shadow AND a background tint AND a gradient.
           Four methods to say one thing: "this is a separate surface".
EXPENSIVE: surfaces differ by TONE. A card is 2–4% lighter (or darker) than its ground. That is all.
```

### THE ONE-SEPARATION RULE (iron)

> A surface earns **exactly one** method of separation: **tone**, **OR** a hairline, **OR** a shadow.
> Never two. Never three. Two methods = 🟡. Three = 🔴 cheap.

| Context | The correct single method |
|---------|---------------------------|
| A card on a page ground | tone (the card is lighter; the ground carries the material) |
| A row inside a card | a hairline divider at 6–8% opacity — no shadow, no tint |
| A floating element (dropdown, drawer) | shadow (it is genuinely above; e2–e3) |
| A section on a public site | space (the gap IS the separation — no box needed) |
| An input field | a hairline; on focus — the accent hairline, NOT a glow ring plus a shadow |

**The tonal ladder** (light theme; invert the direction for dark, keep the deltas):

```
ground   = the material base                (e.g. #F6F4F0 warm paper)
surface  = ground lightened by  2–4%        (cards, panels)
raised   = surface lightened by 2–3% more   (a hovered/active card)
sunken   = ground darkened by   2–3%        (wells, code blocks, inputs)
```

If a card needs a border **and** a shadow to be visible, the tonal step is too small. **Fix the tone, do not add outlines.**

---

## §3. ONE LIGHT SOURCE — the shadow system

```
CHEAP:     shadows in different directions, different blurs, pure black at 15–30% opacity.
           Each component chose its own shadow. The room has five suns.
EXPENSIVE: one light, from above. Low opacity, large blur, tinted with the world's INK — never #000.
```

**The elevation scale — copy these values, do not invent:**

```css
--ink: <the world's darkest neutral, e.g. 26 22 18>;   /* NOT 0 0 0 */

--e0: none;                                                        /* flat: rows, chrome, dividers */
--e1: 0 1px 2px  rgba(var(--ink) / .04);                           /* resting cards */
--e2: 0 2px 8px  rgba(var(--ink) / .06),
      0 1px 2px  rgba(var(--ink) / .04);                           /* hovered cards, popovers */
--e3: 0 8px 24px rgba(var(--ink) / .08),
      0 2px 6px  rgba(var(--ink) / .04);                           /* drawers, modals, menus */
--e4: 0 24px 48px -12px rgba(var(--ink) / .18);                    /* the one hero object, if the world calls for it */
```

**Rules:**
- Shadow colour = the world's ink, **never pure black**. Black shadows on a warm ground read as dirt.
- Opacity ceiling on operational surfaces: **10%**. Above that you are drawing, not lighting.
- Blur ≥ 4× the y-offset. A hard, tight shadow is a 2008 signature (unless the world is *deliberately* offset-print or brutal — then it is a **material**, not a shadow, and it belongs to axis 2 of the DNA).
- **Never** animate a shadow's geometry. Change opacity only (see LAYOUT_INVARIANTS §7).
- A surface's elevation must match its **truth**: a row is not floating; do not give it e2.

---

## §4. CHROMA DISCIPLINE — why the colours "feel wrong"

This is the single most common reason a screen reads as amateur, and almost nobody names it.

```
CHEAP:     full-saturation hues (#FF0000, #0066FF, #00CC66), several accents competing,
           coloured body text, a saturated 100vw hero.
EXPENSIVE: one accent, one hue family. Chroma is spent like money — a little, in the right place.
```

### THE INVERSE-AREA LAW (the core rule)

> **The larger the area, the lower the chroma.**
> A 120×40px button may be vivid. A 100vw hero may not. A full-bleed saturated background is the
> loudest way to look cheap.

```
Area                          Max chroma (roughly, in OKLCH terms)
────────────────────────────────────────────────────────────────
icon / badge / dot            high — this is where colour earns its keep
button / chip / active state  high, but ONE per view (§1)
card tint / status background high lightness + LOW chroma (a whisper, not a shout)
section / hero background     very low chroma — nearly neutral, tinted, not coloured
page ground                   neutral, tinted toward the material (§4.2)
```

### §4.1 One accent, one family
One accent hue. Its variants (hover, active, muted) come from the **same hue**, moved in lightness/chroma —
not from a different colour. A second accent is allowed only if the world explicitly declares a duotone logic
(`CONCEPT_ANATOMY` axis 3) — and then they are **equal partners**, not competitors.

### §4.2 Neutrals are never neutral
Pure grey (`#808080`, `#888`, Tailwind `gray-500`) is **dead**. It belongs to no world.
Neutrals are **tinted toward the material**: warm paper → warm greys; cold metal → cool greys;
botanical → greys with a green undertone. The tint may be barely perceptible (2–5% chroma) — that is enough.
A screen of pure greys reads as "unfinished", even when everything else is right.

### §4.3 Text is not colourful
Body text is **ink** (the darkest neutral), not a colour. Secondary text is ink at lower contrast, not grey-blue.
Coloured text is reserved for: links, one accent number, and destructive labels. That is the entire list.

### §4.4 Status colours are muted to the world
Semantic colours (success/warning/error) do **not** arrive from Bootstrap. They are the semantic hues
pulled toward the world's temperature and chroma. Their form: **a low-chroma tinted background + a coloured
left border or dot**. Never a full saturated fill across a card — that is the loudest cheap-tell in an admin panel.

---

## §5. TYPE AS A SYSTEM — not sizes chosen by eye

```
CHEAP:     15px here, 17px there, 23px for that heading. Everything semibold. One font doing all jobs.
EXPENSIVE: a modular scale. Few sizes. Hierarchy carried by WEIGHT and SPACE, not by size alone.
```

### §5.1 The modular scale
Pick **one** ratio and derive every size from it. Do not invent values between steps.

```
Operational UI (dense, functional):   ratio 1.200 (minor third)
  12 · 14 · 16 · 19 · 23 · 28          → labels · body · lead · h3 · h2 · h1

Public site (editorial, expressive):  ratio 1.333 (perfect fourth) or 1.414
  14 · 16 · 21 · 28 · 37 · 50 · 67     → the display step is where the world speaks
```

**Few sizes:** an interface needs **5–6**, not twelve. If you need a seventh, you have a hierarchy problem, not a type problem.

### §5.2 The display/body contrast — the thing that reads as "designed"
```
Public site:      display is 2.5–4× body.  A hero at 1.5× body is timid and reads as a blog.
Operational:      display is 1.6–2× body.  Restraint is the point; the data is the hero.
```
Timidity at the top of the scale is the most common reason a showcase looks ordinary.

### §5.3 Hierarchy by weight, not by bolding everything
```
400  body, long text
500  data, values, table cells that matter
600  titles, section heads
700  reserved — the one display line, if the world calls for it
```
If everything is 600, nothing is. **Bold is a currency; spend it once per view.**

### §5.4 Optical typography (what separates a designer from a dev)
```
Tracking:  display ≥ 28px  →  −1% … −3%   (large type looks loose; tighten it)
           labels / caps   →  +2% … +8%   (small caps look cramped; open them)
           body            →  0           (leave it alone)

Leading:   inverse to size. display 1.0–1.15 · lead 1.3 · body 1.5–1.6 · dense tables 1.4

Measure:   45–75 characters per line for reading text. A 1200px-wide paragraph is a wall, not a text.

Numerals:  font-variant-numeric: tabular-nums  — in ANY column of numbers. Always. No exceptions.
           Proportional numerals in a table are a guaranteed amateur tell.
```

---

## §6. OPTICAL vs MATHEMATICAL — the invisible 10% that costs nothing and changes everything

The browser aligns boxes. The eye aligns **mass**. Where they disagree, the eye is right.

```
□ ICON + TEXT: align optically, not by box. An icon's visual centre sits ~1px above the text's box centre.
  Use align-items: center on a flex row, then nudge the icon by the optical delta if it still floats.

□ CIRCLE vs SQUARE: a circle of the same box size looks SMALLER. Overshoot circular elements by 2–5%
  (a round avatar next to a square logo of "the same" 40px will look wrong until the circle is 42px).

□ NESTED RADIUS: outer radius = inner radius + padding.
  A 12px card with 8px padding needs an INNER radius of 4px — not 12. Concentric radii are a
  premium tell; parallel radii are a cheap one.

□ TEXT BUTTONS: the visual top/bottom padding is not the box padding — cap-height ≠ line-box.
  A button that "looks" 12/16 usually needs padding 10px 16px 12px. Trust the eye, then lock the number.

□ NUMBERS RIGHT, TEXT LEFT. Always. A left-aligned column of amounts is unreadable and unmistakably amateur.

□ HAIRLINES: a 1px border at 100% opacity is a wall. A hairline is 1px at 6–10% ink. The difference between
  a divider you notice and a divider that works.

□ OPTICAL PADDING: an element with a leading icon needs LESS left padding than one without —
  the icon already occupies the optical margin.
```

---

## §7. SPATIAL RHYTHM

```
CHEAP:     padding 13px here, 18px there, "looks about right". No system, so no rhythm, so no calm.
EXPENSIVE: one scale. Every gap is a step on it. The rhythm is felt before it is seen.
```

```
The scale (4/8-based):  4 · 8 · 12 · 16 · 24 · 32 · 48 · 64 · 96
Arbitrary values (13, 18, 22, 30) are FORBIDDEN in application code. A value not on the scale is a bug.

THE PROXIMITY LAW AS A NUMBER (also LAYOUT_COMPOSITION):
  gap WITHIN a group  <  gap BETWEEN groups × 2
  Violated → the eye cannot see the grouping → the screen reads as "a pile", however pretty the parts.

DENSITY ≠ CRAMPED:
  Data screens MAY be tight — Linear and Bloomberg are tight — IF the rhythm is constant.
  Cramped is what happens when the rhythm is broken, not when the numbers are small.

AIR IS A MATERIAL:
  On a public site, the space around the one important thing IS the design.
  If you cannot afford the space, you have too many things (see §1, §10).
```

---

## §8. THE DETAIL HIERARCHY — what is allowed to be beautiful

Not everything can be special. If everything is special, nothing is — this is §1 restated at the level of detail.

```
THE RANK (only the top of this list may carry decoration):

  1. THE SIGNATURE      — the one owned element (CONCEPT_ANATOMY axis 8). It may be elaborate.
  2. THE PRIMARY ACTION — one per view. It may be vivid.
  3. THE DATA           — must be perfectly legible. Not decorated. Ever.
  4. THE CHROME         — navigation, headers, dividers, breadcrumbs, footers.
                          MUST WHISPER.
```

**THE CHROME RULE (a hard test):**

> If you notice the chrome, the chrome is wrong.
> Navigation, dividers and headers exist to be used, not to be seen. Hairlines at 6–10% ink, no shadows,
> no gradients, no decorative borders, no coloured backgrounds. A "beautiful sidebar" is a design failure:
> it is stealing attention from the work.

Every hour spent decorating chrome is an hour spent making the product cheaper.

---

## §9. THE CHEAPNESS DETECTOR — 12 signs (run before shipping any screen)

Each sign is checkable from the code or the render. Any hit → the screen is not finished.
Owner: @DESIGN (AUDIT), mirrored by @QA_VISUAL as a vector.

| # | Symptom | Why it reads as cheap | Fix |
|---|---------|----------------------|-----|
| **X1** | A card has a border **and** a shadow **and** a background tint | Four voices saying one thing; no confidence in any | Keep exactly one (§2 one-separation rule) |
| **X2** | Shadows are `rgba(0,0,0,.15+)`, tight blur, varying directions | Black shadows read as dirt; many suns read as chaos | The §3 elevation scale; tint with ink; opacity ≤ 10% |
| **X3** | A saturated colour across a large area (hero, sidebar, card fill) | Chroma is loudest where it is largest | The inverse-area law (§4): large = low chroma |
| **X4** | Two or more accents competing (blue CTA, green badge, orange tag) | No hierarchy of colour = no hierarchy of meaning | One accent, one hue family (§4.1) |
| **X5** | A gradient used as decoration (header, button, card) with no material reason | Decorative gradients are the single strongest 2008 signal | Remove it — or make it the world's **material** (DNA axis 1), never garnish |
| **X6** | Radii differ per component (16 on cards, 8 on inputs, 4 on buttons) | Nobody chose; each part chose for itself | ONE radius language from the world; nested radii concentric (§6) |
| **X7** | Font sizes off the scale (15, 17, 23px), more than 6 sizes in one view | The eye reads the disorder even when it cannot name it | The modular scale (§5.1) |
| **X8** | Almost everything is 600/700 weight | Bold everywhere = no hierarchy, and it looks "loud and cheap" | 400/500/600 with one 700 (§5.3) |
| **X9** | Pure greys (#888, gray-500), pure black text (#000) | Untinted neutrals belong to no world; #000 text is harsh and lifeless | Tint neutrals to the material (§4.2); text is ink, not black |
| **X10** | Icons at mixed stroke widths / mixed sets / emoji as icons | The most visible sign that nobody was in charge | One set, one stroke width (1.5), one size ladder (16/18/20) |
| **X11** | Proportional numerals in a numeric column; numbers aligned left | Instantly amateur; also actually unreadable | `tabular-nums` + right-align (§5.4, §6) |
| **X12** | Three identical "icon + title + text" cards as the page's main argument | The universal template of a product with nothing to say | Asymmetry and hierarchy: one thing matters more (§1, §8) |

**Verdict:** 1–2 hits → 🟡, fix in the same iteration. 3+ hits → 🔴, the screen is not "polishable" — it was never composed.

---

## §10. THE REDUCTION PROTOCOL — how to find the design

The Chanel rule, systematised into a procedure you can actually run.

```
BEFORE SHIPPING ANY SCREEN:

1. List every element on it.
2. Remove the one you would defend LEAST.
3. Look again. Did the screen get worse?
      NO  → it was noise. It is gone. Repeat from step 2.
      YES → put it back. You have found the edge.
4. THE EDGE IS THE DESIGN. A screen where removal finally HURTS is a finished screen.
   A screen where you can still remove things is a draft with decoration on it.
```

**The corollary for a whole product:** the same procedure at the level of features is the reason expensive
products feel calm. Craft is mostly subtraction; the additive instinct is the amateur one.

---

## §11. THE FLOOR — the default operational system (when there is NO concept)

> **The rule this exists to enforce:** the absence of a concept is **NOT a licence to invent**.
> It is a licence to take the floor. An internal admin, a tool, an ops panel does not need a *world* —
> it needs **craft**. Improvisation here is exactly how 2008 gets in.

When a screen is operational (`/admin`, `/app`, internal tooling) **and** no `VISUAL_CONCEPT` applies —
apply this system verbatim. Do not choose colours. Do not choose sizes. They are chosen.

```css
:root {
  /* NEUTRALS — tinted slightly cool; never pure grey (§4.2) */
  --ink:          28 30 34;      /* text, shadow tint. NOT 0 0 0 */
  --ink-2:        94 99 108;     /* secondary text */
  --ink-3:        139 145 155;   /* tertiary, placeholders */
  --ground:       247 248 250;   /* page background */
  --surface:      255 255 255;   /* cards (tonal step: lighter than ground) */
  --sunken:       241 243 246;   /* wells, inputs, code */
  --hairline:     28 30 34;      /* used at 6–10% opacity, never 100% */

  /* ONE ACCENT — one hue family (§4.1) */
  --accent:       47 106 217;
  --accent-hover: 38  92 196;
  --accent-wash:  47 106 217;    /* used at 6–10% for selected rows / tints */

  /* STATUS — muted to the system; tinted bg + coloured border, never a fill (§4.4) */
  --ok:   34 122  84;   --warn: 176 118  20;   --err: 190  56  56;

  /* ELEVATION — one light, ink-tinted (§3) */
  --e0: none;
  --e1: 0 1px 2px  rgba(var(--ink) / .04);
  --e2: 0 2px 8px  rgba(var(--ink) / .06), 0 1px 2px rgba(var(--ink) / .04);
  --e3: 0 8px 24px rgba(var(--ink) / .08), 0 2px 6px rgba(var(--ink) / .04);

  /* SPACE — one scale (§7) */
  --s1: 4px; --s2: 8px; --s3: 12px; --s4: 16px; --s5: 24px; --s6: 32px; --s7: 48px;

  /* TYPE — modular 1.200, six sizes (§5.1) */
  --t-label: 12px; --t-body: 14px; --t-lead: 16px;
  --t-h3:    19px; --t-h2:   23px; --t-h1:   28px;
  --lh-tight: 1.25; --lh-body: 1.55;
  --w-body: 400; --w-data: 500; --w-title: 600;

  /* FORM */
  --r-sm: 4px;  --r-md: 8px;  --r-lg: 12px;   /* nested radii stay concentric (§6) */
  --stroke: 1.5;                              /* one icon stroke width, everywhere */
  --row-compact: 32px; --row-default: 40px; --row-comfortable: 48px;
}
```

**Floor rules (non-negotiable):**
- Cards: `--surface` on `--ground` + `--e1`. **No border.** (One separation method — §2.)
- Row dividers: `rgba(var(--hairline) / .08)`. Never a zebra stripe.
- The selected row: `rgba(var(--accent-wash) / .07)` + a 2px left accent bar. Not a full fill.
- Numbers: `tabular-nums`, right-aligned. Always.
- Chrome (sidebar, header): `--ground`, hairline separation, **no shadow**. It must disappear (§8).
- One primary button per view. Secondary = ghost. Destructive = text/ghost in `--err`, never a big red block.
- Focus: a 2px accent ring **only** — no glow, no shadow, no geometry change.

**The floor is not a ceiling.** It is the guaranteed-professional baseline. If the product deserves a world,
`VISUAL_CONCEPT` overrides the palette/type/effects — but **§1–§10 still apply**: a world executed without
craft is just a more colourful 2008.

---

## §12. ACCEPTANCE — how craft is verified (it is not "taste", it is checkable)

| Who | When | What |
|-----|------|------|
| **@CREATOR** | at the concept (Step 5.5.A) | craft applies to the concept too: §1 (one signature, not three), §4 (the accent's area budget), §5.2 (the display contrast is real) |
| **@DESIGN** | before every SPEC | §11 if there is no concept (do not improvise); §5.1 scale, §3 elevation, §7 spacing named as **numbers** in the SPEC |
| **@DESIGN** | in AUDIT | run the **§9 cheapness detector** — 12 signs. 3+ hits = 🔴, the screen is recomposed, not polished. Then §10 reduction. |
| **@FRONTEND** | before handoff to @DEV | owner of the scales: the type scale, the spacing scale, the elevation scale and the floor tokens live in the theme, not in components |
| **@DEV** | while writing code | every value comes from a token. A raw hex, a raw px gap, a hand-rolled shadow = a defect, not a shortcut |
| **@QA_VISUAL** | after @DEV | the §9 detector as a checkable vector (X1–X12 are all greppable/measurable from the render + code) |

**Grep invariants (🔴 in code review):**
```
box-shadow with rgba(0,0,0            → X2  (untinted shadow)
#[0-9a-f]{3,6} outside theme files    → raw hex in a component
padding|gap|margin: (13|15|17|18|22)px → X7/§7 (off the spacing scale)
font-size: (15|17|21|23)px  (if off the project's scale) → X7
stroke-width mixed / icon sets mixed  → X10
a numeric <td>/<Table.Td> without tabular-nums → X11
```

---

Reference: `roles/CONCEPT_ANATOMY.md` (the 8 DNA axes — the world's genetics; craft executes them) · `roles/CONCEPT_DNA_LIBRARY.md` (the worlds as presets) · `roles/VISUAL_CONCEPT_PROTOCOL.md` (TASTE GATE, C1–C10 clichés — the world-level filter; §9 here is the craft-level filter) · `roles/LAYOUT_COMPOSITION.md` (the grammar of space) · `roles/LAYOUT_INVARIANTS.md` (geometry under content) · `roles/FRONTEND_DESIGN_EXCELLENCE.md` (the operational contour) · `roles/INTERFACE_CRAFT_CANON.md` (the craft of operational interaction — the sister canon) · `roles/ROLE_DESIGN.md` · `roles/ROLE_FRONTEND.md` · `roles/ROLE_QA_VISUAL.md`
Version: 1.0 | 2026-07-11
