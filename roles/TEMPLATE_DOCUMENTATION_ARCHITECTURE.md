# TEMPLATE_DOCUMENTATION_ARCHITECTURE

Universal reference template for documentation architecture.

## Goal

Provide a minimal, stable documentation structure for any project without over-layering.

## Canonical Structure (Minimal)

1. `roles/` - global role system, process rules, reusable templates.
2. `docs/artifacts/` - active execution artifacts for current delivery cycle.
3. `docs/product_state/` - verified current product state from code and contracts.

## Optional Sections (Only When Needed)

- `docs/architecture/` - architecture references and scale envelopes.
- `docs/operations/` - runbooks and operating procedures.
- `docs/design/` - design references and UX mappings.
- `docs/archive/` - historical materials not used as active source of truth.

## Source-Of-Truth Priority

1. Code and tests.
2. Active contracts in `docs/artifacts/`.
3. Current state documents in `docs/product_state/`.
4. Reusable process rules in `roles/`.

## Placement Rules

- Reusable across projects -> `roles/`.
- Project-specific and current -> `docs/`.
- Temporary execution context -> `docs/artifacts/`.
- Historical context -> `docs/archive/`.

## LEAD Checklist

- Keep only one active contract per topic.
- Avoid creating new top-level folders without clear need.
- Move stale documents to archive instead of duplicating.
- Ensure `roles/SYSTEM_FILES_MASTER.md` and `docs/DOCUMENTATION_SYSTEM.md` remain aligned.
