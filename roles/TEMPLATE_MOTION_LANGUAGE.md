# TEMPLATE_MOTION_LANGUAGE

> Универсальный шаблон motion language для проекта.

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
export const motion = {
  durationInstant: 80,
  durationFast: 120,
  durationNormal: 180,
  durationSoft: 240,
  durationSlow: 320,

  easing: 'cubic-bezier(0.2, 0, 0, 1)',
  easingSoft: 'cubic-bezier(0.16, 1, 0.3, 1)',
  easingLinear: 'linear',

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

| ID | Moment | Motion | Duration | Reduced motion |
|----|--------|--------|----------|----------------|
| SD-01 | status appears | opacity + tiny translate | 120ms | instant |
| SD-02 | category changes | dot scale + label crossfade | 180ms | switch only |
| SD-03 | active progress | soft breath | 1800–2400ms loop | static |
| SD-04 | running icon | slow spin | 1400ms+ | static |
| SD-05 | waiting enters | one soft bloom | 240ms | static |
| SD-06 | loader to check | crossfade + tiny scale | 180ms | final icon |
| SD-07 | error appears | opacity/color only | 120ms | static |

Rules:

- Success/error/neutral steady states do not loop.
- Progress loops only while work is live.
- Reserve width for labels that can change.
- Max two looping indicators in a visible zone.

---

## 4. General Patterns

| ID | Where | Motion |
|----|-------|--------|
| M1 | Buttons | hover color/opacity; optional press translate 1px |
| M2 | Navigation | active surface/color change |
| M3 | Modal | fade + tiny scale |
| M4 | Toast | small slide/fade |
| M5 | Drawer | fade + tiny translate/scale |
| M6 | Card hover | color/shadow only |
| M7 | Page load | skeleton to content crossfade |
| M8 | KPI | one-shot count-up if useful |

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
