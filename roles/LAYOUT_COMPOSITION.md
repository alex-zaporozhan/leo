# LAYOUT_COMPOSITION.md
# Layout Grammar: how to BUILD positioning so that overlaps cannot arise by construction.
# Sister file to LAYOUT_INVARIANTS: invariants CHECK the result (§1–§12, V1–V12), grammar TEACHES you how to build.
# Readers: @DEV (before any UI code), @DESIGN (SPEC), @FRONTEND (project primitive map). Universal:
# principles are library-agnostic; the Mantine column is the mapping for our stack.

> **Why this file.** "Buttons overlapping, a pile of micro-bugs" — that is not bad luck, it is the result of one and the same thing:
> elements decide for themselves where to stand (margin, absolute, magic px). The grammar flips the model:
> **space belongs to the container**. Anyone who builds by it physically cannot produce an overlap:
> occlusion is only possible through extraction from flow, and extraction here is licensed.

---

## §1. MENTAL MODEL — THREE LAWS OF SPACE

**Screen = a tree of containers.** Each node is responsible for distances between its OWN children and nothing else.

```
LAW 1 — CHILDREN DO NOT PUSH EACH OTHER.
  Distance between siblings is set by the parent (gap / padding), never by the siblings themselves (margin).
  Margin between elements of a group = a child making decisions for the parent = the source of overlaps (invariant §12.3).
  A child may use margin only "outside a section" — and even then, better via a Stack parent.

LAW 2 — WIDTH FLOWS TOP-DOWN, HEIGHT GROWS BOTTOM-UP.
  Container distributes width (columns, max-width); content determines height (+ reserves: LAYOUT_INVARIANTS §1–§2).
  A fixed height on a text container is a BUILD error, not "we'll trim it later".
  A fixed width on a flex child without wrap on the parent is a blueprint for overlap (§12.3).

LAW 3 — FLOW IS LAW, EXTRACTION IS A LICENCE.
  Everything lives in flow. position:absolute/fixed — an exception by licence §12.2 (anchor + reserved space).
  Before extraction — mandatory question: "which §2 primitive solves this in flow?" There is almost always an answer.
```

Consequence worth learning verbatim: **an overlap without extraction from flow and without negative margins
is impossible**. So any overlap is a violation of Law 1 or Law 3, and it is fixed there — not by "nudging 3px".

---

## §2. PRIMITIVE ALGEBRA — eight shapes from which any screen is assembled

Every layout block must be ONE of the primitives. If no primitive fits — the block is not fully thought through:
return it to @DESIGN as a composition question; it is not a task for a CSS hack.

| # | Primitive | Purpose | CSS core | Mantine |
|---|----------|---------|----------|---------|
| P1 | **STACK** | vertical flow with rhythm | `display:flex; flex-direction:column; gap:var(--space-N)` | `Stack` |
| P2 | **CLUSTER** | horizontal group (buttons, chips, meta) | `display:flex; flex-wrap:wrap; gap:var(--space-N); align-items:center` | `Group` |
| P3 | **GRID** | equal cards, auto columns | `display:grid; grid-template-columns:repeat(auto-fit,minmax(Wpx,1fr)); gap` | `SimpleGrid` |
| P4 | **SIDEBAR** | fixed panel + stretchy body | `display:grid; grid-template-columns:Wpx minmax(0,1fr)` | `Grid`/`AppShell` |
| P5 | **SWITCHER** | N columns → column when tight | container condition: `flex-wrap` + `flex-basis:calc((Wpx − 100%)*999)` or GRID auto-fit | `SimpleGrid` cols |
| P6 | **COVER** | screen cover: header/center/footer | `min-height:100svh; display:grid; grid-template-rows:auto 1fr auto` | — |
| P7 | **FRAME** | media with reserved space | `aspect-ratio:X/Y; overflow:hidden; & > img{object-fit:cover}` | `AspectRatio` |
| P8 | **CENTER** | reading column | `max-width:65ch(–75ch); margin-inline:auto; padding-inline:var(--space-N)` | `Container` |

**Algebra rules:**
- Primitives NEST (COVER → CENTER → STACK → CLUSTER — a typical hero), they are not mixed within a single node.
- CLUSTER is the only legitimate way to place interactives in a row. A row of buttons ≠ "button + margin-left".
- GRID handles card height equality (invariant §1) by itself — do not equalise heights manually.
- SWITCHER eliminates media queries for 80% of cases: columns fold based on CONTAINER width, not the window.
- @FRONTEND maintains the "primitive → project component" map in FRONTEND_PASSPORT; @DEV does not invent a ninth primitive.

---

## §3. SPATIAL SCALE AND LAW OF PROXIMITY (Gestalt by the numbers)

Project scale — from the passport (axis 5 of the DNA sets the increment): base row `4 · 8 · 12 · 16 · 24 · 32 · 48 · 64`.
Raw px outside the scale in components are forbidden — use only `var(--space-*)` / theme tokens.

**Law of proximity, operationalised:**

```
gap INSIDE a group  <  gap BETWEEN groups, coefficient ≥ 2×.
   (label↔field 4–8  ·  field↔field 16  ·  section↔section 48–64)
padding of container ≥ gap of its children   (otherwise children "stick" to the walls more than to each other).
One STACK — one gap. Different distances = different nested STACKs, not "margin on the third child".
```

Related — closer than unrelated; this is the ONLY thing that communicates structure to the user without borders.
If a divider line or border was needed to separate groups — first check whether the 2× coefficient has been broken.

---

## §4. SIZING POLICY (who is what size and why)

```
TEXT CONTAINERS:  height = content + reserve (line-clamp + min-height for N lines — invariants §2/§4).
                  height:Npx on a text block — forbidden by grammar, not just checking.
VARIABLE WIDTH    (buttons/badges/statuses): min-width for the longest variant (§3 of invariants);
                  disabled/loading do not change width — state lives within the reserve.
READING:          line length 45–75ch (CENTER); wider = fatigue, narrower = choppy rhythm.
FLEX CHILDREN:    min-width:0 required for any child with truncation (truncate only works this way).
MEDIA/ICONS:      always a rigid box (FRAME / width+height on svg) — icon does not "push out" text.
```

---

## §5. ACTION GRAMMAR — buttons that do not overlap

Button groups are where 80% of overlaps occur; the grammar is stricter for them:

```
G1. ONE PRIMARY per view. A second "main" action means hierarchy is not decided: return to @DESIGN.
G2. ANY action group = CLUSTER (flex + gap ≥ 8 + wrap). No exceptions. Margin chains and
    negative margins on interactives — forbidden (§12.3), including "quick fixes".
G3. ORDER is fixed ONCE in UI_COMPOSITION_PASSPORT (e.g.: dialogs/forms — [Cancel][Primary],
    primary on the right; list pages — primary in header on the right) — and is not reinvented per screen.
G4. DON'T FIT — WRAP or COLLAPSE: wrap (already included in CLUSTER) or overflow into
    ActionMenu "⋯" after 3–4 actions. Shrinking buttons/cutting labels — forbidden.
G5. STICKY/FIXED ActionBar: COVER footer or fixed by licence §12.2 — layout reserves height
    + safe-area; the bar has no right to cover the last form field.
G6. Touch: interactive ≥ 44×44 (icon-button — padding, not "16px icon"); ≥ 8px from another element's text.
G7. FORMS: one column by default (two — only short pairs like first/last name via CLUSTER);
    label→field→hint/error binding — one STACK with error reserve (min-height for one line: an error appearing
    does NOT shift neighbouring fields — §7 of invariants); field+button row ("promo code") — CLUSTER
    with align-items:flex-start, button does not grow from another element's error.
```

---

## §6. EXTRACTION FROM FLOW — three questions before absolute

Full layer rules — `LAYOUT_INVARIANTS §12` (Z-scale, anchor, reserve). Here — the decision "is absolute even needed":

```
1. Is this DECORATION (not interactive, not meaningful text)?    NO → find a §2 primitive.
2. Does the anchor parent RESERVE space (padding-zone/compensation)?  NO → absolute is forbidden.
3. Is there a layer component (Portal/Popover/Tooltip/Menu)?    YES → use it, not a custom implementation.
```

Three "correct" answers → absolute by §12.2. Any other combination → flow.

---

## §7. OVERLAP DIAGNOSIS PROTOCOL (when it still happens)

Fix the CAUSE at the container level — never "shift the child by Npx":

```
S1. WHO OWNS the space between the pair? margin on child → move to parent gap (Law 1).
S2. CAN THE ROW WRAP? no wrap / fixed width on children → CLUSTER/SWITCHER (Law 2, §12.3).
S3. WHO IS EXTRACTED from flow? absolute without anchor-reserve / fixed without compensation → §6 + §12.2.
S4. LAYERS: computed z-index from the scale? relative without need? → §12.1, remove unnecessary stacking contexts.
S5. FIX at the container level; record the finding in V12 format
    (`[V12] selector ∩ selector = Npx² @vp — cause — rule — fix`).
```

Two overlaps with the same cause in one wave → these are not two bugs, but a systemic grammar gap:
escalate to @FRONTEND (primitive map) or @AUDITOR (if fixed for the third time).

---

## §8. FLEXIBILITY — grammar, not a cage

The rules above say HOW to build so you never have to think about overlaps. Deviation is possible — if named
out loud: which law is being violated, why the project world requires it, and the line is recorded in the SPEC/passport.
A silent hack ("here I'll use margin-left, we'll fix it later") — forbidden: it IS the source of "piles of micro-bugs".
Simple rule: **if you cannot explain the positioning via a container and a primitive — the positioning is not ready.**

@DEV minimum checklist before handover (supplements the LAYOUT_INVARIANTS checklist):

```
□ Every block is one of P1–P8; there is no ninth primitive in the task
□ Not a single margin between siblings; distances — gap/padding of containers on the scale
□ Action groups — CLUSTER; one primary; order from the passport
□ Not a single fixed height on text; min-width:0 on truncated flex children
□ Every absolute has passed the three questions of §6
```

---

Reference: `roles/LAYOUT_INVARIANTS.md` (checking, §12 layers/collisions) · `roles/ROLE_QA_VISUAL.md` (V12) · `roles/FRONTEND_DESIGN_EXCELLENCE.md` §8 (Mantine) · `roles/TEMPLATE_UI_COMPOSITION_PASSPORT.md` (fixing action order) · `roles/COMPONENT_REGISTRY.md`
Version: 1.0 (system v6.20) | 2026-07-06
