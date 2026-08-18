# Tech Passport: Alembic migrations, SQLite and local dev (Windows)

**Universal repository document.** Can be referenced from any role, rule, or checklist — not only from @QA_ARCH.

Brief pointer for the **`@QA_ARCH`** tag: [`roles/ROLE_QA_ARCH.md`](QA_ARCH.md).

---

## For agents and Cursor: what `.mdc` is

In the **`.cursor/rules/`** directory there are **Cursor rules** — Markdown files with a YAML "frontmatter" at the top (fields like `description`, `globs`, `alwaysApply`).

- The **`.mdc`** extension here means a **Cursor-format rule** (Markdown + metadata), not a separate language.
- Via **`globs`** the rule is automatically mixed into context when matching files are open or being edited (e.g. migrations).
- For this repository the common tech passport is defined in **[`.cursor/rules/MIGRATIONS_PLAYBOOK.mdc`](../.cursor/rules/MIGRATIONS_PLAYBOOK.mdc)** — it points **to this playbook**, without binding to a single role name.

---

## 1. Migration invariants (Alembic)

- **Schema version** is set by a string in `alembic_version` and must match the actual tables/columns.
- **SQLAlchemy models** must describe the same schema as the migration **head**. Otherwise, on app startup you get `OperationalError: no such column ...`.
- Run Alembic only from the working directory `backend/` (where `alembic.ini` is), or use the root script: `.\scripts\alembic.ps1 ...`.

---

## 2. SQLite: DDL limitations

- **`op.create_foreign_key` / `drop_constraint`** on a live table via regular `ALTER` **are not supported**. Use **`op.batch_alter_table('table')`** and inside it `batch_op.create_foreign_key` / `drop_constraint`.
- For other databases (PostgreSQL, etc.) keep branching: `if bind.dialect.name == 'sqlite': ... else: op.create_foreign_key(...)`.

---

## 3. Data inside migrations and ORM

- In `upgrade()` do **not call** `session.get(Model)` if the model already has fields added **only in a later** revision: SQLite will execute SELECT with all class columns → error "no such column".
- For data migration use **`sa.text` + `SimpleNamespace`/dict** and functions without loading the full ORM entity, or an explicit list of columns that exist **at this step** in the chain.

---

## 4. Idempotency (local SQLite after failure)

Typical failure: table/index already created, entry in `alembic_version` not updated.

- Before `create_table`: `inspect(bind).has_table(...)`.
- Before `create_index`: check index names via `get_indexes`.
- Before `add_column`: check column names via `get_columns`.
- Before `create_foreign_key`: check `get_foreign_keys` (by columns, not only by name).
- Initial data inserts: e.g. do it **only if** `COUNT(*)==0` for the target table.

---

## 5. Downgrade

- Symmetrically check for the existence of objects before `drop_*` to avoid failing on a second pass.
- After `batch_alter_table` operations re-read `inspect`.

---

## 6. After editing migrations

- Run `python -m pytest` in `backend/`.
- If possible: clean DB `upgrade head` and repeat from the "broken" state (table exists, version is old).

---

## 7. PowerShell scripts (`scripts/`)

### 7.1 Parsing and encoding

- In **Windows PowerShell 5.1** avoid **Unicode em-dash (U+2014)** and "smart" quotes **inside double-quoted strings** — may cause false `TerminatorExpectedAtEndOfString`.
- Safe: **single quotes** for literals or **`('pattern {0}' -f $var)`**.
- After external commands check **`$LASTEXITCODE`** (`python`, `npm`). For Alembic on error — **abort** the script.

### 7.2 Backend and ports

- Do not start a second uvicorn on the same port: check TCP on `127.0.0.1:$BACKEND_PORT`; if port is occupied — do **not** `Start-Process` a second server (avoid **WinError 10048**).
- **502 trap:** the port may be occupied by **something other than** FastAPI (container, another HTTP service). Then the frontend sends requests "into the void" and gets HTML/502. The script **`scripts/start-dev.ps1`** after TCP check makes a **`GET http://127.0.0.1:$BACKEND_PORT/health`** (expects JSON `{"status":"ok"}`); on mismatch the script **stops** with a hint to free the port or align **`BACKEND_PORT`** and **`VITE_DEV_PROXY_TARGET`**. After starting uvicorn in a separate window, the script **waits** for `/health` before starting Vite.
- **Full stack in Docker (UI in nginx, without Jenkins/backups):** from root **`.\scripts\docker-stack.ps1`** (or `docker compose up -d postgres minio redis backend frontend`). Before this, free **FRONTEND_PORT** / **BACKEND_PORT** on host from Vite/uvicorn, otherwise the port won't come up. The script sets **`$env:FRONTEND_PORT`** / **`$env:BACKEND_PORT`** from the root **`.env`** so that a random value in the PowerShell window does not override the file (Docker Compose process variables take priority over **`.env`**).
- **Nginx in Docker vs Vite:** on **`localhost:FRONTEND_PORT`** from compose it's **nginx** responding, not Vite; **`frontend/.env` with `VITE_*` does not affect `/api`**. 502 — check **`resale-backend`** in compose, not the Vite proxy. Do not run compose-frontend and **`npm run dev`** on the same port.

- Run only via `.\scripts\alembic.ps1` or manually `cd backend` then `python -m alembic ...`. From root without `cd` you get a configuration error for `script_location`.

### 7.4 Root `.env` and running uvicorn from `backend/`

- **`app.core.config.Settings`** loads the **root** repository `.env`, then if present — **`backend/.env`** (the second file overrides keys from the first). This way secrets and ports from the root work when **`WorkingDirectory = backend`**.

---

## 8. npm on Windows (EPERM, native modules)

### 8.1 Symptom

`npm error EPERM ... unlink ... *.node` in `node_modules`.

### 8.2 Typical causes

- File **locked**: **Vite**, **node**, antivirus/indexing, sometimes **IDE**.
- Incomplete previous installation.

### 8.3 What to do (in order)

1. Close processes holding `frontend/node_modules`.
2. From `frontend/`: **`npm install --no-audit --no-fund`** (gentler on deletion than `npm ci`).
3. If needed, delete the problematic package folder and run `npm install` again.
4. Add antivirus exclusions for the project/`node_modules`.
5. Test **`npm ci`** reproducibility in **Linux CI / Docker** if Windows blocks unlink.

### 8.4 Why specifically "unlink" on `@rolldown` / other `.node` files

The chain **vite → rolldown** pulls a **native binary** for Windows. The command **`npm ci`** first **deletes** almost all packages from `node_modules`; if the `.node` file is **open by another process** (previous Vite, zombie `node.exe`, OS indexing, antivirus), the OS returns **EPERM** on `unlink`.

**How to avoid:** do not run **`npm ci` on top of an existing `node_modules`** if all processes using the dependency tree are not closed. The **`scripts/start-dev.ps1`** script, when `node_modules` exists, calls **`npm install`**; **`npm ci`** — only on **first** install (clean clone).

### 8.5 `start-dev.ps1`

- `node_modules` exists but no Vite: only **`npm install`** (no full wipe).
- No `node_modules`: **`npm ci`**, on error — **`npm install`**.

---

## 9. Vite: ports, proxy, backend connection

### 9.1 Why `node .../vite.js` is called in `package.json`

On Windows the **`vite`** command from PATH is sometimes unavailable during **`npm run dev`**. Running via **`node node_modules/vite/bin/vite.js`** does not depend on the shim in `node_modules/.bin`.

### 9.2 `/api` proxy and 502

- In **`frontend/vite.config.ts`** requests to **`/api/*`** are proxied to the URL from:
  1. env variable **`VITE_DEV_PROXY_TARGET`**;
  2. **`frontend/.env`** (`VITE_DEV_PROXY_TARGET`);
  3. otherwise **`http://127.0.0.1:${BACKEND_PORT}`** where **`BACKEND_PORT`** is read from the **root** `.env` (default **8020**).
- Message **502** or "HTML instead of JSON" on login: **backend is not listening** on this address or the port in **`VITE_DEV_PROXY_TARGET`** does not match **`BACKEND_PORT`**.

### 9.3 Frontend port (5173, 5174, …)

By default Vite listens on the port from **`VITE_DEV_SERVER_PORT`** in **`frontend/.env`** or **5173**.

To **not silently iterate** to 5174, 5175 when the port is busy, the config enables **`strictPort: true`** by default (variable **`VITE_STRICT_PORT`**, enabled by default). Then when the port is busy, the dev server **exits with an explicit error** — close the old **`npm run dev`** or set **`VITE_DEV_SERVER_PORT`**.

If intentionally running multiple instances on the same machine: **`VITE_STRICT_PORT=false`** in **`frontend/.env`**.

**Playwright:** in **`playwright.config.ts`** the port is set via **`FRONTEND_PORT`** and passed as **`npm run dev -- --port ...`** (overrides the `.env` value for that run).

### 9.4 Quick `frontend/.env` setup

See **`frontend/.env.example`**: **`VITE_DEV_PROXY_TARGET`**, **`VITE_DEV_SERVER_PORT`**, **`VITE_STRICT_PORT`**.

### 9.5 Docker: `RUN npm ci` and lock file mismatch

The **`frontend/Dockerfile`** build uses **`npm ci`**: the lock file must match the actual dependency tree **on Linux**. After installing packages on Windows/macOS or changing npm versions, **`Missing: @emnapi/... from lock file`** sometimes appears during **`docker compose build`**.

**This is not about 502 in the browser** and not about deleting the project: the command below runs a **temporary** Node container, mounts the **`frontend/`** directory from the host and runs **`npm install`** to **update** `package-lock.json` (and if necessary rebuild **`node_modules`** inside the mounted folder). This command does not "clean" the source code.

Fix: regenerate **`frontend/package-lock.json`** in the **node:20-alpine** image (as in Dockerfile) and commit:

```powershell
docker run --rm -v "D:/path/to/your-project/frontend:/app" -w /app node:20-alpine npm install
```

(Substitute your absolute path to the **`frontend`** directory.)

---

## 10. Checklist before merging (migrations and scripts)

- [ ] New revision: correct `down_revision`, single head.
- [ ] SQLite: FK via `batch_alter_table` where needed.
- [ ] No ORM with columns "from a future" revision at this step.
- [ ] Protection from repeated application after interruption (idempotency) where appropriate.
- [ ] `pytest` in `backend/` green.
- [ ] Scripts: ASCII-safe strings or `-f`; exit code checks.
- [ ] After changes to **`frontend/package.json`**: **`docker compose build frontend`** passes (or lock regenerated via **`node:20-alpine npm install`**, see §9.5).
- [ ] This playbook and/or `frontend/README.md` updated if the startup or migration contract changes.

## 11. Quick commands

```powershell
.\scripts\alembic.ps1 upgrade head
.\scripts\start-dev.ps1
```

Full product in Docker (Postgres, MinIO, Redis, backend, **frontend in nginx**), without Jenkins:

```powershell
.\scripts\docker-stack.ps1
```

```powershell
cd frontend
npm install --no-audit --no-fund
npm run dev
```

Update **`package-lock.json`** for Docker (**`npm ci`** in node:20-alpine image), see §9.5:

```powershell
docker run --rm -v "$(Resolve-Path .\frontend):/app" -w /app node:20-alpine npm install
```

(From repository root in PowerShell.)

For a "broken" local SQLite DB only, see `frontend/README.md` and `downgrade` / `upgrade` via `.\scripts\alembic.ps1`.

---

## 12. Constraints as invariant carriers (Law 32)

Constraints from the INVARIANT LEDGER (`roles/DATA_INTEGRITY_CANON.md` §9) are added to live tables safely:

- **CHECK / FK:** `ADD CONSTRAINT ... NOT VALID` → backfill/clean data with a job → `VALIDATE CONSTRAINT`
  (validation does not hold an exclusive write lock);
- **UNIQUE:** `CREATE UNIQUE INDEX CONCURRENTLY` → `ADD CONSTRAINT ... USING INDEX`; find and resolve duplicates **before** (include a detection query in the migration PR);
- **EXCLUSION (slots):** requires `btree_gist`; `CONCURRENTLY` is unavailable → pre-clean intersections + fast `ADD` in a short window (plan as expand phase);
- **NOT NULL on a large table:** `ADD CONSTRAINT ... CHECK (col IS NOT NULL) NOT VALID` → `VALIDATE CONSTRAINT` → `SET NOT NULL` (PG12+ uses the check constraint, no full table scan under lock);
- **tenant_id retrofit:** column `NULL` → backfill in batches → `NOT NULL` per the scheme above → composite `FK NOT VALID` → `VALIDATE`.

Removing a constraint = removing invariant protection: only with a justification line in the ledger and @ARCH sign-off.
