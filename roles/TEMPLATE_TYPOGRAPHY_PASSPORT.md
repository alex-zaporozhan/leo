# TEMPLATE_TYPOGRAPHY_PASSPORT

> The project's typography passport. Fill once per project; the craft canon supplies the physics.

---

# Typography Passport — [Project Name]

**Scope:** all product zones with user-visible text.  
**Goal:** readable, characterful typography aligned with the product's audience and commercial posture.

---

## 1. Typographic Thesis

[One paragraph: what reading should feel like and why it suits the product.]

Example frame:

```text
Typography should feel like [metaphor/posture]:
easy to read for long sessions, fast to scan in tables, distinctive without becoming decorative.
```

---

## 2. License Gate

All recommended fonts must allow commercial use and web embedding.

| Font | Role | License | Decision |
|------|------|---------|----------|
| [Primary] | UI/body | [license] | [approved/deferred] |
| [Mono] | meta/code/timers | [license] | [approved/deferred] |
| [Fallback] | multilingual fallback | [license] | [approved/deferred] |

Rules:

- Prefer self-hosted WOFF2 in production.
- Include copyright/license text when bundling fonts.
- Do not use Reserved Font Names for modified derivatives.
- New font package/CDN requires license check.

---

## 3. Chosen Stack

### 3.1 Primary UI

```css
font-family:
  '[Primary Font]',
  '[Fallback Font]',
  system-ui,
  sans-serif;
```

Why:

- [reason 1]
- [reason 2]
- [reason 3]

### 3.2 Mono / Technical Meta

```css
font-family:
  '[Mono Font]',
  ui-monospace,
  SFMono-Regular,
  Menlo,
  Consolas,
  monospace;
```

Use only for:

- ids/version/timers;
- code-like schema names;
- support appendices;
- numeric technical meta.

Do not use mono for normal user copy unless the product concept explicitly requires it.

---

## 4. Type Scale

> Craft: `roles/VISUAL_CRAFT_CANON.md` §5. **Sizes are DERIVED, not chosen by eye.** Pick a ratio; every size is
> a step on it. `15px` here and `17px` there is the most common reason a screen reads as amateur even when
> everything else is right.

**Modular ratio:** `[1.200 minor third — dense/operational | 1.333 perfect fourth — editorial/showcase]`
**Sizes derived:** `[12 · 14 · 16 · 19 · 23 · 28]` — **5–6 sizes total.** A seventh means a hierarchy problem, not a type problem.
**Display/body contrast:** `[2.5–4× on a showcase · 1.6–2× operational]` — timidity here is why a hero looks like a blog.
**Weight hierarchy:** `400` body · `500` data/values · `600` titles · `700` **once** per view. If everything is 600, nothing is.

**Optical rules (the invisible 10%):**
```
Tracking:  display ≥28px → −1…−3%   ·   labels/caps → +2…+8%   ·   body → 0
Leading:   inverse to size — display 1.0–1.15 · lead 1.3 · body 1.5–1.6 · dense tables 1.4
Measure:   45–75 characters per line on reading surfaces
Numerals:  font-variant-numeric: tabular-nums in ANY column of numbers. Always. No exceptions.
```


| Token | Size / line-height | Weight | Use |
|-------|--------------------|--------|-----|
| display.sm | [px/line] | [weight] | rare hero/empty title |
| page.title | [px/line] | [weight] | page title |
| section.title | [px/line] | [weight] | card/section title |
| body.md | [px/line] | [weight] | main UI text |
| body.compact | [px/line] | [weight] | dense rows |
| label.sm | [px/line] | [weight] | labels/meta |
| label.strong | [px/line] | [weight] | status/active labels |
| mono.sm | [px/line] | [weight] | ids/timers |

Rules:

- Required text uses readable foreground token.
- Meta/label tokens are not for primary instructions.
- Use tabular numbers for aligned metrics.
- Avoid wide letter-spacing for Cyrillic/body text.
- Set max line length for reading surfaces.

---

## 5. Zone Calibration

| Zone | Typography |
|------|------------|
| Admin/Ops | compact but breathable; dense tables still readable |
| Learner/Customer | larger line-height and calmer paragraph rhythm |
| Supervisor/Analytics | tabular KPI numbers and concise labels |
| Builder/Technical | compact body + mono only for schema/meta |
| Marketing | may use display font if brand posture needs it |

---

## 6. Reading Composition

Best composition:

- [background] outside;
- [surface] for reading/card area;
- one strong title;
- one readable paragraph;
- one visible action;
- status/progress outside paragraph flow.

Reject:

- low-contrast paragraph body;
- stacked muted paragraphs;
- long copy in dense table rhythm;
- decorative font for operational data.

---

## 7. QA Checklist

- [ ] Primary font loaded or explicitly deferred.
- [ ] Mono used only for technical/meta text.
- [ ] Numeric metrics use tabular numbers.
- [ ] Reading surfaces have comfortable line-height and width.
- [ ] Foreground/background pairs pass contrast rules.
- [ ] Font licenses are recorded.
