# Docker and Deploy Passport

Single source of conventions for consistent work with images, CI, and startup.
Supplemented by:
- env centralisation: `roles/ENV_COMPOSE_CENTRALIZATION.md`
- Jenkins protocol: `roles/JENKINS_PIPELINE_PROTOCOL.md`
- logging protocol: `roles/LOGGING_OBSERVABILITY_PROTOCOL.md`

---

## 1. Goal and boundaries

- **Prod/staging with registry:** **backend** and **frontend** images are built in **Jenkins** (or equivalent CI), published to **GHCR**; on the target host — `pull` + `up`, no mandatory "prod build from scratch" on the server.
- **Quality before push:** pipeline includes lint/test (see `Jenkinsfile`) before image build stages; publish only after green gate (branch policy/conditions — in job).
- **Secrets:** not in Dockerfile, not in Git; **Jenkins Credentials** (GHCR) and **`.env` on the machine** (root `.env` is not committed). See `documentation/LOCAL_CICD_SETUP.md` and `documentation/CICD_JENKINS_S3_RUNBOOK.md`.
- **Variables:** one layer of agreed values — see `roles/ENV_COMPOSE_CENTRALIZATION.md` (root `.env`, substitutions in `docker-compose.yml`, frontend build-args).

---

## 2. Repository artifacts

| What | Path |
|-----|------|
| Backend (FastAPI) | `backend/Dockerfile` (build context — **repository root**, see `docker-compose.yml`) |
| Frontend (Vite → nginx) | `frontend/Dockerfile` |
| Jenkins agent with Docker CLI (optional) | `deploy/jenkins/Dockerfile` |
| Local / demo stack | `docker-compose.yml` |
| CI/CD: Jenkins | `Jenkinsfile` |

**Services in `docker-compose.yml` (typical set):** `postgres`, `minio`, `backend`, `frontend`, `jenkins`. Base images `postgres:16-alpine`, `minio/minio` — external; pin tags per versioning policy.

---

## 3. Images in GHCR

Names from `Jenkinsfile` (example):  
`ghcr.io/<GHCR_NAMESPACE>/backend:<tag>`, `ghcr.io/<GHCR_NAMESPACE>/frontend:<tag>`.

- For prod deploy prefer **immutable** tag (build number, git sha) and optionally **digest**.
- `latest` — optional, **not** the only release tag.

`GHCR_NAMESPACE` to be agreed with org/user on GitHub; token — scopes for packages, in Credentials, not in the repository `.env` (see runbook).

---

## 4. CI/CD: order and conditional push

1. **Checkout** (SCM; may be skipped in local inline job).
2. **Lint / Test** — via `docker run` with mounting `frontend` / `backend` (see `Jenkinsfile`).
3. **Build** (if needed) — frontend `npm run build`, etc.
4. **Docker build** — context `BUILD_CONTEXT_PATH` (in Docker Desktop often `/workspace` with bind mount).
5. **Push to GHCR** — `withCredentials`, only when branch/condition matches (as configured in job).

Variables: `WORKSPACE_HOST_PATH`, `BUILD_CONTEXT_PATH`, `GHCR_NAMESPACE`, `GHCR_CREDENTIALS_ID` — in job env or in agent machine `.env`, **without** secrets in the repository.

Stage-by-stage details, winner strategy, anti-patterns: `roles/JENKINS_PIPELINE_PROTOCOL.md`.

---

## 5. Local development (Compose)

- **Variables:** root **`.env`** (loaded via `docker compose --env-file .env`); template — **`.env.example`**. Separation of **host ports** and **internal ports** — keys `*_PORT` / `*_INTERNAL_PORT` (see `ENV_COMPOSE_CENTRALIZATION.md`).
- **Frontend in browser:** `http://localhost:${FRONTEND_PORT}`; API: `http://localhost:${BACKEND_PORT}`. Frontend built into image: `VITE_API_BASE_URL=http://localhost:${BACKEND_PORT}` via compose build-args — **not** hardcoded `8000` if `BACKEND_PORT` has changed.
- **MinIO / S3:** inside network `http://minio:${MINIO_INTERNAL_PORT}`; from host — `http://localhost:${MINIO_PORT}`. See `CICD_JENKINS_S3_RUNBOOK.md`.
- **DB migrations:** `docker compose exec backend alembic upgrade head` (or team's current practice scenario).

**What to rebuild on changes**

| Changes | Action |
|-----------|----------|
| `frontend/**` (UI code) | `docker compose build frontend` (if `VITE_*` changed — rebuild required) |
| `backend/**`, dependencies, Dockerfile | `docker compose build backend` |
| Only `.env` (ports) | Usually `up -d` + rebuild frontend if `BACKEND_PORT` / build-arg changed |
| First run / lockfile change | `build --no-cache` when in doubt about cache |

Then: `docker compose up -d`.

---

## 6. Slow network / restricted environments: mirrors and buildx

- **Registry mirrors** (Docker Desktop / `daemon.json`) — per environment policy; on mirror failures temporarily remove `registry-mirrors` and restart Docker.
- **Provenance / attestations:** on hangs at "resolving provenance" — env variable `BUILDX_NO_DEFAULT_ATTESTATIONS=1` or buildx configuration (relevant for your version).
- **Base images:** where possible pin tags (`python:3.11-slim`, `node:20-alpine`); for reproducibility — digest as a separate task (see `roles/DOCKER_INFRA_PASSPORT.md`).

### 6.1 Proxy / mirror error (type `127.0.0.1:12334`, hosting providers, etc.)

Messages about proxy unavailability during `docker pull` / `build` often mean: Docker Desktop has a **manual proxy** enabled or a **mirror** pointing to an unreachable host. Steps: Settings → Proxies (disable or fix), restart Docker; check `%USERPROFILE%\.docker\daemon.json` for `registry-mirrors`. Details — in team historical runbooks; principle: **transparent** internet access for the daemon or a correct proxy.

---

## 7. VPS / server (normal deploy)

1. One-time: `docker login ghcr.io` (PAT/robot with packages read scope).
2. Update compose and env on server: `git pull` (no secrets committed).
3. `docker compose pull` — images from GHCR.
4. `docker compose up -d` with the required `IMAGE_TAG` / digest if `image:` is extracted in compose.
5. Verify: `docker ps`, backend health, frontend on published port, migrations on schema change.

Prod deploy does **not** rely on `docker compose build` on the server as a mandatory step (exception — agreed debugging).

---

## 8. Rules when changing infrastructure

- Do not change the `ghcr.io/.../namespace/...` schema and image repository names without agreement and updates to `Jenkinsfile` / job.
- Dockerfile: multi-stage, dependencies in a separate layer from code, no secrets in layers.
- Any new required env — add to **`.env.example`**, compose, or app config — per `ENV_COMPOSE_CENTRALIZATION.md` rules.
- Uncertainty about tags, new service, secrets — briefly in task/PR and agree with release owner.

---

## 9. Maturity (optional)

**Recommended in the future:** image scanning (Trivy), SBOM, image signing, separate e2e job — as explicit tasks, do not mix with the mandatory minimum contract without a decision.

**Already in the repository:** `requirements-ci.txt`, `npm ci` in pipeline, dependency healthchecks in compose, unified env model.

---

## 10. Minimum checklist before merging infrastructure changes

- [ ] Images build locally / in CI without secrets leaking to logs.
- [ ] `.env.example` and `docker-compose.yml` comments (if needed) match the actual state.
- [ ] Centralisation: no duplicate ports/URLs that should come from a single key.
- [ ] Documentation: `documentation/LOCAL_CICD_SETUP.md` updated if startup contour changed.
- [ ] For new/changed business operations, logging is confirmed per `roles/LOGGING_OBSERVABILITY_PROTOCOL.md`.

---

**Reference:** `docker-compose.yml` · `Jenkinsfile` · `roles/ENV_COMPOSE_CENTRALIZATION.md` · `roles/JENKINS_PIPELINE_PROTOCOL.md` · `roles/LOGGING_OBSERVABILITY_PROTOCOL.md` · `documentation/LOCAL_CICD_SETUP.md` · `documentation/CICD_JENKINS_S3_RUNBOOK.md`
