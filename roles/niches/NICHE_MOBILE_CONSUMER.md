# NICHE_MOBILE_CONSUMER

Niche pack for mobile applications (Google Play / Apple App Store).

## Where to apply

- B2C mobile-first products.
- Super-app companion for an existing web/backend.
- Projects with hard App Store / Google Play requirements.

## Priorities

- Onboarding and fast first success for the user.
- Resilience on weak networks and offline scenarios.
- Crash/ANR discipline and stable releases.
- Compliance with store policy and data privacy.

## Micro-invariants (mandatory)

- Every screen has 4 states: Loading / Empty / Error / Success.
- Any mutation blocks repeat submit until the request completes.
- API errors on the client are surfaced in a single `detail + code` format.
- Critical actions have an explicit fallback under network degradation.
- Push and background sync must not break local data integrity.
- Idempotency of sync events is mandatory.

## Mandatory sections in the architecture

- Version management and release channels (alpha/beta/prod).
- Push notifications and permissions.
- Offline-first / synchronization.
- Telemetry (crash, startup time, retention events).

## Mandatory sections in DEV_PROMPTS

- Non-functional constraints for mobile UX (latency, crash-budget).
- Network scenario matrix (online/intermittent/offline).
- Test plan for the upgrade path (app update without state loss).
- Store compliance checklist before release.

## Mandatory templates

- `roles/TESTING_CANON.md`
- `roles/METRICS_PROTOCOL.md`
- `roles/DOCKER_INFRA_PASSPORT.md` (backend/mobile API contour)
- `roles/TEMPLATE_DESIGN_UX.md` (if there are storefront screens)

## Niche-level metrics (minimum)

- Crash-free users (%).
- ANR rate (%).
- Cold start p50/p95.
- Onboarding completion.
- Day-1 / Day-7 retention.
- Sync failure rate.

## Store readiness checklist

- Signing and bundle IDs configured.
- Privacy policy and data usage documented.
- Crash rate, ANR and startup budget defined.
- Rollback/hotfix plan locked in.

## Risks and anti-patterns

- Shipping without a canary group and crash monitoring.
- Offline cache without an invalidation strategy.
- Push logic without event deduplication.
- Logging personal data into telemetry.

## Definition of Done (niche)

- Functional smoke passed on iOS and Android.
- Offline -> online sync correctness confirmed.
- Store checklist closed with no critical blockers.
- Release quality metrics registered in `METRICS_REGISTRY`.

## Handoff to @LEAD (template)

```
HANDOFF @[ROLE] → @LEAD

Niche: MOBILE_CONSUMER
Done: [briefly]
Store readiness: [ready / not ready + blockers]
Crash/ANR budget: [actual]
Next step: @[ROLE] → [task]
```

## COMMAND CENTER (ready-made template)

```
***
COMMAND CENTER:
> Phase: [Start / Architecture / Development / QA_ARCH / Release]
> Niche: MOBILE_CONSUMER
> Done: [briefly: what is ready on iOS/Android]
> Store readiness: [ready / blocker]
> Crash/ANR: [current value vs budget]
> Next step: @[ROLE] → [specific task]
> Prompt to copy: [ready prompt or "not needed"]
***
```
