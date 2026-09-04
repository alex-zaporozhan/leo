# MOTION_CRAFT_CANON.md
# The craft of movement: the floor, the grammar of the in-between, and the twelve signs of stiffness.
# Owner: @MOTION · applied by @FRONTEND and @DEV · measured by @QA_VISUAL.
# Position: one of three floors taken together — tokens (`VISUAL_CRAFT_CANON` §11) · movement (here) ·
#           composition (`INTERFACE_CRAFT_CANON` §3.5). That file makes a screen look right with zero
#           decisions; this one makes it MOVE right with zero decisions.

> **Why this file exists.** LEO had a motion *library* (a rich arsenal of techniques), a motion *dial*
> (permission to be bold) and a motion *role* — and still emitted a 300 ms opacity fade on everything.
> Permission is not craft. An arsenal is not craft. What was missing is what the visual side has had all
> along: **a floor you take when nothing is decided, a grammar for the part between the two endpoints, and
> a closed catalogue of what "stiff" concretely looks like.** This file is those three things.
>
> **The one-line diagnosis it answers:** an animation that does not exist used to pass every motion gate in
> this system, and no rule existed under which it failed.

---

<!-- MIRROR SOURCE (SoT): the motion floor. Any file restating a duration, easing or stagger value names
     this section as its home (RULE_INTEGRITY_PROTOCOL T2). Indexed in CONFLICT_REGISTRY. -->
## §1. THE MOTION FLOOR — what you take when there is no concept

**The absence of a motion concept is not a licence to improvise, and it is not a licence to ship nothing.
It is a licence to take the floor.** Exactly as `VISUAL_CRAFT_CANON` §11 works for colour and type: do not
choose durations. Do not choose easings. They are chosen. Take these verbatim and continue.

```
--- DURATION SCALE (four steps, and only four) ---
--motion-instant : 100ms   state feedback the finger must feel — press, toggle, checkbox
--motion-quick   : 180ms   hover, focus ring, tooltip, icon state
--motion-base    : 280ms   the default — enter, exit, expand, tab change, drawer
--motion-slow    : 480ms   a large surface moving — modal, page transition, hero entrance

Nothing is 500ms "because it felt slow". Four steps. If a moment needs a fifth, the moment is wrong.

--- EASING (three, by role — never `ease` and never `linear` for anything an eye follows) ---
--ease-exit   : cubic-bezier(0.4, 0.0, 1, 1)      leaving. Starts fast, commits, gone.
--ease-enter  : cubic-bezier(0.0, 0.0, 0.2, 1)    arriving. Decelerates into place. THE DEFAULT.
--ease-move   : cubic-bezier(0.4, 0.0, 0.2, 1)    moving between two on-screen positions.
--ease-spring : cubic-bezier(0.34, 1.56, 0.64, 1) overshoot. For ONE affirmative moment per screen —
                a success, a like, a completed step. Never for exits, never for two things at once.

`ease-in-out` on everything is the single most common signature of an interface nobody tuned (M4).

--- STAGGER (the in-between, and the reason a list feels alive) ---
--stagger-tight : 30ms   dense rows, table lines, chips
--stagger-base  : 60ms   cards, list items, form fields — THE DEFAULT
--stagger-loose : 90ms   large blocks, sections, hero lines

Cap total stagger at 400ms. A 30-item list staggers its FIRST 8 and the rest arrive together —
the reader is not made to wait for arithmetic.

--- ENTRANCE (the floor's own reveal, and it is not a bare fade) ---
opacity 0 → 1  +  translateY 8px → 0  +  (optional) scale 0.98 → 1
duration --motion-base · easing --ease-enter · stagger --stagger-base
**Containment:** the entrance offset must not push the element outside the space already reserved for it.
Use **8px flat**, or ≤10% of the element's own height — *whichever is larger*. (A 40px table row keeps the
full 8px; the percentage only ever raises the allowance for tall blocks, never lowers it below the floor's
own entrance. A larger travel is legitimate on a tall block, or behind an `overflow`/`clip-path` guard on
its container — `LAYOUT_INVARIANTS` §10.)

--- REDUCED MOTION ---
@media (prefers-reduced-motion: reduce) — translate and scale are dropped; opacity and duration remain,
clamped to --motion-quick. The interface still ANSWERS; it simply does not travel.
```

**A project with a concept overrides this floor from its world — and a world may legitimately be quieter than the floor.** A curatorial or archival world that specifies a slow opacity reveal is not stiff, it is *decided*; the difference is that it is written in the world's own tokens and cited. Stiffness is what happens when nothing was decided. (`CRAFT_LINT_SPEC` V21 carries the matching waiver, and it requires the citation.) In detail: (`CONCEPT_DNA_LIBRARY` gives every world an
easing token, a duration range and a signature move — that is where a project's real motion personality comes
from). **A project without one takes the floor and ships.** What is never acceptable is the third option this
file was written to delete: a 300 ms `opacity` transition and nothing else.

---

## §2. THE GRAMMAR OF THE IN-BETWEEN — where stiffness actually lives

Endpoint values are not an animation. `from → to` is a *transition*; an animation is what happens between
them, and this is the part every artifact in this system used to omit. Four decisions, and a spec that does
not answer them has not specified motion:

**G1 · ORDER.** When more than one thing moves, what moves first? Default: **outside in, container before
content, and the thing the eye is already looking at before the thing it is not.** A modal: scrim → panel →
panel content. A list: rows in reading order. Never all at once — simultaneity is the visual equivalent of
everyone speaking at the same time.

**G2 · OFFSET.** By how much are they separated in time? From the stagger scale above. A group with no
offset reads as one object; the offset is what tells the eye they are many. **This is the single cheapest
upgrade from stiff to alive**, and it costs one CSS custom property.

**G3 · OVERLAP.** Does the next element start before the previous one finishes? Almost always yes — an
overlap of roughly 60–70% of the previous duration. Sequences where each step waits for the last to complete
feel mechanical and take three times as long as they need to.

**G4 · ORIGIN.** Where does the movement come *from*, spatially? A dropdown grows from its trigger, not from
the centre of the screen. A drawer arrives from the edge it lives on. A deleted row collapses into the gap it
leaves. **Motion with no spatial origin is the "teleport plus fade" that makes an interface feel unbuilt** —
the element did not travel from anywhere, it simply stopped being invisible.

**G5 · WHAT THE MOTION SAYS.** Every moment is one of four verbs, and the verb picks the technique:
`ARRIVE` (enter from an origin, decelerate) · `LEAVE` (accelerate away, shorter than the arrival) ·
`CHANGE` (the same object becomes different — morph or crossfade, never destroy-and-recreate) ·
`CONFIRM` (the one place `--ease-spring` is spent).

**G6 · THE SHAPE OF LONG MOTION.** Anything over `--motion-base` that is not a straight A→B needs its
**keyframe stops written down** — the whole point of the in-between. A three-stop entrance
(`0% invisible and offset · 60% in place, slightly over · 100% settled`) is a different object from a
two-stop fade, and no artifact in this system could express the difference before this section existed.

---

<!-- MIRROR SOURCE (SoT): M1-M12. Cited by ROLE_QA_VISUAL, CRAFT_LINT_SPEC, MOTION_REFLEX, Law 39. -->
## §3. THE TWELVE SIGNS OF STIFFNESS — M1…M12

Closed catalogue, in the shape of the system's other detector sets (`VISUAL_CRAFT` X1–X12,
`INTERFACE_CRAFT` ST1–ST12, `EDITORIAL_CRAFT` Y1–Y12). **Three or more hits on a surface = 🔴, and none of
them is fixed by lengthening a duration.**

| | Sign | What it looks like | The fix |
|---|---|---|---|
| **M1** | **The bare fade** | every entrance is `opacity 0 → 1` and nothing else | §1 entrance: opacity + offset + easing |
| **M2** | **No stagger** | a list, grid or form arrives as one block | G2 — one custom property |
| **M3** | **One duration everywhere** | press, hover, modal and page transition all share a number | the four-step scale |
| **M4** | **`ease-in-out` by default** | one easing for arriving, leaving and moving alike | three easings by role (G5) |
| **M5** | **No origin** | menus, tooltips and drawers materialise in place | G4 — grow from the trigger, arrive from the edge |
| **M6** | **Destroy-and-recreate** | a changing object fades out and a new one fades in | G5 `CHANGE` — morph or crossfade the same node |
| **M7** | **No exit** | things appear animated and vanish instantly | `LEAVE`, at ~70% of the arrival duration |
| **M8** | **Two endpoints only** | nothing in the system has an intermediate keyframe | G6 — write the stops |
| **M9** | **Nothing is confirmed** | a completed action produces no affirmative moment anywhere on the surface | one `--ease-spring` moment per screen |
| **M10** | **Loading is a spinner** | every wait is the same rotating circle regardless of length or kind | skeletons that match the coming layout; progress for anything over 2s (`INTERFACE_CRAFT` I10) |
| **M11** | **Motion contradicts the world** | the world is declared editorial/organic/mechanical and the motion is the generic SaaS 200 ms fade | inherit easing, duration and signature move from `CONCEPT_DNA_LIBRARY` |
| **M12** | **Reduced-motion kills the interface** | with `prefers-reduced-motion` the UI stops answering at all, or ignores the setting entirely | §1 — drop travel, keep the answer |

**How to run it:** on any surface with interaction, walk M1–M12 and mark *hit / clean / N-A + why*. A surface
that cannot produce a hit because it has no motion at all scores **M1, M2, M7 and M9 automatically** — that
is the point, and it is the specific hole this catalogue was built to close.

---

## §4. WHERE THIS IS ENFORCED

- **@MOTION** — owns §1–§2; a `MOTION_SPEC` or `MICRO_SPEC` that answers no G-question is incomplete.
- **@DESIGN** — the DESIGN_SPEC motion block carries order, offset and origin, not just duration and easing.
- **@FRONTEND** — the floor's tokens are registered once in the project passport; nobody re-types a duration.
- **@DEV** — implements from the spec's stops; does not invent motion, and does not silently drop the stagger.
- **@QA_VISUAL** — runs M1–M12 as part of the visual verdict. **V21** below is its measurable half.
- **@LEAD** — a surface delivered with no motion decision at all is not "clean", it is unspecified.

**V21 (measurable, for `roles/CRAFT_LINT_SPEC.md`):** on a surface declaring interaction, count distinct
`transition-duration`/`animation-duration` values in use and distinct easing functions. **`durations ≥ 2` and
`easings ≥ 2`, and at least one entrance carrying a `transform`.** All three fail on a surface that ships one
fade — which is exactly the outcome every previous motion vector scored as perfect.

---

Reference: `roles/MOTION_LIBRARY.md` (the technique arsenal) · `roles/MOTION_AMBITION_DIAL.md` (how bold, and
the MICRO catalogue) · `roles/ROLE_MOTION.md` (the role and its SPEC formats) · `roles/CONCEPT_DNA_LIBRARY.md`
(a world's motion personality) · `roles/LAYOUT_INVARIANTS.md` §10–§11 (what motion may not touch) ·
`roles/VISUAL_CRAFT_CANON.md` §11 (the visual floor this file is the peer of).
Version: 1.0 | 2026-09-04
