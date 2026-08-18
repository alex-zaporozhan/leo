# PROJECT_VISUAL_BOOTSTRAP_PROTOCOL

> Universal private protocol for starting a new project.
> Goal: before the first UI code, create a coherent visual logic system, not a set of random screens.

> **[v6.20] Step 0 (mandatory, before Step 1): `roles/VISUAL_CONCEPT_PROTOCOL.md`** — birth of `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` from a world in `roles/CONCEPT_DNA_LIBRARY.md` + TASTE GATE (blocker). Step 2 "Choose A Design Direction" is executed WITHIN the chosen world: world = values (hex, fonts, effects, motion), decision-library = operational models. Cross-Check is supplemented: [ ] TASTE GATE passed · [ ] no `[hex]` placeholders remain in passports.

---

## When to run

Launched by @CREATOR after the initial product package and before handing the project off to architecture/development, if the product has any user UI:

- admin/app/dashboard;
- learner/customer portal;
- internal ops tool;
- marketing/public surface;
- mobile/PWA.

If the product is API-only for now, the protocol creates a short placeholder with a "UI defer" decision.

---

## Input

Minimum package:

```text
Product one-liner:
Target users:
Market:
Primary job-to-be-done:
Usage context:
Emotional posture:
Commercial posture:
Do-not-do list:
```

If a field is unknown, @CREATOR fills in a reasonable default for the market and marks it `ASSUMPTION`.

---

## Project output files

Files are created in the specific project's documentation. Paths are chosen by local project canon, but the document structure is stable:

```text
01_DESIGN_PASSPORT.md
02_MOTION_LANGUAGE.md
10_TYPOGRAPHY_PASSPORT.md
11_UI_COMPOSITION_PASSPORT.md
```

For projects with different numbering, renaming is allowed, but the four layers are mandatory:

1. Visual identity + tokens + status language.
2. Motion language.
3. Typography.
4. UI composition: navigation, cards, buttons, color balance.

---

## Process

### Step 0 — Concept World (mandatory before Step 1)

Run `roles/VISUAL_CONCEPT_PROTOCOL.md`:
- Choose a world from `roles/CONCEPT_DNA_LIBRARY.md` → TASTE GATE (blocker).
- The world defines values: hex palette, font pairs, effect kit, motion personality, Mantine skin.
- Output: `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md`.

All subsequent steps are executed **within** the chosen world. World values are not overridden by personal preference.

### Step 1 — Product Meaning

Formulate:

- what the user should feel in the first 3 seconds;
- what the user should understand without reading documentation;
- where the product must be calm;
- where the product can be memorable.

Format:

```markdown
Product feeling: [one word]
Memorable gesture: [one visual/action pattern]
Primary reference: [project design passport OR specific product + screen]
Secondary references: [2-4 — omit if project-native canon exists]
Anti-patterns: [what must never appear]
```

**Project-native mode:** if the client requires a unique brand (not a SaaS clone), Primary reference = `docs/knowledge/design/*` + logo; external products are **not** listed as visual benchmarks. A structural competitor reference (IA only) is acceptable as a separate line.

### Step 2 — Choose A Design Direction

Use `DESIGN_DECISION_LIBRARY.md` (within the chosen concept world from Step 0):

- choose one primary palette direction;
- choose typography posture;
- choose card/surface model;
- choose navigation model;
- choose motion ambition.

The decision is fixed as one coherent set. Do not mix "the best of everything".

### Step 3 — Generate Four Passports

Fill the templates:

- `TEMPLATE_DESIGN_PASSPORT.md`;
- `TEMPLATE_MOTION_LANGUAGE.md`;
- `TEMPLATE_TYPOGRAPHY_PASSPORT.md`;
- `TEMPLATE_UI_COMPOSITION_PASSPORT.md`.

Each passport must contain:

- specific tokens or clear placeholders (no `[hex]` placeholders in final version);
- references;
- forbidden effects/patterns;
- implementation notes for @DEV;
- QA checklist for @QA_ARCH and @QA_VISUAL.

### Step 4 — Cross-Check

Before handoff to @LEAD, verify:

```text
[ ] TASTE GATE passed (roles/VISUAL_CONCEPT_PROTOCOL.md)
[ ] No [hex] placeholders remain in passports
[ ] Typography matches emotion and audience.
[ ] Palette has readable foreground/background pairs.
[ ] Navigation order follows product journey, not database order.
[ ] Cards/buttons/statuses use one language.
[ ] Motion supports state changes, not decoration.
[ ] There is no dependency on project-private roles inside project docs.
[ ] There are no role markers in application code instructions.
```

---

## @CREATOR → @LEAD Handoff

```markdown
VISUAL BOOTSTRAP PACKAGE

Created:
- 01_DESIGN_PASSPORT.md
- 02_MOTION_LANGUAGE.md
- 10_TYPOGRAPHY_PASSPORT.md
- 11_UI_COMPOSITION_PASSPORT.md

Design direction:
- Feeling:
- Memorable gesture:
- References:
- Palette:
- Typography:
- Composition:

Assumptions:
- ...

Ready for:
@ARCH / @FRONTEND / @DESIGN / @DEV
```

---

## Prohibitions

- Do not copy the current project as a content template.
- Do not embed paths from the private `roles/` layer into project `docs/` or code.
- Do not use one default "blue admin SaaS" for all markets.
- Do not leave typography at `system-ui` as the final decision.
- Do not create UI without a navigation/card/button composition canon.
