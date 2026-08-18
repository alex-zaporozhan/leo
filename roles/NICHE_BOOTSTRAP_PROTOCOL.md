# NICHE_BOOTSTRAP_PROTOCOL

Protocol for selecting a niche package at project start.

## Trigger

When the user says: "we are building a project in the niche of ...".

## @LEAD Actions

1. Fill out `roles/TEMPLATE_PROJECT_PROFILE.md`.
2. Select a niche package from `roles/niches/`.
3. Create `docs/artifacts/PROJECT_PROFILE.md` based on the completed template.
4. Explicitly state in role handoffs:
   - which niche package is active;
   - which templates are mandatory;
   - which acceptance criteria are included.
5. Record in `docs/[project]/DEVELOPMENT_PLAN.md` (the current project's folder — not a hard-coded product) the active package and activation date.

## Package Selection Map

- CRM/ERP, operational B2B → `NICHE_CRM_ERP.md`
- Mobile B2C (App Store/Google Play) → `NICHE_MOBILE_CONSUMER.md`
- Marketplace → `NICHE_MARKETPLACE.md`
- Content/Social → `NICHE_CONTENT_SOCIAL.md`
- AI assistant/copilot → `NICHE_AI_ASSISTANT.md`

## Result

The team immediately works in the context of the right niche, without mixing in unnecessary templates.

## Niche Launch Quality Gate

```
□ Project profile is fully filled (no empty critical fields)
□ Exactly one primary niche package is selected
□ Mandatory templates added to the first wave DEV_PROMPTS
□ Niche metrics added to plan and registry
□ Niche risks recorded and an owner assigned for monitoring
```

## @LEAD → @ARCH/@DEV Handoff

```
HANDOFF @LEAD → @[ROLE]

Niche: [package name]
Profile: docs/artifacts/PROJECT_PROFILE.md
Mandatory templates: [list]
Critical invariants: [3-5 items]
Acceptance criterion: [what must be proven in QA_ARCH]
```
