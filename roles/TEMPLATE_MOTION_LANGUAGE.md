# TEMPLATE_MOTION_LANGUAGE

> The universal motion-language template for a project.

---

# Motion Language — [Project Name]

**Motion mode:** [operational micro / expressive marketing / hybrid]  
**Implementation file:** `[project motion token file]`  
**Reduced motion:** all non-essential transitions/animations become static.

---

## 1. Motion Thesis

**[One-sentence motion thesis.]**

The interface moves only when:

1. [state actually changes];
2. [work is currently happening];
3. [user needs spatial orientation].

Everything else is static.

| Principle | Rule |
|-----------|------|
| Semantic motion | [what may move] |
| No layout shift | opacity/transform/color/shadow only |
| Small amplitude | [scale/translate limits] |
| Reduced motion | [mapping] |
| Loop budget | [max visible loops] |

---

## 2. Tokens

```typescript
// The four-step scale and the easing set come from MOTION_CRAFT_CANON §1 (THE MOTION FLOOR).
// Override the VALUES from the project's world (CONCEPT_DNA_LIBRARY) if it declares one — never the
// number of steps, and never the roles. A fifth duration means the moment was mis-classified.
export const motion = {
  motionInstant: 100,   // state feedback the finger must feel — press, toggle
  motionQuick:   180,   // hover, focus ring, tooltip, icon state
  motionBase:    280,   // THE DEFAULT — enter, exit, expand, tab change, drawer
  motionSlow:    480,   // a large surface moving — modal, page transition, hero

  easeEnter:  'cubic-bezier(0.0, 0.0, 0.2, 1)',   // arriving — THE DEFAULT
  easeExit:   'cubic-bezier(0.4, 0.0, 1, 1)',     // leaving
  easeMove:   'cubic-bezier(0.4, 0.0, 0.2, 1)',   // travelling between two on-screen positions
  easeSpring: 'cubic-bezier(0.34, 1.56, 0.64, 1)',// ONE confirming moment per screen

  staggerTight: 30,     // dense rows, table lines, chips
  staggerBase:  60,     // cards, list items, form fields — THE DEFAULT
  staggerLoose: 90,     // large blocks, sections, hero lines
  // Cap total stagger at 400ms: stagger the first 8, the rest arrive together.

  statusChange: 180,
  statusBloom: 240,
  progressBreath: 2200,
  iconSpin: 1400,
  modal: 180,
  drawer: 200,
  toast: 180,
  skeletonCrossfade: 120,
  metricCountUp: 400,
};
```

---

## 3. Status Motion

| ID | Moment | Motion | Duration · easing | Origin · order · offset (`MOTION_CRAFT_CANON` §2) | Reduced motion |
|----|--------|--------|----------|----------------|----------------|
| SD-01 | status appears | opacity + `translateY 8px` | `--motion-quick` · `--ease-enter` | from the badge's own edge; single element | instant |
| SD-02 | category changes | dot scale + label crossfade | `--motion-quick` · `--ease-move` | the dot itself; single element · CHANGE | switch only |
| SD-03 | active progress | soft breath | 1800–2400ms loop · `--ease-move` | in place; a loop, not a transition | static |
| SD-04 | running icon | slow spin | 1400ms+ · linear (the one place linear is correct) | in place; a loop | static |
| SD-05 | waiting enters | one soft bloom | `--motion-base` · `--ease-enter` | from the badge · ARRIVE | static |
| SD-06 | loader to check | crossfade + tiny scale | 180ms | final icon |
| SD-07 | error appears | opacity/color only | 120ms | static |

Rules:

- Success/error/neutral steady states do not loop.
- Progress loops only while work is live.
- Reserve width for labels that can change.
- Max two looping indicators in a visible zone.

---

## 4. General Patterns

> **IDs are `PM-xx`, not `Mx`.** `M1–M12` is taken system-wide by the stiffness catalogue
> (`MOTION_CRAFT_CANON` §3) and a bare `M5` would mean two different things in two files a role reads
> together (`.cursorrules` — detector codes are always qualified).
> **Each row names its origin and its verb** (`MOTION_CRAFT_CANON` §2 G4/G5), because a row that names
> only a property is exactly the endpoint-only spec this column exists to prevent.

| ID | Where | Motion | Origin · verb |
|----|-------|--------|---------------|
| PM-01 | Buttons | hover colour/opacity; press `scale(0.98)` `--motion-instant` | from the point touched · CONFIRM-lite |
| PM-02 | Navigation | active surface/colour change, indicator slides between items | travels along the rail · CHANGE |
| PM-03 | Modal | fade + `scale(0.96→1)`, scrim first, then panel, then content | grows from screen centre · ARRIVE, staggered `--stagger-base` |
| PM-04 | Toast | slide + fade from the edge it docks to | its own edge · ARRIVE, exit at ~70% |
| PM-05 | Drawer | slide from the edge it lives on + fade | that edge, never the centre · ARRIVE |
| PM-06 | Card hover | colour/shadow, optional `translateY(-2px)` lift | in place · no verb (a state, not a transition) |
| PM-07 | Page load | skeleton shaped like the arriving layout → content crossfade, staggered | reading order · ARRIVE |
| PM-08 | KPI | one-shot count-up where the number is the point | in place · CONFIRM |
| PM-09 | Row removed | collapses into the gap it leaves, neighbours close after | the gap itself · LEAVE |

---

## 5. Zone Calibration

| Zone | Allowed | Forbidden |
|------|---------|-----------|
| Admin/Ops | live job status, drawer/modal, row hover | decorative loops |
| App/Learner | fade, progress fill, essential live dot | spinner-heavy states |
| Supervisor/Analytics | hover color, KPI count-up once | animated charts by default |
| Builder/Canvas | drag ghost, selected ring, target color | bounce, particles, magnetic gimmicks |
| Marketing | richer choreography if brand needs it | scroll-jacking, unreadable motion |

---

## 6. Forbidden Effects

- Bounce/elastic overshoot in operational UI.
- Shake for errors.
- Confetti/particles in serious workflows.
- Parallax/scroll-jacking in app/admin.
- Shimmer over readable text.
- Animated width/height/margin/padding.
- Flashing wait/error/success.
- Fast spinner under 1200ms.

---

## 7. Implementation Notes

1. Use CSS keyframes/classes first.
2. Add animation library only with license check and clear design reason.
3. Components own their motion; screens do not duplicate status animations.
4. `prefers-reduced-motion` is mandatory.

---

## 8. QA Checklist

- [ ] No layout animation in flow.
- [ ] Reduced motion path verified.
- [ ] Loop budget respected.
- [ ] Motion has semantic reason.
- [ ] Hover/focus/active states are visible and stable.
