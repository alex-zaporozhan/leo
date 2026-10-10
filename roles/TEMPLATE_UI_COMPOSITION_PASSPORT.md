# TEMPLATE_UI_COMPOSITION_PASSPORT

> How the product is **composed** and how it **behaves** — the layer between tokens (`TEMPLATE_DESIGN_PASSPORT`)
> and screens (`DESIGN_SPEC_*`). Fill once per project, update after each UI wave.

**Before filling:** read `roles/VISUAL_CRAFT_CANON.md` (how it looks expensive) and — for any operational zone —
`roles/INTERFACE_CRAFT_CANON.md` (how it **feels** expensive to work in). A product can pass every visual gate
and still be **stiff**: every action a modal, no bulk, no keyboard, filters lost on reload. §7 below is where
that failure is prevented, and it is the section most passports do not have at all.

---

# UI Composition Passport — [Project Name]

**Scope:** section rhythm, allowed compositions, component contracts, navigation, operational capabilities, anti-patterns.
**Goal:** the product feels **intentional** before a single business screen exists.

---

## 1. COMPOSITION THESIS AND THE PRODUCT JOURNEY

[One paragraph: how the workspace should feel and how a person moves through it.]

```
1. [what the user needs first]
2. [the core work / creation]
3. [execution / operation]
4. [review / analytics]
5. [settings / management]
```

**Menu order follows this journey** — not the database, not the sprint order.

---

## 2. SECTION RHYTHM — the composition grammar of a page

> Craft: `VISUAL_CRAFT_CANON` §7 (spatial rhythm) · `LAYOUT_COMPOSITION` (the 8 primitives, the proximity law).

```
[eyebrow] → [display H1/H2] → [lead] → [content]
```

| Element | Contract |
|---------|----------|
| Eyebrow | [pill / rule / caps — pick ONE and it is the same everywhere] |
| Display | [one accent word per heading, max — or none] |
| Lead | max width `[600–720px]` |
| Section gap | `[px]` desktop / `[px]` mobile — one value, on the scale |
| Band alternation | `default` / `soft` — [when each; ≤2–3 bands total] |
| Divider | [hairline / air / none] — **one** answer, not "sometimes both" |
| Container | max `[px]`; side padding `[px]` / `[px]` mobile |

---

## 3. ALLOWED PATTERNS — the named compositions of this product

> This is the product's vocabulary. A screen that needs a composition not on this list goes back to @DESIGN
> (a new pattern is a decision, not an improvisation by @DEV).

| Pattern | Where it lives | Composition |
|---------|----------------|-------------|
| [Hero stack] | [home] | [intro → X → Y → trust] |
| [Signature component] | [where] | [the one owned UI object — see DESIGN_PASSPORT §7] |
| [Card grid] | [catalog] | [thumb + tag + price + CTA; equal-height] |
| [Feature grid] | | [N-up, equal-height] |
| [Proof / trust strip] | | [tabular numbers, equal-height] |
| [Document / artifact showcase] | | |
| [Editorial split] | | |
| [Form + FAQ split] | | |
| [Offer band] | pre-footer | [ONE per page] |
| [Data table] | operational | §6 |
| [Tree / repository] | operational | §8 |

---

## 4. COMPONENT CONTRACTS

> Not "we have a Card". **What a Card IS in this product** — its material, its behaviour, its guarantees.

| Component | Contract |
|-----------|----------|
| `BaseCard` | material: [from DESIGN_PASSPORT §3] · separation: [tone / hairline / shadow — **one**] · equal-height in grids · reserved title rhythm ([N] lines) · hover: [shadow/transform only, zero layout shift] |
| `SectionHeader` | eyebrow + title + lead; one accent word max |
| `[SignatureComponent]` | [the owned object; its 3 carriers] |
| `[EntityCard]` | [cover ratio, price tabular-nums, equal-height, line-clamp title] |
| `Button` | see DESIGN_PASSPORT §8 (matrix by surface); loading keeps width |
| `EmptyState` | icon + title + hint + **CTA** (a list without a CTA is a dead end) |
| `StatusIndicator` | dot + label; tint bg + coloured border — never a filled block |
| `DataTable` | §6 |
| `Tree` | §8 |

---

## 5. NAVIGATION — one model per screen

> `INTERFACE_CRAFT_CANON` §4. A sidebar **plus** top tabs **plus** breadcrumbs **plus** a segmented control on the
> same screen is not "rich" — it is four maps of the same territory, and the user trusts none of them.

**Primary model:** [sidebar / rail / top tabs / breadcrumb / command-first] — **reason:** [why]

| Group | Purpose | Items |
|-------|---------|-------|
| | | |

Rules: the active route is unmistakable · disabled/future items are readable and explained (not broken-looking) ·
hidden groups never contain the current route · raw ids/schema names are never menu labels ·
hover/focus does not shift geometry.

---

## 6. DATA DENSITY — tables and lists

> `INTERFACE_CRAFT_CANON` §3.

| Parameter | Value |
|-----------|-------|
| Row height | compact `32` / default `[40–44]` / comfortable `48` — **and a toggle that persists** |
| Identifying column | FIRST, never truncated into uselessness |
| Numbers | right-aligned + `tabular-nums` — always |
| Dates | one format; relative for recent, absolute on hover |
| Actions column | LAST, sticky on horizontal scroll |
| Header | sticky, always |
| Row dividers | hairline `[6–8]%` — **zebra striping is 2008** |
| Empty cell | `—` at reduced contrast — never blank, never "null" |
| Pagination | [N] default; page size persists |

---

## 7. OPERATIONAL CAPABILITIES — the interaction inventory (the section most passports lack)

> `INTERFACE_CRAFT_CANON` §1. **Answer every line: applies / N/A + why. Silence is how the prim console gets built.**
> These are not features to add later — they are what makes a console an instrument instead of a CRUD form.

| # | Capability | This project |
|---|-----------|--------------|
| I1 | Command palette (`Cmd+K`) — every action reachable by typing | [applies / N/A + why] |
| I2 | Inline edit — a one-field change never opens a drawer | |
| I3 | Bulk select + bulk actions (Shift-range, action bar with count) | |
| I4 | **Undo instead of confirm** — destructive executes, toast offers Undo 5–10s | |
| I5 | Optimistic UI — the local decision shows instantly | |
| I6 | Saved views / persistent filters — the working set survives reload | |
| I7 | Contextual row actions — revealed on hover, not always visible | |
| I8 | Keyboard path — the primary flow is completable without a mouse | |
| I9 | Deep linking — every meaningful state is a URL | |
| I10 | No blocking loaders — skeletons in place, never a page spinner | |
| I11 | Fast search — `/` focuses, 300ms debounce, keyboard-navigable results | |
| I12 | Empty states that teach — name the next action, never "No data" | |

**Typed-confirm survives only for:** [name the irreversible, wide-blast operations — that is the whole list].
Everything else is Undo.

---

## 8. INFORMATION ORGANISATION

> `INTERFACE_CRAFT_CANON` §2. **Ownership is a tree (one parent). Cross-cutting is tags (many).
> Never make one do the other's job** — that is the root of every "why can't I find it".

**Structure per entity:**

| Entity | Structure | Why |
|--------|-----------|-----|
| [entity] | [table / tree / board / gallery / collections] | |

**If there is a tree** (pages, categories, folders, media):
```
□ Expansion state persists across reload (the most common reason a tree feels hostile)
□ "Where am I" always visible — breadcrumb mirrors the tree
□ A node shows its child count
□ "Move to…" is a SEARCHABLE picker with recent destinations — not a giant scrolling tree
□ Drag has an explicit drop target AND a keyboard/menu alternative
□ Multi-select moves together
□ Search crosses the tree and reveals the node IN PLACE (expanding ancestors)
□ Depth ≤ 3 — deeper means search becomes the primary navigation
```

**If there is a repository** (media, documents): current location always on screen · grid + list views, the choice
persists · drag-anywhere upload with per-file progress · referenced items cannot vanish silently (show where used) ·
recent items are first-class · bulk move/tag/delete with one undo.

---

## 9. COLOUR BALANCE

| Layer | Share | Note |
|-------|-------|------|
| Neutral ground | 65–75% | the calm |
| Surfaces | 20–30% | content |
| Accent / status | 3–7% | active states, CTA, status |
| Error / destructive | < 2% | only when real |

If the page feels pale — **fix the hierarchy, do not raise the saturation** (`VISUAL_CRAFT_CANON` §1, §4).

---

## 10. ANTI-PATTERNS (project-specific, 🔴 for @QA_VISUAL)

> Generic bans live in the canons (X1–X12 cheapness, ST1–ST12 stiffness, C1–C10 clichés).
> **Here: what is forbidden in THIS product**, because it would otherwise look reasonable in isolation.

- [ ] [e.g. gradient page backgrounds]
- [ ] [e.g. a filled status pill across a whole card]
- [ ] [e.g. the superseded rhythm/divider from v1]
- [ ] [...]

---

## 11. ACCEPTANCE

**@QA_ARCH (code level):**
```
□ Menu order follows the product journey; groups are named
□ Every card declares its surface role and separation method (ONE)
□ One primary CTA per screen region
□ 4 states on every data list; EmptyState has a CTA
□ The colour ratio (§9) holds
```

**@QA_VISUAL (render level):**
```
□ V13 cheapness detector (VISUAL_CRAFT_CANON §9, X1–X12): 0 hits
□ V14 stiffness detector (INTERFACE_CRAFT_CANON §7, ST1–ST12): 0 hits on operational screens
□ Equal-height siblings; no overflow at 360/768/1280/1920
□ Hover/focus/active: zero layout shift
□ Every pattern on a screen is named in §3 (nothing improvised)
```

---

Reference: `roles/VISUAL_CRAFT_CANON.md` (the physics of expensive) · `roles/INTERFACE_CRAFT_CANON.md` (the craft of the instrument: I1–I12, trees, density, ST1–ST12 stiffness) · `roles/LAYOUT_COMPOSITION.md` (the grammar of space) · `roles/LAYOUT_INVARIANTS.md` (geometry under content) · `roles/COMPONENT_REGISTRY.md` (the living registry) · `roles/TEMPLATE_DESIGN_PASSPORT.md` (tokens, material, atmosphere) · `roles/DOMAIN_STANDARDS.md` · `roles/ROLE_DESIGN.md`
Version: 2.0 | 2026-07-12
