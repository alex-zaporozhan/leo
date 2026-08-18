# CONCEPT_ANATOMY.md
# The anatomy of a visual concept: what it consists of, how it is born, what a reference is obliged to transfer.
# The generative core of the taste layer. The 12 worlds of `CONCEPT_DNA_LIBRARY.md` are ready assemblies of THIS anatomy, not a ceiling.
# Read by: @CREATOR (Step 5.5.A), @DESIGN (SPEC/RESKIN), @MOTION (Step 0). Universal: independent of the UI library.

> **Why this file exists.** A library of presets answers "what to take", but does not teach "how to think". Here is the mechanics itself:
> a concept = the coherent answer of EIGHT axes to one question. Whoever owns the anatomy needs no list —
> they assemble a world for any niche from the tables below, deterministically, without "inspiration".

---

## §1. WHAT A CONCEPT IS (a definition, not a mood)

**A concept is the answer to the question: "which OBJECT from the niche's world became the screen?"**

Not a "style", not a "palette+font", not "a reference I liked". An object: for a notary it is the crested sheet on the desk,
for gloss — a magazine spread, for a car service — a dashboard, for a confectioner — a display case with hand-written price tags.
The object dictates everything else through the 8 axes (§2). The fixing formula:

```
CONCEPT PHRASE: "The screen is [an object], therefore [the material] sets [the light], [the colour-logic] and [the typo-character],
it moves like [the motion-physics], and it is recognised by [the signature]".
```

If the phrase does not assemble — there is no concept, only a set of taste decisions. Stop, back to the niche's objects.

**The concept test (3 checks, all — TASTE GATE blockers):**

| Test | Question | Failure |
|------|----------|---------|
| T1 "Remove the logo" | Does the screen without the logo remain about THIS niche? | "Universal SaaS" — it could be anyone's |
| T2 "Whose world" | Can you name the carrier-object in one word? | "Well, modern and clean" — there's no object |
| T3 "Trace" | Is every decision derived from the object? | The accent is "just pretty", the font "I like it" |

---

## §2. THE EIGHT DNA AXES — what any concept consists of

Every axis is a CHOICE FROM A TABLE, not a generation. A cheap model walks the axes top to bottom;
every answer line is one decision + a token. Together — the DNA passport (§5).

### Axis 1 — MATERIAL (what substance the object is made of)

The material is the root of the DNA: it dictates the light (axis 2) and the texture of the effect-kit.

| Material | Texture on the screen | Preset worlds |
|----------|-----------------------|---------------|
| Matte paper / a letterhead | grain 2–3%, fibre, a warm base | INK & SEAL, MUSEUM |
| Coated print / gloss | pure white, a black slab, a spread highlight | GLOSSY EDITORIAL |
| Kraft cardboard | cardboard texture, tape, stamps | CRAFT PAPER |
| Glass | transparency, blur, refraction (public site only!) | — |
| Metal (chrome/aluminium) | a highlight gradient, cold neutrals | CHROME VELOCITY |
| Fabric / velvet | deep matte, soft vignetting | LUXE NOIR |
| An instrument screen / phosphor | a dark base, glow, scan lines | INSTRUMENT PANEL |
| Pop plastic / vinyl | dense fills, stickers, hard shadows | ELECTRIC POP, NEO-BRUTAL |
| Ceramics / stone / plaster | light matte, pedestal shadows | SOFT CLINIC, MUSEUM |
| Botanical / apothecary | a cream background, a botanical line | APOTHECA BOTANICA |

### Axis 2 — LIGHT AND SHADOW (how the material is lit) — follows from axis 1

| Light | Shadow-recipe (copy it) | Compatible with materials |
|-------|-------------------------|---------------------------|
| Flat print | no shadows; borders — 1–2px lines | print, gloss, poster |
| Soft diffused ("a sheet on the desk") | `0 1px 2px rgba(ink,.06), 0 8px 24px rgba(ink,.07)` | paper, ceramics, fabric |
| Hard offset | `Npx Npx 0 var(--ink)` without blur | kraft, pop plastic, poster |
| Studio pedestal | low and wide: `0 24px 48px -24px rgba(0,0,0,.35)` + a highlight on top | metal, glass, luxe |
| Glow | `0 0 0 1px accent, 0 0 24px rgba(accent,.35)` | phosphor, neon |

### Axis 3 — COLOUR LOGIC (a strategy, not a hex)

The hexes come later (from the object and the material); first the LOGIC of distribution is chosen:

| Logic | The 60/30/10 formula | Where the accent comes from |
|-------|----------------------|------------------------------|
| Monochrome + 1 accent | the material's neutrals 90% + one colour | the most charged object of the niche (sealing wax, lipstick carmine, a stamp) |
| Printing inks | paper + ink + 1 press colour | the print/stamp colour of the document |
| Duotone | two equal + a neutral | a pair from the object (a neon sign: pink+turquoise) |
| Natural pigments | earthy/botanical, low saturation | the pigments of the niche's raw material (pine, clay, spices) |
| Neon on dark | a dark base 80% + phosphor | instrument indicators, a sign at night |
| Pastel polychrome | a light base + 3–4 sticker colours of equal lightness | the product's packaging/labels |

**Rules:** the neutrals INHERIT the material (paper → warm greys `#F6F1E7…`; metal → cold `#0B0C10…`);
the accent has a trace to the object (T3); status colours (success/error) live OUTSIDE this logic — from
`FRONTEND_DESIGN_EXCELLENCE` (the operational canon), they are muted to the world but not replaced.

### Axis 4 — TYPOGRAPHIC CHARACTER (a display voice + a text voice)

The choice = a pair of "voices", not a pair of "fonts". Representatives — with verified Cyrillic:

| Display voice | Character | Cyrillic representatives |
|---------------|-----------|--------------------------|
| Didone / high contrast | gloss, luxe, museum | Prata, Playfair Display, Cormorant (+Infant) |
| Book antiqua | document, knowledge, care | EB Garamond, Literata, Vollkorn, Lora, Source Serif 4 |
| Slab / Egyptian | kraft, craft, weight | Bitter, Podkova |
| Industrial grotesque | grid, speed, system | Inter Tight, Jost, Commissioner |
| Display accidental | pop, poster, boldness | Unbounded, Russo One, Rubik Mono One, Days One |
| Mono | instrument, code, precision | JetBrains Mono, IBM Plex Mono |
| Slanted technical | speed, sport | Exo 2 (Italic) |
| Handwritten (accent only!) | notes, humanity | Caveat, Neucha |

| Text voice (working) | Representatives |
|----------------------|-----------------|
| Humanist grotesque | Golos Text, Onest, Manrope, Nunito Sans, PT Sans |
| Neutral grotesque | Inter, Commissioner |
| Book antiqua for reading | Literata, Lora, PT Serif |

**Pair rules:** a contrast of voices is mandatory (didone+humanist ✓, two neutral grotesques ✗ — a faceless pair);
handwritten — accent only (signatures, notes), never body; the display/text scale contrast ≥ 2.5× on a public site.
⚠️ Latin-only (Space Grotesk, Syne, Italiana, Bodoni Moda, Orbitron, Anton, Bebas Neue GF) — a flag for @LAWYER/a replacement
when the content is Cyrillic.

### Axis 5 — FORM AND GEOMETRY

| Decision | Options | Consequence |
|----------|---------|-------------|
| Radius language | 0 (print/Swiss/brutal) · 2–4 (document) · 8–12 (soft) · 16+ / pill (pop, clinic) | the theme's `defaultRadius`, the shape of buttons |
| Line | hairline 1px · pen 1.5px · poster 2–3px | borders, dividers, icons (a single stroke-width!) |
| Corners | straight · a cut `clip-path` (speed) · "a page corner" (paper) | the card language |
| Density | air (luxe: padding 32–48) · working (16–24) · dense (instrument: 8–12) | the spacing increment, axis 6 |

### Axis 6 — COMPOSITIONAL PRINCIPLE (how the object organises the plane)

| Principle | Essence | Hero affinity (`HERO_ARCHETYPES`) |
|-----------|---------|-----------------------------------|
| Swiss grid | module, alignment, nothing extra | B, C |
| Editorial asymmetry | spreads, a large photo, pull-outs | C, E, G |
| Centred monument | one object on a pedestal | B, D |
| Catalogue strip | labelling, passe-partout, seriality | F, G |
| Instrument panel | indication zones, a mono grid | D, H |
| The craftsman's desk | objects "lie" there, a light disorder under a grid | E, G |

### Axis 7 — MOTION PHYSICS (how much the world weighs and how it rubs)

The vocabulary of personalities and the tokens — `roles/MOTION_AMBITION_DIAL.md` (absorption · snap · spring · velvet · acceleration · page-turn).
Here — the selection rule: the physics = the behaviour of the MATERIAL of axis 1 (paper absorbs; an instrument clicks; gloss turns pages;
velvet flows). Inherited by @MOTION at Step 0; ambition is a separate dial, it does not change the physics.

### Axis 8 — SIGNATURE (one owned element)

The signature criteria — all five are mandatory:

```
□ Originates from the niche's object (passes the T3 trace)
□ Reproducible: CSS/SVG ≤ 30 lines, without heavyweight assets
□ Recognisable within 1 second of showing, without the logo (T1)
□ Lives on at least 3 carriers: hero · an interaction (button/card) · a detail (divider/bullet/loader)
□ Not from the ban-list C1–C10 (VISUAL_CONCEPT_PROTOCOL §4)
```

Examples of the right calibre: a wax seal on confirmation; a gloss-sweep across the CTA; a ticket perforation as a divider;
a carriage-cursor in the heading; a passe-partout around media. "A gradient in the header" is not a signature (it passes neither T1 nor T3).

---

## §3. AXIS-COHERENCE RULES (why worlds are whole)

The axes are not independent. Coherence is what distinguishes a concept from a collage:

```
Material → Light:    paper→soft/flat · print→flat or offset · metal→studio · phosphor→glow
Light → Radius:      a hard offset shadow→radius 0–4 · soft→8–16 · glow→any · flat→0–8
Colour logic → Text: a dark base→display may glow, body NOT thinner than 400 · a light one→ink with contrast ≥ 7:1 on a public site
Typo-voice → Density: didone/antiqua→air · mono/industrial grotesque→working/dense
Material → Physics:  see axis 7 (the physics = the behaviour of the material)
```

**Forbidden combinations (incoherent DNA, an automatic 🔴 at the TASTE GATE):**
a glassmorphism surface in a paper/print world · a neon glow on a pastel polychrome ·
didone-luxe + brutal offset shadows · a handwritten display in an instrument world ·
spring physics in a document world (paper does not spring — it absorbs).

**The paired-replacement rule:** an axis cannot be changed alone — changed the material → revisit the light and the physics;
changed the colour logic → revisit the text contrast. (This is also the mechanics of FUSION and RESKIN.)

---

## §4. THE REFERENCE PROTOCOL — what a reference is OBLIGED to transfer

> A reference ≠ inspiration and ≠ "make it similar". A reference is a donor of EXACTLY ONE extracted technique.
> A record without an extraction = a mood picture; it is not admitted into a concept/SPEC.

### 4.1 The record format (mandatory, 4 lines)

```
REFERENCE [N]: [the source — a concrete carrier, not a brand in general: "a Kinfolk spread", not "magazines"]
EXTRACT:   [one technique, one of the categories: COMPOSITION | MATERIAL/LIGHT | TYPOGRAPHY | MOTION]
TRANSFER:  [what it turns into for us — a concrete token/class/pattern, 1 line]
NOT TAKING: [what in the source is alien to our DNA — a mandatory line]
```

**The "the reference transferred" criterion:** from these 4 lines a third person reproduces the technique WITHOUT seeing the source.
Doesn't reproduce it → the extraction didn't happen, redo the record.

### 4.2 Rules for the set of references per concept

- **2–4 references**, each for its own category (not four "about composition").
- **≤ 1 from SaaS/the web.** At least one — from an indirect world (print/objects/environments): an adjacent material gives
  a signature, a direct competitor gives a cliché.
- The references serve the DNA, they do not replace it: first the §2 axes, then the technique donors. The reverse order
  ("found something pretty → let's stretch it on") is the road to a collage.
- **A search engine's output for a cluster is NOT an aesthetic reference.** The top-10 competitors are a map of intents and
  content minimums (@SEO), and at the same time an ANTI-reference for differentiation: coinciding with their visual default
  fails T1 "remove the logo". The meanings come from demand, the presentation — from the world.

### 4.3 A typology of sources — a universe, not a list of sites

| World of sources | What to take there | Examples of carriers |
|------------------|--------------------|----------------------|
| Print | grids, spreads, labelling, typography | magazines and books, auction catalogues, posters, tickets, crested letterheads, packaging, menus |
| Object | material/light, signatures, form | instruments and tools, display cases, signs, watches, vials, a notary's stationery |
| Environmental | the atmosphere of light, density, passe-partout | museums, clinics, ateliers, old-school pharmacies, markets, car showrooms |
| Screen | motion moves, titles, indication | film titles, instrument interfaces (aviation/automotive), games, oscilloscopes |
| Web (≤1) | ONLY operational patterns or one pointwise move | a concrete page of a concrete product |

The names in this table are search directions, not a closed list: any carrier will do,
if the 4.1 record assembles and "NOT TAKING" is filled in honestly.

---

## §5. THE CONCEPT CONSTRUCTOR (the main path; presets are an accelerator)

```
STEP 0. OBJECTS: write out 5 physical objects from the niche's world (what lies on the desk / hangs on the wall
       / the client holds in their hands). Not abstractions — objects.
STEP 1. THE CARRIER: choose one carrier-object for the screen. Check: it explains WHAT the user
       "holds in their hands" while looking at the site.
STEP 2. AXES 1–8: walk the §2 tables top to bottom; each axis = one line "decision → token".
       Coherence per §3 is checked at every step by the paired dependency.
STEP 3. THE DNA PASSPORT: assemble a table of 8 rows (axis · decision · token/value · the trace to the object).
STEP 4. REFERENCES: 2–4 records by the §4 protocol — donors of techniques for the already-chosen DNA.
STEP 5. THE CONCEPT PHRASE (§1) + the signature (axis 8) + the three "NEVER"s (what this world does not do).
STEP 6. TASTE GATE: T1–T3 + the ban-list C1–C10 + the forbidden combinations of §3. A failure of any = back to the step that failed.
```

**When to take a preset from `CONCEPT_DNA_LIBRARY`:** the niche hit the router and the preset's DNA matches steps 0–2
on ≥ 6 axes of 8 → take the preset, record the divergences as replacement lines. A match of < 6 axes → the constructor.
**FUSION** = a preset with a replacement of ≤ 2 axes by the paired-replacement rule §3 (70/30). More than two axes — that is already
a custom world, assemble it honestly through the constructor.

**Economics:** the constructor is work by tables, available to a cheap model. A strong model is needed only if
the niche yields no objects (the rare case of pure abstractions) — and those are the very 1–2 calls from
`VISUAL_CONCEPT_PROTOCOL §7`.

---

## §6. THE OUTPUT — WHAT GOES INTO VISUAL_CONCEPT_[PROJECT].md

The concept artifact (the template — `VISUAL_CONCEPT_PROTOCOL §3`) must contain: the concept phrase ·
the 8-row DNA passport · the references in the 4.1 format · the copy-paste token block · the signature with 3 carriers ·
the three "NEVER"s · the TASTE GATE mark. The project passports (Step 5.5.B) are derived from the DNA passport line by line:
axes 3–4 → DESIGN/TYPOGRAPHY_PASSPORT, axes 5–6 → UI_COMPOSITION, axis 7 → MOTION_LANGUAGE.

---

Reference: `roles/CONCEPT_DNA_LIBRARY.md` (the presets) · `roles/VISUAL_CONCEPT_PROTOCOL.md` (the process, TASTE GATE, RESKIN) · `roles/HERO_ARCHETYPES.md` · `roles/MOTION_AMBITION_DIAL.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` (the operational canon and status colours) · `roles/ROLE_CREATOR.md` Step 5.5
Version: 1.0 (system v6.20) | 2026-07-06
