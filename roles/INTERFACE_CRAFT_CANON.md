# INTERFACE_CRAFT_CANON.md
# The craft of operational interfaces. The sister of VISUAL_CRAFT_CANON: that one is how a screen LOOKS expensive,
# this one is how it FEELS expensive to work in. Scope: /admin, /app, consoles, internal tools, dashboards, CMS.
# Owners: @DESIGN (the SPEC of an operational screen must name the inventory), @FRONTEND (owns the primitives),
# @QA_VISUAL (the stiffness detector ST1–ST12 as a vector), @DEV (executes), @QA_ARCH (states and mutations — unchanged).

> **The canon's dogma:** an admin panel is not a form on a page. It is an **instrument**.
> An instrument is judged by **speed of thought** — how fast an experienced user gets from intent to result —
> not by how pretty it is. The expensive console is the one where the expert never touches the mouse.
>
> **The failure this file prevents:** the "prim" console. Every screen correct, all four states present, tokens
> clean, TASTE GATE passed — and using it is misery. Every action opens a modal. Every deletion asks
> "are you sure?". Nothing is selectable in bulk. Filters reset on reload. There is no keyboard path.
> The screen is not ugly; it is **stiff**. Until now the system had no name for that, so nobody fixed it.

---

<!-- MIRROR SOURCE (SoT): confirm-vs-undo BY ACTION CLASS (C3) lives in row I4 / §7 ST2 below | cited in .cursorrules Law 15 · ROLE_QA_ARCH · TEMPLATE_ADMIN_UI_UX · LEAD_PRODUCT_GATE_PROTOCOL GATE-3 · ROLE_DESIGN Ergonomics | index: CONFLICT_REGISTRY.md -->
## §1. THE INTERACTION INVENTORY — what separates an instrument from a CRUD form

These are not "nice to have". An operational screen that has none of them is a **draft**, not a product.
Every item has an **acceptance criterion** — @DESIGN names which apply in the SPEC; @QA_VISUAL checks them.

| # | Capability | What it actually means | Acceptance criterion |
|---|-----------|------------------------|----------------------|
| **I1** | **Command palette** (`Cmd/Ctrl+K`) | Every action and every destination in the app is reachable by typing its name. Fuzzy match. It is the map of the product. | From any screen: `Cmd+K` → type 3 letters → the action runs. Zero mouse. |
| **I2** | **Inline edit** | Renaming, status, a number — changed **in place**. A drawer is for real work, not for a title. | A single-field change never opens a drawer or a modal. |
| **I3** | **Bulk select + bulk actions** | Checkbox column, `Shift`-click for a range, `Cmd`-click for individual, a select-all-that-matches-the-filter escape hatch. An action bar appears with the count. | 200 rows → select 50 → one action → one request, one undo. |
| **I4** | **Undo instead of confirm** | A destructive action executes **immediately** and offers `Undo` in a toast for 5–10s. A confirm dialog is a tax on every user to protect against a rare mistake. | Deleting one item = 1 click + a toast with Undo. A typed-confirm ("type DELETE") survives **only** for irreversible, blast-radius-wide operations. |
| **I5** | **Optimistic UI** | The change appears instantly; it rolls back with an explicit error if the server disagrees. | The UI never waits on the network to show the result of a local decision. |
| **I6** | **Saved views / persistent filters** | A user's working set (filters, sort, columns, density) survives reload, and can be **named and saved**. | Reload the page → the filter is still applied. "My open leads" is one click. |
| **I7** | **Contextual row actions** | A row reveals its actions on hover/focus. Actions shown on every row, always, are visual noise. | At rest, the table shows **data**. On hover, the row shows what it can do. |
| **I8** | **Keyboard navigation** | `j/k` or `↑/↓` to move, `Enter` to open, `Esc` to close, `/` to focus search, `x` to select. Focus is always visible. | The whole primary flow is executable without a mouse, and `Tab` order is sane. |
| **I9** | **Deep linking** | Every meaningful state is a URL: the filter, the open record, the tab, the page. | A colleague can be sent a link that opens exactly what you see. |
| **I10** | **Non-blocking loading** | Skeletons occupy the shape of the content in place. A spinner over the whole screen is forbidden. | No state exists in which the whole UI is unusable while something loads. |
| **I11** | **Search that is actually fast** | Fuzzy, forgiving, debounced (300ms), scoped, with keyboard result navigation. `/` focuses it. | Typing 3 characters yields useful results in < 300ms perceived. |
| **I12** | **Empty states that teach** | Not "No data". An empty state says what this is, why it is empty, and gives the one action that fills it. | Every list's zero-state names the next action. |

**The SPEC rule:** an operational-screen `DESIGN_SPEC` **must** answer the inventory explicitly — for each of I1–I12:
*applies / not applicable + why*. Silence is not an answer; silence is how the stiff console gets built.

---

## §2. INFORMATION ORGANISATION — trees, folders, repositories, collections

The weakest part of most admin panels, and the one that decides whether a product scales past 50 objects.

### §2.1 Choosing the structure (do not default to a table)

| Structure | Use when | Do not use when |
|-----------|----------|-----------------|
| **Flat list / table** | < ~200 peers, compared by the same fields, sorted and filtered | items belong to each other; the user thinks "inside" |
| **Tree** | true ownership hierarchy (pages, categories, org units, files); the user needs to see **where** a thing lives | the hierarchy is fake, invented to look organised; depth > 3 |
| **Board (kanban)** | status progression is the main thing; drag is the main verb | status is a field nobody changes by hand |
| **Gallery / grid** | the visual **is** the identifier (media, documents, templates) | items are distinguished by text fields |
| **Collections / tags** | cross-cutting membership (a course is "popular" AND "B2B") | ownership — a thing lives in exactly one place |

**The two-axis law:** ownership is a **tree** (one parent). Cross-cutting is **tags** (many). Do not force one to do the other's job —
that is the root of every "why can't I find it" complaint. A mature product has **both**, and never confuses them.

### §2.2 The craft of a tree (where implementations fail)

```
□ EXPANSION STATE PERSISTS. Reloading collapses everything = the user re-navigates from scratch every time.
  Persist per-user, per-tree. This single omission is the most common reason a tree feels hostile.
□ "WHERE AM I" IS ALWAYS VISIBLE. Breadcrumbs mirror the tree; the active node is highlighted in it.
□ A NODE SHOWS ITS WEIGHT. A count of children ("Courses · 24"). A node whose contents are invisible until
  clicked is a coin flip.
□ LAZY LOAD DEEP BRANCHES, but show the expected count before loading (no jumping).
□ DRAG-DROP WITH AN EXPLICIT DROP TARGET. Show the insertion line and the target node. An ambiguous drop is
  worse than no drag at all. Always provide a keyboard/menu alternative ("Move to…") — drag is never the only path.
□ MOVE VIA COMMAND. "Move to…" opens a searchable picker of destinations, with recent destinations first.
  Dragging a node 300px down a scrolling tree is a punishment, not a feature.
□ MULTI-SELECT MOVES TOGETHER. Selecting 5 nodes and moving them is one operation, not five.
□ DEPTH LAW: more than 3 levels and the user is lost. Flatten, or make search the primary navigation and the
  tree a secondary orientation device.
□ SEARCH CROSSES THE TREE. Searching must find a node anywhere and reveal it **in place** (expanding its
  ancestors), not just list it out of context.
```

### §2.3 The craft of a repository / library (media, documents, files)

```
□ CURRENT LOCATION IS ALWAYS ON SCREEN (breadcrumb + the folder's name as the page title).
□ TWO VIEWS: grid (visual identification) and list (metadata comparison). The choice persists.
□ UPLOAD IS DRAG-ANYWHERE, not a button in one corner. Show progress per file; do not block the UI.
□ REFERENCED ITEMS CANNOT VANISH SILENTLY. Deleting an item used elsewhere shows WHERE it is used and
  requires an explicit decision. (Your ADMIN_01 gets this right — keep it.)
□ RECENT and RECENTLY USED are first-class: most work touches the last 10 things.
□ BULK: select many → move / tag / delete → one undo.
□ METADATA THAT MATTERS IS ON THE TILE (dimensions, size, type, "used in 3 places") — not hidden behind a click.
```

### §2.4 Anti-patterns of organisation (🔴)

```
✗ A tree used for a flat set (fake hierarchy invented to look tidy)
✗ A table used for a genuine hierarchy (the user cannot see what contains what)
✗ Folders AND tags where one of them is fake (a tag that only ever has one value; a folder nobody moves things between)
✗ Depth > 3 with no search
✗ Drag as the ONLY way to move (unusable with a keyboard, punishing at scale)
✗ Expansion state lost on reload
✗ A "Move" dialog that shows the whole tree as a giant scrolling list instead of a searchable picker
```

---

## §3. DATA DENSITY AND RHYTHM

An operational table is where the product either respects the user's time or wastes it.

```
ROW HEIGHT LADDER:  compact 32px · default 40px · comfortable 48px
  A density toggle is a power-user feature, and it persists (I6). Default 40px; your ADMIN_01's 44px is fine
  as a default but should not be the only option.

COLUMN DISCIPLINE:
  · the IDENTIFYING column is FIRST and never truncated into uselessness
  · numbers right-aligned + `tabular-nums` (VISUAL_CRAFT §5.4) — always
  · dates in one format, relative for recent ("2h ago"), absolute on hover
  · actions column is LAST and STICKY on horizontal scroll
  · a column set the user can choose — and it persists

THE SCAN PATH: the eye reads DOWN one column, not across a row. Put what identifies the row first,
  what discriminates between rows second. Everything else is detail — consider hiding it (§5).

ZEBRA STRIPING IS 2008. Use hairline row dividers at 6–8% ink. If rows are hard to track, the row height
  or the column count is wrong — striping only masks the real problem.

STICKY HEADER, always, on any table that can scroll. A header that scrolls away is a bug.

EMPTY CELLS: an em-dash "—" at reduced contrast, never blank, never "N/A", never "null".
```

---

## §3.5 THE COMPOSITION FLOOR — what to BUILD on the recurring surfaces

> **Why this section exists, measured rather than felt.** The design canons of this system carry roughly
> **twice the density of prohibitions** of the backend canons at the same density of thresholds. The cause is
> structural: a backend ban states the construction in the same sentence — "every outgoing call has a timeout"
> also tells you what to type — and a design ban does not. *"Never two separation methods on one surface"*
> never says which one to use. So the construction has to be stated separately, and mostly it was not.
> Law 5 names what is left when it is not: **a warning with no call to action is noise.**
>
> **This section is the affirmative half of the bans in this file and adds no rules of its own.** It repeats no
> value that §2, §3, §5 or another canon already sets — where one does, it points. Take it verbatim when the
> project has decided nothing; it is the third floor, with the tokens (`VISUAL_CRAFT_CANON` §11) and the
> movement (`MOTION_CRAFT_CANON` §1), and **a screen that took only the token floor took a third of it**.
>
> **A floor, not a ceiling.** `PRODUCT_MATURITY_CANON` puts these constructions at roughly L2–L3, and a client
> product does not ship below L3 — so this is where a surface *starts*, not where it is finished.

**States** — owned by `roles/ROLE_DESIGN.md` State Spec (four base + the intermediate list). Not restated here.

**THE LIST / TABLE**
```
Row height, columns, alignment, dates, zebra, sticky header, empty cells → §3 above. Not repeated.
IDENTIFIER      column 1, clickable through to the record, never truncated to uselessness (§3)
STATUS          not a column of its own — a tinted background + a LEFT border on the row
                (VISUAL_CRAFT §4.4), so one glance down the leading edge answers "what needs me"
ACTIONS         last column, sticky on horizontal scroll. Revealed on hover/focus, not always visible (I7/ST4);
                one primary verb + a row menu for the rest
SORT            one default that answers "what did I come here for" — usually most-recent-first, and it is
                STATED in the spec, not left to the ORM
SELECTION       a checkbox column only where a bulk action exists (I3). Selecting shows a bar with the count
                and the actions, and shifts the table by zero pixels
ROW CLICK       the whole row opens the record; an inline-editable cell edits on click (I2)
PAGINATION      server-enforced page size; the control reads "1–50 of 4,102". Infinite scroll only for a feed
STRUCTURE       under ~200 peers compared by the same fields — otherwise §2.1 picks a different structure
```

**THE FORM**
```
PLACE           Drawer over the record for a short form, a page for a long one; a Modal is for a DECISION,
                never for data entry (§5, TEMPLATE_ADMIN_UI_UX §4.1 own the threshold — not restated here)
LABELS          above the field. A placeholder is an example, never a label — it disappears exactly when needed
REQUIRED        mark the OPTIONAL ones. Asterisks on every field are noise; "(optional)" on three is information
GROUPING        3–6 fields per group, one heading, one column. Two columns only for paired values (from/to)
VALIDATION      on blur for a field the user has left; never per keystroke; again on submit. The message sits
                under its own field and says what to do: "Use a work email", not "Invalid format"
SUBMIT          one primary, bottom right. Disabled ONLY while in flight, and showing it.
                **Never disabled because the form is invalid** — say what is wrong instead of going dead
API ERRORS      field errors map back to their fields; a general error sits above the actions;
                a 409 says what changed and offers to reload (FRONTEND_CAPABILITY C5)
UNSAVED         a dirty form asks once — Save · Discard · Cancel. An autosaved form says when, and never asks
```

**THE RECORD / DETAIL**
```
HEADER          the name, the status, and the ONE number that matters for this entity. Nothing competes with it
ACTIONS         one primary; destructive never adjacent to it
BODY            tabs only where sections are genuinely independent and the user works in one at a time —
                a tab that hides a field the user is comparing is worse than a long page
RELATED         related collections render as their own lists above, paginated, with their own states
HISTORY         a surface, not a modal (FRONTEND_CAPABILITY C6)
NAMES           a human name always. Law 8 is unconditional: a technical id does not appear in the UI
```

**THE FILTER / SEARCH BAR**
```
PLACE           above the table, aligned with its first column
ACTIVE          every applied filter is a removable chip carrying its value — a filter the user cannot see is
                why they think the data is missing
COUNT           "N of M" whenever any filter is active. This one line prevents the most common support ticket
                in any admin product
SEARCH          debounce 300ms; search the fields a human would name, and say which ones
PERSISTENCE     filters survive navigation and live in the URL (I9/ST6) — a filtered view is a thing people send
RESET           one [Clear all] whenever more than one filter is active
INDEXES         a column offered as a filter or sort is indexed — cannot be indexed, it is not offered.
                The decision is owned by SYSTEM_DESIGN_PROTOCOL Step 1 (THE LOAD FLOOR) and checked by
                LOAD_REFLEX LD3; it appears here because this is the surface where it gets decided
```

**THE DESTRUCTIVE ACTION** — the confirm/undo winner is owned by I4 and registered in `CONFLICT_REGISTRY`.
```
DEFAULT         undo (I4). Do it, say it, offer 5–10 seconds to reverse
CONFIRM         only for the genuinely irreversible — money moved, data destroyed, a message sent, an external
                system called. Then the dialog NAMES the object ("Delete invoice #2841 for Acme Ltd?") and the
                primary button is the verb, not "OK"
TYPE-TO-CONFIRM where the blast radius is a whole tenant or an account
AFTER           the row collapses into the gap it leaves; neighbours close after it (MOTION_CRAFT §2 G4)
NEVER           destructive adjacent to the primary · destructive as a row's only visible action ·
                destructive with no way to know what it will destroy
```

**What this floor does not decide:** the world · the register (Law 33) · the tokens (`VISUAL_CRAFT_CANON` §11) ·
the motion values (`MOTION_CRAFT_CANON` §1) · the states (`ROLE_DESIGN` State Spec) · the business minimum per
page type (`DOMAIN_STANDARDS`) · the load ceilings (`SYSTEM_DESIGN_PROTOCOL`). Composition only, and overridden
by any of those where they speak.

---

## §4. NAVIGATION MODELS — one per screen, not three

| Model | Use when | Cost |
|-------|----------|------|
| **Sidebar** | many sections (> 5), persistent context, groups | takes 200–260px forever |
| **Icon rail** | few sections, screen real estate is precious | icons need tooltips; discoverability is lower |
| **Top tabs** | 2–5 **peer** views of the same object | breaks down past 5 |
| **Breadcrumb** | hierarchy — it is the tree's projection | useless without a real hierarchy |
| **Command palette** (I1) | **everything** — the universal accelerator | must be built once, well |

**The one-model law:** a screen has **one** primary navigation model. A sidebar + top tabs + breadcrumbs +
a segmented control on the same screen is not "rich" — it is four maps of the same territory, and the user
trusts none of them.

---

## §5. PROGRESSIVE DISCLOSURE — the cure for the cluttered console

```
THE 20/80 RULE: show the 20% used 80% of the time. Everything else lives behind "More", a details panel,
  or an "Advanced" section that is COLLAPSED — never removed.

DRAWER vs PAGE:
  · a DRAWER is for a PEEK: view details, make a small edit, and get back to the list without losing your place
  · a PAGE is for WORK: a long form, a builder, a multi-tab editor
  Rule: if the user must lose the list to do the thing, the thing deserves a page. If not, it deserves a drawer.
  (A drawer that is really a page is a scrolling prison; a page that is really a drawer is a navigation tax.)

ADVANCED SETTINGS: collapsed, labelled honestly, remembered per user.

DENSITY OF CHOICE: a form with 30 fields visible at once is not "complete" — it is unfinished thinking.
  Group, section, and stage it.
```

---

## §6. SPEED IS A FEATURE (perceived, not measured)

```
□ OPTIMISTIC WRITES (I5): the local decision is shown instantly; the network is an implementation detail.
□ SKELETONS IN PLACE (I10): the shape of the content, not a spinner. The layout must not shift when data lands
  (LAYOUT_INVARIANTS §3 — CLS is the tax on a bad skeleton).
□ PREFETCH ON HOVER: hovering a row for 100ms starts loading its detail. Opening then feels instant.
□ DEBOUNCE, don't throttle, user input: 300ms on search.
□ NOTHING BLOCKS EVERYTHING. There is no state where the entire UI is unusable because one thing is loading.
□ THE MUTATION IS NOT A WALL: the button shows progress, the rest of the screen stays alive.
```

---

<!-- MIRROR SOURCE (SoT): stiffness detector ST1–ST12 (C5) | cited in .cursorrules Law 33 · ROLE_LEAD · ROLE_DESIGN · ROLE_QA_ARCH · ROLE_QA_VISUAL · QA_VISUAL_AESTHETE_SENSOR · TEMPLATE_ADMIN_UI_UX · TEMPLATE_UI_COMPOSITION_PASSPORT | index: CONFLICT_REGISTRY.md -->
## §7. THE STIFFNESS DETECTOR — 12 signs a console is "prim" (ST1–ST12)

Run on every operational screen. Each sign is checkable from the code. Owner: @DESIGN (AUDIT), mirrored by @QA_VISUAL.

> **Labels are `ST1–ST12` (stiffness)** — deliberately distinct from the SECURITY SURFACE `S1–S12` (`roles/SECURITY_GATE_PROTOCOL.md`) to avoid a namespace collision (C5, `roles/CONFLICT_REGISTRY.md`).

| # | Symptom | Why it hurts | Fix |
|---|---------|--------------|-----|
| **ST1** | Every action opens a modal or a drawer | The user is thrown out of context for a one-field change | Inline edit (I2); modal only for real work |
| **ST2** | A confirm dialog on every destructive action | A tax on all users, every time, to prevent a rare mistake | Undo (I4). Typed-confirm survives only for irreversible/wide-blast operations |
| **ST3** | No keyboard path through the primary flow | The expert user is forced to the mouse — the console can never feel fast | I8 + the command palette (I1) |
| **ST4** | Row actions visible on every row, always | Visual noise that competes with the data | Reveal on hover/focus (I7) |
| **ST5** | A full-screen loader / spinner | The whole product freezes on one request | Skeletons in place (I10) |
| **ST6** | Filters and sort reset on reload | The user rebuilds their working context every session | Persist and allow saving (I6) |
| **ST7** | No bulk operations | 50 items = 50 repetitions of the same click sequence | Bulk select + action bar (I3) |
| **ST8** | Deep hierarchy with no search | Navigation becomes an archaeology dig | Search across the tree (§2.2) |
| **ST9** | Nothing is deep-linkable | Work cannot be shared or resumed | State in the URL (I9) |
| **ST10** | Empty state says "No data" | The dead end at the exact moment the user needs guidance | An empty state that teaches (I12) |
| **ST11** | Tree expansion collapses on every reload | The user re-navigates from the root every time | Persist expansion (§2.2) |
| **ST12** | The form has 30 visible fields | Unstaged complexity dumped on the user | Progressive disclosure (§5) |

**Verdict:** 1–2 hits → 🟡. **3+ hits → 🔴 — the screen is a CRUD form wearing a console's clothes.**
It is not fixed by styling; it is fixed by adding the missing capabilities.

---

## §8. ACCEPTANCE — the premium console checklist

```
□ The SPEC named every I1–I12 item as "applies / N/A + why" — no silence
□ Cmd+K exists and reaches every action and destination
□ A single-field change never opens a modal
□ Destructive = execute + Undo (5–10s); typed-confirm only for irreversible/wide operations
□ Bulk select works with Shift-range; the action bar shows the count; one undo covers the batch
□ Filters/sort/density/columns persist; a view can be saved and named
□ Row actions reveal on hover; the resting table shows data
□ The primary flow is completable with the keyboard alone; focus is always visible
□ Every meaningful state is a URL
□ No full-screen spinner exists anywhere; skeletons hold the shape (CLS ≤ 0.1)
□ Search: "/" focuses it, 300ms debounce, results navigable by keyboard
□ Every empty state names the next action
□ Trees: expansion persists · "Move to…" is searchable · multi-select moves together · search reveals in place
□ Tables: identifying column first · numbers right + tabular · actions sticky last · header sticky · hairline dividers
□ One navigation model per screen
```

---

## §9. BOUNDARIES (do not duplicate — reference)

```
VISUAL_CRAFT_CANON   → how it LOOKS expensive (restraint, tone, light, chroma, type, the floor §11)
INTERFACE_CRAFT_CANON→ how it FEELS expensive to work in (this file)
LAYOUT_INVARIANTS    → does the geometry hold under content (equal-height, overflow, zero-shift)
LAYOUT_COMPOSITION   → the grammar of space (primitives, proximity)
FRONTEND_DESIGN_EXCELLENCE → the patterns of the two contours
DOMAIN_STANDARDS     → what the business requires on this page type
ROLE_QA_ARCH         → the State Matrix and mutations (correctness) — unchanged by this file
```

A screen can pass every other canon and still be stiff. That is precisely the gap this file closes.

---

Reference: `roles/VISUAL_CRAFT_CANON.md` (the sister canon — the physics of expensive) · `roles/FRONTEND_DESIGN_EXCELLENCE.md` (§2 the operational contour) · `roles/TEMPLATE_ADMIN_UI_UX.md` · `roles/LAYOUT_INVARIANTS.md` · `roles/LAYOUT_COMPOSITION.md` · `roles/COMPONENT_REGISTRY.md` · `roles/DOMAIN_STANDARDS.md` · `roles/ROLE_DESIGN.md` · `roles/ROLE_FRONTEND.md` · `roles/ROLE_QA_VISUAL.md` · `roles/MOTION_AMBITION_DIAL.md` (MICRO — the micro-moments of operational screens)
Version: 1.0 | 2026-07-12
