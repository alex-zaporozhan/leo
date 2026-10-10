# Variable Centralisation: .env, Compose, Docker, Frontend

Goal: **one canonical set** of values for local/CI, without copy-pasting ports and URLs across different files. Any new reference to "where a service connects" is assembled from variable names, not duplicated numbers.

---

## 1. Source hierarchy

| Layer | File / location | Rule |
|------|----------------|---------|
| Local Docker Compose run | Root **`.env`** (not in Git) | The only place where real ports, passwords, secrets for compose are set |
| Template for the team | **`.env.example`** | Same **keys** as in `.env`, placeholder values, no real passwords or tokens |
| Compose | `docker-compose.yml` | Only `${VAR}` **substitutions** and derived expressions from base variables, no "magic" `8020` inline |
| Backend in container | `environment:` in compose | `DATABASE_URL`, `S3_*` assembled from `POSTGRES_*`, `MINIO_*`, service paths; do not duplicate a password in two unrelated lines manually |
| Frontend (production build) | `frontend/Dockerfile` + **build args** in compose | `VITE_API_BASE_URL` = `http://localhost:${BACKEND_PORT}` — browser accesses the host, port **only** from `BACKEND_PORT` |
| Frontend dev (Vite) | `frontend/.env` (local, in `.gitignore` / not in image) | `VITE_API_BASE_URL` matches `BACKEND_PORT` host semantics |
| CI (Jenkins) | Credentials UI + job/pipeline env | Secrets (GHCR etc.) **not** in repository; credential id names — in `Jenkinsfile` / parameters, no hardcoded tokens |
| Backend-only example | `backend/.env.example` | Only for "DB without compose" scenario; when using compose, root `.env` is primary |

---

## 2. Ports: host vs inside network

- **`HOST:CONTAINER` in `ports:`** — left is the public port (visible in browser: `localhost:${FRONTEND_PORT}`), right is the port **inside** the image.
- In **`.env`** they are explicitly separated:
  - `BACKEND_PORT`, `FRONTEND_PORT`, `POSTGRES_PORT`, `MINIO_PORT`, … — **external** (host);
  - `BACKEND_INTERNAL_PORT=8000`, `POSTGRES_INTERNAL_PORT=5432`, `MINIO_INTERNAL_PORT=9000`, … — **inside** Docker network.
- **Between services** in compose, URLs are always `http://postgres:${POSTGRES_INTERNAL_PORT}`, `http://minio:${MINIO_INTERNAL_PORT}` — **do not** substitute `POSTGRES_PORT` here (that is the host-side port).
- **Browser** accesses `http://localhost:${BACKEND_PORT}`; `VITE_API_BASE_URL` when building frontend into image — same scheme.

---

## 3. Rules for `.env` and `.env.example`

- **One set of keys**: any key from `.env.example` must be meaningful in `.env` (and vice versa — do not add unused "junk" to example that compose/app does not reference).
- **Secrets**: `SECRET_KEY`, DB passwords, MinIO, PAT — only in `.env` and Jenkins Credentials; in `.env.example` — `change-me` / description, no live values.
- **Semantic sync**: `MINIO_ROOT_PASSWORD` and S3 keys, if MinIO = local S3, must be **agreed** (one source of truth, no manual drift).
- **`DATABASE_URL`**: in the current project it is **derived** in `docker-compose` from `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`, `POSTGRES_INTERNAL_PORT` — **do not** duplicate in `.env` as a separate long string if the canon is separate fields (otherwise risk of desync).

---

## 4. Rules for Dockerfiles

- **Secrets**: no `ENV MY_SECRET=...`, no `COPY .env` — only build-args approved in CI, no tokens.
- **Frontend + Vite**: `ARG`/`ENV` arguments for `VITE_*` — values provided by compose; the `frontend` directory does not place a second, unsynced `.env` in the image if `/.env*` is excluded in `.dockerignore`.
- **Base images**: where possible pin tags (`python:3.11-slim`, `node:20-alpine`); for reproducibility — digest as a separate task (see `roles/DOCKER_INFRA_PASSPORT.md`).

---

## 5. Rules for `docker-compose.yml`

- Services reference variables **only** via `${...}`.
- Derived strings (DB URL, S3 endpoint inside network) are assembled in `environment` from base variables, not copied verbatim from another environment.
- After changing a port by updating **one** variable in `.env`, all of the following must be consistent: `ports:`, `VITE_API_BASE_URL` (build), healthcheck if it uses a host port (usually inside the container — internal port).

---

## 6. Rules for Jenkins

- Image names, registry, tag — from pipeline parameters/variables (`GHCR_NAMESPACE`, `BUILD_CONTEXT_PATH`, …), as in `Jenkinsfile`.
- GHCR token: **Credentials** (`withCredentials`), not repository variables.
- Code paths on agent: account for Windows/WSL and `WORKSPACE_HOST_PATH` / `BUILD_CONTEXT_PATH` when building inside a Jenkins container.

---

## 7. Checklist when changing ports or URLs

- [ ] Root `.env.example` updated (and `backend/.env.example`, `frontend/.env.example` if needed).
- [ ] `docker compose config` passes without unresolved variables.
- [ ] Frontend rebuilt with new `VITE_API_BASE_URL` (build-arg), not just the old image restarted.
- [ ] Documentation: `documentation/LOCAL_CICD_SETUP.md` (or port table in passport) does not contradict the code.

---

## 8. Reference

Full image contour, GHCR, deploy, common Docker errors: `roles/DOCKER_INFRA_PASSPORT.md`.
