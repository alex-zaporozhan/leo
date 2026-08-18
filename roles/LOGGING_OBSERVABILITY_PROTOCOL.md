# LOGGING_OBSERVABILITY_PROTOCOL

A unified protocol for **what to always log**, how to choose the format, and how to make the "winner decision" on the level of detail.

---

## 1. Goal

- Speed up incident diagnosis without guesswork.
- Preserve business context (who/what/where/result) without leaking PII.
- Make logs usable for QA, DEV, OPS, and audit.

---

## 2. Mandatory logging zones

Always logged (minimum `INFO` + `ERROR`):

1. **Auth and sessions**
   - login/register/refresh/logout/reset-password
   - rate-limit triggers
2. **Forms and mutations**
   - submit start/success/fail
   - idempotency conflict / duplicate key
3. **File manager / documents**
   - upload start/success/fail
   - presigned URL issue/use
4. **Financial operations**
   - create/update transaction, rollback, state transition
5. **Integrations**
   - external call start/status/error + latency
6. **Admin actions**
   - role change, critical settings, destructive operations

---

## 3. Typical log coverage (minimum contract)

Each entry contains:

- `ts` (UTC ISO)
- `level` (`INFO`/`WARN`/`ERROR`)
- `service` (`backend`/`frontend`/`jenkins`)
- `event` (semantic name, `snake_case`)
- `request_id` / `trace_id` (if available)
- `actor_id` (if available)
- `entity_type`, `entity_id` (if available)
- `tenant_id` (for multi-tenancy)
- `result` (`success`/`fail`)
- `code` (error per API contract)

Forbidden: tokens, passwords, raw secrets, full card/document numbers, PII in labels.

---

## 4. Log levels

- `INFO`: successful business actions and pipeline stages.
- `WARN`: recoverable anomalies (retry, fallback, 4xx within expected bounds).
- `ERROR`: exceptions, 5xx, inability to complete the scenario.

Rule: one business operation must have at least `start` and `result`.

---

## 5. Winner decision (how to choose depth)

If options exist:

- **A:** minimal logs for errors only
- **B:** structured start/success/fail with context
- **C:** extremely detailed debug of full payload

Default winner: **B**.

Rationale:
- A is insufficient for root-cause without reproduction.
- C is too noisy and risks data leakage.
- B gives the best balance: diagnosis, cost, security.

---

## 6. Log examples

Backend success:

```json
{"ts":"2026-04-24T10:12:21Z","level":"INFO","service":"backend","event":"auth_login","actor_id":"u_123","tenant_id":"t_01","result":"success"}
```

Backend error:

```json
{"ts":"2026-04-24T10:12:23Z","level":"ERROR","service":"backend","event":"auth_login","tenant_id":"t_01","result":"fail","code":"AUTH_INVALID_CREDENTIALS","request_id":"r-9f2"}
```

Jenkins stage:

```json
{"ts":"2026-04-24T10:18:01Z","level":"INFO","service":"jenkins","event":"pipeline_stage","stage":"docker_build","result":"success","build":"#182"}
```

---

## 7. Review checklist

- [ ] All mandatory zones (section 2) covered for the affected module.
- [ ] Logs are structured (key-value), not "wall of text".
- [ ] Correlation present (`request_id`/`trace_id`) for chained calls.
- [ ] No secrets or forbidden data.
- [ ] From the error it is possible to recover the cause without manual reproduction.

---

## 8. Where roles should reference this

- @DEV: apply to any backend/frontend mutation, auth, upload, integration.
- @QA_ARCH: verify presence and usefulness of logs in the report.
- @LEAD: require logging checklist as a gate.
