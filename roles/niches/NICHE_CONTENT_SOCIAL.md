# NICHE_CONTENT_SOCIAL

Niche pack for content and social applications.

## Where to apply

- Social networks, media platforms, UGC products.
- Content feeds and community apps.
- Products with a high rate of posts/reactions.

## Priorities

- Feed and personalization.
- Moderation and content safety.
- Engagement and retention metrics.
- Read/write scalability.

## Micro-invariants (mandatory)

- Publishing and deleting content must stay consistent in the feed.
- Deleted/hidden content never comes back in recommendations.
- A content report is recorded with a traceable status.
- Privacy restrictions are enforced at every API level.
- The feed must not depend on unbounded full-scan queries.

## Critical domain contours

- Content lifecycle (draft -> published -> moderated -> removed).
- Engagement lifecycle (view -> react -> comment -> share).
- Moderation lifecycle (flag -> review -> decision -> appeal).

## Mandatory templates

- `roles/METRICS_PROTOCOL.md`
- `roles/ROLE_SEC.md`
- `roles/TESTING_CANON.md`
- `roles/TEMPLATE_DESIGN_UX.md`

## Mandatory sections in DEV_PROMPTS

- Moderation policy and decision statuses.
- Content privacy contract.
- Feed tests at large data volumes.
- Ranking degradation plan for external service failures.

## Niche-level metrics (minimum)

- Retention D1/D7/D30.
- Session duration and frequency.
- Reported content rate.
- Moderation SLA.
- Feed freshness latency.

## Critical checks

- Protection against spam and abuse.
- Content privacy and visibility control.
- Low latency in the key read scenarios.

## Risks and anti-patterns

- Manual-only moderation with no risk prioritization.
- Public content with no clear privacy model.
- Opaque "shadow" bans with no status for the user.
- Engagement metrics without safety metrics.

## Definition of Done (niche)

- The content and moderation contours are covered end-to-end.
- Privacy and visibility pass negative tests.
- Retention/safety metrics are set up and verified.
- Feed performance meets the target budgets.

## COMMAND CENTER (ready-made template)

```
***
COMMAND CENTER:
> Phase: [Start / Architecture / Development / QA_ARCH / Moderation]
> Niche: CONTENT_SOCIAL
> Done: [what is closed on the feed and moderation]
> Privacy/moderation: [ok / risk]
> Feed latency: [current value vs budget]
> Next step: @[ROLE] → [specific task]
> Prompt to copy: [ready prompt or "not needed"]
***
```
