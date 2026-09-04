# CONCEPT_DNA_LIBRARY.md
# Library of concept worlds: material, light, typography, motion personality — as executable recipes.
# Used by @CREATOR (VISUAL_CONCEPT), @DESIGN (SPEC / RESKIN), @MOTION (CONCEPT — inherits motion personality).
# This is the semantic layer ON TOP OF DESIGN_DECISION_LIBRARY: the world sets the MEANING of tokens, the decision-library — operational models (nav/cards/buttons).
# [v6.20+] The 12 worlds below are PRESETS of the `roles/CONCEPT_ANATOMY.md` anatomy (8 DNA axes). A preset is used when ≥6
# axes match the niche; otherwise the world is assembled with the constructor `CONCEPT_ANATOMY §5` — the universe is not limited to this list.

> **Principle:** "Taste is not a list of sites in the model's head. It is a library of worlds with ready-made recipes: exact hex values, exact font pairs, exact CSS effects, exact physics of motion. A model whose taste lives in a file assembles. A model whose taste lives in weights invents — and on cheap weights it invents badly."
>
> **Second principle:** "A concept is an answer to the question 'from what world is this page made?' Not 'which colours are beautiful', but: a glossy editorial spread? the stamp paper of a notary? a cockpit? a poster on the wall? The material of the world dictates everything: palette, font, shadow, radius, the sound of motion."

---

## HOW TO USE

1. **@CREATOR** at the VISUAL CONCEPT step (`roles/VISUAL_CONCEPT_PROTOCOL.md`) goes through the World Selection Protocol → fixes the world (or FUSION of two) in `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` → **copies recipes as-is**. Palette and font pairs are not "re-generated in the spirit of" — they are taken from the world; replacing any value is a conscious decision with one explanatory phrase.
2. **@DESIGN** in SPEC works within the project world (world = Tier 0 of the golden library). In **RESKIN** mode, changes the world entirely by the protocol — structure stays, the skin changes.
3. **@MOTION** in CONCEPT inherits the world's **motion personality** (easing/durations/signature move) and develops it with `MOTION_LIBRARY` techniques — does not bring "Linear-style" into a world where the material is different.
4. **@DEV** receives values through project passports — this file does not end up in project `docs/` (private layer rule).

**Rails do not move:** any world is executed on top of `LAYOUT_INVARIANTS.md` (§1–§12), `MOTION_LIBRARY §VII` (perf), WCAG AA and `prefers-reduced-motion`. The world changes character, not the right to break geometry and accessibility.

---

## WORLD SELECTION PROTOCOL (3 questions → world)

```
Q1. MATERIAL: what is the product "made of" in the mind of the client's client?
    (paper and print? glossy spread? metal? fabric? shop window glass? instruments? a stage?)
    Name 2–3 physical objects from the niche world. Not UI words — objects.

Q2. AUDIENCE POSTURE: how does the buyer want to feel, buying this?
    (respectably · luxuriously · caringly · boldly · precisely · homely · technologically · festively)

Q3. BRAND AMBITION: the dial from roles/MOTION_AMBITION_DIAL.md
    (restrained · confident · bold · experimental)
```

The intersection yields 1–2 candidates from the routing table (at the bottom of the file). With two — the winner rule (`ROLE_DESIGN`): @CREATOR/@DESIGN names one with justification through material (Q1).

**Strict selection rules:**
- **Grey SaaS** — the neutral, low-chroma, hairline-and-density minimalism the whole category converged on — is **not a default world for the showcase**. It is the operational contour (`FRONTEND_DESIGN_EXCELLENCE §2`) and an acceptable choice only when Q1 genuinely is "tool/data".
- The world is chosen by niche objects (Q1), not by the author's taste or the last successful project.
- If no world fits — Custom World Constructor (below), not "Inter + blue".

## FUSION RULE (mixing worlds)

No more than **two** worlds in a ~70/30 ratio:
- **Dominant (70%)** sets: background, surfaces, body typography, radii, base motion.
- **Accent (30%)** sets: only accent colour, 1 effect from its own effect kit, display font OR signature element — one or two things, not everything.
- Forbidden: three worlds; 50/50; mixing two display fonts from different worlds; effect kits of both worlds simultaneously.
- Record in VISUAL_CONCEPT: `FUSION: [Dominant world] 70 / [Accent world] 30 — accent contributes: [what exactly]`.

---
---

## WORLD 1 — GLOSSY EDITORIAL

**Feel:** desirable, expensive, "Vogue spread".
**For:** fashion, beauty, premium B2C services, personal brands, events, D2C products with strong photography.
**NOT for:** dense operational tools, regulatory contexts, "budget" pricing.
**Compositional references:** print magazine spreads (Vogue/Harper's as composition, not content), fashion house sites, Apple product pages (photography direction), Readymag showcases.
**Hero archetypes:** C (Full-Bleed Type), F (Editorial), D (Product-Immersive). **Ambition:** bold.

### Palette (copy-paste)
```css
:root {
  --paper: #FFFFFF;        /* magazine spread — pure white only */
  --ink: #0A0A0A;          /* typographic ink */
  --ink-2: #52525B;        /* captions, footnotes */
  --line: #E4E4E7;         /* hairlines */
  --accent: #C81D25;       /* carmine; alternatives: klein #002FA7, fuchsia #D6006E — ONE per project */
  --accent-ink: #FFFFFF;
  --plate: #0A0A0A;        /* "black spread" — inverse section-spreads */
  --plate-ink: #F5F5F4;
  --gloss: rgba(255,255,255,.55); /* gloss sheen */
}
```
| Role | Pair | Rule |
|------|------|---------|
| Spread text | `--ink` on `--paper` | maximum contrast, AA with margin |
| Inverse spread | `--plate-ink` on `--plate` | whole sections, not cards |
| Accent | ≤ 5% of area | page number, one word, one CTA |

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Prata** (didone) | ✅ | H1 96–160px, flush against grid edge; alternative Playfair Display ✅ |
| Body | **Manrope** or **Golos Text** | ✅ | 16–18px, tracking 0 |
| Captions/headers | Inter, caps 11px, letter-spacing .12em | ✅ | like magazine photo captions |
Latin luxury display variant (Italiana, Bodoni Moda) — only if the entire heading layer is in Latin; record in the typography passport License Gate.

### Surface and light
- Radii: **0px** (magazine spread does not round); exceptions — avatars/pill-tags 999px.
- No card "boxes": composition = full-bleed photo + text + hairlines `1px var(--line)`.
- Shadows nearly absent; depth — through photo/text overlap (text can extend 8–16px onto photo).

### Effect kit (signature techniques)
```css
/* 1. Gloss sheen — sweeps across photo/CTA on hover (showcase only) */
.gloss { position: relative; overflow: hidden; }
.gloss::after {
  content:''; position:absolute; inset:0;
  background: linear-gradient(105deg, transparent 40%, var(--gloss) 50%, transparent 60%);
  transform: translateX(-120%); transition: transform 900ms cubic-bezier(.77,0,.18,1);
  pointer-events:none;
}
.gloss:hover::after { transform: translateX(120%); }

/* 2. Folio number — giant figure as in a magazine section header */
.folio { font-family: Prata; font-size: clamp(96px, 14vw, 200px); line-height: .85;
         color: transparent; -webkit-text-stroke: 1px var(--ink); }

/* 3. Photo credit caption */
.credit { font: 600 10px/1 Inter; letter-spacing:.14em; text-transform:uppercase;
          color: var(--ink-2); writing-mode: vertical-rl; }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-page` | `cubic-bezier(.77, 0, .18, 1)` — "page turn" |
| `--dur-page` | 600ms · gloss hover 900ms · captions 300ms |
| Signature move | sections shift horizontally / curtain like flipping; photos "breathe" scale 1→1.04 over 6s |
| MOTION_LIBRARY hooks | `T4` split-typography, `S1` horizontal section, `I4` hover distortion (photos), `TR1` curtain |
| Forbidden | standard fade-up cards "like everyone else"; spring bounce |

### Mantine skin
`defaultRadius: 0`; `headings.fontFamily: Prata`; Button → variant "filled" = black plate `--ink`, uppercase, letter-spacing .08em, no radius; Card/Paper practically unused on showcase — sections and hairlines; operational contour (`/admin`) stays per `FRONTEND_DESIGN_EXCELLENCE §2` with the world's accent.

### Signature element (ideas)
Vertical "spine" with section name along the edge · giant folio-number for section · inverse black spread in the middle of the page.

### Anti-patterns
Rounded "SaaS cards" with shadows · three-column icon-heading-text · pastels · stock smiles instead of art photos.

---
---

## WORLD 2 — INK & SEAL

**Feel:** notarially, weighty, "a document one trusts".
**For:** notaries, lawyers, audit, assessment, insurance of trust, premium accounting, official registries.
**NOT for:** entertainment and youth products, discounters.
**Compositional references:** forms and stamp paper, book typography (print-like editorial sites as a web example of "print" site), iA Writer (the silence of the page).
**Hero archetypes:** B (Centered Statement), F (Editorial). **Ambition:** restrained–confident.

### Palette (copy-paste)
```css
:root {
  --paper: #F6F1E7;        /* ivory, warm */
  --paper-deep: #EFE7D6;   /* second sheet / backing */
  --sheet: #FCF9F2;        /* "top sheet" for forms and cards */
  --ink: #2B2620;          /* iron-gall ink (warm near-black) */
  --ink-2: #6B6155;
  --line: #D8CDB8;         /* form ruling line */
  --seal: #8C2F2F;         /* sealing wax */
  --seal-ink: #F6F1E7;
  --gilt: #9A7B4F;         /* gilded edge — ONLY details ≤2% */
}
```
Rules: background is never pure white or grey; `--seal` red — only stamp/signature/one CTA; status semantics (`success/error/...`) stays standard but in muted earthy tones.

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display + Body | **EB Garamond** or **Literata** | ✅ | document set in one typeface; H1 44–72px, body 17–18px/1.65 |
| Reference numbers | **IBM Plex Mono** | ✅ | case numbers, dates, amounts — tabular |
| Small caps | `font-variant-caps: small-caps; letter-spacing:.06em` | — | form section headers |
Alternative for "formal austerity": Old Standard TT ✅.

### Surface and light
- Radii: 2px (paper sheet barely rounds).
- "Sheet" instead of card: `background: var(--sheet); border: 1px solid var(--line); box-shadow: 0 1px 0 #fff inset, 0 12px 24px -18px rgba(43,38,32,.35);` — sheet rests on desk, does not float.
- Dividers: double ruling `border-top: 3px double var(--line)` for major sections.

### Effect kit
```css
/* 1. Letterpress — embossed impression for headings on paper */
.letterpress { color: var(--ink); text-shadow: 0 1px 0 rgba(255,255,255,.55); }

/* 2. Seal (wax/stamp) — signature trust mark */
.seal { display:grid; place-items:center; width:88px; aspect-ratio:1; border-radius:50%;
  border: 2px solid var(--seal); color: var(--seal);
  font: 700 10px/1.2 'EB Garamond'; text-transform: uppercase; letter-spacing:.12em;
  transform: rotate(-6deg); box-shadow: inset 0 0 0 4px var(--paper), inset 0 0 0 5px var(--seal); }

/* 3. Paper grain — one layer on body, barely perceptible */
.grain::before { content:''; position:fixed; inset:0; pointer-events:none; opacity:.05;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.9' numOctaves='2'/%3E%3C/filter%3E%3Crect width='120' height='120' filter='url(%23n)' opacity='.6'/%3E%3C/svg%3E"); }

/* 4. Signature — handwritten flourish (SVG stroke-dashoffset, drawn once) */
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-ink` | `cubic-bezier(.25,.46,.45,.94)` — ink is absorbed |
| `--dur-ink` | appearance 500ms (opacity + blur 2px→0); stamp 260ms `cubic-bezier(.2,.9,.3,1.3)` (slight press-down) |
| Signature move | "stamp is applied": scale(1.12)→1 + rotate(-8deg→-6deg) on confirmation/form submission |
| MOTION_LIBRARY hooks | `T5` line reveal (like document lines), `B3` grain (static), signature flourish — SVG line-draw |
| Forbidden | gloss, springs, floating cards, neon glows |

### Mantine skin
`defaultRadius: 2`; `fontFamily: Literata/EB Garamond`; shadows redefined as "sheet on desk" (see above); Button primary = `--ink` fill with `--paper` text (ink plate), `--seal` — only one "main" CTA per page; Input → `variant: unstyled` + bottom form ruling `border-bottom:1px solid var(--line)`.

### Signature element (ideas)
Round stamp with rotation on a key promise · mono reference line under the hero ("Registry № · Licence № · Since 20XX") · "stitched binding" — dotted thread between sections.

### Anti-patterns
Pure #FFF background · blue SaaS buttons · glassmorphism · md/lg shadows on "sheets" · emoji icons.

---
---

## WORLD 3 — CRAFT PAPER

**Feel:** by hand, honest, neighbourhood warmth.
**For:** bakeries, coffee shops, workshops, local brands, hand-made marketplaces, children's studios, farm produce.
**NOT for:** enterprise, finance, "expensive" luxury.
**References:** craft packaging, chalk price tags, stickers on a box; on the web — local brand showcases and Etsy aesthetics (compositionally).
**Hero archetypes:** H (Bento), F (Editorial), A (if product-as-tool). **Ambition:** confident.

### Palette (copy-paste)
```css
:root {
  --kraft: #E9DCC3;        /* kraft cardboard — background */
  --sheet: #FBF7EE;        /* white sheet/label */
  --ink: #3B3126;          /* dark sepia */
  --ink-2: #7A6A54;
  --line: #CBB893;
  --stamp: #2F6F4F;        /* rubber stamp green — primary */
  --stamp-ink: #FBF7EE;
  --tomato: #C8552D;       /* wax-tomato — second accent, ≤3% */
  --tape: rgba(244,238,224,.9); /* "sellotape" */
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Bitter** (slab) or **Podkova** | ✅ | like a stamp impression, 40–72px |
| Body | **PT Sans** or **Nunito Sans** | ✅ | 16–17px, friendly |
| Handwritten accent | **Caveat** / **Neucha** | ✅ | 1–2 words: "fresh!", master's signature — not paragraphs |

### Surface and light
- Radii: 10–14px (soft cardboard); for "labels" — 999px or irregular `border-radius: 45% 55% 50% 50% / 55% 45% 55% 45%`.
- "Label on kraft": `background: var(--sheet); box-shadow: 0 2px 0 rgba(59,49,38,.15), 0 10px 20px -14px rgba(59,49,38,.35); transform: rotate(var(--tilt, -1deg));` — slight organic tilt ±1.5°, alternate between adjacent cards.
- Dotted stitch line: `border: 2px dashed var(--line)` for coupons/promotions.

### Effect kit
```css
/* 1. Stamp impression for badges */
.stamp { color: var(--stamp); border: 2.5px solid currentColor; border-radius: 8px;
  padding: 4px 10px; font: 800 12px/1 'Bitter'; text-transform: uppercase;
  letter-spacing:.06em; transform: rotate(-2deg); mix-blend-mode: multiply; }

/* 2. Strip of sellotape on top of a "note" card */
.tape { position:absolute; top:-10px; left:50%; width:96px; height:26px;
  background: var(--tape); transform: translateX(-50%) rotate(-3deg);
  box-shadow: 0 1px 2px rgba(59,49,38,.18); opacity:.9; }

/* 3. Chalk price tag — inverse plate */
.chalk { background: var(--ink); color: var(--sheet); border-radius: 6px;
  font-family: Caveat; font-size: 22px; padding: 2px 12px; transform: rotate(1.5deg); }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-drop` | `cubic-bezier(.34,1.4,.64,1)` — label "slaps" down |
| `--dur-drop` | 240ms; hover wobble rotate ±1.5° 180ms |
| Signature move | cards on appear "stick on": translateY(-8px)+rotate(-2deg)→0 with slight bounce |
| MOTION_LIBRARY hooks | `T5` (gentle), stagger tiles H-archetype, `I1` magnetic — weak |
| Forbidden | neon, gloss, strict "Swiss" snap |

### Mantine skin
`defaultRadius: 12`; Button primary = `--stamp`; Badge → `.stamp` component; Card → "label" via Styles API (root: rotate + double shadow); Chips for tags with "stamped" border.

### Signature (ideas)
Sellotape on hero photo · alternating card tilt · handwritten word over the heading.

### Anti-patterns
Perfectly straight grid without tilt (the world dies) · glossy gradients · Caveat in paragraphs · dark theme.

---
---

## WORLD 4 — LUXE NOIR

**Feel:** private members' club, jewellery display case at night.
**For:** jewellery, private banking-like services, premium salons, whiskey/cigars/watches, luxury property, concierge.
**NOT for:** mass retail, "friendly" services, daytime operational tools.
**References:** watch house and jewellery brand showcases (composition), Rimowa/luxury e-com (restraint).
**Hero archetypes:** B, G, F. **Ambition:** restrained–confident (luxury does not rush).

### Palette (copy-paste)
```css
:root {
  --noir: #0E0D0B;         /* display case velvet */
  --panel: #171512;        /* raised panel */
  --ink: #F4EFE6;          /* warm light text (NOT pure white) */
  --ink-2: #B9AE9C;
  --line: rgba(244,239,230,.14);
  --gold: #C9A96A;         /* champagne gold — accent */
  --gold-deep: #8F7440;    /* gold shadow for foil gradient */
  --gold-ink: #0E0D0B;
}
```
Rules: gold ≤ 5% of area (lines, numbers, one CTA); photos — with warm lighting on black; no "blue" greys.

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Cormorant** 300–500 | ✅ | large light serif 56–110px, letter-spacing .01em |
| Body | **Jost** or **Tenor Sans** | ✅ | 15–16px, calm grotesque |
| Numbers/references | Inter tabular / IBM Plex Mono Light | ✅ | prices, carats |

### Surface and light
- Radii: 4–8px; thin panels: `background: var(--panel); border: 1px solid var(--line);` — depth through light, not shadow.
- Light: point source, from above: `box-shadow: inset 0 1px 0 rgba(244,239,230,.06);` cards as lit display cases.
- Images: vignette `mask-image: radial-gradient(120% 90% at 50% 30%, #000 60%, transparent 100%)`.

### Effect kit
```css
/* 1. Foil text — gold as material */
.foil { background: linear-gradient(100deg, var(--gold-deep) 0%, var(--gold) 35%, #F1DFB8 50%, var(--gold) 65%, var(--gold-deep) 100%);
  background-size: 200% 100%; -webkit-background-clip: text; background-clip: text; color: transparent; }
.foil:hover { animation: foil-sweep 1200ms ease forwards; }
@keyframes foil-sweep { to { background-position: 100% 0; } }

/* 2. Gold hairline under headings */
.rule-gold { height:1px; background: linear-gradient(90deg, transparent, var(--gold), transparent); }

/* 3. Engraved button */
.btn-noir { background: transparent; color: var(--gold); border: 1px solid var(--gold);
  letter-spacing:.14em; text-transform: uppercase; font: 500 12px/1 Jost; padding: 14px 28px;
  transition: background 300ms ease, color 300ms ease; }
.btn-noir:hover { background: var(--gold); color: var(--gold-ink); }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-velvet` | `ease` / `cubic-bezier(.4,0,.2,1)` |
| `--dur-velvet` | appearances 700ms opacity(+blur 3→0); hover 300ms; foil 1200ms |
| Signature move | slow emergence from darkness; foil sheen sweeps across price figure |
| MOTION_LIBRARY hooks | `T6` scale entrance (micro), `B2` orbs — warm, opacity <6%, `S2` scrub on product |
| Forbidden | abrupt entrances, springs, marquee text, confetti |

### Mantine skin
Dark theme with override: `colors.dark` → warm (`--noir/--panel`), not Mantine blue; `headings: Cormorant`; Button → `.btn-noir` as default variant; Divider → `.rule-gold`.

### Signature (ideas)
Price/carat "in foil" · thin gold passe-partout border around hero photo · collection numbering in Roman numerals.

### Anti-patterns
Pure white text #FFF (too sharp) · gold fills on buttons everywhere · neon · dense tables on showcase.

---
---

## WORLD 5 — SWISS MODERN

**Feel:** precision, intellect, "a studio that knows what it is doing".
**For:** architectural firms, consulting, agencies, designer/engineer portfolios, publishers, conferences.
**NOT for:** "warm" everyday niches, children's products.
**References:** Swiss poster school, Pentagram catalogues (compositionally), museum posters.
**Hero archetypes:** C, B, H. **Ambition:** confident (character in typography, not effects).

### Palette (copy-paste)
```css
:root {
  --white: #FFFFFF;
  --ink: #111111;
  --ink-2: #5B5B5B;
  --line: #111111;               /* rules — in ink, not grey */
  --accent: #E63329;             /* Swiss red; alternative klein #002FA7 */
  --accent-ink: #FFFFFF;
  --grid: rgba(17,17,17,.06);    /* visible module grid on background (option) */
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Single typeface | **Inter Tight** or **Golos Text** | ✅ | EVERYTHING in one family; hierarchy by size/weight: H1 72–140px/0.95, body 16px |
| Meta | the same, 11px caps, letter-spacing .1em | ✅ | like technical annotations on a drawing |
Latin ideal — Helvetica Now/Neue (commercial licence → License Gate).

### Surface and light
- Radii: **0**. Shadows: **none**. Depth — only through order and grid.
- Rules `1px solid var(--line)` above sections; active column can be underlined with a `4px` line.
- Asymmetry within a strict 12-column grid: large left block / narrow right column for meta.

### Effect kit
```css
/* 1. Block index meta — like drawing annotation */
.index { font: 500 11px/1 'Inter Tight'; letter-spacing:.12em; color: var(--ink-2); }
.index b { color: var(--accent); font-weight: 700; }

/* 2. Modernist link: underline slides in */
.link-sw { text-decoration: none; background: linear-gradient(var(--ink), var(--ink)) 0 100%/0 2px no-repeat;
  transition: background-size 200ms cubic-bezier(.7,0,.3,1); }
.link-sw:hover { background-size: 100% 2px; }

/* 3. Red accent plate — one word in H1 */
.mark { background: var(--accent); color: var(--accent-ink); padding: 0 .12em; }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-snap` | `cubic-bezier(.7, 0, .3, 1)` — sharp and precise |
| `--dur-snap` | 160–220ms; block entrances — 12px grid shift, no "flights" |
| Signature move | heading assembles line by line with a hard snap (`T5` with short stagger 40ms) |
| MOTION_LIBRARY hooks | `T5`, `S4` letter-spacing scrub (restrainedly), hover inversions |
| Forbidden | blur, glow, springs, parallax "for beauty" |

### Mantine skin
`defaultRadius: 0`; remove all shadows (`shadows: { ...: 'none' }`); Button = rectangle `--ink`/`--accent` with uppercase; Table — ink rules; grid via Grid with visible gutters.

### Signature (ideas)
Visible module grid in hero background · red word plate in heading · technical indices at sections (`A.01`, `B.02` — only if structure is genuinely indexed).

### Anti-patterns
Rounding and shadows "for softness" · gradients · two fonts · grey rules instead of ink.

---
---

## WORLD 6 — NEO-BRUTAL POSTER

**Feel:** bold, loud, "torn off the wall".
**For:** events, streetwear, youth services, creative tools, communities, merch.
**NOT for:** finance, medicine, luxury, government sector.
**References:** posters, zines, the Gumroad neo-brutalism wave, Figma community posters.
**Hero archetypes:** C, H. **Ambition:** bold–experimental.

### Palette (copy-paste)
```css
:root {
  --bone: #F5F2EC;         /* poster paper */
  --ink: #101010;
  --ink-2: #4A4A4A;
  --accent: #FF4D2E;       /* marker orange */
  --accent-2: #2E5BFF;     /* second marker — ONLY for details */
  --accent-ink: #101010;
  --shadow: #101010;       /* hard offset shadow */
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Unbounded** / **Russo One** / **Rubik Mono One** | ✅ | 64–140px, may break grid by 2–4° |
| Body | **Commissioner** or **Manrope** | ✅ | 16px, honest grotesque |
| Meta | **JetBrains Mono** | ✅ | timings, numbers |

### Surface and light
- Radii 0–6px. Borders **2px solid var(--ink)** on all interactives.
- Hard shadow, no blur: `box-shadow: 6px 6px 0 var(--shadow);`
- Light block rotation ±2° is allowed (within motion-island only, not breaking the grid of siblings — `LAYOUT_INVARIANTS §1`).

### Effect kit
```css
/* 1. Poster button: shadow "presses in" */
.btn-br { background: var(--accent); color: var(--accent-ink); border: 2px solid var(--ink);
  box-shadow: 6px 6px 0 var(--ink); font: 800 16px/1 Commissioner; padding: 14px 22px;
  transition: transform 90ms ease-out, box-shadow 90ms ease-out; }
.btn-br:hover { transform: translate(-2px,-2px); box-shadow: 8px 8px 0 var(--ink); }
.btn-br:active { transform: translate(4px,4px); box-shadow: 0 0 0 var(--ink); }

/* 2. Marker underline */
.hl { background: linear-gradient(transparent 55%, var(--accent) 55% 92%, transparent 92%); }

/* 3. Scrolling marquee — island with fixed height */
.marquee { overflow:hidden; border-block: 2px solid var(--ink); height: 44px; }
.marquee-track { display:flex; gap:32px; width:max-content; animation: mq 18s linear infinite; }
@keyframes mq { to { transform: translateX(-50%); } }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-punch` | `ease-out` 90–140ms — punch, not a flight |
| Signature move | shadow press on buttons; heading "stamps" in word by word (`T1` with hard steps) |
| MOTION_LIBRARY hooks | `T1`, `S1` marquee/horizontal, `I2` custom cursor (bold+), `TR1` |
| Forbidden | soft 600ms fades, blur, elegant serif transitions |

### Mantine skin
Radius 0–4; shadows → hard (override all levels with `Npx Npx 0 var(--ink)`); Button/Badge/Chip via Styles API for `.btn-br` language; Notification — "poster" border 2px.

### Signature (ideas)
Marquee fact ticker · "NEW" sticker with rotation · circle cursor with inversion.

### Anti-patterns
Pastels · thin 1px rgba grey borders · glassmorphism · soft shadows.

---
---

## WORLD 7 — INSTRUMENT PANEL

**Feel:** engineering credibility, "system under control".
**For:** dev tools, monitoring, analytics, cybersecurity, IoT, trading analytics, API products.
**NOT for:** "warm" B2C, luxury, children's.
**References:** aviation instruments and oscilloscopes (material), dark observability dashboards (compositionally, not as default palette).
**Hero archetypes:** G, A. **Ambition:** confident.

### Palette (copy-paste)
```css
:root {
  --graphite: #101418;     /* instrument casing */
  --panel: #171C22;
  --ink: #E7EDF3;
  --ink-2: #8FA0B0;
  --line: rgba(231,237,243,.12);
  --phosphor: #37E29A;     /* phosphor — live data; alternative amber #FFB454 */
  --phosphor-dim: rgba(55,226,154,.14);
  --alert: #FF5D5D;
  --grid: rgba(231,237,243,.05);
}
```
Rule: `--phosphor` — DATA and live states, not decorative fills; interface text — `--ink`.

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **JetBrains Mono** / **IBM Plex Mono** 600 | ✅ | yes, mono as display: 40–80px, that IS the character |
| Body | **IBM Plex Sans** | ✅ | 15–16px |
| Data | mono, `font-variant-numeric: tabular-nums` | ✅ | always |

### Surface and light
- Radii 6–8px; panels `--panel` + `1px solid var(--line)`; background — graph paper: `background-image: linear-gradient(var(--grid) 1px, transparent 1px), linear-gradient(90deg, var(--grid) 1px, transparent 1px); background-size: 32px 32px;`
- Glow only for live values: `text-shadow: 0 0 12px var(--phosphor-dim)`.

### Effect kit
```css
/* 1. Terminal caret */
.caret::after { content:'▌'; margin-left:2px; color: var(--phosphor); animation: blink 1.1s steps(1) infinite; }
@keyframes blink { 50% { opacity: 0; } }

/* 2. Phosphor indicator */
.led { width:8px; height:8px; border-radius:50%; background: var(--phosphor);
  box-shadow: 0 0 10px var(--phosphor-dim); }

/* 3. Instrument data row with dividers */
.readout { font-family:'JetBrains Mono'; color: var(--ink);
  border-block: 1px solid var(--line); padding: 10px 0; display:flex; gap:24px; }
.readout b { color: var(--phosphor); font-weight: 600; }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-tick` | `linear` / `steps()` — an instrument does not "float" |
| `--dur-tick` | 100–160ms; heading type `T3` scramble 600–900ms once |
| Signature move | values "roll in" count-up with tabular-nums; sweep line across a graph |
| MOTION_LIBRARY hooks | `T3`, `B1` live grid (weak), `B6` code rain — hero only and experimental only |
| Forbidden | springs, swings, serifs, "soft" pastel backgrounds |

### Mantine skin
Dark theme with blue replaced by graphite; `fontFamilyMonospace` globally for data; Code/Table/Kbd — native strong Mantine components, use them; Progress/RingProgress recoloured to `--phosphor`.

### Signature (ideas)
Hero heading "typed" with caret · readout row with live metrics · LED status indicators instead of coloured badges.

### Anti-patterns
Casino neon with 4 colours · glow on everything · serif · round "friendly" buttons.

---
---

## WORLD 8 — SOFT CLINIC

**Feel:** safe, clean, "help is here".
**For:** medicine, psychology, wellness, health insurance, care, vet clinics.
**NOT for:** bold brands, dev tools.
**References:** modern clinics (environment), One Medical-class sites (compositionally), soft pharmacy print.
**Hero archetypes:** B, A. **Ambition:** restrained.

### Palette (copy-paste)
```css
:root {
  --mist: #F4F7F8;         /* clinic air */
  --card: #FFFFFF;
  --ink: #253238;          /* deep blue-grey, NOT black */
  --ink-2: #5E7178;
  --line: #DDE6E8;
  --calm: #2F8F83;         /* calm teal — primary */
  --calm-soft: #E4F2EF;
  --warm: #E8A65D;         /* warm attention accent, ≤3% */
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Onest** 600 | ✅ | soft geometry, 40–64px |
| Body | **Golos Text** / **Nunito Sans** | ✅ | 17px/1.6 — easy to read |
| Meta | Inter 12px | ✅ | no caps shouting |

### Surface and light
- Radii 14–18px; shadows scattered and very light: `0 8px 24px -16px rgba(37,50,56,.25)`.
- Lots of air: sections 96–120px; illustrations/photos in "soft" masks `border-radius: 24px` or blob-masks (fixed, not animated).

### Effect kit
```css
/* 1. Breathing appointment/online dot */
.pulse { position:relative; width:10px; height:10px; border-radius:50%; background: var(--calm); }
.pulse::after { content:''; position:absolute; inset:-6px; border-radius:50%;
  border: 2px solid var(--calm); opacity:.5; animation: breathe 2.4s ease-out infinite; }
@keyframes breathe { to { transform: scale(1.5); opacity: 0; } }

/* 2. Care step — progress with soft fill */
.step-fill { background: linear-gradient(90deg, var(--calm) var(--p,40%), var(--calm-soft) 0); height:6px; border-radius:999px; transition: --p 400ms ease; }

/* 3. Appointment card */
.visit { background: var(--card); border: 1px solid var(--line); border-radius: 16px;
  box-shadow: 0 8px 24px -16px rgba(37,50,56,.25); }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-hush` | `cubic-bezier(.22,1,.36,1)` — an exhale |
| `--dur-hush` | 280–360ms; lifts ≤6px; nothing "jumps" |
| Signature move | breathing indicator; smooth progress fill for appointment booking |
| MOTION_LIBRARY hooks | `T5` (very gentle), progress-fill, `B2` — barely perceptible |
| Forbidden | sharp snaps, red outside errors, confetti, auto-carousels |

### Mantine skin
`defaultRadius: 16`; primary = `--calm`; Stepper/Timeline — native, gently recoloured; Alert only `variant: light`.

### Signature (ideas)
Breathing dot "doctor is online" · "patient journey" scale · warm `--warm` tag on one key promise.

### Anti-patterns
Stock white coats with crossed arms · medical blue #007BFF · dense tables on showcase · alarming red in marketing.

---
---

## WORLD 9 — APOTHECA BOTANICA

**Feel:** natural, precise, "a recipe with history".
**For:** cosmetics, teas/herbs, eco brands, farms, nutrition, boutique hotels.
**NOT for:** technology tools, speed/trading.
**References:** apothecary labels and herbaria (material), Aesop-class showcases (composition and restraint).
**Hero archetypes:** F, B. **Ambition:** confident.

### Palette (copy-paste)
```css
:root {
  --cream: #F5F0E6;
  --sheet: #FCFAF3;
  --ink: #2A3C31;          /* conifer ink — text in DARK GREEN */
  --ink-2: #5C6F60;
  --line: #D9D2BF;
  --leaf: #29473A;         /* deep foliage — plates/CTA */
  --leaf-ink: #F5F0E6;
  --tincture: #B8863B;     /* amber tincture — details ≤3% */
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Vollkorn** or **Lora** (can use Italic) | ✅ | 44–80px, botanical serif |
| Body | **PT Serif** OR **Manrope** | ✅ | serif — if brand is "editorial", sans — if "laboratory" |
| Label | Manrope 11px caps, letter-spacing .1em | ✅ | ingredients, volume |

### Surface and light
- Radii 8–12px; "label": `--sheet` + border `1px solid var(--line)` + inner thin border `outline: 1px solid var(--line); outline-offset: -6px;`
- Botanical line art (SVG stroke 1.25px in `--ink-2` colour) instead of stock photos.

### Effect kit
```css
/* 1. Sprouting — SVG branch is drawn */
.sprout path { stroke: var(--ink-2); fill: none; stroke-dasharray: 1; stroke-dashoffset: 1;
  transition: stroke-dashoffset 900ms cubic-bezier(.22,1,.36,1); }
.in-view .sprout path { stroke-dashoffset: 0; }

/* 2. Label with double border */
.label { background: var(--sheet); border:1px solid var(--line); outline:1px solid var(--line);
  outline-offset:-6px; padding: 20px; }

/* 3. Recipe number drop-accent */
.no { font: 600 12px/1 Manrope; color: var(--tincture); letter-spacing:.12em; }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-grow` | `cubic-bezier(.22,1,.36,1)` |
| `--dur-grow` | 450–900ms; appearances scale .98→1 + opacity |
| Signature move | botanical line-draw on scroll; ingredients "spread out" with stagger like a herbarium |
| MOTION_LIBRARY hooks | line-draw (SVG), `T5`, `I3` light photo parallax |
| Forbidden | neon, glitch, dark theme, springs |

### Mantine skin
Radius 10; primary = `--leaf`; Accordion (ingredients) — native, arrows recoloured; Image with "label" border via Styles API.

### Signature (ideas)
Herbarium recipe numbering · drawing branch next to a benefit · double-border label on hero product card.

### Anti-patterns
"Eco" = acid lime green · leaf clipart · Comic-style handwriting · overloading with kraft (that's World 3).

---
---

## WORLD 10 — CHROME VELOCITY

**Feel:** power, movement, engineering adrenaline.
**For:** automotive/moto, sports technology, gaming peripherals, "fast" delivery, logistics-speed, tuning.
**NOT for:** care/medicine, documents/law.
**References:** car configurators, sports equipment promos, esports brands (the restrained part).
**Hero archetypes:** D, C, E. **Ambition:** bold.

### Palette (copy-paste)
```css
:root {
  --asphalt: #0B0C10;
  --panel: #12141B;
  --ink: #EEF1F7;
  --ink-2: #9AA3B5;
  --line: rgba(238,241,247,.12);
  --heat: #FF7A18;         /* scorching — primary action */
  --heat-ink: #0B0C10;
  --electric: #4D7CFE;     /* cold counter-accent, details */
  --chrome-hi: #F8FAFF; --chrome-mid: #B9C3D6; --chrome-lo: #7C8AA5;
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Exo 2** 700 *Italic* | ✅ | italic = velocity vector, 56–120px |
| Body | **Onest** / **Inter** | ✅ | 15–16px |
| Telemetry | **JetBrains Mono** | ✅ | speed, time, VIN |

### Surface and light
- Radii 8px with clipped corner: `clip-path: polygon(0 0, calc(100% - 14px) 0, 100% 14px, 100% 100%, 0 100%);`
- Chrome sheen on borders: `border-image: linear-gradient(135deg, var(--chrome-hi), var(--chrome-lo)) 1;`
- Diagonal speed-lines as background: `repeating-linear-gradient(115deg, transparent 0 22px, var(--line) 22px 23px)` opacity ≤ .5.

### Effect kit
```css
/* 1. Chrome text */
.chrome { background: linear-gradient(180deg, var(--chrome-hi) 0%, var(--chrome-mid) 45%, var(--chrome-lo) 55%, var(--chrome-hi) 100%);
  -webkit-background-clip:text; background-clip:text; color:transparent; }

/* 2. Acceleration button */
.btn-vel { background: var(--heat); color: var(--heat-ink); font: 800 15px/1 'Exo 2'; font-style: italic;
  padding: 14px 26px; clip-path: polygon(10px 0, 100% 0, calc(100% - 10px) 100%, 0 100%);
  transition: transform 140ms cubic-bezier(.7,0,.3,1), filter 140ms; }
.btn-vel:hover { transform: skewX(-4deg) translateX(2px); filter: brightness(1.06); }

/* 3. Telemetry row */
.telemetry { font-family:'JetBrains Mono'; color: var(--ink-2); }
.telemetry b { color: var(--heat); }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-launch` | `cubic-bezier(.7, 0, .2, 1)` — acceleration snap |
| `--dur-launch` | entrances 260ms with skewX(-6°)→0; hover 140ms |
| Signature move | heading flies in along the italic vector; telemetry numbers count-up |
| MOTION_LIBRARY hooks | `T1` (horizontal impulse), `S2`/`S3` product on scroll, `I3` parallax, E-archetype → `W2` |
| Forbidden | soft pastel fades, serifs, "breathing" |

### Mantine skin
Dark theme on `--asphalt/--panel`; Button via Styles API → `.btn-vel`; Progress → "rev counter" with heat gradient; RingProgress for acceleration metrics.

### Signature (ideas)
Chrome heading · clipped card corners · telemetry bar under hero (build/delivery/response speed).

### Anti-patterns
RGB gamer rainbow · glow on everything · italic in body text · speed-lines over text.

---
---

## WORLD 11 — MUSEUM CATALOG

**Feel:** cultured, singular, "exhibit with provenance".
**For:** galleries, auction houses, foundations, premium property development, premium education, interior design bureaus, private collections.
**NOT for:** mass utilities, "fast" services.
**References:** museum catalogues and object labels, auction house sites (composition), passe-partout and plinths (material).
**Hero archetypes:** F, B, D. **Ambition:** restrained–confident.

### Palette (copy-paste)
```css
:root {
  --gallery: #F4F3F0;      /* gallery wall */
  --wall: #FFFFFF;         /* passe-partout */
  --ink: #1C1B1A;
  --ink-2: #6E6B66;
  --line: #E4E1DB;
  --archive: #274690;      /* archival blue — links/indices; alternative oxblood #7A2E2E */
  --plinth: 0 24px 40px -28px rgba(28,27,26,.35); /* plinth shadow */
}
```

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Cormorant Infant** / **STIX Two Text** | ✅ | 48–96px, museum antiqua |
| Body | **Literata** / **Source Serif 4** | ✅ | 17px/1.65 |
| Object labels | Inter 11px, letter-spacing .08em | ✅ | "Artist · Year · Medium" |

### Surface and light
- Exhibit on passe-partout: media in mount `background: var(--wall); padding: clamp(12px, 2vw, 28px); box-shadow: var(--plinth);` — air around the work is mandatory.
- Radii 0–2px. Rules `--line` horizontal only.
- Catalogue grid: large exhibit + label column; NOT equal honeycomb tiles.

### Effect kit
```css
/* 1. Object label */
.plate { font: 500 11px/1.5 Inter; letter-spacing:.08em; color: var(--ink-2);
  text-transform: uppercase; }
.plate b { color: var(--ink); font-weight: 600; }

/* 2. Exhibit appearance — lights switched on */
.exhibit { opacity: 0; transition: opacity 500ms ease; }
.in-view .exhibit { opacity: 1; }

/* 3. Lot index */
.lot { font-family: 'Cormorant Infant'; font-size: 20px; color: var(--archive); }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-curator` | `ease` |
| `--dur-curator` | 500ms opacity-only; caption arrives 8px over 300ms |
| Signature move | "gallery lights come on" — sequential opacity appearance of exhibits; hover on artwork — label materialises |
| MOTION_LIBRARY hooks | `T5` (slow), opacity-reveal (canon §11 — fits perfectly), light `I3` |
| Forbidden | anything that "jerks"; auto-play carousels; gloss |

### Mantine skin
Radius 0–2; shadow only `--plinth` on media; Image in passe-partout via wrapper component; Table for provenance — thin lines, serif values.

### Signature (ideas)
Passe-partout around hero media · object label under each case ("Project · Year · Area") · lot numbering in antiqua.

### Anti-patterns
Dense bento grid of identical tiles · shadow-glow · bright CTA buttons (CTA here — restrained ink plate).

---
---

## WORLD 12 — ELECTRIC POP

**Feel:** celebration, energy, "addictively engaging".
**For:** B2C loyalty/bonuses, food tech promos, youth apps, streaming/creators, quizzes and promotions.
**NOT for:** law, medicine, enterprise reporting.
**References:** neon shop fronts and arcade machines (material), promo mechanics of consumer super-apps (compositionally).
**Hero archetypes:** H, C. **Ambition:** bold.

### Palette (copy-paste)
```css
:root {
  --night: #14121F;
  --card: #1D1A2E;
  --ink: #F5F3FF;
  --ink-2: #A9A3C2;
  --line: rgba(245,243,255,.14);
  --pop-1: #FF5DA2;        /* neon pink — primary */
  --pop-1-ink: #14121F;
  --pop-2: #35E0C0;        /* cyan — progress/success */
  --lime: #D3F26A;         /* rare "jackpot" details ≤2% */
}
```
Duotone rule: `--pop-1 + --pop-2` work together; `--lime` — spot use; more than three neons = casino (forbidden).

### Typography
| Role | Font | Cyrillic | Notes |
|------|-------|-----------|---------|
| Display | **Unbounded** 700 / **Days One** | ✅ | 44–96px |
| Body | **Manrope** / **Onest** | ✅ | 15–16px |
| Counters | **JetBrains Mono** tabular | ✅ | points, timers |

### Surface and light
- Radii 16–20px; "sticker": thick outline `2px solid var(--ink)` + offset colour shadow `4px 4px 0 var(--pop-2)`.
- Neon glow only on active element: `box-shadow: 0 0 0 1px var(--pop-1), 0 0 24px -6px var(--pop-1);`
- Background — deep night + rare "pixel stars" (radial-gradient dots, static).

### Effect kit
```css
/* 1. Level/bonus sticker badge */
.sticker { background: var(--pop-1); color: var(--pop-1-ink); border: 2px solid var(--ink);
  border-radius: 12px; box-shadow: 4px 4px 0 var(--pop-2); font: 800 13px/1 Unbounded;
  padding: 8px 12px; transform: rotate(-3deg); }

/* 2. Spring press */
.springy { transition: transform 260ms cubic-bezier(.34,1.56,.64,1); }
.springy:hover { transform: translateY(-3px) scale(1.03); }
.springy:active { transform: scale(.96); }

/* 3. Combo progress bar */
.combo { height: 12px; border-radius: 999px; background: var(--card);
  box-shadow: inset 0 0 0 1px var(--line); overflow: hidden; }
.combo > i { display:block; height:100%; width: var(--p, 40%);
  background: linear-gradient(90deg, var(--pop-2), var(--pop-1)); transition: width 500ms cubic-bezier(.34,1.56,.64,1); }
```

### Motion personality
| Token | Value |
|-------|----------|
| `--ease-spring` | `cubic-bezier(.34,1.56,.64,1)` |
| `--dur-spring` | 220–300ms; points count-up 600ms |
| Signature move | badge "drops" with overshoot when bonus is received; combo progress fills with a spring |
| MOTION_LIBRARY hooks | `T1` (springy), stagger tiles `H`, `I1` magnetic on main CTA, count-up |
| Forbidden | confetti rain on any occasion; neon glow on static elements; auto-carousels |

### Mantine skin
Radius 16; primary `--pop-1`; Badge → `.sticker`; RingProgress — combo rings; Notification with "sticker" outline; dark theme on `--night/--card` (not Mantine dark blue).

### Signature (ideas)
Combo bar in hero · level sticker with rotation · mono point counter with spring count-up.

### Anti-patterns
4+ neons · glow on paragraph text · eye-searing lime as background · "casino" flashing.

---
---

## CUSTOM WORLD CONSTRUCTOR (if none of the above fits)

Launched by @CREATOR/@DESIGN. A strong model is justified here (one call), then everything is executed as a recipe.

```
STEP 1 — WORLD OBJECTS: list 5 physical objects from the niche world
        (for a florist: kraft paper, ribbon, stem, water, price tag).
STEP 2 — DOMINANT MATERIAL: choose one object as the interface material.
        It answers: what background? what shadow? what radius? does this material round in real life?
STEP 3 — 60/30/10 PALETTE FROM THE MATERIAL:
        60% background (material colour, muted), 30% ink/surfaces, 10% one accent
        (the most saturated colour of an object from Step 1). Check pairs against WCAG AA (TEMPLATE_DESIGN_PASSPORT §2).
STEP 4 — TYPOGRAPHY BY POSTURE (Q2): pick a pair from the neighbouring world tables
        (respectably → serif worlds 2/11; boldly → 6; technically → 7; warmly → 3/8).
        Cyrillic support mandatory for RU market — "Cyrillic" column from the worlds.
STEP 5 — MOTION FROM MATERIAL PHYSICS: how does this material move in real life?
        (paper settles · metal clicks · fabric billows · glass glides) → easing/duration.
STEP 6 — SIGNATURE: one effect that exists only in this world (modelled on the effect kits).
```

**Constructor prohibitions:** palette "by taste" without material · >1 accent hue · display font without Cyrillic for content in Cyrillic-language markets · mixing motion physics · skipping contrast check.

---

## ROUTING TABLE: niche → candidate worlds

| Niche / message | Candidate 1 | Candidate 2 |
|--------------|-----------|-----------|
| Notaries, lawyers, audit, assessment | 2 INK & SEAL | 11 MUSEUM |
| Fashion, beauty, personal brand | 1 GLOSSY EDITORIAL | 4 LUXE NOIR |
| Jewellery, premium services, private | 4 LUXE NOIR | 11 MUSEUM |
| Bakery, coffee shop, hand-made, farm | 3 CRAFT PAPER | 9 BOTANICA |
| Cosmetics, eco, nutrition | 9 BOTANICA | 1 GLOSSY EDITORIAL |
| Medicine, psychology, care | 8 SOFT CLINIC | 9 BOTANICA |
| Agency, architecture, consulting | 5 SWISS MODERN | 11 MUSEUM |
| Events, streetwear, community | 6 NEO-BRUTAL POSTER | 12 ELECTRIC POP |
| Dev tools, monitoring, API | 7 INSTRUMENT PANEL | 5 SWISS MODERN |
| Automotive/moto, sports tech, "fast" logistics | 10 CHROME VELOCITY | 7 INSTRUMENT PANEL |
| Galleries, foundations, premium education | 11 MUSEUM CATALOG | 2 INK & SEAL |
| Loyalty, food tech promos, youth B2C | 12 ELECTRIC POP | 6 NEO-BRUTAL POSTER |
| Operational SaaS tool (admin-first) | `FRONTEND_DESIGN_EXCELLENCE §2` (operational contour) + world for showcase only | — |

**Reminder about two contours:** the world governs the **showcase** entirely; in the **operational contour** (`/admin`, `/app`) the world appears in measured doses — palette/font/radii are inherited, effect kit is NOT transferred (`FRONTEND_DESIGN_EXCELLENCE §1`), micro-moments — `MOTION_AMBITION_DIAL` MICRO mode.

---

Reference: `roles/VISUAL_CONCEPT_PROTOCOL.md` · `roles/DESIGN_DECISION_LIBRARY.md` (operational models) · `roles/HERO_ARCHETYPES.md` · `roles/MOTION_LIBRARY.md` · `roles/MOTION_AMBITION_DIAL.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/LAYOUT_INVARIANTS.md` · `roles/TEMPLATE_TYPOGRAPHY_PASSPORT.md` (License Gate)
Version: 1.0 | 2026-07-05
