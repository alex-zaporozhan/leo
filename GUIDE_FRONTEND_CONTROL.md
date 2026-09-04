# GUIDE — Steering the frontend

**How to direct the design and frontend layer of LEO: which role owns what, whom to address with
what, and what you have to name in the request.**

This is an operator's guide, not a canon. It carries no rules of its own — every rule it describes
lives in `.cursorrules` or in `roles/`, and where this guide and a canon disagree, **the canon wins
and this file is the bug**. Its purpose is narrower and practical: the design layer of this system
is six roles, seven ordered steps, four modes and three floors, and none of that is discoverable
from a chat prompt. This is the map you read once so you stop guessing.

Related: [`ARCHITECTURE.md`](./ARCHITECTURE.md) (jurisdictions and gates across the whole system) ·
`roles/ROLE_DESIGN.md` (the design constitution, including its own READING MAP) ·
`roles/RAG_CANON.md` §2 (the router).

---

## The first thing to know: you do not name files

**Do not cite canons, templates or detectors in your request.** The router (`roles/RAG_CANON.md` §2)
resolves the task class and hands each role its own reading minimum; `roles/ROLE_DESIGN.md`'s
READING MAP then narrows that to sections inside the design domain. You name a **role and an
intent** — the system is responsible for opening the right files. If it opens the wrong ones, that
is a finding in the routing, not a defect in your phrasing.

There are exactly two exceptions, and neither is about navigation.

**1 — Project artifacts, because they are inputs that can be missing.** These live in
`docs/artifacts/`, not in `roles/`: the world (`VISUAL_CONCEPT_*`), the capability map
(`CAPABILITY_MAP_*`), the domain model (`DOMAIN_MODEL_*`), `SEO_ONPAGE_*`, the project passports.
A role that needs one and does not have it is required to **stop and request it** rather than draw
around the hole. Mentioning them is useful only to tell the system something it cannot know:
*"there is no concept yet, we are starting from zero"* or *"the capability map is from before the
refactor, it is stale."*

**2 — You are unhappy with the result.** Then naming the file is not an address, it is a charge.
*"The floor was taken and the screen is still dead"* points at `MOTION_CRAFT_CANON` §1 and the
M1–M12 detector, and what you are reporting is that **a detector did not fire**. A request in that
shape repairs the system; a request that just says "make it nicer" repairs one screen.

---

## The six roles

| Role | Owns | Call it when |
|---|---|---|
| **@CREATOR** | **The world** — `VISUAL_CONCEPT_*`: the concept phrase, eight DNA axes, palette / typography / effect kit / motion personality taken as a ready recipe. TASTE GATE (cliché ban-list C1–C10 + the "remove the logo" test). | Once per project. Also on a world change — but there the work continues as @DESIGN RESKIN |
| **@MEDIA_ENGINEER** | **Rendering the world** into clean plates: photography, illustration, still life, video, 3D/WebGL source. The brand mark stays a CSS/SVG layer over the plate, never baked into pixels. | After the concept is approved, when real media is needed |
| **@MOTION** | **The language of movement.** DIAGNOSIS · CONCEPT · SPEC · AUDIT · **MICRO** | **First, before @DESIGN**, on any landing, public site or portfolio. **MICRO** for `/admin` and `/app`: focus, press, success, transition |
| **@FRONTEND** | **Primitives and the system**: the component registry, the palette as CSS variables, the Z-scale, the single `<Button>` primitive, the lint. And **`CAPABILITY_MAP_*`** — it reads the actual backend before anyone draws over it | Before @DESIGN (the capability map); when a primitive is missing; Visual Quality Gate before every handoff to @DEV |
| **@DESIGN** | **Arbiter of pattern and composition.** Holds the order of production and refuses a step out of sequence | On a new pattern or composition, before @DEV. After @QA_ARCH, on design problems |
| **@QA_VISUAL** | **The render sensor**: render → measure → compare. Geometry, overflow, CLS, states and micro-moments under hostile content | After @QA_ARCH returns 🟢, before @QA — on any UI change |

**The boundary worth memorising:** @DEV makes no design decisions. Law 19 — *the frontend is edited
from decisions, not from the screen*. A UI change with no decision behind it is an **undeclared
design decision**, and that is how a product loses coherence one commit at a time.

---

## The four modes of @DESIGN — this is your vocabulary

You do not name the mode. The phrasing selects it:

| You say | Mode | You get |
|---|---|---|
| "check this screen / what is wrong / design audit" | **AUDIT** | a verdict plus a fix list, from 11 lenses |
| "design this / how should it look / I need a spec" | **SPEC** | `DESIGN_SPEC_*` with a Component Map, handed to @DEV |
| "choose between / which is better / compare these" | **VERDICT** | one winner and the reasoning — one page |
| "change the concept / different character / rebrand / make it like X" | **RESKIN** | a Swap Map; the skeleton is inviolable |

**The distinction people get wrong most often:** *"make it look more expensive / cleaner"* **inside
the current world is an AUDIT, not a RESKIN.** The one-axis rule. RESKIN replaces the world;
everything short of that is improvement within it.

**What AUDIT will not do:** rebuild the concept. An audit reports upward — it does not re-decide the
world. If the audit's real conclusion is "the world is wrong", that goes back to @CREATOR.

**What VERDICT will not do:** re-open something already decided. `CONFLICT_REGISTRY` is checked
first — if the question has a registered winner, this is a lookup, not a verdict.

---

## The order of production (Law 25) — and why @DESIGN sends work back

Seven steps. A surface arriving at @DEV with a step skipped goes back; it is not caught later.

```
(1) World   → (2) Register → (3) Typography & spatial grammar → (4) Component design
            → (5) Adaptive composition → (6) Motion → (7) Verdict
```

1. **World** — the aesthetic is born once per surface-world (Law 28). A product may hold several
   worlds, and each surface is edited from **its own**.
2. **Register** — declared before any surface is designed (Law 33). See below; this is the cheapest
   mistake to make and the most expensive to carry.
3. **Typography and spatial grammar** — before a pixel is placed. The project's own type scale, not
   a generic one; the eight layout primitives; space belongs to the container; proximity as a number.
4. **Component design** — a screen is assembled **only from registered components**. A block that
   duplicates a primitive's markup is a defect, not a shortcut.
5. **Adaptive composition** — the narrow viewport is **designed, not derived**. Every declared
   viewport carries an answered Responsive Matrix: stack order · the table→cards point · hidden
   versus moved · navigation model · thumb-reachable primary action.
6. **Motion** — the language of the world, not decoration added afterwards.
7. **Verdict** — the machine floor and the aesthete crime catalogue (Law 39), then **rendered**
   geometry measured by @QA_VISUAL. A verdict is a measurement or a catalogued crime, never an
   opinion.

### Two places where the order is misleading

**The register (2) decides more than it looks like it does.** `instrument` (admin, app, tools,
dashboards, forms) and `statement` (landing, hero, brand page, campaign) carry **partly opposite
laws**. Restraint *is* the craft of the instrument; scale as a weapon is the craft of the showcase.
Applying instrument restraint to a showcase is exactly how a landing ends up looking like a
settings screen with a big button on it. **Timidity is a craft failure exactly as much as
gaudiness is.** A page that tries to be both is neither.

**Motion (6) is numbered sixth but decided second.** Sixth is where its *detail* is written, not
where it is chosen. On a public site @MOTION is called **first**. On any surface the **motion floor
is taken at step (2)** together with the visual floor. A screen that reached step (6) with no motion
decision has already shipped stiff.

---

## The three floors — what gets taken when nothing has been decided

The absence of a concept is a **licence to take the floor, never a licence to improvise**.

| Floor | Home | What it supplies |
|---|---|---|
| Tokens | `VISUAL_CRAFT_CANON` §11 | palette, type scale, shadows, hairline, chrome |
| Movement | `MOTION_CRAFT_CANON` §1 | four durations, easings by role, stagger, the entrance |
| Composition | `INTERFACE_CRAFT_CANON` §3.5 | list/table, form, record, filter bar, destructive action |

And the split by register, which is what makes the floors safe: **`instrument` with no world takes
the floor and continues. `statement` stops and requests the world.** A showcase cannot be built out
of a floor — it has no default.

A screen that took one of the three took a third of the floor. All three now say so from inside
their own canon.

---

## Who to address with what

| What you want to say | Address | What comes back |
|---|---|---|
| "Starting a project, it needs a character" | **@CREATOR** | `VISUAL_CONCEPT_*` plus four passports by derivation |
| "This screen looks cheap" | **@DESIGN** AUDIT | detectors X1–X12 / ST1–ST12 / Y1–Y12; 3+ hits = 🔴 |
| "I need a new screen" | **@FRONTEND** (map) → **@DESIGN** SPEC | no capability map, no spec — that is a hard stop |
| "Everything is static, it feels wooden" | **@MOTION** MICRO | `MICRO_SPEC_*` for focus / press / success / transition |
| "The landing does not sell" | **@MOTION** first, then @DESIGN | hero archetype, ambition dial (default `confident`) |
| "Two options, I cannot choose" | **@DESIGN** VERDICT | one winner with reasoning — never "both are fine" |
| "I am tired of how it looks, I want another character" | **@DESIGN** RESKIN | Swap Map; the skeleton is not touched |
| "It is built — check it for real" | **@QA_VISUAL** | measurement of the render, not an opinion |
| "Buttons are different heights, text is unreadable" | **@QA_VISUAL** + **@FRONTEND** | V15–V21 machine floor; the lint and the primitive |
| "The backend is rich and the UI is a tablecloth" | **@FRONTEND** | `CAPABILITY_MAP_*`: C1–C12 surfaced, or explicitly not, plus why |
| "The narrow viewport is broken" | **@DESIGN** (class of the surface) | an answered Responsive Matrix, not a media-query patch |

---

## Two rules that save the most time

**Never ask for "make it like X."** An external reference is legal in this system only in the form
`SOURCE → EXTRACT → TRANSFER → NOT TAKING`, extracting exactly **one named technique**, at most one
external product per concept, and only for the `instrument` register. A spec citing an outside
product while a native canon exists does not pass. The system's own sentence: **reproducing last
season's product is not a standard — it is a lag.**

**Never ask to "copy the neighbouring screen."** Copying propagates whatever that screen got wrong.
The prescribed path is: the surface's **world** → its **register** → the derived **passports** → the
screen's own **`DESIGN_SPEC_*`** and its Component Map → the project's live component registry.

---

## Details

### The task class your request resolves to

The router speaks in `TC-xx`; you speak in plain tasks. The join, for the design layer:

| Your task | Class |
|---|---|
| Operational screen — admin, app, internal tool | **TC-01** |
| Public / marketing page (indexable) | **TC-02** |
| Statement surface — hero, brand page, campaign, portfolio | **TC-03** |
| Node graph, pipeline builder, agent canvas | **TC-04** |
| A new world, or a RESKIN | **TC-05** |
| The gate on the render | **TC-16** |
| Text, a colour, a one-line fix | **TC-00** |

**AUDIT, VERDICT and narrow-viewport work have no class of their own** — they take the class of the
surface being worked on. This matters when you ask for an audit of a landing: it is `TC-03` and it
loads editorial craft, not the operational checklist.

### The ambition dial (public surfaces)

Boldness is an **explicit input parameter**, set by you, @CREATOR (by brand) or @LEAD. Unset means
`confident`, **not** `restrained` — that inversion is deliberate: an unconfigured system used to
clamp itself down by default.

| Level | Meaning | Selection hint |
|---|---|---|
| `restrained` | Quiet confidence. Trust over effect | Regulated, enterprise, conservative audience |
| `confident` *(default)* | Character is present but does not shout | Mass-market, B2B, trust over wow |
| `bold` | Memorable, stands out. 3D considered on equal footing | Premium product in a dull market |
| `experimental` | Full range | Personal brand, portfolio, agency, creative tool, AI / spatial products |

Two things the dial never changes: performance rules and `prefers-reduced-motion`. It changes
expressiveness and technique priority, not the right to violate accessibility. And at `confident`
and above, **one easing curve across the whole project is a 🔴** — personality degeneration.

### What @QA_VISUAL actually measures

Not taste. Fourteen vectors, each a measurement against a number: V1 equal-height siblings ·
V2 overflow and silent clipping · V3 layout shift on load · V4 adaptivity and targets ·
V5 rendered state matrix · V6 rhythm and alignment · V7 interactive states at runtime ·
V8 micro-moments · V9 baseline diff · V10 dark theme · V11 document scroll stability ·
V12 collision and layer discipline · V13 cheapness · V14 interface stiffness. A violated
measurable invariant is 🔴 and @QA_VISUAL does not issue 🟢 over it.

This is why *"it looks off to me"* is a weak instruction and *"cards jump when the text is long"*
is a strong one — the second names V1 and it is settled by a number rather than a discussion.

### What a strong request looks like

A request works well when it carries three things: **the surface** (which screen, which register),
**the symptom** (what is observably wrong, not what you would like added), and **what already
exists** (is there a world, a spec, a capability map).

> *"The client list in /admin. It was built on the composition floor and it is technically fine,
> but it reads as a table dump — nothing tells you what to look at first. There is a world, the
> spec is from wave 2."*

That resolves to `TC-01`, AUDIT mode, and it will come back through the density and hierarchy
lenses plus the stiffness detector. Compare:

> *"Make the client list nicer."*

Same screen, no register, no symptom, no state — and the system has to guess all three.

### Escalation — what to do when you disagree with the result

In order of cost:

1. **Name the detector or the vector.** "This should have been caught by ST-something" is a
   testable claim, and the answer is either a fix or a demonstration that the detector does not
   cover it — which is itself the finding.
2. **Ask for the decision behind it.** Every UI element is supposed to trace to a decision. "Which
   decision produced this?" is always a legitimate question, and an inability to answer it is a
   Law 19 violation regardless of how the screen looks.
3. **Ask whether the register is right.** A surprising number of "this looks wrong" cases are a
   showcase built with instrument laws, or the reverse.
4. **If the rule itself is wrong** — that is `@LEO_EDITOR`, invoked deliberately and outside
   delivery work. Do not fix a canon in passing during a feature.

---

## What this guide deliberately does not cover

Backend, architecture, security, testing and release. Those layers are directed by different roles
under different laws — see [`ARCHITECTURE.md`](./ARCHITECTURE.md) and the router in
`roles/RAG_CANON.md` §2.
