# JENKINS_PIPELINE_PROTOCOL

Practical protocol for Jenkins in this project: stable, reproducible, no secrets in code.

---

## 1. Canon

- CI/CD orchestrator: **Jenkins**.
- Registry: **GHCR** (`ghcr.io`).
- Jenkinsfile in the repository — source of pipeline steps.
- Credentials only in Jenkins UI (`withCredentials`), not in Git.

---

## 2. Base stages (minimum)

1. `Checkout`
2. `Lint`
3. `Test`
4. `Build` (app artifacts)
5. `Docker Build`
6. `Push` (conditional on branch/policy)

Each stage logs `start/success/fail` to console with a clear name.

---

## 3. Variable centralisation

Use only env variables:

- `GHCR_NAMESPACE`
- `GHCR_CREDENTIALS_ID`
- `WORKSPACE_HOST_PATH`
- `BUILD_CONTEXT_PATH`
- `IMAGE_TAG`

Forbidden:
- hardcoded Windows/Linux paths in multiple places;
- token/password in Jenkinsfile;
- duplicate namespace/tag constants across stages.

---

## 4. Winner solution (pipeline strategy)

Options:

- A: build+push always
- B: lint/test → build, push only on gate/branch
- C: push without tests for speed

Winner: **B**.

Why:
- A wastes resources and pollutes the registry.
- C breaks quality and increases risk of release defects.
- B delivers predictable releases and controlled artifacts.

---

## 5. Security and audit

- All secrets via Credentials bindings.
- Do not print secret values in logs.
- Stage errors must terminate the pipeline as `failed`.
- For push: store trace — image name, tag, digest (if available).

---

## 6. Common errors and quick checks

1. **Path mapping Windows/WSL**
   - Check `WORKSPACE_HOST_PATH`, `BUILD_CONTEXT_PATH`.
2. **Permission denied GHCR**
   - Check `GHCR_NAMESPACE`, PAT scopes, credentials id.
3. **Old Jenkinsfile being used**
   - Job must reference `Pipeline script from SCM`.
4. **Random 500s in app smoke**
   - Verify `.env`, migrations, DB volume state.

---

## 7. Checklist before enabling push

- [ ] Lint and Test green.
- [ ] Docker Build green.
- [ ] Credentials id valid.
- [ ] Namespace and tag match release policy.
- [ ] No secret leakage in logs.
