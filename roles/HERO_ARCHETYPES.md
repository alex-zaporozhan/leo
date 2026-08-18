# HERO_ARCHETYPES.md
# Library of hero archetypes and public site composition
# Used by @DESIGN (public site SPEC) and @MOTION (CONCEPT) BEFORE @DEV.
# Purpose: eliminate the monoculture of "text left + mockup right" as the default.

> **Principle:** "'Text on the left, card on the right' is ONE archetype out of eight, not a law of nature. The hero composition is chosen for the product and the feeling, not taken by inertia."
> This document closes the gap that caused the hero to always converge to a single Linear/Vercel template: the `@DESIGN` golden library was a density monoculture, and composition archetypes were not described.

---

## WHY AND HOW TO READ THIS

Previously the hero defaulted to "7/5 grid, mockup on the right" — because that was recorded as the reference in `FRONTEND_DESIGN_EXCELLENCE §5` and `TEMPLATE_DESIGN_UX §3.2`. That template is **not cancelled** — it becomes Archetype A: appropriate in specific cases, not the default for everything.

Work order:
1. `@MOTION CONCEPT` or `@DESIGN SPEC` first runs the **Selection Protocol** (below) — chooses the archetype consciously.
2. The chosen archetype provides: structure, typography scale, motion hooks (links to `MOTION_LIBRARY` techniques), mobile degradation, anti-pattern.
3. The decision is recorded in `MOTION_CONCEPT_*.md` / `DESIGN_SPEC_*.md` with the line: **"Archetype: [letter] — [name]. Basis: [selection condition]".**

Each archetype is tied to a **reference**, and the references are intentionally diverse — not SaaS minimalism only.

---

## SELECTION PROTOCOL (3 questions → archetype)

> **[v6.20]** Q1–Q3 are aligned with `docs/artifacts/VISUAL_CONCEPT_*`: the project world has archetype affinities (world recipe in `roles/CONCEPT_DNA_LIBRARY.md`). @MOTION/@DESIGN choose from the world's affinities; stepping outside the affinity — with justification through the world's material, not "I like it".

Not "which looks nicer", but a deterministic choice based on the product's nature.

```
Q1. WHAT should the visitor FEEL in 3 seconds? (one word — from @MOTION CONCEPT)
Q2. WHAT is the nature of the product?
    - operational/data (SaaS tool, dashboard)          → tends towards A, H, G
    - visual/creative (design, media, portfolio)        → tends towards C, D, F
    - spatial/3D (environment, game, hardware, AI world) → tends towards E
    - brand/statement (manifesto, B2B trust, fintech)  → tends towards B, G
Q3. WHAT is the brand's ambition level? (dial from roles/MOTION_AMBITION_DIAL.md)
    - restrained / confident → A, B, H, G
    - bold / experimental    → C, D, E, F
```

The intersection of three answers yields 1–2 candidates. With two — `@DESIGN`/`@MOTION` chooses with justification through a reference (the "one winner" rule, `ROLE_DESIGN`).

**Hard rule:** Archetype A (split + mockup) is **not chosen by default**. It is chosen only if Q1–Q3 genuinely lead to it (operational product, restrained/confident, the interface itself needs to be shown). Otherwise it is inertia, not a decision.

---

## ARCHETYPE A — Split: text + mockup

**What:** two-column grid (e.g. 7/5 or 6/6); left side heading→subheading→CTA, right side pedestal/product mockup.
**When to choose:** an operational SaaS tool where **showing the interface itself is the argument**; restrained/confident; target audience is a rational B2B buyer.
**When NOT:** a creative/brand product; when the mockup is still "empty" and unimpressive; when a feeling is needed, not a demonstration.
**Reference:** Linear, Stripe Dashboard, Vercel (product pages).
**Structure:** 7/5 grid; H1 38–64px on the left; mockup on the right on a pedestal (`FRONTEND_DESIGN_EXCELLENCE §4.3`); one dominant H1, one primary CTA.
**Motion hooks:** `T1` kinetic heading (moderate stagger), `B1`/`B2` live background/orbs at low intensity, mockup reveal `S2`.
**Mobile:** columns into stack, mockup below text, simplify parallax.
**Anti-pattern:** chosen "out of habit" for a creative product; mockup on white background in a dark hero (`ROLE_MOTION` anti-pattern).

---

## ARCHETYPE B — Centered Statement

**What:** one large centred manifesto heading, subheading, 1–2 CTAs; no mockup. Air around it.
**When to choose:** **trust/statement** is needed, not a UI demonstration; platform product or brand; restrained/confident; when the product is hard to show in a single screenshot.
**When NOT:** when the product visual is the main argument (then A or D).
**Reference:** Stripe.com (modern iterations), Vercel home, Anthropic, Linear (statement pages).
**Structure:** centred content column (`--max-w-content`), H1 56–96px, subheading 18–22px `dimmed`, CTA centred; background carries the atmosphere (`§4.1` unified background).
**Motion hooks:** `T5` staggered line reveal, `T6` headline scale entrance, background `B2`/`B5` atmospheric, does not shout.
**Mobile:** reduce H1 type size, preserve centring and stagger.
**Anti-pattern:** "empty centre" without background atmosphere → feeling of incompleteness; competing H1 elements.

---

## ARCHETYPE C — Full-Bleed Typographic

**What:** giant typography fills the viewport; the text itself is the visual; minimal other elements.
**When to choose:** creative/brand/portfolio; bold/experimental; when **a word/name is the primary carrier of meaning** (`@MOTION` "one word").
**When NOT:** an operational SaaS with a rational buyer; when data/interface needs to be shown.
**Reference:** obys.agency, activetheory.net, studio/portfolio sites.
**Structure:** H1 80–160px (`ROLE_MOTION` principle 4 — dominance), fills the width; everything else at 14–18px serves it; asymmetry is permitted.
**Motion hooks:** `T1` word-by-word kinetics, `T2` morphing (word switching), `T4` split-screen typography, `S4` letter-spacing scrub, magnetic button `I1`.
**Mobile:** preserve kinetics, simplify easing; reduce type size but maintain dominance.
**Anti-pattern:** "micro-animation instead of character" (`ROLE_MOTION`); typography is large but lacks an idea/metaphor.

---

## ARCHETYPE D — Product-Immersive

**What:** the product itself (large perspective screenshot, live demo, interactive fragment) occupies centre stage; text serves.
**When to choose:** the product is **visually convincing on its own** (canvas tool, media, design product); confident/bold.
**When NOT:** the product is "boring" visually (then B); an early product without strong UI.
**Reference:** Figma, Notion, Framer, media/creative tools.
**Structure:** large product visual in the centre or offset; H1 above/beside; perspective/tilt (`FRONTEND_DESIGN_EXCELLENCE §4.3`), glow pedestal.
**Motion hooks:** `S3` sticky hero transform (product "comes alive" on scroll), `S2` scale+opacity scrub, `I3` parallax layers, hover demo `I4`.
**Mobile:** static/simplified product frame, remove parallax.
**Anti-pattern:** "technology for technology's sake" — interactive elements unconnected to the product.

---

## ARCHETYPE E — 3D / WebGL Scene

**What:** an interactive 3D scene carries the hero; the product itself is spatial, or the scene is a metaphor for the essence.
**When to choose:** **the product is spatial/immersive** (environment, hardware, game, AI world) OR the metaphor is conceptually justified (`MOTION_LIBRARY W1` — three questions); bold/experimental; ambition dial ≥ bold.
**When NOT:** "abstract sphere because it looks technical" (`W1` — NOT justified); operational SaaS; restrained.
**Reference:** bruno-simon.com (3D is his product), resn.global, activetheory, Igloo/hardware sites.
**Structure:** Canvas in background/centre, text overlay on top with readable contrast; one dominant meaningful object (`W2` shape = meaning).
**Motion hooks:** `W2` geometry (shape carries meaning), `W3` shader (fluid/adaptive — for AI/creative), cursor influence `I2`, smooth scroll `TR2`.
**Mobile (mandatory):** static fallback or CSS replacement; remove WebGL on `prefers-reduced-motion` and on weak devices (`MOTION_LIBRARY §VII`).
**Anti-pattern:** 3D without an idea; Lighthouse mobile performance < 85; no fallback → blank/black screen.

---

## ARCHETYPE F — Editorial / Magazine

**What:** asymmetric grid, large typography (often serif for the heading), large image/collage, narrative rhythm.
**When to choose:** content/brand/premium product where **story and character matter**; bold; audience values taste and editorial.
**When NOT:** a utilitarian tool; when data density is needed.
**Reference:** Stripe Sessions/brand landings, premium agencies, editorial/fashion formats (as a composition reference, not as content).
**Structure:** broken grid; H1 serif 64–120px (`var(--font-serif)`); large media with `aspect-ratio` (`LAYOUT_INVARIANTS §5`); deliberate asymmetry while maintaining grid alignment.
**Motion hooks:** `S1` horizontal scroll section, `T5` line reveal, `I3` parallax media, `TR1` curtain page transition.
**Mobile:** reduce broken grid to one column, preserve heading size hierarchy.
**Anti-pattern:** asymmetry without a grid → "wandering" edges (violates `LAYOUT_INVARIANTS §9`); serif for serif's sake without character.

---

## ARCHETYPE G — Background-Driven

**What:** animated/atmospheric background carries the hero; text is minimal and calm on top.
**When to choose:** platform/infrastructure/fintech where a **"living system" feeling is needed** and showing the UI is premature; confident; "moves/thinks/breathes" as a metaphor.
**When NOT:** when a concrete product argument is needed (A/D); when the background will compete with content.
**Reference:** Vercel (gradient/grid backgrounds), Linear (atmosphere), fintech landings.
**Structure:** unified background scene (`§4.1`), H1 centred/left calm 48–80px, background opacity < 10% towards content (`ROLE_MOTION` principle 5).
**Motion hooks:** `B1` live grid, `B2` orbs, `B5` aurora, `B4` particle field (moderate), all transform/opacity (`LAYOUT_INVARIANTS §10`).
**Mobile:** lighten/freeze background, CSS-only.
**Anti-pattern:** background shouts (opacity 20%+, 2–3s cycle) and drowns the heading (`ROLE_MOTION` principle 5).

---

## ARCHETYPE H — Bento / Modular

**What:** the hero is assembled from "bento" tiles of different sizes, each showing a facet of the product; heading at the top.
**When to choose:** a product with **several strong facets/features** that are better shown immediately; confident; a modern modular brand.
**When NOT:** one key argument (then A/B/D); an early product without several strong facets.
**Reference:** Apple (bento sections), Vercel, modern modular landings.
**Structure:** H1 at the top; below — bento grid (`LAYOUT_INVARIANTS §1` equal-height mandatory!); tiles of different area but aligned to the grid; one dominant tile.
**Motion hooks:** stagger reveal of tiles (`T5`-logic), hover-elevate tile (transform), `I1` magnetic accents on the dominant tile.
**Mobile:** bento → vertical stack, preserve tile priority order.
**Anti-pattern:** tiles of different heights "jump" (violates `LAYOUT_INVARIANTS §1`); no dominant tile → "grey noise" (`ROLE_MOTION`).

---

## SELECTION SUMMARY TABLE

| Archetype | Product nature | Ambition | Feeling | Reference |
|---------|----------------|---------|---------|--------|
| A Split + mockup | operational/data | restrained–confident | "clear, reliable" | Linear, Stripe |
| B Centered Statement | brand/platform | restrained–confident | "I trust, serious" | Stripe, Vercel, Anthropic |
| C Full-Bleed Type | creative/portfolio | bold–experimental | "character, bold" | obys, activetheory |
| D Product-Immersive | visual product | confident–bold | "wow" | Figma, Notion, Framer |
| E 3D / WebGL | spatial/AI | bold–experimental | "immersion" | bruno-simon, resn |
| F Editorial | content/premium | bold | "taste, story" | premium brands |
| G Background-Driven | infra/fintech | confident | "living system" | Vercel, Linear |
| H Bento / Modular | multi-faceted product | confident | "rich, modern" | Apple, Vercel |

---

## EMBEDDING IN ROLES

- **`@MOTION` (CONCEPT):** after "one word" and metaphor — runs the Selection Protocol, names the archetype, pulls motion hooks from `MOTION_LIBRARY` for the archetype. Ambition level is taken from `MOTION_AMBITION_DIAL`.
- **`@DESIGN` (public site SPEC):** details the statics within the chosen archetype; does not change the archetype without a VERDICT.
- **`@DEV`:** implements; equal-height/aspect-ratio/no-layout-animation from `LAYOUT_INVARIANTS` are mandatory for archetypes H, F, D, E.
- **`@QA_VISUAL`:** verifies archetype geometry (especially H/F — equal-height and alignment; E — fallback and performance) and regression against baseline.

**Decision record (mandatory line in CONCEPT/SPEC):**
```
Archetype: [A–H] — [name]
Basis: Q1 feeling=[word] · Q2 nature=[type] · Q3 ambition=[level]
Reference: [specific site + what we take]
Motion hooks: [codes from MOTION_LIBRARY]
Mobile degradation: [what is simplified]
```

---

Reference: `roles/ROLE_MOTION.md` · `roles/MOTION_LIBRARY.md` · `roles/MOTION_AMBITION_DIAL.md` · `roles/ROLE_DESIGN.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` §4–§5 · `roles/TEMPLATE_DESIGN_UX.md` §3 · `roles/LAYOUT_INVARIANTS.md` (§1 equal-height, §5 aspect-ratio, §9 alignment, §10 animation) · `roles/ROLE_QA_VISUAL.md`
Version: 1.0 | 2026-06-14
