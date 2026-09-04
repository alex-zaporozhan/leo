# CRAFT_LINT_SPEC — the machine floor of taste and layout

> **What this file is, and is not.** It is the **specification** of the machine floor — what is measured, by what rule, at what threshold. It is **not** an implementation, and this system ships no runnable code by design (no dependency, no attack surface, nothing that ages out of step with the project). The paths named below (`.ci/craft.yml`, `scripts/craft-report.js`, `tests/visual/craft.spec.ts`) are **the shape the project generates in its first frontend wave**, not files that exist here. Owner in the project: @FRONTEND for the greps and lint rules, @ARCH for wiring them into the declared release contour. **Until that executor exists in a given project, every check below is self-signed by the agent — and a self-signed floor is reported as such, never presented as a passed gate.**

**Status:** 🟢 canon · the layer between `LAYOUT_INVARIANTS` (geometry) and `VISUAL_CRAFT`/`INTERFACE_CRAFT` (taste)
**Date:** 2026-07-20 · **Roles:** @QA_VISUAL (owner) · @DEV (applies fixes) · @FRONTEND (lint rules)
**Thesis:** every rule of taste that reduces to a count, a grep or a meter becomes a **blocking check**. Whatever does not reduce goes to `QA_VISUAL_AESTHETE_SENSOR.md` as a catalogue of crimes with a mandatory verdict.

> Principle: **the machine holds the floor, the eye holds the ceiling.** Geometry (`V1–V12`) is already machine-checked. This file adds `V15–V18` — new *measurable* vectors that close frequent "eye" bugs — and reduces X1–X12 / I1–I12 to checks wherever that is possible.

---

## 0. How to read the tables

| Column | Meaning |
|---|---|
| **Check** | a concrete grep / eslint rule / Playwright meter |
| **Type** | 🟩 machine (deterministic) · 🟨 hybrid (meter + threshold, but zone context required) · 🟥 eye (only `AESTHETE_SENSOR`) |
| **Gate** | 🔴 blocking (fails CI) · 🟡 advisory (reported, not blocking) |

---

## 1. New measurable vectors V15–V18 (they close frequent bugs)

> These four are a direct answer to "a two-line button at ×2 height", "a row of buttons of unequal height", "text becomes unreadable on hover", "huge fonts in the menu". Previously this was caught by eye (and missed). Now — by number.

### V15 — State Contrast (text readable in ALL states)

**The bug that kills:** a blue button → on hover the text drifts into dark blue/violet → unreadable.

```ts
// measures.ts
/** Minimum text↔background contrast across all states. WCAG: ≥4.5 normal, ≥3 large. */
export async function stateContrast(page, selector,
  states = ['rest','hover','active','focus-visible','disabled']): Promise<{state:string,ratio:number}> {
  let worst = {state:'rest', ratio:99};
  for (const s of states) {
    await applyState(page, selector, s);           // hover/focus/[data-state]/:active emulation
    const { color, bg } = await page.$eval(selector, el => {
      const cs = getComputedStyle(el);
      return { color: cs.color, bg: effectiveBg(el) }; // effectiveBg walks up the ancestors to an opaque one
    });
    const ratio = wcagContrast(color, bg);
    if (ratio < worst.ratio) worst = { state:s, ratio };
  }
  return worst;
}
```

| Check | Type | Gate | Threshold |
|---|---|---|---|
| `stateContrast(button).ratio ≥ 4.5` (normal text) / `≥ 3` (large ≥18.66px bold) in every state | 🟩 | 🔴 | disabled may be 🟡 (but still ≥3) |

**Rule for @DEV:** hover changes the **background/shadow**, but does NOT push the text toward a colour close to the background. `color` on hover either stays the same or moves to an equally contrasting value. Blue button → the text stays white.

---

### V16 — Control-Row Equal-Height (a row of buttons/chips stays even)

**The bug that kills:** in one row one button is single-line, another is two-line → height ×2 → a "staircase".

```ts
/** Height spread within a control group + line-wrap detection. */
export async function controlRowDelta(page, groupSelector): Promise<{delta:number, wrapped:number}> {
  const items = await page.$$eval(`${groupSelector} > *`, els => els.map(el => {
    const r = el.getBoundingClientRect();
    const lh = parseFloat(getComputedStyle(el).lineHeight) || r.height;
    return { h: Math.round(r.height), lines: Math.round(r.height / lh) };
  }));
  const heights = items.map(i => i.h);
  return { delta: heights.length<2 ? 0 : Math.max(...heights)-Math.min(...heights),
           wrapped: items.filter(i => i.lines > 1).length };
}
```

| Check | Type | Gate | Threshold |
|---|---|---|---|
| `controlRowDelta('[data-btn-group]').delta == 0` | 🟩 | 🔴 | strict |
| `.wrapped == 0` (no wraps inside a button row) OR the group is `align-items:stretch` and all are equal | 🟩 | 🔴 | — |

**Rule for @DEV (as an addendum in `LAYOUT_INVARIANTS §12.3`):** buttons inside a CLUSTER carry a **fixed `min-height`** and `white-space:nowrap` (or `line-clamp:1`). If the label is long — it is truncated, or the button gets wider, but **never taller**. Two-line buttons in the same row as single-line ones are forbidden. If a wrap is unavoidable by design — the whole row goes `align-items:stretch`, and then everything stretches to the tallest.

---

### V17 — Chrome Type-Scale Ceiling (font in the "furniture" stays under the ceiling)

**The bug that kills:** menus/nav/tabs in huge fonts "at the expense of the interface".

```ts
/** Actual font-size of elements per zone against that zone's ceiling. */
export async function typeScaleCeiling(page, zoneCaps): Promise<Array<{zone,el,size,cap}>> {
  const viol = [];
  for (const [zone, cap] of Object.entries(zoneCaps)) {
    const sizes = await page.$$eval(`[data-zone="${zone}"] :is(a,button,span,li,td)`,
      els => els.map(el => Math.round(parseFloat(getComputedStyle(el).fontSize))));
    sizes.forEach(s => { if (s > cap) viol.push({zone, size:s, cap}); });
  }
  return viol;
}
```

| Zone | font-size ceiling | Type | Gate |
|---|---|---|---|
| `nav` / header menu | **16px** | 🟩 | 🔴 |
| mega-menu / dropdown item | **15px** | 🟩 | 🔴 |
| segmented / tab | **15px** | 🟩 | 🔴 |
| table cell | **15px** (header 13) | 🟩 | 🔴 |
| chip / badge | **14px** | 🟩 | 🔴 |
| display / hero / section h1–h2 | **no ceiling** (this is content, not chrome) | 🟥 | — |

**Resolving the "44px tap target vs huge font" conflict:** a 44×44 touch target is reached through **padding**, not by inflating the font. A large tap target ≠ large text. This removes your objection that "geometry dictates menus with huge fonts".

---

### V18 — Primitive Source (the button has a single source)

**The bug that kills:** buttons in differing styles, because someone hand-drew a `<button className="...">`.

| Check | Type | Gate |
|---|---|---|
| eslint: `<button>` in JSX is forbidden outside `ui/Button.tsx` (allowlist) → use `<Button>` | 🟩 | 🔴 |
| grep: no inline `style=` with `background`/`padding`/`border-radius` on interactive elements | 🟩 | 🔴 |
| grep: no `class*="btn"` definitions outside `theme/*.css` (every variant comes from tokens) | 🟩 | 🔴 |
| primitive-conformance (QA_VISUAL): the primitive's class on render = the class from the passport | 🟨 | 🔴 |

The rule inserts into the roles — see §4.

---

## 1b. Canvas legibility — vector V19 (Toy-Graph Detector)

> For node-graph products (pipeline builders, agent canvas). The canon is `CANVAS_CRAFT_CANON.md`; its §9 detector G1–G10 becomes **vector V19**. Part of it is machine-checked, part is eye. The reference for a readable node: `pipeline_constructor_readable.html`.

| # | Check | Type | Gate |
|---|---|---|---|
| V19.1 | every node carries a **glyph + category tint** (not text alone) — `[data-node] .glyph` present, 5–7 categories | 🟩 | 🔴 (G1) |
| V19.2 | every node surfaces **≥1 meaningful parameter in its header** (`.node-sub` non-empty) — a node with no subtitle = 🔴 | 🟩 | 🔴 (G1) |
| V19.3 | ports are **typed and labelled** (`data-port-type` + a visible name); an illegal target dims during the drag, not a toast afterwards | 🟨 | 🔴 (G2/G3) |
| V19.4 | there is **auto-layout / "Align"** in a single keypress; flow direction is uniform (L→R) | 🟩 | 🔴 (G4) |
| V19.5 | **minimap + canvas search** above 20 nodes | 🟨 | 🔴 (G5) |
| V19.6 | node hover → the subgraph lights up (upstream+downstream), the rest dims to ~30% | 🟨 | 🟡 (G6) |
| V19.7 | a node is **not a form** (no `input`/`select` inside the node body; config lives only in the inspector) | 🟩 | 🔴 (G7) |
| V19.8 | **run overlay** on the same graph, not a separate log screen | 🟥 | 🔴 (G8) |
| V19.9 | clicking a node after a run → its actual **input/output** | 🟥 | 🔴 (G9) |
| V19.10 | a loop is visually distinct + a visible exit condition + a visible iteration cap | 🟨 | 🔴 (G10) |

**V19 verdict:** any one of G7/G8/G9/G10 → 🔴; 3+ hits in total → 🔴 (the product is a diagram editor pretending to be a control panel).

---

## 1c. Page pacing — vector V20 (page rhythm / "screens")

> A page-level axis, separate from A/V1 (rhythm inside a section). It cures the feeling of "the site as an attic: blocks piled up unevenly, everything in a heap".

| # | Check | Type | Gate |
|---|---|---|---|
| V20.1 | one section-spacing token — distinct `padding-block` on top-level `<section>` ≤ 2 | 🟩 | 🔴 (P4) |
| V20.2 | sections do not collide — `pairwiseIntersection` of top-level sections == 0px² (catches an overlapping CTA/panel) | 🟩 | 🔴 (P5) |
| V20.3 | no stuck "Loading…"/skeleton on a settled page (settle 3s) | 🟨 | 🔴 (P2) |
| V20.4 | a placeholder section ("coming soon") is **hidden on prod**, not rendered at full size | 🟨 | 🔴 (P2) |
| V20.5 | >10 top-level sections on a marketing page → raise a "pacing review" flag | 🟩 | 🟡 (P1) |
| V20.6 | the hero holds the first viewport; the peek of the next section is ≤ ~15vh as a deliberate scroll hint | 🟨 | 🟡 (P3) |

---

## 2. X1–X12 (cheapness, `VISUAL_CRAFT §9`) → checks

> **The numbering is owned by the canon.** The meaning of each X is set by `VISUAL_CRAFT_CANON.md` §9; this table is only the machine projection of it and introduces no numbers of its own. Previously X6/X7/X8/X10/X11 meant something here other than in the canon — fixed.

| # | Rule | Check | Type | Gate |
|---|---|---|---|---|
| X1 | ≤1 separation method per surface | AST: an element carries no more than one of {border, boxShadow, background-tint} as a separator | 🟨 | 🟡 |
| X2 | Warm/tinted shadows, not `rgba(0,0,0)` | grep: `box-shadow` contains neither `rgba(0,0,0` nor `#000` | 🟩 | 🔴 |
| X3 | Saturated colour over a large area is forbidden | meter: area of elements with chroma>C and area>40vw → 0 | 🟨 | 🔴 |
| X4 | One accent hue family | token check: `--accent-*` from a single hue family; active hue counter ≤ 2 | 🟩 | 🔴 |
| X5 | A decorative gradient without a reason | grep counter of `linear-gradient` per section ≤ budget (hero ≤3, section ≤2); `.btn--primary` carries no gradient | 🟩 | 🟡 |
| X6 | Radii are consistent and concentric (nested = outer − padding) | meter: nested radius ≈ outer − pad (±2px); distinct(border-radius) counter per route ≤ 3 | 🟨 | 🟡 |
| X7 | Modular type scale, ≤6 sizes per screen | meter: `distinct(font-size)` per route ≤ 6 | 🟩 | 🟡 |
| X8 | Bold ≤ ~30% of text | meter: share of weight≥700 by text area ≤ 0.3 | 🟩 | 🟡 |
| X9 | No pure greys (`#888` and the like) | grep: greys must carry a trace of the brand; "dead" hex values from the blacklist are banned | 🟩 | 🔴 |
| X10 | One icon set, one stroke weight, no emoji in the UI | grep: a single icon package across imports; `stroke`/`strokeWidth` at one value; emoji in JSX/UI strings → 0 | 🟩 | 🟡 |
| X11 | `tabular-nums` in numeric columns and metrics | grep: a column of numbers without `font-variant-numeric: tabular-nums` / an equivalent class → finding | 🟩 | 🟡 |
| X12 | Not a row of 3–4 identical cards as the main argument | 🟥 (composition) | 🟥 | — |

**Checks outside the X row** (in the canon these are separate sections, not the cheapness detector — they used to occupy the numbers X7/X8 and crowd out the real ones):

| Code | Rule | Check | Type | Gate |
|---|---|---|---|---|
| OPT | Optical alignment (`VISUAL_CRAFT §6`) | — | 🟥 | — |
| LIGHT | A single light source across shadows (`VISUAL_CRAFT §3`) | AST: the direction of every `box-shadow` is consistent (Δangle ≤ 15°) | 🟨 | 🟡 |

---

## 3. ST1–ST12 (stiffness, `INTERFACE_CRAFT §7`) → checks

> **Mind the numbering.** The stiffness detector is called **ST**, not I: `CONFLICT_REGISTRY` C5 freed the I1–I12 row for the **interaction inventory** (`INTERFACE_CRAFT §1`). The rows below are renumbered per the canon; in particular `I4` in the old edition meant "filters survive a reload", while the live `I4` of the canon is "undo instead of confirm". Referring to a bare `I4` is not allowed.

| # | Rule | Check | Type | Gate |
|---|---|---|---|---|
| ST1 | Not everything through a modal | meter: share of actions that open a modal ≤ threshold; create/edit → Drawer | 🟨 | 🟡 |
| ST2 | No "are you sure?" on a non-destructive action | grep: confirm only on destructive actions (the action class is `INTERFACE_CRAFT` I4) | 🟨 | 🟡 |
| ST3 | A keyboard path exists | a11y: every action is reachable from the keyboard; `tabindex` is correct | 🟨 | 🔴 |
| ST6 | Filters and view survive a reload | test: apply a filter → reload → the filter is alive (URL/storage) | 🟩 | 🔴 |
| ST7 | Bulk-select wherever there are lists | presence of select-all + a bulk bar on list screens | 🟨 | 🟡 |
| remaining ST | inline edit, optimistic updates, undo, empty states, speed of thought | 🟥 (mostly behaviour) — the verdict is issued by @QA_VISUAL from the catalogue | 🟥 | — |

---

## 4. Role inserts (ready to copy)

### 4.1 Into `ROLE_DEV.md` → the "Frontend geometry" section (after §12.3)

```
□ §12.4 A BUTTON IS THE PRIMITIVE, NOTHING ELSE. A button renders through <Button> (ui/Button.tsx).
  A raw <button className>, inline background/padding/radius styles on interactive elements,
  local .btn-* classes — FORBIDDEN (CRAFT_LINT V18). No suitable variant →
  escalate to @FRONTEND, do not hand-draw one.
□ §12.5 THE CONTROL ROW STAYS EVEN. Buttons/chips in a group: fixed min-height +
  white-space:nowrap (or line-clamp:1). A two-line button in a row with single-line ones is
  forbidden. Wrapping unavoidable → the whole group goes align-items:stretch (V16).
□ §12.6 TEXT IS READABLE IN EVERY STATE. hover/active/focus/disabled change background/shadow,
  but never pull color toward the background colour. Contrast ≥4.5 in every state (V15).
□ §12.7 CHROME STAYS UNDER THE CEILING. Font in nav/menu/tab/table/chip ≤ the zone ceiling (V17).
  A 44px tap target comes from padding, NOT from font size.
```

### 4.2 Into `ROLE_QA_VISUAL.md` → the "base meters" section (add the vectors)

```
V15 State Contrast   — stateContrast(button).ratio ≥ 4.5 in every state
V16 Control-Row      — controlRowDelta('[data-btn-group]').delta == 0 && wrapped == 0
V17 Type Ceiling     — typeScaleCeiling(zoneCaps) == []  (chrome does not exceed the zone ceiling)
V18 Primitive Source — eslint + primitive-conformance: buttons come from <Button>, class = passport
```
And add to PILLARS: **"12. The aesthete's catalogue is mandatory — a report without a filled-in crime verdict from `QA_VISUAL_AESTHETE_SENSOR.md` is incomplete."**

---

## 5. CI job `craft` (skeleton)

```yaml
# .ci/craft.yml  — sits next to job:visual, blocks the merge
craft:
  needs: [build]
  steps:
    - run: npm run lint:craft         # eslint: V18 (button primitive), no-inline-style, no magic hex
    - run: npm run grep:craft         # X2/X4/X5/X9: shadows, hue-count, gradient-budget, dead-greys
    - run: npx playwright test tests/visual/craft.spec.ts   # V15/V16/V17 measurably
    - run: node scripts/craft-report.js --fail-on=blocking  # 🔴 → exit 1
```

```ts
// tests/visual/craft.spec.ts (core)
for (const route of ROUTES) {
  test(`craft ${route}`, async ({ page }) => {
    await render(page, route, { viewport:[360,768,1280,1920], fixture:'longtext' });
    // V15
    for (const b of await page.$$('[data-btn-group] button, .btn'))
      expect((await stateContrast(page, b)).ratio).toBeGreaterThanOrEqual(4.5);
    // V16
    for (const g of await page.$$('[data-btn-group]')) {
      const r = await controlRowDelta(page, g);
      expect(r.delta).toBe(0); expect(r.wrapped).toBe(0);
    }
    // V17
    expect(await typeScaleCeiling(page, ZONE_CAPS)).toEqual([]);
  });
}
```

**Rule:** blocking findings (🔴) fail the build — a weak model cannot "sign them off for itself". Advisory ones (🟡) go into the report and on to the aesthete's eye review.

---

## 6. What did NOT fit here (it goes to the eye)

X7 (optics), X12 (row-as-argument), I6–I12 (speed of thought), composition, the balance of asymmetry, colour combination, "bad taste" — that is `QA_VISUAL_AESTHETE_SENSOR.md`. There is no meter there, but there is **a catalogue with a mandatory verdict**: silence on an item = a miss = the report is incomplete.

---

*Version 1.0 — 2026-07-20 — the machine floor; V15–V18 close frequent bugs of rows/states/fonts.*
