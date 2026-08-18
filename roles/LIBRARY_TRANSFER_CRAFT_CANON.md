# LIBRARY_TRANSFER_CRAFT_CANON.md
# The craft of versioned shared libraries: shelves, adoption, provenance, draft/published.
# Class of product: Material Bank · npm registry · Figma libraries · Stripe Connect templates · Notion template gallery.
# Position: a specialisation of INTERFACE_CRAFT_CANON (§2.3 repository) + FRONTEND_CAPABILITY_CANON (C3/C6/C7/C11) for the **transfer** genre — when materials move between orgs as immutable snapshots, not live links.
# Owners: @DESIGN (the SPEC of a shelf/adopt flow), @FRONTEND (primitives), @ARCH (invariants), @QA_ARCH (state matrix + C5), @QA_VISUAL (shelf detector).

> **The canon's dogma:** a shared library is not a folder of files. It is a **shelf of published snapshots** with a **workshop** where live objects are edited, and a **conveyor** that copies snapshots into a target workshop with **provenance you can follow back**.
> Prettiness that hides whether something is draft, published, adopted, or live-linked is worse than ugliness that tells the truth.
>
> **The failure this file prevents — the "mystery copy":** the user applies something from "the bank", gets a copy, cannot tell where it came from, cannot tell draft from published, hits a 409 after clicking, and has no idea what will conflict. The backend has lineage, idempotency, preflight, and immutable versions — the UI shows a dropdown and a toast.

---

## §1. WHICH CRAFT AM I DOING?

| | **Repository** (INTERFACE §2.3) | **Transfer shelf** (this file) |
|---|---|---|
| Where | single org, browse/upload/move | Primary shelf → customer workshop |
| Core verb | upload, move, tag | **publish**, **adopt**, **contribute**, **review** |
| Versioning | optional | **mandatory** — published = immutable |
| Provenance | "used in N places" | **lineage**: adopted-from vN, contributed-to |
| Runtime | live objects | **copy-on-adopt only** — no live-link (ADR-041/043) |
| Failure mode | delete blocked if referenced | preflight **before** commit; partial_failed + retry/rollback |

**The rule:** name the register in the SPEC's first line. `REGISTER: repository` or `REGISTER: transfer_shelf`. A screen that mixes both without labeling is neither.

---

## §2. THE SHELF — anatomy (what the user sees on the Primary catalog)

A shelf item must answer five questions **at a glance, without opening the version**:
**what type · what version is current · is it published · how many adoptions · can I take it**.

```
┌─[type glyph]─ Title ───────────────────── [published v3] ─┐
│  subtitle: item_type · taxonomy path · updated 2d ago      │
├────────────────────────────────────────────────────────────┤
│ ● type: rag_library | pipeline_template | prompt_bundle   │
│ ● adoptions: 12 clients · last: Acme Corp · 3d ago         │
│ ● sources in snapshot: 2 libraries · 1 graph · 3 prompts   │
└────────────────────────────────────────────────────────────┘
     ▲ actions: Preview · Adopt · Compare versions · Archive
```

**Laws:**
```
□ TYPE IS READABLE WITHOUT TEXT: glyph + category tint (same discipline as CANVAS §1 — five to seven categories).
□ PUBLISHED VERSION IS THE ONLY ADOPTABLE UNIT — draft versions show on shelf with neutral/wait dot, never as default adopt source.
□ ADOPTION COUNT IS VISIBLE — a shelf item whose usage is invisible is a coin flip (INTERFACE §2.2).
□ SNAPSHOT SUMMARY ON THE TILE — not hidden behind a click: "2 RAG libraries · 14 documents · 1 pipeline graph".
□ ONE PRIMARY ACTION PER TILE: Adopt (pull) or Review (for contributions queue) — not six equal buttons.
□ FILTER BY item_type IS FIRST-CLASS — "Библиотека промптов" = filter prompt_bundle, not a separate product.
```

---

## §3. THE WORKSHOP — live objects in each org

```
Primary org workshop          Customer org workshop
  (edit → publish → shelf)      (edit local copy OR adopt from shelf)
```

**Laws:**
```
□ LIVE ≠ SHELF — editing a live pipeline_definition does not mutate a published bank version.
□ CONTRIBUTION = propose to shelf — creates contribution row (wait), not instant publish.
□ ADOPTED COPY SHOWS PROVENANCE INLINE — every adopted entity: "Импортировано из Банка материалов · v3 · 2026-07-01" as clickable backlink (CAPABILITY C3).
□ NO LIVE-LINK INDICATOR — if UI ever shows "shared from Primary" without "copied", that is a 🔴 isolation bug.
□ CREDENTIAL_REF NEVER TRANSFERS — adopted graph shows "требует привязки credential" badge until bound (ADR-043 §3.5).
```

---

## §4. THE CONVEYOR — adopt/contribute wizard (INTERFACE Wizard + CAPABILITY C4/C5/C7)

The conveyor is a **Stepper in AppDrawer** (right, page continuity), never a centered modal for multi-step work.

| Step | Purpose | Capability surfaced |
|------|---------|---------------------|
| 1 Source | pick published version (human name, type, snapshot summary) | C3 backlinks to version detail |
| 2 Destination | customer org (server-side picker) + target (new program / existing / rag_library) | C5 — invalid target disabled + reason |
| 3 Preflight | conflicts, capacity, deferred entities, unresolved refs | **C4/C5** — show before commit, not 409 after |
| 4 Launch | idempotency key sent; optimistic "queued" | C11 |
| 5 Progress | real cursor `n/m`, phase, Cancel (before irreversible), Retry | **C7** — instrument panel |

**Laws:**
```
□ PREFLIGHT IS NOT OPTIONAL for template/pipeline types — unresolved retrieval ref or missing bundle → shown here as blocking list.
□ CAPACITY EXCEEDED (429) — blocking message with retry_after, not silent failure.
□ PARTIAL_FAILED — show lineage summary + explicit Retry / Rollback actions; do not show "succeeded".
□ CANCEL — only before irreversible_started_at; after S3 copy UI shows "отмена недоступна" + reason (C7).
□ PROGRESS NEVER SPINNER-ONLY — if backend has cursor, UI shows cursor (T5).
□ IDEMPOTENCY — duplicate adopt with same key → same result, no double copy (C11); UI says "уже применяется" not error soup.
```

---

## §5. RETRIEVAL-SET AS ORCHESTRATION GLUE (between libraries)

Retrieval-set is not a folder. It is a **named mix recipe** for AI:

```
retrieval_set "Sales onboarding mix"
  source A: RAG library (adopted from bank v2) · weight 0.6 · max_chunks 8
  source B: local niche library           · weight 0.4 · max_chunks 6
```

**Laws:**
```
□ RETRIEVAL-SET HAS ITS OWN SCREEN — not buried in collapsed dock on RAG page (first-class nav).
□ SOURCE LIST SHOWS HUMAN LIBRARY NAMES + provenance (source_bank_version_id as label, not filter).
□ WEIGHT + max_chunks VISIBLE ON TILE — user understands the mix without opening editor.
□ DEBUG RETRIEVAL FROM SET EDITOR — "Проверить поиск" uses same API as pipeline node (C4).
□ STALE/ARCHIVED SOURCE — warning badge on source row; publish/run blocked with reason (C5).
□ PROVENANCE PER HIT in debug — program_name, topic_name, source_bank_version_id (never UUID in label).
```

Canvas integration: retrieval node inspector offers `retrieval_set_id` picker fed from same org's sets (CANVAS §11).

---

## §6. DRAFT / PUBLISHED / ROP LIFECYCLE (artifact status, not shelf status)

Two parallel status machines — do not conflate:

| Machine | Entities | States | UI zone |
|---------|----------|--------|---------|
| **Shelf** | bank item versions | draft → published (immutable) | Material Bank |
| **Artifact** | lesson packages, rop_content_package | draft → methodologist_approved → sent_to_supervisor → … | Admin «Готовые» + ROP «Входящие» |

**Laws:**
```
□ DRAFT ≠ PUBLISHED visually unmistakable — neutral dot vs success dot; published rows non-editable (only new draft version).
□ METHODOLOGIST_APPROVED = "Готовые" cluster (ADR-022) — not visible to learner until sent.
□ SEND TO ROP — outside pipeline graph (A-32); HG-1 approve → persist_artifact → deep link «Готовые», not inline send in graph.
□ ROP SEES ONLY sent_to_supervisor+ — employee never sees methodologist_approved (server-side filter, ADR-022).
□ WITHDRAW_FROM_READY — confirm + returns to draft; visible only in «Готовые» row actions.
```

ROP supervisor view (`/app` zone): segmented **Входящие** | **Опубликовано для команды** — data-comfort density, StatusIndicator `text`/`row`, no creator glow.

---

## §7. INFORMATION ARCHITECTURE (three planes — binds to 15_ADMIN_INTERACTION_CONCEPT)

| Plane | Shelf role | Key routes |
|-------|------------|------------|
| **МАТЕРИАЛЫ** | Primary shelf + local corpora | `/admin/material-bank`, `/admin/kb`, `/admin/retrieval-sets`, `/admin/programs` |
| **ИНСТРУМЕНТЫ** | adopt creates local copies used here | `/admin/pipelines`, `/admin/prompts`, `/admin/trainer-agents` |
| **ИСПОЛНЕНИЕ** | adoption audit + runs + HITL + Готовые | `/admin/pipeline-runs`, `/admin/hitl-queue`, A-32 |

Push/pull verbs in UI (consistent Russian copy):
- **Применить клиенту** = pull/adopt
- **Сохранить в банк** = push/contribute
- **Опубликовать версию** = publish to shelf
- **Сравнить версии** = C6 time-travel

---

## §8. THE MYSTERY-COPY DETECTOR — 12 signs (@QA_ARCH / @QA_VISUAL)

| # | Symptom | Consequence |
|---|---------|-------------|
| **L1** | Adopted object has no provenance backlink | User cannot trace AI output to source version |
| **L2** | Draft bank version adoptable from shelf | Immutable contract violated in UX |
| **L3** | Preflight skipped — 409/422 reaches user for known conflict | C5 violated |
| **L4** | Adoption progress = spinner only | C7/T5 violated |
| **L5** | Retrieval-set buried in collapsed dock | User does not know multi-library mix exists |
| **L6** | UUID shown as primary label on shelf/adopt/wizard | Law 8 violated |
| **L7** | Live-link implied ("shared from Primary" without "copy") | ADR-041 isolation risk |
| **L8** | Credential required but graph shows green publish | ADR-043 ref resolution hole |
| **L9** | Contribution instant-publishes without review queue | Governance bypass |
| **L10** | partial_failed shown as success | Data integrity UX lie |
| **L11** | ROP sees draft/methodologist_approved | ADR-022 visibility leak |
| **L12** | Three parallel transfer UIs (bank + .json import + separate agent store) | ADR-043 violated — one shelf |

**Verdict:** any of L7/L11 → 🔴 (security/product invariant). 3+ hits overall → 🔴.

---

## §9. ACCEPTANCE — how transfer craft is verified

| Who | When | What |
|-----|------|------|
| **@ARCH** | ADR/spine | copy-on-adopt, item_type enum, ref resolution, no live-link |
| **@FRONTEND** | CAPABILITY_MAP per module | C3/C4/C5/C6/C7/C11 explicitly SURFACED |
| **@DESIGN** | SPEC per shelf/adopt/retrieval-set screen | REGISTER: transfer_shelf; L1–L12 checklist |
| **@DEV** | implementation | preflight before POST adopt; provenance on tiles; cursor progress |
| **@QA_ARCH** | gate | State Matrix includes preflight/partial_failed/rollback; L7/L11 tests |
| **@QA_VISUAL** | render | shelf tiles equal-height; wizard steps no overflow; provenance readable |

---

Reference: `roles/INTERFACE_CRAFT_CANON.md` (§2.3 repository · §5 drawer vs page) · `roles/FRONTEND_CAPABILITY_CANON.md` (C3/C6/C7/C11 · §6 module map) · `roles/CANVAS_CRAFT_CANON.md` (§11 material source ports) · `roles/VISUAL_CRAFT_CANON.md` (instrument register) · `docs/decisions/ADR_[NNN]_[SLUG].md` (project license / library ADRs, when present) · `docs/execution/` (admin interaction concept, when present) · `roles/ROLE_DESIGN.md` · `roles/ROLE_QA_ARCH.md`
Version: 1.0 | 2026-07-13
