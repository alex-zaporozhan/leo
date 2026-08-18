# LAYOUT_INVARIANTS.md
# Canon of layout invariants — deterministic rules for layout stability
# Mandatory for @DEV on any layout work and for @QA_VISUAL on audit.
# Source of truth for measurable geometry rules (not taste — taste is in FRONTEND_DESIGN_EXCELLENCE).

> **Principle:** "A jumping block is not a matter of taste, but a violated invariant. An invariant is either met deterministically, or it is not."
> This document closes the gap that caused layouts to behave like a "house of cards": tokens were described, but **geometry stability under variable content was not**.

---

> **[v6.20+] The companion file is `roles/LAYOUT_COMPOSITION.md` (grammar of CONSTRUCTION):** that file covers how to build so that invariants hold automatically (three laws of space, 8 primitives, law of proximity, button grammar).
> This file covers deterministic VERIFICATION of the finished result. @DEV reads the grammar before coding; @QA_VISUAL measures against this file.

## WHAT THIS IS AND HOW IT DIFFERS

| Document | Answers the question |
|----------|--------------------|
| `roles/FRONTEND_DESIGN_EXCELLENCE.md` | How it should **look** (tokens, hierarchy, taste, reference) |
| **`roles/LAYOUT_INVARIANTS.md` (this file)** | How it should **hold** (geometry under any content and viewport) |
| `roles/ROLE_QA_VISUAL.md` | How it is **measured** (render → metric → threshold) |

Each invariant below: **rule → why it breaks without it → reference code → how `@QA_VISUAL` verifies it**. All rules are deterministic: when met, the corresponding `@QA_VISUAL` metric must return "pass" as a number.

---

## §0. ABSOLUTE LAW for `.cursorrules` (Law 26)

> Inserted in `.cursorrules` after Law 25. Full patch text — in `INTEGRATION_PATCHES.md`.

```
26. **Layout invariants** — geometry is stable under variable content and across all viewports,
    deterministically, not "by eye". Cards/tiles/rows at the same level have equal height
    regardless of text length (equal-height grid + line-clamp with reserved height).
    Variable-width elements (buttons, badges, statuses) have min-width for the longest
    variant. Any content-driven size has a ceiling (max-height/line-clamp/overflow guard).
    Media — with reserved aspect-ratio. Hover/focus/press do NOT change geometry in the flow
    (only colour/shadow/transform). Horizontal overflow is forbidden on any viewport.
    Canon: roles/LAYOUT_INVARIANTS.md. Verified by @QA_VISUAL (render → measure → compare):
    violation of a measurable invariant = 🔴, @QA_VISUAL does not issue 🟢.
```

---

## §1. EQUAL-HEIGHT — siblings are equal in height

**Rule:** cards/tiles/rows at the same level in a grid or row have **identical height**, determined by the layout, not by the content.

**Why it breaks without it:** with `display:flex`/`inline-block` without stretching, card height = its content height. A two-line heading → taller card → the row "jumps". This is exactly the symptom "one has two title lines — the size jumped".

**Reference code:**
```css
/* CSS Grid: equal-height rows — height is taken from the tallest sibling, the rest stretch */
.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 16px;
  align-items: stretch;   /* critical: siblings stretch to row height */
}
.cards-grid > .card { height: 100%; } /* card occupies the full cell height */

/* Flex row: same effect */
.cards-row { display: flex; gap: 16px; align-items: stretch; }
.cards-row > .card { flex: 1 1 0; } /* equal width + equal height via stretch */
```
Inside the card — `display:flex; flex-direction:column` so that the footer/button sticks to the bottom (`margin-top:auto`), and with different content heights the bottom remains aligned.

**`@QA_VISUAL` verification:** `V1` — `siblingHeightDelta('.card') == 0` on the `longtext` fixture.

---

## §2. RESERVED TEXT HEIGHT — the heading reserves height

**Rule:** multi-line text fields (card heading, description) have a **fixed number of lines** via `line-clamp` and a **reserved min-height** for that number of lines. A 1-line and a 2-line text occupy the same vertical space.

**Why it breaks without it:** even with equal-height, the "internal" difference (1 line vs 2) shifts elements below the heading to different positions between siblings — buttons/metrics end up at different heights.

**Reference code:**
```css
.card-title {
  display: -webkit-box;
  -webkit-line-clamp: 2;          /* maximum 2 lines */
  -webkit-box-orient: vertical;
  overflow: hidden;
  min-height: calc(2 * 1.3em);    /* reserve exactly for 2 lines — a 1-line heading takes the same space */
  line-height: 1.3;
}
```
A long heading is clipped with an ellipsis; a short one holds the same height. Full text — in `title`/`Tooltip` (see §8).

**`@QA_VISUAL` verification:** `V1` (via sibling equality) + `V2` (`clippedText == 0` — clipping is explicit, not silent).

---

## §3. MIN-WIDTH for the longest variant — variable-width elements

**Rule:** buttons, badges, status chips, tabs, metrics whose content changes ("Save"/"Saving…", "1"/"1000", "Active"/"Awaiting payment") have `min-width`/reserve for **the longest variant**, so they do not "jitter" on state change.

**Why it breaks without it:** "Save" button → "Saving…" changes width → neighbours shift → "runaway". Counter `9` → `10` shifts the layout.

**Reference code:**
```css
.btn-async { min-width: 132px; }     /* fits the longest state label */
.badge { min-width: 64px; text-align: center; }
.metric-value { font-variant-numeric: tabular-nums; } /* equal-width digits — counters don't jump */
```
For binary label switching — reserve for the longer one; for numbers — `tabular-nums`.

**`@QA_VISUAL` verification:** `V7` — `geometryShiftOnState == 0`; changing button state in spec does not move neighbours.

---

## §4. SIZE CAP — every content-driven size has a ceiling

**Rule:** no block grows unboundedly from its content. Lists inside cards — with scroll or clamp; tags — with wrap/truncation by count; modals/drawers — with `max-height` and internal scroll.

**Why it breaks without it:** a long note/many tags "inflate" the card, breaking equal-height and pushing content off screen.

**Reference code:**
```css
.tag-row { display:flex; flex-wrap:wrap; gap:6px; max-height: calc(2 * 28px); overflow:hidden; }
.note-preview { -webkit-line-clamp: 3; display:-webkit-box; -webkit-box-orient:vertical; overflow:hidden; }
.drawer-body { max-height: calc(100vh - 120px); overflow-y: auto; } /* §6 desktop-app scroll */
```

**`@QA_VISUAL` verification:** `V2` (overflow ≤ 0) + `V1` (siblings equal despite different content volume).

---

## §5. ASPECT-RATIO for media — space reserved before load

**Rule:** images, avatars, previews, videos, charts have reserved geometry (`aspect-ratio` or fixed sizes) **before** content loads.

**Why it breaks without it:** an image loads and "pushes" text down — high CLS, "jump" on load.

**Reference code:**
```css
.thumb { aspect-ratio: 16 / 9; width: 100%; object-fit: cover; }
.avatar { width: 40px; height: 40px; border-radius: 50%; flex: 0 0 40px; } /* does not shrink */
```

**`@QA_VISUAL` verification:** `V3` — CLS ≤ 0.1; frames before/after load without shift.

---

## §6. DESKTOP-APP SCROLL — operational screens do not scroll as a whole

**Rule (synchronised with `TECH_PASSPORT_FRONTEND_UI_LOGIC §9.1`):** on operational screens (chat, calendar grid, kanban, dense tables) **there is no global page scroll**; only the column/list body scrolls. Column headers — outside the scroll area or `position: sticky`.

**Why it breaks without it:** "landing-page" scroll of the whole page on a work screen, floating headers, loss of context.

**Reference code:**
```css
.work-root { height: calc(100vh - var(--shell-offset)); display:flex; flex-direction:column; min-height:0; }
.work-col { flex:1; min-height:0; display:flex; flex-direction:column; }
.work-col__header { flex:0 0 auto; position:sticky; top:0; }   /* header does not scroll away */
.work-col__body { flex:1; min-height:0; overflow-y:auto; }      /* only the body scrolls */
```
`min-height:0` on the flex chain is mandatory — otherwise the body will not shrink and a global scroll will appear.

**`@QA_VISUAL` verification:** `V2` — `overflow.y('body') ≤ 0` on operational routes; header stays in place when the body scrolls.

---

## §7. INTERACTION ZERO-SHIFT — states do not move the layout

**Rule:** hover/focus/active/selected/disabled change **only** colour, background, shadow, `transform`, and `opacity`. Never — `width/height/margin/padding/border-width` that affect the flow.

**Why it breaks without it:** hover adds `border: 2px` → element grows by 2px → row jitters; "glitchy buttons" on hover.

**Reference code:**
```css
.row { transition: background-color 150ms ease; }
.row:hover { background: var(--mantine-color-gray-0); } /* no shift */

/* If a border on hover is needed — always reserve it, only change the colour */
.tile { border: 1px solid transparent; }
.tile:hover { border-color: var(--mantine-color-gray-3); } /* width does not change → no shift */

/* focus-visible is mandatory and does not shift the flow */
.clickable:focus-visible { outline: 2px solid var(--input-focus); outline-offset: 2px; }
```

**`@QA_VISUAL` verification:** `V7` — `geometryShiftOnState('hover'|'focus') == 0`; `:focus-visible` is present.

---

## §8. OVERFLOW GUARD + TRUNCATION CONTRACT — long content is always handled

**Rule:** any user text of variable length (name, email, title, amount, URL) has a truncation contract: `truncate` (1 line) or `line-clamp` (N lines) + full text in `title`/`Tooltip`. Long non-breaking strings (email, tokens) — with `overflow-wrap`/`word-break`.

**Why it breaks without it:** a long email without spaces stretches the container → horizontal overflow → scrollbar on the whole page.

**Reference code:**
```css
.truncate { white-space:nowrap; overflow:hidden; text-overflow:ellipsis; min-width:0; }
.break-anywhere { overflow-wrap:anywhere; word-break:break-word; }
/* min-width:0 on a flex descendant is mandatory — otherwise truncate does not work inside flex */
.flex-cell { min-width: 0; }
```
Contract: if truncated — must provide the full text on hover (`title={fullText}`).

**`@QA_VISUAL` verification:** `V2` — `overflow.x ≤ 0`, `clippedText == 0` (no silent clipping without ellipsis/clamp).

---

## §9. GRID-GAP DISCIPLINE — spacing from the system, symmetrically

**Rule:** distances are set via the container's `gap` and spacing tokens, not via `margin` on each descendant. Padding is symmetric (left=right, top=bottom), unless asymmetry carries meaning.

**Why it breaks without it:** manual `margin` on children creates rhythm desync and asymmetry; "wandering" column edges.

**Reference code:**
```css
.stack { display:flex; flex-direction:column; gap: var(--space-md); } /* not margin on children */
.card { padding: 16px; }            /* symmetric */
.context-bar { padding: 12px 16px; } /* meaningful asymmetry (vertical < horizontal) — permitted */
```

**`@QA_VISUAL` verification:** `V6` — padding asymmetry ≤ 1px (or explicitly meaningful); sibling column edges aligned ≤ 1px.

---

## §10. NO-LAYOUT-ANIMATION — no layout properties; transform only in island (§11)

**Rule (synchronised with `MOTION_LIBRARY §VII` and `ROLE_FRONTEND` Pillar 9):** transitions do **not animate** `top/left/right/bottom/width/height/margin/padding` — reflow on every frame.

**Two contours:**

| Contour | Permitted | Forbidden |
|---------|-----------|-----------|
| **Document flow** (sections, RevealSection) | **opacity only** — §11 | `translateY` / `scale` on sections |
| **Motion island** (carousel, dots, strip) | `transform` + `opacity` | layout properties; influence on document `scrollY` |

**Why it breaks without it:** `translateY` reveal shifts content and scroll; `width` animation jitters neighbours.

**Reference code:**
```css
/* ✅ flow */ .prism-reveal--fade { transition: opacity 0.35s ease; }
/* ✅ island */ .dot.is-active { transform: scaleX(3); }
/* ❌ flow */ .bad-reveal { transform: translateY(20px); }
/* ❌ */ .bad { transition: left 200ms, width 200ms; }
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: .001ms !important; transition-duration: .001ms !important; }
}
```

**`@QA_VISUAL` verification:** `V8` — 0 layout property animations; run in `reduced` disables heavy ones.

---

## §11. MOTION ISLANDS & DOCUMENT SCROLL STABILITY — animation does not touch the page

**Rule:** any animation, autoplay, carousel, horizontal strip, or reveal does **not change** the document's `scrollY`, does not "pull" the viewport to a block, and does not change the height/width of flow neighbours. Animation lives in a **motion island** — an isolated zone with fixed geometry.

**Why it breaks without it:** global `scroll-behavior: smooth` + focus-on-click on carousel buttons → browser scrolls to hero; `scrollIntoView` on slide change; `translateY` in reveal shifts content below; `100vw` causes horizontal overflow; `scroll-snap` on desktop without overflow jitters the anchor; autoplay outside the viewport changes the DOM while the user reads the bottom of the page.

**Architecture (three layers):**

| Layer | Where | What is permitted |
|-------|-------|----------------|
| **Document** | page sections in the flow | **opacity only** for reveal (`prism-reveal--fade`). No `translateY`/`scale` in the flow. |
| **Motion Island** | carousel, tab strip, interactive hero | `transform`/`opacity` inside a container with `overflow: anchor none`, `contain: layout style`, fixed `height` |
| **Horizontal scroll** | card strip | `overscroll-behavior-x: contain`; `scroll-snap` **mobile only**; `scrollTo` **only if** `scrollWidth > clientWidth` |

**Reference code (marketing / Next.js — adapt paths to project):**
```css
/* motion-islands.css — canon classes */
.prism-motion-island {
  position: relative;
  overflow: anchor none;
  contain: layout style;
}
.prism-scroll-x {
  overflow-x: auto;
  overflow-y: hidden;
  overscroll-behavior-x: contain;
  overscroll-behavior-y: none;
}
html { scroll-behavior: auto; } /* smooth — only for explicit scrollToElement for anchor */
```

```tsx
// Autoplay only when in viewport
useInViewAutoplay(islandRef, { onTick: () => setIndex((i) => (i + 1) % n) });

// Carousel control click — without focus-scroll
onMouseDown={(e) => e.preventDefault()}

// Horizontal scroll — guarded
if (strip.scrollWidth > strip.clientWidth + 1) {
  strip.scrollTo({ left: nextLeft, top: 0, behavior: smooth ? 'smooth' : 'auto' });
}
```

**Forbidden in the document flow:**
- `scrollIntoView` on slide/tab change (except an explicit UX anchor on user click on `#section`)
- `html { scroll-behavior: smooth }` globally
- `role="tab"` on carousel dots without tablist (focus side-effects)
- `100vw` on full-bleed without `overflow-x: clip` on the shell
- reveal with `translateY` / `scale` on page sections
- carousel autoplay when the island is outside the viewport

**Mandatory for carousel / switchable content:**
- fixed height of the slides container (`height`, not `min-height` alone for the whole block)
- reserved heading height per slide (`min-height` for N lines + `line-clamp`)
- fixed card heights in the selection strip (all states at the same height)
- inactive slides: `aria-hidden` + `inert` (no hidden focus target)
- dots/indicators: `transform: scaleX` instead of changing `width`

**`@QA_ARCH` verification (code, Vector 6.1):** no `scrollIntoView` in carousel/autoplay; no global smooth scroll; island classes on interactive blocks; guarded horizontal scroll.

**`@QA_VISUAL` verification:** `V11` — on slide change/autoplay `ΔscrollY == 0` (mid-page fixture); `V2` — no horizontal overflow; `V7` — changing the active card does not change the strip height.

---

## §12. COLLISION & STACKING — interactives do not overlap, layers follow the scale [v6.20]

> Symptom this section closes: "buttons overlap each other", a badge covers an adjacent link, a sticky bar covers the CTA, a dropdown goes under the card. The causes are always the same: raw z-index, absolute outside its anchor, button groups on margin, fixed widths on flex children without wrap, negative margin on interactive elements.

### 12.1 Z-SCALE — raw z-index values are forbidden

The single project scale (`index.css`), aligned with Mantine layers:

```css
:root {
  --z-base: 0;          /* flow */
  --z-raised: 1;        /* raised card, active tile */
  --z-sticky: 20;       /* sticky table headers, local bars */
  --z-page-overlay: 50; /* in-page overlays (drag-ghost, spoiler) */
  /* System layers — Mantine variables ONLY, not numbers:
     --mantine-z-index-app: 100 · -modal/-overlay: 200 · -popover: 300 · -max: 9999 */
}
```

Rules: in components — only `var(--z-*)` or a Mantine layer variable; a literal `z-index: 999` in application code = 🔴; a new scale level — an @FRONTEND decision, not a local improvisation; `position: relative` without need must not be created (unnecessary stacking contexts break layer order).

### 12.2 ANCHOR POSITIONING — absolute/fixed only inside a declared anchor

- `position:absolute` is permitted only inside a parent anchor (`position:relative`) that **reserves space** for the element: a badge on a card lives in the anchor's padding zone (`top:8px; right:8px` with `padding-top ≥ 40px` or in a designated corner), not "hanging over" neighbours.
- Exceeding the anchor boundary (`top:-N`, "sticker half outside") — only if the anchor compensates with an external spacing ≥ the overhang: `margin-top: calc(N + 4px)` on the anchor or wrapper.
- `position:fixed` (headers, bottom bars, FAB): body/layout reserves space (`padding-block` = bar height + safe area `env(safe-area-inset-*)`); the fixed element must not cover flow interactive elements on any viewport.
- Tooltips/dropdowns — via layer components (Mantine Portal/Popover), not a hand-rolled absolute in the flow.

### 12.3 INTERACTIVE DISTANCE — action groups are not built on margin

- Any group of buttons/chips/icons: flex/grid container + `gap ≥ 8px` (touch targets 44px — §Ergonomics) + `flex-wrap: wrap` by default. Chains of `margin-left` between sibling buttons are forbidden.
- Negative margin on interactive elements is forbidden (avatar stack — exception: decorative, not individually clickable, or only the top one is clickable).
- Flex children with fixed width: the container must support wrap OR the child `min-width:0` + truncate (§8); "two 240px items in a 400px container without wrap" — source of collisions, caught by V12.
- Text next to an icon button: the interactive does not "cling" to adjacent text — 8px minimum.

### 12.4 CRITERION (deterministic, for @QA_VISUAL V12)

```
At 360 / 768 / 1280 / 1920, states default + hover + open menu/drawer:
  pairwiseIntersection(visible [button, a, input, select, [role=button], [tabindex]]):
    intersection area of any pair's bounding-box = 0 px² (parent-child pairs excluded).
  zIndexAudit: computed z-index ∉ {var(--z-*), Mantine layers} → violation.
  fixedCoverage: fixed/sticky do not intersect flow interactives after scrolling to anchors.
Any number > 0 = 🔴 with pair selectors and intersection area in px².
```

---

## @DEV CHECKLIST (before submitting a UI task)

```
□ §1  Siblings in grid/row — equal-height (grid align-items:stretch / flex stretch)
□ §2  Multi-line headings — line-clamp + min-height for N lines
□ §3  Variable-width elements — min-width for longest variant; numbers tabular-nums
□ §4  Lists/tags/notes inside blocks — ceiling (clamp/scroll), do not inflate
□ §5  Media — aspect-ratio/fixed size before load
□ §6  Operational screen — no global scroll; body scrollable; header sticky; min-height:0
□ §7  hover/focus/press — do not shift layout; focus-visible present
□ §8  Variable text — truncate/line-clamp + title; min-width:0 on flex descendant; break for long strings
□ §9  Spacing via gap/tokens, symmetric padding
□ §10 Animations only transform/opacity; prefers-reduced-motion respected
□ §11 Motion islands: fixed carousel geometry; reveal in flow — opacity-only; no scrollIntoView/autoplay outside viewport; focus-scroll blocked on controls
□ §12.1 No literal z-index in task — only --z-* / Mantine layers
□ §12.2 Every absolute — inside an anchor with reserve; fixed bars compensated with layout padding + safe-area
□ §12.3 Action groups = flex + gap + wrap; no margin collision, no negative margin on interactive elements
```

## @QA_VISUAL CHECKLIST (invariant → vector mapping)

```
§1,§2 → V1 (siblingHeightDelta == 0 @longtext)
§4,§8 → V2 (overflow.x ≤ 0; clippedText == 0)
§5    → V3 (CLS ≤ 0.1)
§6,§8 → V2/V4 (no global/horizontal scroll; targets ≥ 44 @mobile)
§3,§7 → V7 (geometryShiftOnState == 0)
§10   → V8 (0 layout animations; reduced-motion)
§11   → V11 (ΔscrollY==0 @carousel tick/click); V2 (overflow); V7 (strip height stable @active change)
§9    → V6 (padding symmetry; edge alignment)
§12   → V12 (pairwiseIntersection == 0 · zIndexAudit · fixedCoverage)
```

Reference: `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/ROLE_QA_VISUAL.md` · `roles/COMPONENT_REGISTRY.md` · `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` §9 · `roles/ROLE_FRONTEND.md` (Pillar 9) · `roles/MOTION_LIBRARY.md` §VII · `roles/ROLE_DEV.md` (Type D/E) · `.cursorrules` (Law 15 Zero Tolerance, Law 26 Layout Invariants)
Version: 1.2 | 2026-07-06
