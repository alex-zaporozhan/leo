# DESIGN_DECISION_LIBRARY

> Universal decision library for @CREATOR, @DESIGN and @FRONTEND.  
> Used as a source of patterns when creating project design passports.

> **[v6.20] The semantic layer above this file — `roles/CONCEPT_DNA_LIBRARY.md`:** the project world sets VALUES (hex palette, font pairs, effect kit, motion personality). What remains here are OPERATIONAL models (navigation, card/button models, motion profiles per contour, color balance). Palette/Typography Directions below are legacy hints for the operational contour; for the showcase the world sets values (Law 28).

---

## 1. Palette Directions

Choose based on product meaning, not author preference.

| Direction | Feeling | Good For | Avoid |
|-----------|---------|----------|-------|
| Warm Operations | calm, human, trustworthy | education, service ops, healthcare ops, HR | high-frequency trading, cyber tools |
| Crisp Ledger | precise, financial, serious | billing, accounting, inventory, compliance | playful consumer apps |
| Technical Calm | thoughtful, constructive, technical | builders, workflow editors, expert learning systems | luxury brands |
| Quiet Growth | optimistic, organic, non-salesy | coaching, wellness, learning, CRM for SMB | hard enterprise admin |
| Executive Slate | premium, restrained, strategic | analytics, CEO dashboards, board tools | kids/creator tools |
| Field Utility | practical, durable, outdoor/industrial | logistics, maintenance, construction | editorial/marketing-heavy products |
| Editorial Studio | curated, expressive, intelligent | knowledge tools, publishing, creative ops | dense transactional ops |
| Civic Trust | neutral, accessible, public-service | govtech, legal, public portals | playful SaaS |

### Palette Recipes

| Direction | Primary | Background | Surface | Accent | Notes |
|-----------|---------|------------|---------|--------|-------|
| Warm Operations | deep teal/green | warm ivory | white/soft cream | amber/plum sparingly | comfort + credibility |
| Crisp Ledger | navy/slate | cool gray | white | blue/green semantic | high contrast for data |
| Technical Calm | teal/blue | warm slate/ivory | white/soft | blue-iris for builder | calm technical workspace |
| Quiet Growth | sage/teal | cream | warm white | peach/amber | soft, human |
| Executive Slate | charcoal/plum | light slate | white | muted violet/blue | premium without noise |
| Field Utility | olive/steel | sand/gray | white | orange warning | rugged clarity |
| Editorial Studio | ink/indigo | paper | white | one vivid editorial accent | typography leads |
| Civic Trust | blue/teal | neutral light | white | status only | accessibility first |

---

## 2. Typography Directions

| Direction | Primary Style | Best For | Notes |
|-----------|---------------|----------|-------|
| Humanist Sans | Source Sans 3, IBM Plex Sans, Noto Sans | learning, ops, long reading | warm and readable |
| Crisp Tech Sans | Geist, IBM Plex Sans, Inter fallback | builders, developer tools | precise, can feel cold |
| Editorial Sans | Instrument Sans, Source Sans 3 | knowledge/editorial products | good for content hierarchy |
| Financial Sans | IBM Plex Sans, Noto Sans | billing/compliance | stable and serious |
| Friendly Sans | Nunito Sans, Atkinson Hyperlegible | care, health, accessible products | use carefully in B2B |
| Mono Accent | JetBrains Mono, IBM Plex Mono | ids/timers/schema/meta | never overuse in normal copy |

Rules:

- Default body font must support the product's main language.
- Do not use over-stylized display fonts in dense operational UI.
- Typography must have a commercial posture: serious buyer, learner, operator, creator, or public user.

---

## 3. Navigation Models

| Model | Best For | Structure |
|-------|----------|-----------|
| Grouped Sidebar | admin/workflow products | grouped journey: workspace / create / operate / manage |
| Top Pill Nav | learner/customer portals | small number of future routes, calm orientation |
| Split Sidebar + Inspector | builders/editors | nav left, editor center, inspector right |
| Command-First | power tools | sidebar + Cmd/K, not command-only |
| Hub Cards | low-frequency portals | cards grouped by job-to-be-done |
| Bottom Mobile Nav | mobile-first app | 3-5 high-frequency destinations |

Rules:

- Settings never automatically comes second.
- Menu order follows product journey.
- Disabled future items need explanation.
- Three-dot overflow is for secondary actions, not primary product areas.

---

## 4. Card Models

| Model | Use | Visual |
|-------|-----|--------|
| Paper Panel | default operational section | white surface + subtle border |
| Quiet Reading Card | learner/help/content | soft surface + generous line-height |
| Metric Tile | dashboard KPI | fixed height, tabular number, small label |
| Action Card | entry point | one title, one description, one CTA |
| Status Callout | state explanation | semantic tint + dot/label |
| Canvas Node | builder graph | compact label/type/status dot |
| Detail Drawer Panel | secondary data | surface + hierarchy, not heavy shadow |

Rules:

- Cards need a role. If a card has no role, remove it.
- Equal-level cards align height.
- Do not solve hierarchy with random colors.

---

## 5. Button Models

| Model | Use |
|-------|-----|
| Primary CTA | one main forward action |
| Secondary CTA | alternative safe action |
| Ghost Row Action | table/list action |
| Icon Tool Button | compact toolbar with tooltip |
| Destructive Confirm | delete/archive/revoke |
| Split Action | rare, only if primary + dropdown share one object |

Rules:

- One primary per region.
- Destructive action requires confirm.
- Disabled action explains why if user can fix it.

---

## 6. Motion Profiles

| Profile | Good For | Motion |
|---------|----------|--------|
| Static Premium | finance/compliance/executive | hover/focus only |
| Quiet Operational | admin/workflow/learning | status breath, drawer/modal, tiny transitions |
| Guided Learning | learner/onboarding | progress fill, gentle reveals |
| Builder Micro | graph/canvas/tools | selection rings, drag ghost, target color |
| Expressive Marketing | landing/portfolio | controlled choreography |

---

## 7. Selection Protocol

When creating passports, choose one row from each:

```text
Palette direction:
Typography direction:
Navigation model:
Card model baseline:
Button model baseline:
Motion profile:
```

Then validate coherence:

- Warm palette + harsh technical mono everywhere = mismatch.
- Executive slate + playful rounded bubbles = mismatch.
- Dense admin + expressive motion = mismatch.
- Learner portal + raw table-first layout = mismatch.

If mismatch is intentional, document why.
