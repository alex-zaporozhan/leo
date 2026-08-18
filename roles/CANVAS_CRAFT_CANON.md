# CANVAS_CRAFT_CANON.md
# The craft of node-graph and canvas editors: pipeline builders, agent graphs, automation canvases, flow editors.
# Class of product: n8n · LangGraph Studio · Figma · Blender node editor · Unreal Blueprints · Retool workflows.
# Position: a specialisation of INTERFACE_CRAFT_CANON for a UI where the CANVAS is the product, not a screen in it.
# Owners: @DESIGN (the SPEC of a canvas), @FRONTEND (the primitives), @QA_VISUAL (the detector), @DEV (execution).

> **The canon's dogma:** in a graph editor, the graph is not a *picture of* the program — the graph **is** the
> program. Therefore every visual decision is a semantic decision. A node's shape tells you what it does;
> an edge's colour tells you what flows; the layout tells you the order of thought.
> Prettiness that lies about semantics is worse than ugliness that tells the truth.
>
> **The failure this file prevents — the "toy graph":** boxes with rounded corners and bezier lines, a drag
> that works, and nothing else. It looks like n8n in a screenshot and collapses at 40 nodes: you cannot find
> anything, you cannot see what ran, you cannot debug, a loop is indistinguishable from a mistake, and the
> canvas becomes a plate of spaghetti. Beautiful in the demo, unusable at work.

---

## §1. THE NODE — anatomy (the node is a sentence, not a box)

A node must answer four questions **at a glance, at 100% zoom, without being opened**:
**what am I · what state am I in · what goes in/out · did I just run.**

```
┌─[type glyph]─ Node title ──────────────── [status] ─┐   ← the header IS the identity
│  ▸ subtitle: the ONE parameter that matters          │   ← e.g. the model name, the URL, the condition
├──────────────────────────────────────────────────────┤
│ ● in:trigger        (typed, named, few)   out:ok ●   │   ← ports: TYPED and NAMED. Never "port 1"
│ ● in:context                             out:err ●   │
└──────────────────────────────────────────────────────┘
     ▲ badge: [runs · last duration · error count]          ← only when it has run
```

**Laws:**
```
□ TYPE IS READABLE WITHOUT TEXT: a glyph + a category tint. Categories: trigger · transform · LLM/agent ·
  tool/IO · control-flow (branch/loop) · terminal. Five to seven categories, ONE tint family each
  (VISUAL_CRAFT §4: small area = high chroma is allowed HERE — the glyph, the port, the header line, not the body).
□ THE BODY IS QUIET. A node is chrome (VISUAL_CRAFT §8): white/surface, hairline, e1. It must not shout.
  What shouts is STATE (running/failed/selected), never decoration.
□ ONE SIGNIFICANT PARAMETER IS VISIBLE IN THE HEADER. "OpenAI" is useless; "gpt-4o · temp 0.2" is a node
  you can read from across the canvas. Choose that parameter per node type — it is a design decision.
□ PORTS ARE TYPED AND NAMED. A typed port refuses an illegal edge BEFORE the drop (C5-style proactive
  guidance, FRONTEND_CAPABILITY_CANON): the illegal target dims while dragging. Never a red toast after.
□ FEW PORTS. If a node needs 8 inputs, it is two nodes, or it needs a config panel, not eight sockets.
□ SIZE ENCODES NOTHING. All nodes of a category are the same size. Variable size = a lie the user will decode wrong.
□ COLLAPSED STATE EXISTS. A big graph needs nodes that can shrink to a title bar.
```

---

## §2. EDGES — what flows, and where it goes wrong

```
□ AN EDGE HAS A MEANING, AND THE MEANING IS VISIBLE:
    data flow      → solid, neutral, thin
    control flow   → solid, accent
    error path     → the error hue, DASHED (an error edge that looks like a data edge is a bug factory)
    conditional    → labelled at the fork ("true" / "false" / the condition, short)
    loop back-edge → a DIFFERENT visual (dashed + a curve that reads as "returns"), because a loop that looks
                     like a forward edge turns a graph into a maze (§5)
□ ORTHOGONAL OR SPLINE — pick ONE and never mix. Orthogonal (right-angle) reads as a circuit and scales
  better past ~30 nodes; splines read as organic and get tangled. For an engineering tool: orthogonal, with
  rounded corners. This is a project decision, made once.
□ EDGES ROUTE AROUND NODES, they do not cross them. An edge crossing a node body is a lie about connectivity.
□ CROSSINGS ARE MINIMISED, not hidden. If crossings are unavoidable, use a hop/jump marker.
□ AN EDGE IS SELECTABLE AND DELETABLE. Hovering it highlights BOTH endpoints and dims everything else.
□ HOVER A NODE → its whole subgraph (upstream + downstream) highlights, the rest dims to 30%.
  This single feature is what makes a 60-node canvas comprehensible. It is not optional.
□ DURING A DRAG: legal targets glow, illegal ones dim. The user should never be able to make an invalid edge.
```

---

## §3. THE CANVAS — layout, order, and not getting lost

```
□ AUTO-LAYOUT EXISTS AND IS ONE KEYPRESS. Dagre/ELK, layered left→right (or top→bottom — pick one, forever).
  Manual positions are respected but "Tidy up" must always be available. A canvas where the user must be the
  layout engine is a canvas they will abandon.
□ FLOW DIRECTION IS SACRED. Left→right for pipelines. Every forward edge goes right. This is why back-edges
  must look different: they are the ONLY thing going the other way, and that is information (§5).
□ MINIMAP for graphs > ~20 nodes, with the viewport rectangle and node-status colours in it.
□ SEARCH ON THE CANVAS (`/` or Cmd+F): find a node by title/type/parameter → the canvas pans and highlights it.
  Non-negotiable past 30 nodes.
□ ZOOM: fit-to-screen (`Shift+1`), zoom-to-selection (`Shift+2`), 100% (`Cmd+0`). Semantic zoom: below ~50%
  nodes render as coloured blocks with titles only — the graph becomes a map.
□ GROUPS / FRAMES: a labelled region that moves with its contents. This is how a 100-node graph stays human.
□ ALIGNMENT: snap to a grid; align/distribute commands. A crooked graph reads as a careless product.
□ THE CANVAS IS NOT INFINITE IN PRACTICE. Provide "fit" and "reset view" — a user who has panned into the
  void must always be one key from home.
```

---

## §4. THE INSPECTOR — where configuration lives

```
□ SELECT A NODE → the inspector panel shows its config. The node itself is NEVER a form. A node with input
  fields inside it destroys the canvas's readability and cannot be laid out.
□ THE INSPECTOR IS A PANEL, NOT A MODAL. A modal breaks the mental link between the config and the graph.
  You must be able to see the graph while configuring a node in it.
□ SCHEMA-DRIVEN. The node's config form is generated from the node type's schema. Hand-written forms per node
  type is how a 30-node-type product dies.
□ THE INSPECTOR SHOWS THE LAST RUN'S I/O for this node — the input it received, the output it produced.
  This is the single most valuable debugging surface in the entire product (§6).
□ INLINE VALIDATION: an invalid config marks the NODE on the canvas (a badge), not just the panel. A user
  scrolled away from the panel must still see that something is broken.
```

---

## §5. CONTROL FLOW — branching, loops, and the maze problem

The moment a graph gains loops, it stops being a diagram and becomes a program. Most canvas UIs fail here.

```
□ A LOOP IS EXPLICIT AND STRUCTURAL, not "an edge that happens to point backwards".
  Prefer a LOOP CONSTRUCT: a labelled frame containing the body, with a visible condition and a visible
  iteration source ("for each item in X" / "while condition"). A raw back-edge is allowed, but it must be
  visually unmistakable (§2) and it must display its exit condition ON the edge.
□ THE EXIT CONDITION IS ALWAYS VISIBLE. A loop whose termination is invisible is a hang the user cannot see
  coming. Show: the condition, the max-iterations guard, and (at run time) the current iteration (`3/10`).
□ A MAX-ITERATIONS GUARD IS MANDATORY AND VISIBLE — in the UI and in the runtime. An agent graph without an
  iteration ceiling is an incident waiting to happen (this mirrors ASYNC_WORKERS_CANON: everything has a deadline).
□ BRANCHES ARE LABELLED AT THE FORK. "true/false" or the condition text — short, on the edge, always.
□ A NODE'S ERROR PATH IS AN EXPLICIT PORT, not an invisible global handler. If a node can fail, its `err`
  port either goes somewhere or the graph declares "unhandled" — and unhandled is a VISIBLE state, not silence.
□ PARALLEL FAN-OUT / FAN-IN reads as fan-out / fan-in: the split and the join are visible nodes, and the join
  states its policy (all / any / first). An invisible join is where race conditions are born.
```

---

## §6. EXECUTION IS A FIRST-CLASS LAYER — this is what makes it a control panel instead of a diagram editor

> A canvas that can only *build* is half a product. A canvas that shows you what **happened** is an instrument.
> This section is the "spaceship console" the user actually wants.

```
RUN OVERLAY (the same graph, a different layer — never a separate screen):
  □ node state, live: idle · queued · running (a pulse — the ONLY thing on the canvas allowed to loop) ·
    ok · failed · skipped · cached
  □ the ACTIVE PATH LIGHTS UP as it executes: edges that carried data are drawn bright; branches not taken
    fade to 20%. The user watches the program think. This is the single most delightful thing in this class
    of product, and it is nearly free once state is on the node.
  □ per-node: duration, token/cost (for LLM nodes), retry count, iteration (`3/10` in loops)
  □ progress on the RUN itself: nodes done / total, elapsed, and a working CANCEL
    (cooperative — the backend supports it; ASYNC_WORKERS_CANON AW-2)

DEBUG (the reason people stay):
  □ CLICK A NODE MID-RUN OR AFTER → see its exact INPUT and OUTPUT (§4). Nothing else in the product comes close
    in value.
  □ Run from here / re-run only this node with the previous inputs (the backend must support it — if it does not,
    request it: FRONTEND_CAPABILITY_CANON §2 reverse direction).
  □ A failed node shows the error INLINE on the node (a short badge) and in full in the inspector, with the
    exact input that caused it.
  □ RUN HISTORY: past runs are listed; selecting one replays its overlay ON THE GRAPH. A log table is not a
    replacement — the graph IS the log, and this is why (FRONTEND_CAPABILITY_CANON C6).
  □ A DIFF between two runs: which nodes behaved differently.

STREAMING (agent graphs specifically):
  □ Tokens stream into the node's output preview as they arrive. The node shows life.
  □ Tool calls appear as sub-steps under the agent node, in order, with their results.
```

---

## §7. BUILDING — how a node gets onto the canvas

```
□ NODE PALETTE: searchable, grouped by category, keyboard-first. Type 3 letters → Enter → the node lands
  connected to the current selection. (INTERFACE_CRAFT I1: the command palette, applied to the canvas.)
□ DROP AN EDGE ON EMPTY CANVAS → the palette opens, filtered to nodes whose input type MATCHES the dragged
  output. This is the single best affordance in the entire genre: the tool teaches the schema by making the
  wrong thing impossible.
□ DUPLICATE (`Cmd+D`), copy/paste subgraphs, and paste-into-group all work. A canvas without copy/paste of a
  selection is a canvas users will not build big things in.
□ UNDO/REDO IS UNLIMITED AND TRUSTED (INTERFACE_CRAFT I4). Every canvas mutation — move, connect, delete,
  config change — is undoable. A canvas without undo is a canvas people are afraid of, and fear makes them
  build small.
□ TEMPLATES / subgraph reuse: a working graph can be saved as a reusable block.
□ AUTOSAVE + explicit versioning. "Did I lose my work" must never be a thought.
```

---

## §8. KEYBOARD (this is what makes it feel professional)

```
/  or Cmd+F   find node          Tab / Shift+Tab   next / prev node along the flow
Cmd+K         node palette       Enter             open the inspector for the selection
Cmd+D         duplicate          Delete            delete selection (undoable — no confirm; INTERFACE_CRAFT I4)
Cmd+Z / ⇧⌘Z   undo / redo        Shift+1 / Cmd+0   fit / 100%
Cmd+Enter     run                Esc               cancel drag / close inspector
Space+drag    pan                Cmd+A             select all (in the group, if inside one)
Arrow keys    nudge selection    Shift+click       add to selection · drag on canvas = box-select
```

An expert must be able to build a five-node pipeline without touching the mouse except to pan.

---

## §9. THE TOY-GRAPH DETECTOR (10 signs) — @QA_VISUAL vector

| # | Symptom | Consequence |
|---|---------|-------------|
| **G1** | Node types are distinguishable only by their text label | At 50% zoom the canvas is meaningless soup |
| **G2** | Ports are untyped — any edge can connect to anything | The tool teaches nothing; errors are found at runtime |
| **G3** | An invalid edge is rejected with a toast **after** the drop | Guidance should be during the drag (dim illegal targets) |
| **G4** | No auto-layout / "tidy up" | The user is the layout engine; graphs stay small and ugly |
| **G5** | No canvas search, no minimap | Unusable past 30 nodes — and every real graph passes 30 |
| **G6** | Hovering a node does not highlight its subgraph | Connectivity is unreadable in a dense area |
| **G7** | The node is a form (inputs inside the box) | The canvas cannot be laid out, read, or zoomed |
| **G8** | No run overlay — execution results live in a separate log screen | The graph is a diagram, not an instrument. **This is the big one.** |
| **G9** | You cannot see a node's actual input/output after a run | Debugging is impossible; users leave |
| **G10** | A loop looks like a normal edge; no visible exit condition or iteration cap | A maze, and an unbounded-agent incident waiting to happen |

**Verdict:** any of G7/G8/G9/G10 → 🔴 (the product is a diagram editor pretending to be a control panel).
3+ hits overall → 🔴.

---

## §10. THE AESTHETIC (how it becomes "the spaceship console")

The genre's aesthetic is **instrument**, not decoration — and `VISUAL_CRAFT_CANON` applies without exception:

```
□ THE CANVAS IS THE QUIET (VISUAL_CRAFT §1): a deep, near-neutral field (dark or light — pick one, and if dark,
  it is INK-tinted, never #000). A subtle grid or dot-field: it gives scale and communicates snapping.
□ CHROMA LIVES IN SEMANTICS ONLY (§4, §8): category tints on glyphs/headers/ports; state colours on run.
  A decorative gradient on a node body is X5 — the loudest cheap tell in the genre.
□ ONE LIGHT SOURCE (§3): nodes are cards — e1 at rest, e2 when selected/dragged. Selection is an accent RING,
  not a glow.
□ THE ONLY LOOPING ANIMATION ON THE CANVAS IS "RUNNING". Everything else is a transition. This is the loop budget,
  and it is what makes execution feel alive: motion means work is happening, always, everywhere, with no exceptions.
□ EDGES ARE HAIRLINES (1–1.5px) at rest; they thicken and brighten only when active, selected, or hovered.
  Fat static edges are what make a canvas look like a toy.
□ tabular-nums on every duration, cost and iteration counter (§5.4).
```

---

Reference: `roles/INTERFACE_CRAFT_CANON.md` (the parent canon: I1–I12, keyboard, undo, palette) · `roles/VISUAL_CRAFT_CANON.md` (restraint, one light, chroma discipline, the cheapness detector) · `roles/FRONTEND_CAPABILITY_CANON.md` (C2 events → live overlay · C5 constraints → typed ports · C6 history → run replay · C7 pipelines → progress and cancel — a canvas is the purest case of capability surfacing) · `roles/ASYNC_WORKERS_CANON.md` (AW-2 cooperative cancellation — the Cancel button is only real if the backend is; iteration caps) · `roles/LAYOUT_INVARIANTS.md` · `roles/ROLE_DESIGN.md` · `roles/ROLE_FRONTEND.md` · `roles/ROLE_QA_VISUAL.md`
Version: 1.0 | 2026-07-12
