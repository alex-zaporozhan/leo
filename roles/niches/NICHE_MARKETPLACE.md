# NICHE_MARKETPLACE

Niche pack for marketplaces and two-sided platforms.

## Where to apply

- C2C/B2C/B2B marketplaces.
- "Supplier <-> buyer" platforms.
- Products with escrow, dispute, ratings and SLA.

## Priorities

- Balance of supply and demand.
- Safe transactions and disputes.
- Rating/reputation and anti-fraud.
- Retention of both sides of the platform.

## Micro-invariants (mandatory)

- Every payment/refund carries an idempotency key.
- Order status changes only through allowed transitions.
- A dispute/chargeback cannot sit in two final statuses at once.
- Commissions and payouts are reproducible from the event log.
- Cards/listings have predictable Empty/Error states.

## Critical domain contours

- Listing lifecycle (draft -> active -> paused -> archived).
- Order lifecycle (created -> paid -> fulfilled -> completed/cancelled).
- Settlement lifecycle (commission, payout, refund).
- Trust layer (rating, moderation, anti-fraud flags).

## Mandatory templates

- `roles/TEMPLATE_MODULE_DEV.md`
- `roles/METRICS_PROTOCOL.md`
- `roles/ROLE_SEC.md`
- `roles/ROLE_QA_ARCH.md`

## Mandatory sections in DEV_PROMPTS

- State machine for orders/payments/disputes.
- Error contract for status conflicts.
- Anti-fraud hypotheses and the minimal set of blocks.
- Tests for concurrency and repeated payment callbacks.

## Niche-level metrics (minimum)

- Conversion: listing -> order -> payment -> completion.
- Refund/dispute rate.
- Fraud alert rate and false positive rate.
- Dispute resolution time.

## Critical checks

- Idempotency of money operations.
- Order and dispute statuses are consistent.
- Funnel observability (listing -> order -> payment -> completion).

## Risks and anti-patterns

- Commission logic living in code with no explicit contract description.
- No retry-safe mechanics in the payment webhooks.
- Mixing user-facing and financial statuses.
- Rating transparency without anti-manipulation measures.

## Definition of Done (niche)

- The payment and dispute contours pass the success/fail/retry scenarios.
- All key statuses are covered by transition tests.
- Funnel and risk metrics are fixed and observable.
- SEC and QA_ARCH confirm there are no critical blockers.

## COMMAND CENTER (ready-made template)

```
***
COMMAND CENTER:
> Phase: [Start / Architecture / Development / QA_ARCH / Security]
> Niche: MARKETPLACE
> Done: [what is closed on the funnel and payments]
> Payment/dispute: [ok / risk]
> Anti-fraud: [ok / risk]
> Next step: @[ROLE] → [specific task]
> Prompt to copy: [ready prompt or "not needed"]
***
```
