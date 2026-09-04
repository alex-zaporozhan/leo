# COMPONENT_REGISTRY.md
# Canon of component-first design and the UI primitive registry
# Mandatory for @DESIGN (SPEC), @FRONTEND (VQG + handoff), @QA_ARCH (Vector 6.1)

> **Principle:** a screen is designed and assembled **only from registered components**. A raw "div + styles" block is permitted only as a temporary stub marked in DEV_PROMPTS — not as final UI.
> **Related:** geometry and motion — `roles/LAYOUT_INVARIANTS.md` (§1–§12); taste — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; the live project registry — **`docs/knowledge/FRONTEND_PASSPORT_[PROJECT].md` §3** (the project canon; template — `roles/TEMPLATE_PROJECT_FRONTEND_PASSPORT.md` §3).

---

## §1. THE GOLDEN RULE OF COMPONENT-FIRST

```
@DESIGN in SPEC → for each UI block specifies the component from the registry (or "new: name + purpose")
@FRONTEND in handoff → does not pass a task to @DEV without a "block → component" mapping
@DEV → does not copy markup between pages; extends an existing component or creates a new one in the correct layer
@QA_ARCH → 🔴 if a page duplicates markup already extracted into a component, or has a block with no mapping in DESIGN_SPEC
```

**Exceptions (a new component is not needed):**
- a one-off legal text (`/privacy`) with no interactivity;
- a unique hero composition already structured as `HeroFullBleed` / a CMS block.

**A new component is mandatory when:**
- the pattern will repeat ≥2 times on the site or in the admin panel;
- the block has Loading/Empty/Error/Success states;
- the block contains a motion island (carousel, tab strip, autoplay).

---

## §2. THREE COMPONENT LAYERS

| Layer | Folder (convention) | Responsibility | Examples |
|-------|---------------------|----------------|---------|
| **T1 — Primitives** | `components/ui/` | Atoms: button, card, input, badge, metric, section heading | `Button`, `Card`, `SectionHeader`, `TrustMetric`, `DocCard` |
| **T2 — Blocks** | `components/blocks/` | CMS blocks and marketing sections with a fixed payload contract | `HeroBlock`, `TrustStripBlock`, `CourseGridBlock` |
| **T3 — Composers** | `components/marketing/`, layouts | Page assembly from blocks; chrome | `HomeMarketing`, `HeroFullBleed`, `Header`, `Footer` |

**Dependency rule:** T3 → T2 → T1. T1 does not import T2/T3. A block does not duplicate a primitive's markup — it uses `<Card>`, not its own `div.prism-card`.

---

## §3. MANDATORY PRIMITIVES (minimum for any SaaS + public site)

### Operational contour (`/admin`, `/app`)

| Component | Purpose | States |
|-----------|---------|--------|
| `EmptyState` | Empty list + CTA | icon, title, hint, action |
| `DataSkeleton` | Loading of tables/cards | shape mirrors the content |
| `ErrorState` / toast | Network error | retry |
| `Button` | primary/secondary/destructive | disabled @ pending |
| Drawer shell | Entity forms | loading, validation |
| `StatusBadge` | Entity status | colour by semantics |

### Public site (marketing)

| Component | Purpose | Stability |
|-----------|---------|-----------|
| `SectionHeader` | eyebrow + title + lead | fixed typography |
| `Card` / `DocCard` | benefit tiles, documents | equal-height §1 LAYOUT_INVARIANTS |
| `RevealSection` | section wrapper | **only** `prism-reveal--fade` (opacity) |
| `HeroFullBleed` | hero + carousel + course strip | `prism-motion-island` §11 |
| `LeadForm` | lead capture form | 4 states + disabled submit |
| `TrustMetric` | number + caption | `tabular-nums`, min-width |

---

## §4. MOTION COMPONENTS (mandatory wrapper)

> **Two kinds, and only one of them existed before v6.38.** A **containment** component keeps motion from
> touching layout or scroll — that is the six below. An **expression** component *is* the motion: a staggered
> list, a word reveal, a sequenced entrance. A registry holding only containment devices makes stiffness the
> only registered option, and Law 25 step (4) — "a screen is assembled only from registered components" —
> then forbids everything else as an unregistered pattern. That is how a motion library of a thousand lines
> ended up unreachable.
> **Both kinds are registered.** The expression set derives from `roles/MOTION_CRAFT_CANON.md` §1–§2 and is
> entered in the project passport §3 like any other component: **`Stagger`** (a group with an order and an
> offset — G1/G2) · **`Reveal`** (opacity + transform from a named origin — G4) · **`Sequence`** (an ordered
> multi-element entrance with overlap — G3) · **`StateTransition`** (the same node changing, not destroyed
> and recreated — G5 CHANGE) · **`Confirm`** (the one spring moment per screen — G5 CONFIRM).
> A project may name them differently. What it may not do is have none of them.

Any block with autoplay, swipe, or crossfade — **not a section with classes**, but a component with a contract:

| Pattern | Class / hook | Where |
|---------|-------------|-------|
| Motion island | `.prism-motion-island` | hero root, carousel |
| Horizontal strip | `.prism-scroll-x` (+ `--snap` mobile only) | course cards, tabs |
| Scroll reveal | `useRevealOnScroll` + `prism-reveal--fade` | `RevealOnScroll` provider |
| Autoplay in viewport | `useInViewAutoplay` | carousel |
| No focus-scroll | `onMouseDownPreventFocus` | carousel buttons, dots, strip cards |
| Guarded strip scroll | `scrollCourseStripToIndex` | only with overflow-x |

@DESIGN in the SPEC's **Motion** section must specify: island yes/no, fixed heights (numbers), autoplay (yes/no + pause offscreen).

---

## §5. MAPPING FORMAT IN DESIGN_SPEC

Every `DESIGN_SPEC_*.md` includes a table:

```markdown
## Component Map
| Screen zone | Component | Layer | New? | Stability |
|-------------|-----------|-------|------|-----------|
| Hero carousel | HeroFullBleed | T3 | No | island, h=360px, opacity crossfade |
| Benefits grid | Card ×4 in RevealSection | T1+T3 | No | equal-height, line-clamp 2 |
| CTA form | LeadForm | T1 | No | — |
```

The **Stability** column references items from `LAYOUT_INVARIANTS` (§1, §2, §11…).

---

## §6. PROJECT REGISTRY (example of a filled registry)

The live registry is maintained in **`docs/artifacts/FRONTEND_PASSPORT_[PROJECT].md` §3** (@FRONTEND updates it after every UI wave).

| Component | Path | Status |
|-----------|------|--------|
| HeroFullBleed | `frontend/src/components/marketing/HeroFullBleed.tsx` | ✅ hero canon |
| HeroNavigator | `frontend/src/components/marketing/HeroNavigator.tsx` | ✅ |
| RevealSection | `frontend/src/components/marketing/shared.tsx` | ✅ |
| Card | `frontend/src/components/ui/Card.tsx` | ✅ |
| SectionHeader | `frontend/src/components/ui/SectionHeader.tsx` | ✅ |
| LeadForm | `frontend/src/components/forms/LeadForm.tsx` | ✅ |
| PageBlocks | `frontend/src/components/blocks/PageBlocks.tsx` | ✅ CMS composer |
| HomeMarketing | `frontend/src/components/marketing/HomeMarketing.tsx` | ✅ main page fallback |

**Motion theme (code):** `frontend/src/theme/motion-islands.css`, `motion.css`, `frontend/src/lib/motion/*`, `frontend/src/hooks/useInViewAutoplay.ts`, `useRevealOnScroll.ts`.

---

## §7. CHECKLIST BEFORE THE FIRST SCREEN (@FRONTEND)

```
□ T1 primitives from §3 exist or are registered in the project passport
□ Reveal / motion in flow — `transform`+`opacity` in the element's own reserved box (layout properties never, `scrollY` never) (§10–§11); at least one Stagger, Reveal or Sequence expression component registered (§4)
□ Hero/carousel — a separate motion component, not inlined in the page
□ FRONTEND_PASSPORT §3.1 filled with current paths
□ DESIGN_SPEC contains a Component Map for the new screen
```

---

Reference: `roles/LAYOUT_INVARIANTS.md` · `roles/FRONTEND_DESIGN_EXCELLENCE.md` · `roles/TEMPLATE_PROJECT_FRONTEND_PASSPORT.md` §3 · `roles/ROLE_DESIGN.md` · `roles/ROLE_FRONTEND.md` · `roles/ROLE_QA_ARCH.md` (Vector 6.1)
Version: 1.0 | 2026-06-30
