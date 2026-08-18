# TEMPLATE_PROJECT_PROFILE

Project profile template for launching in the target niche.

## 1. Base profile

- Project name:
- Niche:
- Product type: (B2B / B2C / B2B2C / Internal)
- Target platform: (Web / Mobile / API / Multi-platform)
- Market: (RU / CIS / Global)
- Main value scenario (1 sentence):

## 2. Operational profile

- Critical user scenario:
- Constraints: (deadline, budget, stack, compliance)
- Expected scale: (users, RPS, data)
- Critical integrations:
- Error model (unified contract confirmed?): [yes/no]
- Async risks: [where races and repeated events are possible]

## 3. Niche package

- Selected package: `roles/niches/[NICHE_*.md]`
- Required templates from package:
- Roles with elevated priority:
- Niche metrics (minimum 5):
- Key niche risks and owner:

## 4. Artifacts that must be created immediately

- `docs/artifacts/BUSINESS_LOGIC.md`
- `docs/artifacts/BUSINESS_ROUTES.md`
- `docs/artifacts/SAAS_ARCHITECTURE_SPINE_2026.md`
- `docs/knowledge/DEVELOPMENT_PLAN.md` (or `docs/[project]/DEVELOPMENT_PLAN.md` in a multi-product repo)
- `docs/artifacts/PROJECT_PROFILE.md` (copy of filled profile)

## 5. Launch micro-checklist (mandatory)

```
□ 4 UI states defined for the key screen (Loading/Empty/Error/Success)
□ Critical mutations have disabled + retry/fallback strategy
□ Destructive actions have confirm
□ For money/statuses an idempotency approach is defined
□ Active niche package recorded in DEVELOPMENT_PLAN
```
