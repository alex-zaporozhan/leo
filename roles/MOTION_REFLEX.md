# MOTION_REFLEX.md
# Reflex map of motion for @DEV and @FRONTEND. NOT a canon (the canon — MOTION_CRAFT_CANON.md — covers the "why").
# This is "what's under your fingers right now": a pattern in the diff → stop → fix. Run BEFORE handoff, over your own diff.
# Mirror at @QA_VISUAL: the same greps. If @DEV ran them already — @QA_VISUAL finds zero and doesn't send things back.

> **Why this file exists.** The craft is already in `MOTION_CRAFT_CANON`. Stiff motion does not come from not
> knowing better — it comes from the gap between "a rule in the system" and the hand typing
> `transition: opacity 0.3s`. Every check below fires on a **literal string an implementer types**, not on a topic.
>
> **The failure this closes:** for a year every motion vector in this system measured *harmlessness* — geometry
> shift 0, layout animations 0, ΔscrollY 0 — all of which a page with no animation satisfies perfectly. Nothing
> looked at what was typed. These greps do.

---

## HOW TO USE (30 seconds before handoff)

1. Run the greps in §1 **over your own diff**, not over the file as it now stands.
2. Every hit is a **stop**, not a warning: answer its question or fix it. There is no "probably fine".
3. Write one line in your report: `MOTION REFLEX: <n> triggers, <n> fixed, <n> N/A + reason`.
   The line is required even when the count is zero — silence reads as "not run" (Law 12).
4. A trigger you consciously leave goes in `NOT DONE:` with the reason (Law 13).

**Applies to** any diff touching `transition`, `animation`, `@keyframes`, `transform`, a motion library call
(`motion.`, `animate(`, `useSpring`, `gsap.`, `Transition`, `AnimatePresence`), or a component that mounts a list.

---

## §1. GREP SELF-CHECK (trigger in the diff → stop-questions → fix)

### R1 · The bare fade — catches M1
```
grep -nE "transition[^;]*opacity" <diff> | grep -v "transform"
grep -nE "@keyframes[^}]*\{[^}]*opacity[^}]*\}" <diff>
```
**Stop:** is this an *entrance*? An entrance that animates opacity and nothing else is the single most common
defect in this system's history.
**Fix:** the floor entrance — `opacity 0→1` **+** `translateY 8px→0`, `--motion-base`, `--ease-enter`
(`MOTION_CRAFT_CANON` §1). Hover, focus and colour changes are legitimately opacity/colour only — say so.

### R2 · A rendered list with no stagger — catches M2
```
grep -nE "\.map\(" <diff> -A6 | grep -E "transition|animate|initial|variants"
```
**Stop:** does any sibling get a different delay?
**Fix:** `transition-delay: calc(var(--stagger-base) * var(--i))` or the library's `staggerChildren`.
Cap at 8 elements / 400 ms total; the rest arrive together (§1). **This is one line and it is the cheapest
upgrade from stiff to alive in the whole system.**

### R3 · One duration everywhere — catches M3
```
grep -noE "[0-9]+m?s" <diff> | grep -oE "[0-9]+m?s$" | sort -u | wc -l      # < 2 → stop
grep -nE "transition[^;]*(0\.3s|300ms|0\.2s|200ms)" <diff>
```
**Stop:** does a press, a hover and a modal really deserve the same number?
**Fix:** the four-step scale — `--motion-instant/quick/base/slow`. A hand-typed `300ms` is almost always a
duration nobody chose.

### R4 · `ease-in-out` / `ease` / `linear` by default — catches M4
```
grep -nE "(ease-in-out|[^-]ease[,;)]|linear)" <diff>
```
**Stop:** is this thing arriving, leaving, or moving between two on-screen positions?
**Fix:** `--ease-enter` (arriving, the default) · `--ease-exit` (leaving) · `--ease-move` (travelling) ·
`--ease-spring` (one confirming moment per screen). `linear` is for a spinner and nothing an eye follows.

### R5 · No spatial origin — catches M5
```
grep -nE "(Menu|Dropdown|Popover|Tooltip|Drawer|Modal|Sheet)" <diff> -A4 | grep -E "transition|animate"
grep -nE "transform-origin" <diff>          # absent near a scaling panel → stop
```
**Stop:** where does this come **from**? A dropdown grows from its trigger; a drawer arrives from its edge.
**Fix:** `transform-origin` at the trigger, or a translate from the owning edge (G4). Motion with no origin is
the "teleport plus fade" that makes an interface feel unbuilt.

### R6 · Destroy-and-recreate instead of change — catches M6
```
grep -nE "(key=\{[^}]*(status|state|tab|value|mode)[^}]*\})" <diff>
grep -nE "\{(isOpen|show|visible|active)[^}]*&&[^}]*<" <diff>
```
**Stop:** is the *same* object becoming different, or is it genuinely a different object?
**Fix:** a changing object morphs or crossfades on the same node. A `key` bound to a status forces React to
unmount and remount, which is why the badge blinks instead of transitioning (G5 CHANGE).

### R7 · An entrance with no exit — catches M7
```
grep -nE "(enter|in|mount|appear)" <diff> -A3 | grep -E "transition|animate"   # then grep the exit counterpart
grep -nE "AnimatePresence|Transition" <diff>                                    # absent around a conditional unmount → stop
```
**Stop:** what happens when this leaves?
**Fix:** an exit at roughly 70% of the entrance duration, `--ease-exit`. Things that appear animated and vanish
instantly read as a bug, not as speed.

### R8 · Layout properties animated — the one true 🔴, catches the invariant
```
grep -nE "transition[^;]*(top|left|right|bottom|width|height|margin|padding)" <diff>
grep -nE "@keyframes[^}]*(top|left|width|height|margin|padding):" <diff>
```
**Stop:** none. This is a defect, not a question — it reflows on every frame.
**Fix:** `transform` instead. **Note what is NOT a hit:** `transform: translateY()` in the document flow is
correct and expected — the invariant owns reflow and `scrollY`, never movement (`LAYOUT_INVARIANTS` §10–§11).

### R9 · Reduced motion killed or ignored — catches M12
```
grep -nE "prefers-reduced-motion" <diff>                        # absent in a file with animation → stop
grep -nE "prefers-reduced-motion" <diff> -A3 | grep -E "0\.0*1m?s|none !important"
```
**Stop:** with the setting on, does the interface still **answer**?
**Fix:** drop travel (`transform: none`), keep opacity and clamp to `--motion-quick`. Clamping everything to
`.001ms` is not accessibility — it is an interface that stopped responding.

### R10 · A wait with no shape — catches M10
```
grep -nE "(Spinner|Loader|isLoading|CircularProgress)" <diff>
```
**Stop:** how long is this wait, and does the user know what is coming?
**Fix:** a skeleton shaped like the arriving layout for a short wait; real progress for anything over 2 s
(`INTERFACE_CRAFT_CANON` I10). One rotating circle for every wait is the default nobody chose.

### R11 · Nothing is ever confirmed — catches M9
```
grep -nE "(onSuccess|isSuccess|toast\.success|saved|completed)" <diff> -A4 | grep -E "transition|animate|spring"
```
**Stop:** does the completed action produce **any** affirmative moment on the surface?
**Fix:** exactly one `--ease-spring` moment per screen (G5 CONFIRM). Not a toast — the object itself.

### R12 · Motion that ignores the world — catches M11
```
grep -nE "cubic-bezier|--ease-|--motion-|--dur-" <diff>
```
**Stop:** does the project declare a world (`VISUAL_CONCEPT_[PROJECT].md`)? Then its easing, duration range and
signature move come from `CONCEPT_DNA_LIBRARY`, not from the floor and not from your fingers.
**Fix:** take the world's tokens. The floor is for projects with no world — it is a floor, not a ceiling.

---

## §2. SIGNATURE COMPARISON (quick recognition of bad → good)

| ✗ what gets typed | ✓ what it should be | Sign |
|---|---|---|
| `transition: opacity .3s ease` | `transition: opacity var(--motion-base) var(--ease-enter), transform var(--motion-base) var(--ease-enter)` | M1 · M3 · M4 |
| `{items.map(i => <Row/>)}` with one shared transition | `style={{'--i': index}}` + `transition-delay: calc(var(--stagger-base) * var(--i))` | M2 |
| `.menu { transition: transform .2s }` | `+ transform-origin: top right;` (the trigger) | M5 |
| `<Badge key={status}/>` | one node, crossfading its content | M6 |
| `{open && <Panel/>}` | `<AnimatePresence>` / a CSS exit state | M7 |
| `transition: height .3s` | `transform: scaleY()` + a reserved box | 🔴 §10 |
| `transition-duration: .001ms !important` | `var(--motion-quick)` + `transform: none` | M12 |

---

## §3. THE STOP-CONDITION

A surface is not ready for handoff while **any** of these is true:
- an entrance animates `opacity` alone (R1);
- a rendered list of siblings shares one delay (R2);
- fewer than two distinct durations or two distinct easings are in use (R3, R4 — this is `CRAFT_LINT_SPEC` **V21**);
- any layout property is animated (R8 — this one is 🔴 on its own);
- `prefers-reduced-motion` is absent, or present and disabling the interface (R9).

Three or more `M`-hits from `MOTION_CRAFT_CANON` §3 is 🔴 and is **not** fixed by lengthening a duration.

---

## §4. REFERENCE MAP (where to go for the "why")

`roles/MOTION_CRAFT_CANON.md` — §1 the floor (tokens) · §2 the grammar of the in-between (G1–G6) · §3 M1–M12 ·
`roles/LAYOUT_INVARIANTS.md` §10–§11 — what motion may not touch, and why `transform` is not on that list ·
`roles/MOTION_LIBRARY.md` — the technique arsenal · `roles/MOTION_AMBITION_DIAL.md` — how bold, and the MICRO
catalogue · `roles/CONCEPT_DNA_LIBRARY.md` — a world's own motion personality · `roles/CRAFT_LINT_SPEC.md` §1d —
V21, the measurable half.
Version: 1.0 | 2026-09-04
