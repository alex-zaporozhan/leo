# VISUAL_CONCEPT_PROTOCOL.md
# The "Concept before code" protocol: the birth, verification and change of a project's visual concept.
# [v6.20+] The generative core — `roles/CONCEPT_ANATOMY.md` (8 DNA axes, coherence, the reference protocol, the constructor).
# This file is the PROCESS around the core; the 12 worlds of `CONCEPT_DNA_LIBRARY.md` are presets of the anatomy (≥6 axes → a preset, otherwise the constructor).
# Owners: @CREATOR (the birth of the concept, Step 5.5), @DESIGN (the concept's life: a SPEC inside the world, mode 4 RESKIN — a world change),
# @MOTION (inheriting the motion-personality), @QA_VISUAL (TASTE GATE as part of the visual gate),
# @MEDIA_ENGINEER (rendering the approved concept into real media assets via AI — photo/video/3D plates on a reproducible pipeline; `roles/ROLE_MEDIA_ENGINEER.md` + `roles/MEDIA_SYNTHESIS_CANON.md`).

> **Principle 1:** "Taste that lives in the model costs $70 per project. Taste that lives in files costs pennies — a cheap model assembles from recipes rather than inventing."
> **Principle 2:** "A concept is one phrase 'the screen is [an object from the niche's world]', from which everything is derived: palette, font, shadow, radius, the physics of motion. If a block can't be derived from the phrase — the block is random."
> **Principle 3 (for RESKIN):** "A good system separates the skeleton from the skin. If changing the concept requires changing the DOM — the culprit is not the concept but a violation of component-first."

---

## §1. WHAT THIS PROTOCOL CHANGES IN THE CHAIN

Before: `@CREATOR (business) → VISUAL BOOTSTRAP (4 passports from the decision library) → @MOTION → @DESIGN → @DEV`.
The weak spot: the passports are **slots** (Brand primary: `[hex]`), and slots without recipes the model fills with defaults: blue, Inter, gray-0. This is how "tastelessness with formally filled passports" is born.

After:

```
@CREATOR STEP 5.5.A — VISUAL CONCEPT (this protocol §2–§3)
   choosing a world from roles/CONCEPT_DNA_LIBRARY.md (or the custom-world Constructor)
   → docs/artifacts/VISUAL_CONCEPT_[PROJECT].md  ← THE SINGLE source of the project's aesthetic
        ↓ TASTE GATE (§4) — a mandatory cliché filter
@CREATOR STEP 5.5.B — the 4 passports are DERIVED from the concept (§5, the transfer map)
        ↓
@MOTION CONCEPT — inherits the world's motion-personality (easing/durations/the signature move),
   develops it with MOTION_LIBRARY techniques; ambition — MOTION_AMBITION_DIAL (the world's affinity = a hint)
        ↓
@DESIGN SPEC — works INSIDE the world (the world = Tier 0 of references); the SaaS library — only
   operational patterns (drawer vs modal, table density), not a visual reference for the public site
        ↓
@DEV → @QA_ARCH → @QA_VISUAL (V1–V12 + the TASTE GATE check)
```

A concept change on a live project — **@DESIGN mode 4: RESKIN** (§6), not "redo everything".

---

## §2. THE BIRTH OF THE CONCEPT (@CREATOR, Step 5.5.A)

Input: the result of INDUSTRY INTELLIGENCE (the niche, the audience, the competitors) + Product Meaning (`PROJECT_VISUAL_BOOTSTRAP_PROTOCOL` Step 1) + ambition (user/brand; default `confident`).

**The two-pass rule (mandatory):**

```
PASS 1 — THE PLAN: the anatomy Constructor (CONCEPT_ANATOMY §5): 5 niche objects → a carrier →
  8 DNA axes → a DNA passport; a preset from CONCEPT_DNA_LIBRARY — on a match of ≥6 axes
  (FUSION = a replacement of ≤2 axes by the paired-replacement rule §3) → the concept phrase + the signature + 3 "never"s.

PASS 2 — SELF-CRITIQUE AGAINST THE DEFAULT: ask yourself
  "What would I output for ANY similar brief?" If the plan coincides with that default
  on at least one axis (palette / font / composition / signature) — reassemble the axis
  and record the line: "Replaced: [what] → [with what], because it was the default".
  Only after that — assembling the artifact.
```

**The Chanel rule (before fixing):** look at the plan and remove one "accessory" — one effect/colour/technique without which the concept does not weaken. Boldness is spent in one place (the signature), the rest is discipline.

**Economy of questions:** the concept adds no questions for the user. The only clarification, if critical: "Brand ambition: calm / confident / bold?" — and only if it can't be derived from the niche.

---

## §3. THE ARTIFACT `VISUAL_CONCEPT_[PROJECT].md` (template)

```markdown
# VISUAL CONCEPT: [Project]
> Created: [date] | Owner: @CREATOR | Lives: until RESKIN | Version: 1.0

## Concept phrase
The screen is [an object from the niche's world: "the spread of a glossy magazine" / "a notary's letterhead" /
"a dashboard" / "a poster on the wall" / "the label of an apothecary vial"].

## World
World: [№ + name from CONCEPT_DNA_LIBRARY]  (or FUSION: [dominant] 70 / [accent] 30 — the accent gives: [what])
Basis: Q1 material=[objects] · Q2 posture=[word] · Q3 ambition=[level]
Recipe replacements (if any): [token] → [value] — [one phrase why]. Otherwise: "recipe without replacements".

## DNA passport (8 axes — `CONCEPT_ANATOMY §2`)
| Axis | Decision | Token/value | Trace to the object |
|------|----------|-------------|---------------------|
| 1 Material | | | |
| 2 Light/shadow | | shadow-recipe | |
| 3 Colour-logic | | 60/30/10 | accent ← [object] |
| 4 Typo-character | display+text voices | a font pair ✅cyr | |
| 5 Form | radius/line/density | | |
| 6 Composition | principle | hero-affinity | |
| 7 Motion-physics | personality | --ease/--dur | |
| 8 Signature | | ≤30 lines CSS/SVG | |
Coherence §3 checked: [the pairs material→light→radius, colour→text — ok / what was fixed]

## References (2–4, format `CONCEPT_ANATOMY §4.1`; ≤1 SaaS, ≥1 indirect world)
REFERENCE 1: [source-carrier] · EXTRACT: [technique] · TRANSFER: [token/class] · NOT TAKING: [what]
REFERENCE 2: …

## Signature element
[One. The 5 criteria of axis 8: from the object · ≤30 lines · recognisable in 1s · ≥3 carriers · not from C1–C10.]

## Tokens (copy-paste — source: the world recipe)
[The full :root block of the world palette + a typography line: Display=[font ✅cyr] Body=[font] Util=[font]]
[Radii / shadow-recipe / motion-tokens (--ease-*, --dur-*) — from the world]

## Motion-personality (inherited by @MOTION)
Easing: [from the world] · Durations: [from the world] · The signature move: [from the world]
MOTION_LIBRARY hooks: [codes] · Hero-archetype candidates: [letters from the world]

## The three "NEVER"s in this project
1. [the world's anti-pattern] 2. [...] 3. [...]

## TASTE GATE
[ ] The cliché ban-list is passed (§4) — not a single match
[ ] The "remove the logo" test: by palette+font+signature the project is distinguishable from 3 competitors from MARKET_AUDIT
[ ] Every element is derivable from the concept phrase (selectively: hero, CTA, card, background)
[ ] Pass 2 done: [N] default axes replaced / "no default detected" with a justification
[ ] The Chanel rule: [what] was removed

## Contours
Public site: the whole world. Operational (/admin,/app): inherits the palette/font/radii;
the effect-kit is NOT carried over (FRONTEND_DESIGN_EXCELLENCE §1); micro — MOTION MICRO.
```

Without a filled TASTE GATE the concept is not handed off further — this is a blocker at the level of the Component Map.

---

## §4. TASTE GATE — the filter of clichés and defaults

Run by: @CREATOR at the concept's birth; @DESIGN at a public-site SPEC and at a RESKIN; @QA_VISUAL — selectively at the visual gate (a mismatch with the concept = 🟡/🔴 with escalation to @DESIGN).

### 4.1 THE CLICHÉ BAN-LIST (a match = reassemble the axis, not "let's keep it, it's pretty")

```
C1. A cream background + a contrasting serif + terracotta/warm clay — "AI default #1".
    Allowed ONLY if the brief directly asks; otherwise — change at least one axis of the three.
C2. An almost-black background + one acid accent (acid green / vermilion) without the world's material.
C3. The "AI-startup" purple-blue gradient (hero, buttons, icons) without a brand justification.
C4. Glassmorphism as the base of the surface (glass is an effect, not a system).
C5. Inter + grey + blue "SaaS-by-default" on the PUBLIC SITE of a non-operational product.
C6. A row of three identical "icon-title-text" cards as the MAIN argument of a page.
C7. Numbering 01/02/03 where there is no real sequence of steps.
C8. Emoji instead of icons; stock "smiling people in an office/in lab coats with crossed arms".
C9. The same fade-up stagger on all sections ("the same stagger everywhere" — already 🔴 in MOTION_AMBITION_DIAL at bold+; here — at any level above restrained).
C10. "Text on the left + a mockup on the right" without passing the HERO_ARCHETYPES Protocol (duplicates their hard rule — checked here too).
```

### 4.2 Distinguishability tests

```
T1. "Remove the logo": a hero screenshot without the logo next to the heroes of three competitors from MARKET_AUDIT —
    is ours named unmistakably? If "it could be any of the four" — the concept doesn't work.
T2. "Whose world is this?": an unfamiliar person, by palette+font, must guess the niche
    (a notary? gloss? instruments?) without reading the text.
T3. "Phrase-trace": for 4 random blocks, answer how the block follows from the concept phrase.
    No answer → the block is random → remove or redo it.
```

---

## §5. THE TRANSFER MAP: concept → 4 passports (Step 5.5.B)

The project passports are filled **from the concept**, not from the head. The decision library remains the source of operational models (nav/cards/buttons), the world — the source of values.

| Passport | What it takes from VISUAL_CONCEPT | What it adds from DESIGN_DECISION_LIBRARY |
|----------|-----------------------------------|--------------------------------------------|
| `01_DESIGN_PASSPORT` | the whole palette block → §2.1; identity/thesis ← the concept phrase; the memorable gesture ← the signature; anti-patterns ← the three "never"s | semantic tint pairs (§2.2) — status semantics standard, tinted to the world |
| `02_MOTION_LANGUAGE` | motion-tokens + the signature move + hooks | the operational-contour motion profile (Quiet Operational, etc.) |
| `10_TYPOGRAPHY_PASSPORT` | the font pairs + Cyrillic flags + size posture | the License Gate procedure; zone calibration |
| `11_UI_COMPOSITION_PASSPORT` | the world's card/button language (radii/borders/shadow-recipe) | the navigation model; colour balance 65/25/7/2 |

Rule: no `[hex]` placeholders remain in the passports — either a value from the world, or an explicit `ASSUMPTION` with the world's default.

---

## §6. MODE 4 — RESKIN (@DESIGN): a qualitative concept change

**What it is:** a full world change on a live project without changing the structure. The skeleton (IA, Component Map, DOM, states, business logic) stays; the skin (tokens, fonts, effect-kit, motion-personality, signature) is replaced entirely.

**Triggers:** the user asks for "a different character"; a rebrand; the T1 "remove the logo" is failed on a live project; entering a new market/segment.

**Input:** the current `VISUAL_CONCEPT` + the reason for the change + the new ambition/posture (if changed) + the project Component Map.

### The RESKIN protocol (7 steps)

```
STEP 1 — THE CHANGE DIAGNOSIS: one phrase — why the old world doesn't work
        (not "got bored of it", but: "the audience's posture shifted from X to Y" / "the market reads us as Z").
STEP 2 — THE NEW WORLD: the CONCEPT_DNA_LIBRARY selection Protocol anew (Q1–Q3 with new inputs).
        "The same world, different colours" is forbidden — that's not a reskin, that's a token edit.
STEP 3 — THE NEW VISUAL_CONCEPT v[N+1]: the full §3 artifact, including TASTE GATE.
        The old one is archived with a SUPERSEDED mark (the passport's Decision Log).
STEP 4 — THE SWAP MAP (the main RESKIN output):
        | Layer | Was (world A) | Became (world B) | Where it lives |
        | :root tokens | ... | ... | index.css / theme.ts |
        | Fonts | ... | ... | the typography passport + the actual imports |
        | Shadow/radius/border | ... | ... | theme.ts shadows/defaultRadius + the Styles API |
        | Effect-kit | .gloss/.folio | .letterpress/.seal | theme/effects.css — the old kit is DELETED |
        | Motion-tokens | --ease-page 600ms | --ease-ink 500ms | motion.css; the signature move is reassembled |
        | Signature | [old] | [new] | add/replace 1 component |
        | Hero-archetype | reconsider per HERO_ARCHETYPES; an archetype change = a separate hero SPEC |
STEP 5 — WHAT WE DO NOT TOUCH (the stability contract):
        IA/routes · the Component Map (component names and boundaries) · the DOM structure
        (except the effect-kit wrappers and one signature component) · the Loading/Empty/Error/Success states ·
        business texts · LAYOUT_INVARIANTS geometry (equal-height and reserves are not recomputed "by eye").
STEP 6 — THE OPERATIONAL CONTOUR: /admin,/app receive ONLY the new world's palette/font/radii;
        check that §2 FRONTEND_DESIGN_EXCELLENCE is not broken (elevation, the status system are alive).
STEP 7 — ACCEPTANCE: the new concept's TASTE GATE + a full @QA_VISUAL V1–V12 run +
        the V9 baseline is recreated (the old reference is invalid) + Lighthouse ≥ 85 mobile
        (a font change = recheck loading: self-hosted WOFF2, the License Gate).
```

**Output:** `docs/artifacts/DESIGN_RESKIN_[PROJECT]_v[N].md` = the Diagnosis + the new concept (a reference) + the Swap Map + the acceptance checklist. Handed off to @DEV as a normal SPEC package.

**The one-axis-at-a-time rule (for partial requests):** if the user asks to change only the mood ("make it more expensive"), @DESIGN first checks — is it solvable INSIDE the current world (amplitude, density, photo style)? If yes — those are AUDIT fixes, not a RESKIN. A RESKIN only on a world change.

---

## §7. MODEL ECONOMICS (how to stop paying $70 for taste)

| Stage | Model class | Why |
|-------|-------------|-----|
| VISUAL CONCEPT: a world from the library | medium | selection by Q1–Q3 + copying the recipe — not creativity |
| VISUAL CONCEPT: the custom-world Constructor or FUSION | strong (1 call) | the project's single creative leap |
| Passports (the §5 derivation) | cheap | transferring values by the map |
| @MOTION CONCEPT | medium | developing a ready motion-personality with MOTION_LIBRARY hooks |
| @DESIGN SPEC / a RESKIN Swap Map | medium | decisions within recipes + references |
| @DEV layout | cheap/medium | assembly from tokens, effect-kits, COMPONENT_REGISTRY |
| @QA_VISUAL | cheap | measuring numbers (V1–V12), not judgements |

Economy rules:
- The palette and font pairs are **never generated** — they are taken from the world; a replacement = one line with a reason.
- The effect-kit is **not invented** — it is copied from the world; a new effect = only the signature (one per project).
- The strong model — at most 1–2 calls per project (a custom world; a disputed RESKIN diagnosis). Everything else is determined by files.
- Layout holes are caught by numbers (V12 collisions, V1 equal-height), not by iterations of "look again" — fewer rounds = fewer tokens.

---

## §8. EMBEDDING (a summary; the exact insertions — INTEGRATION_PATCHES_TASTE.md)

- `.cursorrules`: Law 28 "Concept before code"; ROLE MAP: @CREATOR + VISUAL_CONCEPT, @DESIGN + the RESKIN mode.
- `ROLE_CREATOR.md`: Step 5.5 → 5.5.A (the concept) + 5.5.B (the passport derivation); the user summary is extended with the line "Concept: world — signature".
- `ROLE_DESIGN.md`: mode 4 RESKIN; Tier 0 "The project world" in the golden library; the public-site SPEC input requires VISUAL_CONCEPT.
- `ROLE_MOTION.md`/`MOTION_AMBITION_DIAL.md`: CONCEPT Step 0 — read VISUAL_CONCEPT, inherit the motion-personality; "power2.out everywhere" = degeneration.
- `PROJECT_VISUAL_BOOTSTRAP_PROTOCOL.md`: Step 0 = this protocol; Step 2 takes values from the world; the Cross-Check += TASTE GATE.
- `ROLE_QA_VISUAL.md`: the TASTE GATE check in the visual gate (a mismatch of the implementation with VISUAL_CONCEPT = 🟡/🔴 → @DESIGN).

---

Reference: `roles/CONCEPT_DNA_LIBRARY.md` · `roles/CONCEPT_ANATOMY.md` · `roles/PROJECT_VISUAL_BOOTSTRAP_PROTOCOL.md` · `roles/DESIGN_DECISION_LIBRARY.md` · `roles/HERO_ARCHETYPES.md` · `roles/MOTION_AMBITION_DIAL.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/LAYOUT_INVARIANTS.md` · `roles/ROLE_QA_VISUAL.md` · `roles/COMPONENT_REGISTRY.md` · `roles/ROLE_MEDIA_ENGINEER.md` (renders the concept into real media assets) · `roles/MEDIA_SYNTHESIS_CANON.md`
Version: 1.1 | 2026-07-22
