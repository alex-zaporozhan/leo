# NICHE_AI_ASSISTANT

Niche pack for AI-first applications and assistants.

## Where to apply

- AI copilot/assistant in web and mobile apps.
- Text/code/analytics generation tools.
- Products with RAG, tool-calling and agentic scenarios.

## Priorities

- Answer quality and context controllability.
- Inference cost and usage limits.
- Prompt and data security.
- Quality observability (hallucination, refusal, latency).

## Micro-invariants (mandatory)

- An answer with no source in the RAG contour is marked as uncertain.
- Requests carrying sensitive data pass through the policy filter.
- Tool-calling has an allowlist and input validation.
- Retry must not duplicate side effects.
- The user sees an explicit message when a fallback model is used.

## Critical domain contours

- Context lifecycle (ingest -> index -> retrieve -> respond).
- Guardrails lifecycle (detect -> block -> fallback -> log).
- Cost lifecycle (quota -> consume -> alert -> throttle).

## Mandatory templates

- `roles/RAG_CANON.md`
- `roles/METRICS_PROTOCOL.md`
- `roles/ROLE_SEC.md`
- `roles/TEMPLATE_DOCUMENTATION_ARCHITECTURE.md`

## Mandatory sections in DEV_PROMPTS

- Sources of truth and RAG context boundaries.
- Fallback policy (model/mode/message to the user).
- Tool execution limits and permission boundary.
- Tests for hallucination/unsafe output/latency budget.

## Niche-level metrics (minimum)

- Answer grounded rate.
- Hallucination report rate.
- Refusal correctness rate.
- p50/p95 latency per model.
- Cost per successful session.

## Critical checks

- A hard list of sources of truth for RAG.
- Access control for the data sent in model requests.
- Explicit fallback scenarios for LLM provider failures.

## Risks and anti-patterns

- Unrestricted RAG with no allowlist and no source priority.
- Tool execution without sandbox/permission checks.
- Cutting corners on safety filters to ship faster.
- No telemetry on answer quality.

## Definition of Done (niche)

- The RAG contour is verified and passes the anchor integrity check.
- Guardrails cover the critical unsafe scenarios.
- Cost/latency budgets are fixed and monitored.
- The user-facing fallback is clear and repeatable.

## COMMAND CENTER (ready-made template)

```
***
COMMAND CENTER:
> Phase: [Start / RAG / Architecture / Development / QA_ARCH]
> Niche: AI_ASSISTANT
> Done: [what is closed on answer quality]
> Grounding/safety: [ok / risk]
> Cost/latency: [current value vs budget]
> Next step: @[ROLE] → [specific task]
> Prompt to copy: [ready prompt or "not needed"]
***
```
