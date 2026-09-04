# RAG_CANON — the Task Router and the source reading order for AI

> Version: 2026-09-03 · v3.0
> Purpose: **one map of the world**. Every task starts here: resolve the class, read the class minimum in the stated order, then act.
> Entry point: `.cursorrules` → **TASK ROUTING** → this file §2.
> Maintenance is not optional: §6. A canon that is not routed from here does not exist.

---

## 1. Source priority on conflict — which *document* wins when two describe the project differently

This ladder orders **sources of fact about the project**. It is not the law ladder and not the reading order: `.cursorrules` is deliberately absent from it, because the laws are the frame every layer below is written inside and no artifact overrides one (`.cursorrules` → **LAW PRECEDENCE** decides between laws; §2 below decides what to read).

1. **Code and tests** of the repository.
2. **Layer W:** `docs/artifacts/` — active project contracts of the current wave.
3. **Layer S:** `docs/product_state/` — summary of current product state, confirmed by code.
4. **Layer P:** `roles/` — roles, protocols, templates, and process standards.
5. **Archive and external user materials** (`docs/artifacts/archive/`, `documentation/`) — context only.

When documents at the same level conflict, the more recent document with an explicit version/date and owner wins. A conflict that repeats gets a row in `roles/CONFLICT_REGISTRY.md` with a single named winner — it is not re-decided per task.

### 1.1 Citing a rule — detector codes are always qualified

*(This is the full text of the rule, not a summary. Its owner is `.cursorrules` → **LAW PRECEDENCE**; it is repeated here in full and on purpose, because it governs every citation the agent writes and a rule that frequent is cheaper repeated than followed by a link. If it changes, it changes in the owner first.)*

Series letters are reused across canons — `C`, `G`, `T`, `S`, `A`, `E` and `X` each mean different things in different files. **Never cite a bare `C1` or `T3`: write the canon with it** (`FRONTEND_CAPABILITY C1`, `ARCH_SPINE T3`). An unqualified code is not a finding, it is a guess — and a guess that looks like a citation is worse than an open question, because the next reader treats it as checked.

---

## 2. THE TASK ROUTER

**How to use.** @LEAD names the class in the first line of the reply (`CLASS: TC-xx`), opens the **READ** list of that class in the given order, then proceeds with the chain. One task may carry two classes (a screen over a new domain = TC-03 + TC-11) — read both minimums, the earlier-numbered class leads.

**What this is not.** Not a whitelist and not a ceiling. It states what must be read **first** and **in what order**, because the failure mode it exists to remove is *not knowing where to start* — not curiosity. Any role may open any other file and say so in the report. Reading beyond the minimum is normal work.

**About the OUT lists.** A class states what is *deliberately out of scope* where a neighbouring class would otherwise bleed into it — most sharply between the registers (TC-01 vs TC-02/03) and around the canvas class. Where no OUT is stated, nothing is forbidden: read what the work needs.

**Budget — files AND size.** Every class minimum is capped at **six files**; if a class needs a seventh, the class is wrong or two canons need merging (`roles/FRONTEND_CONSOLIDATION.md`). Six files is not automatically cheap: where a canon is large, the class names **the sections to read first** and the rest is on demand. The size budget is a **maintainer-side** rule, applied when a class is written or a canon grows — not a runtime measurement the agent is expected to perform on itself. A class whose six files run past roughly 100 KB is over budget and is trimmed by naming sections, never by dropping a canon silently.

**Cascade.** This file is the middle level: laws in `.cursorrules` · the task class here · the acting role's own reading map for detail inside its domain. The more specific section pointer wins on overlap; a role may add a file this class does not name and may not drop one it does. **Role sets are defaults, never permission lists** — the developer composes them freely.

**After delivery.** Every delivered unit is followed by the clean-context audit — the rule and its levels live in `roles/ROLE_LEAD.md` §C; the class resolved here supplies the default role set (`roles/SECOND_PASS_PROTOCOL.md` §4).

---

### TC-01 · operational-screen — `/admin`, `/app`, internal tools
**Fires:** a screen or component of the working contour — tables, forms, drawers, filters, dashboards. **REGISTER: instrument.**
**READ, in order:** 1) `roles/VISUAL_CRAFT_CANON.md` **§1–§6 · §9 · §11** (restraint, tonal depth, one light source, chroma, type scale, optics · the cheapness detector X1–X12 · **THE FLOOR** when there is no concept) · 2) `roles/INTERFACE_CRAFT_CANON.md` **§1 · §3 · §7** (the interaction inventory I1–I12 · data density · the stiffness detector ST1–ST12) · 3) `roles/LAYOUT_COMPOSITION.md` **§2 · §3 · §5** (primitive algebra · proximity as a number · action grammar) · 4) `roles/LAYOUT_INVARIANTS.md` **§1–§9 · §12** (deterministic geometry · collision and stacking) · 5) `roles/COMPONENT_REGISTRY.md` (whole — every block maps to a registered component) · 6) `roles/MOTION_CRAFT_CANON.md` **§1 · §3** (**THE MOTION FLOOR** taken verbatim when no motion concept exists · the stiffness detector M1–M12) — and `roles/MOTION_REFLEX.md` at implementation time, which is a reflex map run over the diff rather than a canon read up front, and does not count against the cap. 
**Then, on the same reading:** `roles/DOMAIN_STANDARDS.md` **§0 · §9 + the section of this page type** — the business minimum. It is the class's *content* requirement rather than a craft canon and does not count against the six-file craft cap.
**ON DEMAND:** `roles/LIBRARY_TRANSFER_CRAFT_CANON.md` (bringing an external pattern or library into the project without importing its aesthetic) · `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 · `roles/CRAFT_LINT_SPEC.md` (V15–V21, incl. **V21 motion presence**) · `roles/TEMPLATE_ADMIN_UI_UX.md`.
**OUT:** `EDITORIAL_CRAFT_CANON` (statement register — applying it here makes a settings screen shout) · `HERO_ARCHETYPES` · `MOTION_LIBRARY` **scroll narrative S1–S4 only** (a scrub belongs to a showcase; the arsenal's entrance and interaction techniques are in scope here) · SEO canons.
**Motion is IN scope for this class and always was — it is the class that forgot it.** An operational screen takes the motion floor (`MOTION_CRAFT_CANON` §1) and the MICRO catalogue (`MOTION_AMBITION_DIAL` Part 2, ON DEMAND) without waiting for a complaint about twitching buttons.

### TC-02 · public-screen — an indexable page of a public site
**Fires:** a page that must be found by search and read by a stranger. **REGISTER: statement** unless the page is a utility page.
**READ, in order:** 1) `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` — the project world; no concept → stop, @CREATOR · 2) `roles/EDITORIAL_CRAFT_CANON.md` **§1–§5 · §8** (which craft am I doing · scale as the primary instrument · tension and asymmetry · one gesture committed to completely · editorial typography · the timidity detector Y1–Y12) · 3) `roles/HERO_ARCHETYPES.md` (composition archetype — "text left, mockup right" is not a default) · 4) `docs/artifacts/waves/[N]/SEO_ONPAGE_*.md` (the H-structure is an INPUT to design, not a suggestion) · 5) `roles/LAYOUT_COMPOSITION.md` · 6) `roles/MOTION_AMBITION_DIAL.md` (the ambition level, default confident).
**ON DEMAND:** `roles/CONCEPT_DNA_LIBRARY.md` (the world recipe — incl. its motion personality) · `roles/MOTION_LIBRARY.md` · `roles/MOTION_CRAFT_CANON.md` (**§2 the grammar of the in-between, §3 M1–M12**) · `roles/ROLE_MOTION.md` (the SPEC formats and the timing principles) · `roles/SEO_CANON.md` · `roles/LAYOUT_INVARIANTS.md`.
**OUT:** the operational Visual Quality Gate checklist (`gray.0` background, Drawer-for-forms, three-dot row menus) — it describes the instrument register and disfigures a public page.

### TC-03 · statement-surface — hero, brand page, campaign, portfolio
**Fires:** a surface whose job is to make an impression, not to be operated. **REGISTER: statement.**
**READ, in order:** 1) `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` · 2) `roles/EDITORIAL_CRAFT_CANON.md` **§1–§5 · §7–§8** (incl. **§7 what does NOT invert** — accessibility, contrast and geometry survive every gesture) · 3) `roles/CONCEPT_ANATOMY.md` (the reference protocol SOURCE→EXTRACT→TRANSFER→NOT TAKING; ≤1 SaaS per concept) · 4) `roles/HERO_ARCHETYPES.md` · 5) `roles/MOTION_AMBITION_DIAL.md` · 6) `roles/MOTION_CRAFT_CANON.md` (**§2 grammar · §3 M1–M12** — the dial grants the ambition, this spends it).
**ON DEMAND:** `roles/MOTION_LIBRARY.md` (the technique arsenal) · `roles/ROLE_MOTION.md` (SPEC formats, timing principles) · `roles/MEDIA_SYNTHESIS_CANON.md` (generated plates) · `roles/VISUAL_CONCEPT_PROTOCOL.md` §6 RESKIN.
**OUT:** restraint canons for the instrument register. **Timidity here is a craft failure exactly as much as gaudiness is.**

### TC-04 · node-graph — canvas, pipeline builder, agent graph
**READ, in order:** 1) `roles/CANVAS_CRAFT_CANON.md` (typed ports, run overlay on the same graph, loops with a visible exit, the toy-graph detector G1–G10) · 2) `roles/FRONTEND_CAPABILITY_CANON.md` (the CAPABILITY_MAP — the graph is a view over a real backend) · 3) `roles/VISUAL_CRAFT_CANON.md` · 4) `roles/LAYOUT_INVARIANTS.md` · 5) `roles/INTERFACE_CRAFT_CANON.md` · 6) `roles/ASYNC_WORKERS_CANON.md` (the run overlay shows a real execution contour).
**OUT:** editorial craft.

### TC-05 · visual-concept — the birth or replacement of the project world
**Fires:** a new product with UI, a rebrand, "change the concept". Runs **once**, before any screen.
**READ, in order:** 1) `roles/CONCEPT_ANATOMY.md` (**first** — the eight DNA axes and the reference protocol) · 2) `roles/CONCEPT_DNA_LIBRARY.md` (twelve worlds as executable recipes + the custom-world constructor + the niche router) · 3) `roles/VISUAL_CONCEPT_PROTOCOL.md` (Step 5.5.A/B, the TASTE GATE cliché ban-list, the transfer map into the passports) · 4) `roles/EDITORIAL_CRAFT_CANON.md` or `roles/VISUAL_CRAFT_CANON.md` by the product's dominant register · 5) `roles/TEMPLATE_DESIGN_PASSPORT.md` · 6) `roles/TEMPLATE_TYPOGRAPHY_PASSPORT.md`.
**ON DEMAND:** `roles/DESIGN_DECISION_LIBRARY.md` (a menu of decisions — subordinate to the world, never a substitute for it) · `roles/PROJECT_VISUAL_BOOTSTRAP_PROTOCOL.md` (**Step 0 only**; the concept itself is born through `VISUAL_CONCEPT_PROTOCOL`, which supersedes the older slot-filling route) · `roles/MEDIA_SYNTHESIS_CANON.md` + `roles/ROLE_MEDIA_ENGINEER.md` (rendering the world into plates).
**Produces:** `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` + the derived passports. **No `[hex]` placeholder survives this step.**
**OUT:** the golden SaaS library as a source of the world — for a public site the world is the reference (Tier 0), not Linear.

### TC-06 · backend-slice — schema → service → router → test
**READ, in order:** 1) `docs/artifacts/DOMAIN_MODEL_[MODULE].md` (Law 42 — the slice restates an existing model, it does not invent one) · 2) `docs/artifacts/ARCH_SPINE_*.md` (the twelve vertebrae with numbers) · 3) `roles/DEV_EXECUTION_PASSPORT.md` (the checkpoint map + pattern catalogue) · 4) `roles/DATA_INTEGRITY_CANON.md` (the invariant-protection hierarchy; an `if` is a UX hint, not protection) · 5) `roles/ASYNC_AWAIT_REFLEX.md` (the grep self-check over your own diff before handoff) · 6) `roles/TESTING_CANON.md`.
**ON DEMAND:** `roles/DATABASE_RUNTIME_CANON.md` · `roles/MIGRATIONS_PLAYBOOK.md` · `roles/CACHE_STRATEGY.md`.

### TC-07 · async-pipeline — queues, workers, background jobs
**READ, in order:** 1) `roles/ASYNC_WORKERS_CANON.md` (three planes, laws AW-1…13, JOB and PIPELINE passports, crash-tests) · 2) `roles/ASYNC_AWAIT_REFLEX.md` (**mandatory before handoff**) · 3) `docs/artifacts/JOB_PASSPORTS_[PROJECT].md` / `PIPELINE_PASSPORT_[pipeline].md` — filled **before** code · 4) `roles/DATABASE_RUNTIME_CANON.md` (the lease, the corpse lock, SKIP LOCKED) · 5) `roles/DATA_INTEGRITY_CANON.md` (idempotency) · 6) `roles/LOGGING_OBSERVABILITY_PROTOCOL.md`.
**This class is the system's reference implementation of a closed rule loop** — canon + reflex + passport + route. New classes are built in its shape.

### TC-08 · data-integrity-and-tenancy — invariants under concurrency, multi-tenant isolation
**READ, in order:** 1) `docs/artifacts/DOMAIN_MODEL_[MODULE].md` layer 4 (invariants) and layer 7 (authority) · 2) `roles/DATA_INTEGRITY_CANON.md` (the race catalogue, SQL recipes, the INVARIANT LEDGER, tests T-H) · 3) `roles/DATABASE_RUNTIME_CANON.md` · 4) `docs/artifacts/ARCH_SPINE_*.md` (tenancy model + leak test) · 5) `roles/SECURITY_GATE_PROTOCOL.md` §1 (S4 IDOR, S2 tenant boundary) · 6) `roles/ASYNC_AWAIT_REFLEX.md` block B.

### TC-09 · migration — schema change on live data
**READ, in order:** 1) `roles/MIGRATIONS_PLAYBOOK.md` (expand/contract) · 2) `docs/artifacts/DOMAIN_MODEL_[MODULE].md` layer 1–4 · 3) `roles/DATA_INTEGRITY_CANON.md` · 4) `docs/artifacts/ARCH_SPINE_*.md` (additive-only contracts) · 5) `roles/DATABASE_RUNTIME_CANON.md` (lock discipline during migration) · 6) `roles/TESTING_CANON.md`.

### TC-10 · security-surface — S1–S12 touched
**Fires:** by a **mechanical grep of the diff** (`roles/SECURITY_GATE_PROTOCOL.md` §1), never by judgement.
**READ, in order:** 1) `roles/SECURITY_GATE_PROTOCOL.md` (the surface, the three checkpoints, the Security Contract) · 2) `roles/ROLE_PENTEST.md` (vector tree A–G, the T-series, the modes) · 3) `docs/artifacts/DOMAIN_MODEL_[MODULE].md` layer 7 + adversaries A7/A11 · 4) `roles/PENTEST_SCENARIOS.md` (the attacking artifacts) · 5) `roles/DATA_INTEGRITY_CANON.md` · 6) `roles/ROLE_SEC.md` (the advisory 18 pillars).

### TC-11 · domain-model — a new module or a changed domain
**Fires:** Law 42. **This class runs BEFORE @ARCH, not after.**
**READ, in order:** 1) `roles/LOGIC_MODELING_CANON.md` (seven layers, the twelve adversaries, the terminating cycle) · 2) `docs/artifacts/BUSINESS_ROUTES.md` (money, roles, data, critical journeys) · 3) `docs/artifacts/BUSINESS_LOGIC.md` · 4) `roles/ROLE_PRINCIPLE.md` · 5) `roles/LEAD_PRODUCT_LOGIC_EXCELLENCE.md` (role · trigger · session goal · mandatory chain · deliberate noes) · 6) `roles/DATA_INTEGRITY_CANON.md` (layer 4 becomes the ledger).
**Produces:** `docs/artifacts/DOMAIN_MODEL_[MODULE].md`. **A hole that is a business decision goes to @LEAD/@BIZ and is written into the model — never guessed by the code.**

### TC-12 · architecture-decision — spine, ADR, stack, store
**READ, in order:** 1) `roles/ARCH_SPINE_PROTOCOL.md` (twelve vertebrae as numbers, the complexity ladder) · 2) `docs/artifacts/DOMAIN_MODEL_[MODULE].md` (structure is derived FROM the model) · 3) `roles/SYSTEM_DESIGN_PROTOCOL.md` (load profile, latency budget, failure modes) · 4) `roles/DATA_STORE_SELECTION.md` · 5) `roles/STACK_SELECTION.md` · 6) `roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md` (the ADR registry).
**ON DEMAND:** `roles/DOCKER_INFRA_PASSPORT.md` · `roles/ENV_COMPOSE_CENTRALIZATION.md` · `roles/JENKINS_PIPELINE_PROTOCOL.md` (one worked CI contour — Law 21 forbids no engine).

### TC-13 · ai-contour — RAG, retrieval, agent graphs, evaluation
**READ, in order:** 1) `roles/ROLE_AI_ENGINEER.md` (the pillars, the passports, the EVAL_PLAN) · 2) `roles/RAG_ARCHITECTURE_STACK_2026.md` · 3) `docs/artifacts/RAG_PASSPORT.md` / `AGENT_GRAPH_PASSPORT.md` · 4) `docs/artifacts/EVAL_PLAN.md` (a release-blocking threshold as a number; "by eye in the chat" is a blocker) · 5) `roles/ASYNC_WORKERS_CANON.md` · 6) `roles/DATA_INTEGRITY_CANON.md` (an agent with an external money/status effect).

### TC-14 · visibility — semantics, rendering, on-page, technical SEO
**READ, in order:** 1) `roles/ROLE_SEO.md` (the four modes and the deploy veto) · 2) `roles/SEO_CANON.md` (semantics→IA, the SSG/SSR table, CWV budgets) · 3) `docs/artifacts/SEMANTIC_CORE_[PROJECT].md` · 4) `roles/HERO_ARCHETYPES.md` (the page composition must not cut the semantics) · 5) `roles/METRICS_PROTOCOL.md` (M-SEO).
**Rule of the class:** one cluster = one page = one intent. Semantics precede information architecture and design.

### TC-15 · qa-audit — the business and code gate after @DEV
**READ, in order:** 1) `roles/ROLE_QA_ARCH.md` (the audit vectors) · 2) `docs/artifacts/DOMAIN_MODEL_[MODULE].md` (an invariant without protection = 🔴; a state the model forbids but the code can reach = 🔴) · 3) `roles/LEAD_PRODUCT_LOGIC_EXCELLENCE.md` (dead buttons vs gaps, duplicate contours) · 4) `roles/DOMAIN_STANDARDS.md` · 5) `roles/ASYNC_AWAIT_REFLEX.md` (mirror — what @DEV already caught is not sent back) · 6) `roles/LEAD_ANTI_CHECKBOX_PROTOCOL.md`.

### TC-16 · qa-visual — the gate on the render
**READ, in order:** 1) `roles/ROLE_QA_VISUAL.md` (the harness, the meters, vectors V1–V14 + the craft floor V15–V21, the baseline anchor) · 2) `roles/LAYOUT_INVARIANTS.md` (the numeric criteria) · 3) `roles/MOTION_CRAFT_CANON.md` **§3 M1–M12** (the stiffness catalogue — the only detector set that fails on the ABSENCE of motion; every other motion vector scores a dead page as perfect) · 4) `roles/QA_VISUAL_AESTHETE_SENSOR.md` (the closed crime catalogue A–H; a missing verdict cell = an incomplete report = no 🟢) · 4) `roles/CRAFT_LINT_SPEC.md` (V15–V21 with numbers) · 5) the register canon of the surface — `VISUAL_CRAFT_CANON` for instrument, `EDITORIAL_CRAFT_CANON` for statement · 6) `roles/LAYOUT_COMPOSITION.md`.
**Measure, do not read code only.** Fixtures empty/single/typical/many/longtext/i18n on the project's declared surfaces (`FRONTEND_PASSPORT_[PROJECT].md` §Surfaces; 360/768/1280/1920 until they are declared — Law 26).

### TC-17 · product-package — a new product, domain or market entry
**READ, in order:** 1) `roles/ROLE_CREATOR.md` · 2) `roles/ROLE_DOMAIN_EXPERT.md` · 3) `roles/ROLE_BIZ.md` (KILL SIGNAL, MARKET_AUDIT, ROI) · 4) `roles/LEAD_PRODUCT_GATE_PROTOCOL.md` (PRE-PLAN GATE) · 5) `roles/PRODUCTION_READINESS_CANON.md` + `roles/PLANNING_MATURITY_CANON.md` (Law 41: the foundation is designed whole, features ship in waves) · 6) `roles/LEAD_PRODUCT_LOGIC_EXCELLENCE.md`.
**ON DEMAND:** `roles/NICHE_BOOTSTRAP_PROTOCOL.md` + `roles/niches/*` · `roles/PRODUCT_MATURITY_CANON.md`.

### TC-18 · execution-planning — waves, dev prompts, cursor-queue batches
**READ, in order:** 1) `roles/PRE_DEVELOPMENT_EXECUTION_PACK_PROTOCOL.md` (the pre-code pack, the screen registry) · 2) `roles/LEAD_PRODUCT_GATE_PROTOCOL.md` (which gate closes which rung) · 3) `roles/ENGINEERING_PLAN.md` (the state machine and who reads what) · 4) `roles/ROLE_LEAD.md` · 5) `roles/DOC_TOPOLOGY.md` (nature before folder — where each produced artifact belongs) · 6) `roles/LEAD_ANTI_CHECKBOX_PROTOCOL.md`.
**Law 43 applies hardest here:** declare the goal, the expected result and the allowed cost before minting a series; a prompt series whose output is documents about documents has failed the law.

### TC-19 · documentation — pitch, knowledge base, user docs
**READ, in order:** 1) `roles/ROLE_SCRIBE.md` · 2) `docs/product_state/` (the actual state per code — never invent) · 3) `roles/EDITORIAL_CRAFT_CANON.md` (when the output is read by a stranger) · 4) `roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md` · 5) `roles/DOC_TOPOLOGY.md`.
**Rule:** a fact absent from code or artifacts is `[UNDOCUMENTED]` and escalates — it is never written as if true.

### TC-20 · system-evolution — changing LEO itself
**Fires:** only on an explicit human request (Law 16). **The system never edits itself.**
**READ, in order:** 1) **`roles/RULE_INTEGRITY_PROTOCOL.md`** (the seven tests a rule must pass, and the ladder between two true rules — run it on the proposed change **and on the finding that prompted it**) · 2) `roles/SYSTEM_EVOLUTION_PROTOCOL.md` (`@EVOLVE`) · 3) `roles/CONFLICT_REGISTRY.md` (does this change contradict a decided winner?) · 4) `roles/SYSTEM_FILES_MASTER.md` · 5) `roles/FILE_MAP.md` · 6) `roles/FRONTEND_CONSOLIDATION.md` (before adding a rule: does it already live somewhere?).
**Then §6 of this file** — already open as the entry point, so it does not count against the six-file cap: **the change is not finished until the router knows about it.**

### TC-21 · operations — release contour, deploy, performance, licensing
**Fires:** rolling out or changing the release contour · a deploy or environment change · a latency/throughput investigation · a dependency licence question. These are real work with no product surface, and before this class they resolved to nothing.
**READ, in order:** 1) `roles/DOCKER_INFRA_PASSPORT.md` · 2) `roles/ENV_COMPOSE_CENTRALIZATION.md` (one centralised config contract — Law 22) · 3) `docs/artifacts/ARCH_SPINE_*.md` (the declared contour — Law 21: an undeclared contour is the violation, not the choice of engine) · 4) `roles/LOGGING_OBSERVABILITY_PROTOCOL.md` · 5) `roles/JENKINS_PIPELINE_PROTOCOL.md` **or** the project's declared equivalent · 6) `roles/DEPLOY_LICENSE_AND_PIRACY.md` for a licence question.
**ON DEMAND:** `roles/SYSTEM_DESIGN_PROTOCOL.md` and `roles/ROLE_PERF.md` (latency budget · bottleneck) · `roles/DATABASE_RUNTIME_CANON.md` (the corpse-lock alarm) · `roles/ROLE_LAWYER.md`.
**Law 36 lives here:** before investigating any environment behaviour, prove the running artifact is the one that was built — digest matches, the pipeline pulls and cannot rebuild locally.

### TC-00 · trivial — a text, a colour, a one-line fix
**READ:** nothing from `roles/`. The current file and its neighbours. Law 43 forbids ceremony here; Law 1 and Law 2 still hold.

---

### 2.1 · Always read, in every class

- **`roles/ROLE_[NAME].md` of the acting role** — a role reads its own constitution on activation; it is never listed in a class minimum and never counts against the six-file cap. Where a class names another role's file (e.g. TC-15 naming `ROLE_QA_ARCH.md`), that is because the *class* is that role's work.
- **`.cursorrules`** — loaded automatically by the environment.

### 2.2 · Templates — opened when producing that artifact, not when reading

`TEMPLATE_PROJECT_PROFILE` · `TEMPLATE_BIZ_LOGIC` · `TEMPLATE_MODULE_DEV` · `TEMPLATE_DESIGN_PASSPORT` · `TEMPLATE_TYPOGRAPHY_PASSPORT` · `TEMPLATE_UI_COMPOSITION_PASSPORT` · `TEMPLATE_MOTION_LANGUAGE` · `TEMPLATE_PROJECT_FRONTEND_PASSPORT` · `TEMPLATE_QA_FRONTEND_VISUAL_CANON` · `TEMPLATE_DOCUMENTATION_ARCHITECTURE` · `TEMPLATE_COMMERCIAL_PACK` · `TEMPLATE_ADMIN_UI_UX` · `TEMPLATE_DESIGN_UX`.
A template is a **form to fill**, not a rule to obey: where a template's example values disagree with the project's own concept or passport, the project wins.

### 2.3 · Event-driven, not task-driven

Opened by a lifecycle event rather than by a task class: **`roles/SECOND_PASS_PROTOCOL.md` (after every delivered unit, stage and batch — see §2 "After delivery")** · `roles/PROCESS_LAUNCH.md` (project start) · `roles/SEED_PROTOCOL.md` (demo/seed data) · `roles/MIRROR_PROTOCOL.md` (role-boundary gaps) · `roles/NONFUNCTIONAL_SCORECARD.md` and `roles/ARCHITECTURE_EXCELLENCE_PASSPORT.md` (maturity review) · `roles/DEPLOY_LICENSE_AND_PIRACY.md` (handover/deploy) · `roles/SYSTEM_UPGRADE_MANIFEST.md` (the system's own changelog) · `roles/METRICS_PROTOCOL.md` (a metric appears).

### 2.4 · Project examples — never a default for a new project

`roles/TPF_MASTER.md` + the eleven `roles/TPF_MODULE_*.md` · `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` · `roles/ARCH_FRONTEND_UI_LOGIC.md`. These describe **one** delivered product. They are read as a worked example when explicitly asked for one, and they never supply defaults, routes, hotkeys or tokens to another project. Their target home is `docs/artifacts/reference/tpf/`.

### 2.5 · Under review — do not treat as an independent source

`roles/INTEGRATION_PATCHES.md` · `roles/INTEGRATION_PATCHES_TASTE.md` · `roles/INTEGRATION_PATCHES_SECURITY.md` — historical patch sets whose content has already been integrated into `FRONTEND_DESIGN_EXCELLENCE`, `LAYOUT_INVARIANTS` and `SECURITY_GATE_PROTOCOL`. Read only to trace why a rule exists; on any disagreement the integrated canon wins.

---

## 3. The project base route (read once per project or when context is lost)

Order for any project — each level refines the previous one. This is orientation, not a per-task cost.

**Layer W — current project contracts**
1. `docs/artifacts/SAAS_ARCHITECTURE_SPINE_*.md` — the technical contract (stack, DB, API, errors)
2. `docs/artifacts/BUSINESS_LOGIC.md` — what we are building and why
3. `docs/artifacts/BUSINESS_ROUTES.md` — routes of money, roles and data; critical journeys
4. `docs/artifacts/DOMAIN_MODEL_*.md` — what must be true (Law 42)
5. `docs/artifacts/VISUAL_CONCEPT_[PROJECT].md` — the world the product lives in
6. `docs/artifacts/ADR_REGISTRY.md` · `docs/artifacts/DEVELOPMENT_PLAN.md`

**Layer S — product state by facts**
7. `docs/product_state/INDEX.md` · `BACKEND_PASSPORT.md` · `FRONTEND_PASSPORT.md`

**Layer P — process**
8. `.cursorrules` (loaded automatically) → §2 of this file for the task class → `roles/ROLE_[NAME].md` of the acting role

**Single sources on conflict:** project aesthetic → `docs/artifacts/VISUAL_CONCEPT_*` (the world) · tokens → the project design passport, and only in its absence `roles/FRONTEND_DESIGN_EXCELLENCE.md` · geometry → `roles/LAYOUT_INVARIANTS.md` · layout grammar → `roles/LAYOUT_COMPOSITION.md` · business minimum by page type → `roles/DOMAIN_STANDARDS.md` · what must be true → `docs/artifacts/DOMAIN_MODEL_*`. `roles/TPF_*` and `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` are **project examples, not canon** — never a default for a new project.

---

## 4. Anti-hallucination rules

- Use only references that exist in the repository. A path that does not resolve is a defect to report, not a gap to fill from memory.
- `[UNDOCUMENTED]` and `[STUB]` are **not** implemented functionality and are never described as working.
- For facts about system behaviour — cross-reference the code, not `ARCH_*.md`.
- For process decisions — rely on `roles/`, not on `docs/artifacts/`.
- Before changing any file, read its current content on disk (Law 12) — not the version remembered from earlier in the session.

---

## 5. Layer responsibility map

| Layer | Folder | Owner | Purpose |
|-------|--------|-------|---------|
| W — working contracts | `docs/artifacts/` | @LEAD / @ARCH | Project decisions of the current wave |
| S — product state | `docs/product_state/` | @SCRIBE / @LEAD | Actual state per code |
| P — process and roles | `roles/` | @LEAD | Universal standards. In a product repository this layer is typically gitignored and does not ship to the client; in the LEO repository itself it is the tracked content |
| Public | `documentation/` | @SCRIBE | Goes to the client |

---

## 6. Maintaining the router (the rule with teeth)

**An unrouted canon is dead text.** It will not be opened, its rules will not hold, and the effort that produced it is lost. Therefore:

1. Any change that **adds, renames, merges or retires** a `roles/*.md` file updates §2 of this file **in the same change**. Not "on the next touch" — in the same change.
2. Any change that adds a **new class of work** adds a `TC-xx` entry with its six-file minimum and its explicit OUT list.
3. A class minimum that grows past **six** files is a signal to merge canons, not to raise the cap (`roles/FRONTEND_CONSOLIDATION.md`).
4. @EVOLVE closes with a router diff. A system upgrade without one is incomplete (`roles/SYSTEM_EVOLUTION_PROTOCOL.md`).
5. **Drift check — owner @LEAD, with a trigger rather than a good intention.** It runs on entry to any `TC-20` task and on any system version bump; "periodically" is exactly how the rule this one replaces died. Two directions: (a) every `roles/*.md` is accounted for — either inside a `TC-xx` minimum/on-demand list, or inside a categorical group §2.1–§2.5 (roles · templates · event-driven · project examples · under review); a file in neither is promoted into a class or retired. (b) every path named anywhere in §2 resolves on disk — a broken path here is the highest-severity defect in the system, because it is the one place the agent is told to trust.

---

Reference: `.cursorrules` (TASK ROUTING · ABSOLUTE LAWS) · `roles/ENGINEERING_PLAN.md` · `roles/FILE_MAP.md` · `roles/SYSTEM_FILES_MASTER.md` · `roles/DOC_TOPOLOGY.md` · `roles/CONFLICT_REGISTRY.md` · `roles/SYSTEM_EVOLUTION_PROTOCOL.md` · `roles/ARCHITECTURE_DOCUMENTATION_STANDARD.md`
Version: 3.0 | 2026-09-03
