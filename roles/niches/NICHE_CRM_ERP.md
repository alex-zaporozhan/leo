# NICHE_CRM_ERP

Niche pack for CRM/ERP and business operations systems.

## Where to apply

- CRM, ERP, accounting and operations platforms.
- B2B systems with roles, permissions and audit trail.
- Products with financial and document contours.

## Priorities

- Processes, roles and access rights.
- Stability of reference data and records.
- Financial operations and auditability.
- Reporting and metrics.

## Micro-invariants (mandatory)

- The UI never shows UUIDs in user-facing fields.
- Tables and lists have an EmptyState + CTA.
- Destructive actions always go through a confirm dialog.
- After POST/PUT/DELETE the data is invalidated and refetched.
- Financial operations are idempotent and traceable.
- API errors follow a single contract (`detail`, `code`).

## Critical business contours

- Master data (customers, goods, services, employees).
- Operational cycle (task/deal/order/fulfillment).
- Financial cycle (charge/write-off/refund/adjustment).
- Reporting and aggregate reconciliation.

## Mandatory templates

- `roles/TEMPLATE_MODULE_DEV.md`
- `roles/DOMAIN_STANDARDS.md`
- `roles/TEMPLATE_ADMIN_UI_UX.md`
- `roles/METRICS_PROTOCOL.md`

## Mandatory sections in DEV_PROMPTS

- Domain Checklist per page type.
- Error contract and the list of codes for the module.
- Test set for the business invariants.
- Refresh and list synchronization checklist.

## Niche-level metrics (minimum)

- Time to close the key process (lead/order/task cycle time).
- Business validation errors per module.
- Aggregate mismatches (raw vs report).
- Financial transaction success rate.

## Critical checks

- No UUIDs in the UI.
- Mutations do not break data integrity.
- Empty/Error/Loading/Success are present.
- Data is refreshed after mutations.

## Risks and anti-patterns

- Money operations without idempotency.
- Reports with no control over source and period.
- The form saves the data, but the UI does not reflect the new state.
- Bulk operations without an audit trail.

## Definition of Done (niche)

- The main business route runs end-to-end with no manual fixes.
- Reported values are consistent with the source data.
- QA_ARCH records no red defects on state/data/UX.
- Financial/critical mutations have confirmed traceability.

## COMMAND CENTER (ready-made template)

```
***
COMMAND CENTER:
> Phase: [Start / Cartography / Architecture / Development / QA_ARCH]
> Niche: CRM_ERP
> Done: [what is closed on the business routes]
> Data integrity: [ok / risk]
> Finance contour: [ok / risk]
> Next step: @[ROLE] → [specific task]
> Prompt to copy: [ready prompt or "not needed"]
***
```
