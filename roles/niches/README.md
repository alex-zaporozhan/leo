# NICHES README

Catalog of niche packs that are plugged in at project start.

## Available packs

- `NICHE_CRM_ERP.md`
- `NICHE_MOBILE_CONSUMER.md`
- `NICHE_MARKETPLACE.md`
- `NICHE_CONTENT_SOCIAL.md`
- `NICHE_AI_ASSISTANT.md`

## How to use

1. @LEAD selects the pack per `roles/NICHE_BOOTSTRAP_PROTOCOL.md`.
2. `roles/TEMPLATE_PROJECT_PROFILE.md` is filled in.
3. The profile is saved to `docs/artifacts/PROJECT_PROFILE.md`.
4. The active pack is recorded in `docs/knowledge/DEVELOPMENT_PLAN.md` (or `docs/[project]/DEVELOPMENT_PLAN.md`).
5. The invariants and metrics of the selected niche are added to the first `DEV_PROMPTS`.

## Pack structure rule

Every `NICHE_*` file must contain:

- Where to apply
- Micro-invariants
- Mandatory templates
- Mandatory DEV_PROMPTS sections
- Niche metrics
- Risks and anti-patterns
- Definition of Done
- Ready-made COMMAND CENTER template for @LEAD
