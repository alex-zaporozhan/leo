# SYSTEM_UPGRADE_MANIFEST.md
# System upgrade log. Newest first.

---

# UPGRADE v6.37 — "Routing, the model, and the cost" (2026-09-03)

> **One sentence:** the system gained an entry point (every task resolves a class in the router before any role
> acts), the domain model became a law rather than a canon nobody was routed to, effort became a declared and
> countable quantity, and the clean-context audit pass — the strongest verification in practice — became a
> declared object instead of a habit.

## What was added
- **TASK ROUTING block** in `.cursorrules`, before the laws: task → class → the class minimum → LPA → the chain.
  Stated explicitly as a **priority order, not a whitelist**, and carrying the registration rule: a canon that
  is not in the router does not exist.
- **`roles/RAG_CANON.md` v3.0 — the Task Router.** Twenty task classes (TC-01…TC-20 + TC-00 trivial), each with
  its register, a **≤6-file minimum with the sections named**, an on-demand list and an explicit **OUT** list.
  Plus §2.1–2.5: roles read on activation · templates (a form to fill, not a rule) · event-driven files ·
  **project examples that are never a default** (`TPF_*`, `TECH_PASSPORT_FRONTEND_UI_LOGIC`,
  `ARCH_FRONTEND_UI_LOGIC`) · files under review. §6 gives the router a maintenance rule with teeth and a
  two-directional drift check.
- **Law 42 — the model precedes the structure.** `LOGIC_MODELING_CANON` (seven layers, twelve adversaries,
  a terminating cycle) wired into the ROLE MAP, the CHAIN PROTOCOL, @PRINCIPLE ROUTING and Layer P. Duties
  mirrored into `ROLE_ARCH` (structure derived FROM the model), `ROLE_QA_ARCH` (model↔code reconciliation both
  ways — a bug class with no other detector), `ROLE_CREATOR` (first model of the differentiator journey).
- **Law 43 — leverage before effort.** Effort measured in **decisions reopened**: E1 TOUCH · E2 EXTENSION ·
  E3 INTERLOCK (4–6 reopened) · E4 RECONSTRUCTION. Take the lowest tier reaching the declared result; the deep
  pass is refused unless the last 10% *is* the product, the domain is **binary rather than fractional**
  (money/medical/legal/statistics/algorithms), it is **foundation under Law 41** (where this law is silent), or
  the owner asked. E4 is never entered by drift. Declaration format and enforcement: `ROLE_LEAD` §THE MODEL AND
  THE COST.
- **`roles/SECOND_PASS_PROTOCOL.md`** — the clean-context audit: why isolation, not wording, is the mechanism;
  SP-0 interceptor · SP-1 per unit · SP-2 per stage · SP-3 per batch; the role set looked up from the task
  class; **the derivation chain** (what each artifact is checked against — what turns re-reading into checking);
  the false-green catalogue FG-1…FG-12; the boundary that stops a pass from silently reconstructing.
- **`ROLE_FRONTEND` v3.0** — ACTIVATES_CANONS header (router first, then the register canon); Tier 0 replaces
  "Linear-grade" as the standard; the contour table restated by register; the stack demoted to a class default;
  the `DESIGN SOLUTION` section (glass cards, gradient icons, text gradients) replaced by a four-step
  register-aware procedure; and a new **MOBILE AND PLATFORM COMPOSITION** section — the surface set is declared,
  the responsive matrix has five mandatory answers, `dvh`/safe-area/scroll ownership are non-negotiable, and an
  unspecified narrow viewport blocks the handoff to @DEV.

## What was rewritten
- **Law 21** — from "Jenkins + GHCR by default, do not propose GitHub Actions" to **"CI/CD is a declared
  decision"**: no engine forbidden or mandatory; what stays mandatory is pull-not-rebuild (Law 36), digest
  addressing, and the centralised config contract. The violation is an undeclared contour, not a choice of tool.
- **QA_ARCH visual gate** — now asks for the REGISTER before applying the instrument checklist, and marks the
  checklist's concrete values as superseded by the project design passport wherever it is filled.
- **`ROLE_QA_VISUAL` viewports** — from a fixed four to the project's declared surfaces, with the four as default.

## Conflicts resolved
C9 (which visual checklist applies to which surface) · C10 (where the viewport set comes from) ·
C11 (project world outranks the golden library) — see `roles/CONFLICT_REGISTRY.md`.

## Known open after this upgrade
Dead references (`TEMPLATE_ERP_REPORTING_VITRINES`, `CRYSTALS`, five more) · X-numbering divergence between
`CRAFT_LINT_SPEC` §2 and `VISUAL_CRAFT_CANON` §9 · I1–I12 vs ST1–ST12 residue in `CRAFT_LINT_SPEC` §3 and
Law 39 · the duplication policy contradiction between `CONFLICT_REGISTRY` and `FRONTEND_CONSOLIDATION` ·
no craft reflex yet (frontend has R1 routing, not R2 self-check) · no mobile composition canon and no mobile
task class · Laws 19/25 still name the golden library without Tier 0 · `FRONTEND_DESIGN_EXCELLENCE` §6 values
still written as absolutes.


## Second wave of v6.37 (same upgrade, later the same day)

- **Law 19 rewritten** — from "Design is a decision, not taste" (which carried a brand list) to **"The frontend is edited from decisions, not from the screen"**: @DEV opens world → passports → `DESIGN_SPEC` → component registry before touching UI; a UI change with no decision behind it is an **undeclared design decision**; several worlds per product are explicit, and carrying one surface's skin onto another is a defect.
- **Law 25 rewritten** — from a second copy of Law 19 plus a second, differently-composed brand list, to **"Design is produced in an order, and the order is the quality"**: the seven-step chain world → register → typography and spatial grammar → component design → adaptive composition → **motion** → verdict, each step naming the canon that owns its detail. This is also the first law that **owns motion as a discipline** rather than mentioning it in passing.
- **Brand templates removed from the constitution** — the golden-library enumerations are gone from Laws 19 and 25, from the @DESIGN row of the ROLE MAP, and from the @QA_ARCH acceptance checklist. Precedence (Tier 0 = the project world) lives in `roles/ROLE_DESIGN.md`; the constitution points, it no longer prescribes a look.
- **Law 12 extended** — the **diff is the primary evidence surface** (a claim is checked against the change, not the file it landed in), and every report carries a **`NOT DONE:`** line: what was in scope and consciously left, or "nothing declined".
- **Cascade rule** — three levels, not three competing lists: laws → task class (`RAG_CANON` §2) → the acting role's own **reading map**. The more specific section pointer wins; a role may add a file the class does not name and may not drop one it does. Stated in both `.cursorrules` and the router.
- **Role sets are defaults, never permission lists** — written into the cascade rule and into `SECOND_PASS_PROTOCOL` §4. Composition of roles stays the developer's instrument; no table excludes a role.
- **READING MAP sections** in `ROLE_DESIGN` (7 design task types) and `ROLE_FRONTEND` (7 frontend task types) — each row: what to open in what order, with **section pointers**, plus an explicit *not in scope* column.
- **Product invariants** — new class and artifact `PRODUCT_INVARIANTS_[PROJECT].md` (`LEAD_PRODUCT_LOGIC_EXCELLENCE` §7): statements about the shape of the product that no schema holds and no per-screen gate sees. Checked by @QA_ARCH every audit and at SP-2/SP-3; countable, not judged.
- **False-green counter** — `SECOND_PASS_PROTOCOL` §6 now tallies hits per shape; **three hits promote the shape to an `@EVOLVE` candidate**, closing incident → rule.
- **@LEAD fitness gate `A0`** — three questions on an unplanned feature (whose scenario · where is the home · what do we remove), with routing by the answers. @CREATOR is reserved for a positioning change, not a button.
- **@ARCH ADR discipline** — an ADR is born only from a decision with a spine vertebra or a model layer behind it; anything else is a task-report line.

**Known open after the second wave:** seven dead `roles/*.md` references (`CRYSTALS`, `TEMPLATE_ERP_REPORTING_VITRINES`, `DEPLOY_VPS_RUNBOOK`, `DEPLOY_VPS_STEP_BY_STEP`, `RUN_SERVICES`, `MIGRATION_UPGRADE`, `roles/README`) · X-numbering divergence between `CRAFT_LINT_SPEC` §2 and `VISUAL_CRAFT_CANON` §9 · I1–I12 vs ST1–ST12 residue · `ACTIVATES_CANONS` present in 8 roles of 22 · no craft reflex · no mobile composition canon · no `PROJECT_CLASS` · batch protocol still a draft.


## Third wave of v6.37 — repairs found by a clean-context audit of the second wave

The second wave was audited from an empty context window (the mechanism `SECOND_PASS_PROTOCOL` describes). It returned **44 findings against work that had just been reported as done** — the strongest available evidence that the isolation is the active ingredient, not the wording. Repaired:

**Contradictions between new and existing laws.** Law 43 was silent about Law 13 — an improvement seen mid-task is now explicitly made *inside* the declared tier, and one that would raise the tier goes through a Law 23 objection instead of silently. Law 19 skipped the REGISTER step that Laws 25 and 33 make first, and gave @MOTION only the public site while Law 25 and the chain also give it operational MICRO. The `foundation` list was duplicated in Laws 41 and 43 with different wording — 43 now points to 41 and keeps no copy. The DESIGN GATE still demanded "the main reference (a concrete product + screen)", which the rewritten 19/25 and the acceptance checklist now refuse — replaced by Tier 0, and the fourth mode RESKIN added with its output.

**Rules without a place of execution.** Law 42 called @QA_ARCH the only detector of an entire bug class and that detector was absent from the QA_ARCH checklist — added, together with a release-contour line that gives the rewritten Law 21 the owner it lost. Law 25 gained owners and a named failure ("a skipped step is a finding with a name"). Law 21 gained Owners. The `NOT DONE:` line was required of "every task report" while living only in @LEAD's footer — it is now in @DEV's report shape alongside `EVIDENCE:`.

**Uncountable thresholds.** The effort tiers had a numeric gap (nothing covered 1–3 reopened decisions) and the stop rule "twice the declared tier's scope" could not be computed from a range. The scale is now continuous (E2 = 0 reopened · E3 = 1–6 · E4 = >6 or the set rewritten) and the stop is a count: two past the upper bound. Percentages were demoted to illustration; the tier is the deciding unit.

**Ghost artifacts.** `PRODUCT_INVARIANTS_[PROJECT].md` was read by three roles and created by none — @CREATOR now opens it with the package, and @QA_ARCH treats its absence as a finding rather than an N/A. The false-green counter was unusable: one stub row for twelve shapes, kept in `roles/` where Law 16 forbids per-project writing, with no increment trigger — the tally moved to a per-project `FALSE_GREEN_REGISTER.md` and the increment became a line in CLOSURE. `§Surfaces` was outside the passport's own filling instructions and unknown to the @ARCH who confirms it — both fixed, and iOS/Android split into separate rows since they are separate surfaces.

**Cascade violated at the moment it was introduced.** Both READING MAPs dropped canons their class names (`LAYOUT_INVARIANTS` and `ASYNC_WORKERS_CANON` from the canvas row, `LAYOUT_COMPOSITION` from the public row) — restored. The maps spoke in prose while the router speaks in `TC-xx`, with no join: a class-join line added to each. A class for **operations** (release contour · deploy · performance · licensing) did not exist at all, so @OPS, @PERF and @LAWYER work resolved to no class — **TC-21** added.

**Broken text.** The opening sentence of TASK ROUTING — the most-read sentence in the file — had been mangled into an unparseable clause and is rewritten as two. `SECOND_PASS` §3 carried an unnamed reference to a "batch protocol queue doctrine" that does not exist in the repository; the doctrine is now stated in place. `LEAD_PRODUCT_LOGIC_EXCELLENCE` had §7 physically before §6. `CONFLICT_REGISTRY` C2 quoted law text that this upgrade had deleted. The live component registry had three different addresses across four files; one address now.

**Still open:** seven dead `roles/*.md` references · X-numbering divergence (`CRAFT_LINT_SPEC` §2 vs `VISUAL_CRAFT_CANON` §9) · I1–I12 vs ST1–ST12 residue · `ACTIVATES_CANONS` in 8 roles of 22 · `CRAFT_REFLEX` · `TENANCY_REFLEX` and the tenant passport · a mobile composition canon · `PROJECT_CLASS` · promotion of the batch protocol · GATE MATRIX in `LEAD_PRODUCT_GATE_PROTOCOL` does not yet list A0/A/B.


## Fourth wave of v6.37 — system rectification

Five independent clean-context audits ran against the whole system along different axes: the lifecycle as a state machine · role-to-role interaction · law-level simultaneity · reference and namespace integrity · duplication. They returned roughly **110 findings**, most of them older than this upgrade. Repaired in this wave:

**A precedence rule for the laws — the largest structural gap in the system.** 43 laws act simultaneously and most of them can block, and until now exactly one pair had a declared winner. A `## LAW PRECEDENCE` block now opens the constitution, before Law 1: safety and irreversibility (38 · 40 · 27 · 32) → truth about the current state (12 · 36 · 4; a law whose input is unproven does not apply yet) → **stopping beats proceeding**, with every stop funnelled into the single Law 23 objection format and one queue (16 · 37 · 41 · 43) → the specific narrows the general **inside its declared scope only**, and a general law with no scope clause is amended through `@EVOLVE` rather than reinterpreted in the moment → otherwise the later, more specific law wins and the pair is recorded once in `CONFLICT_REGISTRY`. Plus two standing obligations: every law names an owner and a gate, and a resolution between two laws lives in one place.

**Law 16 protected a file that does not exist.** It listed `.cursorrules.md` — the real file is `.cursorrules` — and covered 36 files of 122. It now covers the constitution itself and the whole `roles/` directory: the boundary is the directory, not a list that drifts.

**Law 26 blocked the craft Law 33 requires.** Geometry invariants carried no register clause, so deliberate overlap and bleed — canonical `statement` technique — measured as 🔴. A register clause now allows them for **non-interactive** elements while keeping what never inverts: no two interactive boxes intersect, no horizontal overflow, contrast in every state, reachable targets.

**`MOTION_LIBRARY` contradicted the law that binds it.** The mandatory arsenal contains morph, blur, clip-path reveals and size transitions — all forbidden by Law 26 in the document flow. A boundary note now scopes them to motion islands with reserved space, and to `statement` surfaces for the heavier ones: listing a technique is not permission to use it in flow.

**Law 19 gave a second answer to "there is no concept"** where Laws 25 and 33 already had one. Routed by register: `instrument` takes THE FLOOR verbatim and continues; `statement` stops and requests the world.

**113 broken references repaired in that wave** — 7 dead `roles/*` targets across 21 mentions, and 89 legacy `docs/<file>.md` paths pointing at files that moved into `roles/` years of edits ago. Zero dead `roles/*` references remain.

**Detector namespaces.** `X1–X12` diverged in five of twelve numbers between `CRAFT_LINT_SPEC` §2 and the canon it claims to project (`VISUAL_CRAFT_CANON` §9) — renumbered to the canon, with the two genuinely missing machine checks added (icon set and stroke consistency · `tabular-nums`) and the two that were never X-codes moved out of the series. The stiffness detector still carried its pre-rename identity `I1–I12` in `CRAFT_LINT_SPEC` §3 and in Law 39, colliding with the live `I4` ("undo instead of confirm") — now `ST1–ST12` everywhere. And because `C`, `G`, `T`, `S`, `A`, `E` and `X` each mean different things in different canons, LAW PRECEDENCE now requires every detector code to be cited **with its canon**: an unqualified `C1` is a guess, not a finding.

**Ghost artifacts given owners.** GATE-1 did not require `DOMAIN_MODEL_[MODULE].md` — the central artifact of Law 42 — nor `QA_TEST_STRATEGY_[MODULE].md`; both are blockers now. `FALSE_GREEN_REGISTER.md` is opened by @LEAD at the first matching pass. The "wave ledger" had no name: `docs/artifacts/waves/[N]/WAVE_LEDGER.md`, owned by @LEAD.

**`CAPABILITY_MAP` could not exist on a greenfield module** while being a hard blocker before @DESIGN — a genuine deadlock. A greenfield clause derives the map from the domain model and the spine with rows marked `PLANNED`, converting to `VERIFIED` at the first @QA_ARCH pass; a row still `PLANNED` then is a finding.

**One opening order.** `ROLE_LEAD` declared two different sequences for its own gates in the same file. Canonical now: **A0 fitness → 0.5 LPA → 0.7 model (Law 42) → 0.8 foundation (Law 41) → cost (Law 43)** — cost last, because the tier cannot be known before the model and the foundation question are answered.

**Both sides of every contract.** The audit found 17 of 20 role-to-role handoffs written only in the file of the role that *demands* them: `ROLE_DEV` knew nothing of `DESIGN_SPEC`, the Component Map, Class A/B findings, `PIPELINE_PASSPORT`, the RAG passports or `MEDIA_PASSPORT`, while four roles addressed obligations to it; `ROLE_ARCH` knew nothing of @PRINCIPLE, who precedes it in the chain. Both now carry `RECEIVES` tables (artifact · from whom · what you must do · **what to do when it is missing**) and `RETURNS` lines, and the ROLE MAP makes the pattern normative for the rest: *an obligation written only in the file of the role that demands it is a wish, not a contract*.

**Known open after the fourth wave:** the remaining ~19 roles still need `RECEIVES`/`RETURNS` and `ACTIVATES_CANONS` (a per-role list is prepared) · @MEDIA_ENGINEER is absent from `ROLE_LEAD` · @PERF names no return address · `ROLE_LAWYER` delegates to a role `@PRE` that does not exist · `DEV_PROMPTS` has three declared addresses across three files · seven broken `§`-references and a citation of a non-existent `AW-14` · Law 5 (positive phrasing) is contradicted by most laws and has no owner · Laws 5 · 13 · 17 exist only in the constitution and nowhere in `roles/` · `CRAFT_LINT_SPEC` and `QA_VISUAL_AESTHETE_SENSOR` are written in Russian while the rest of the layer is English · `CRAFT_REFLEX` · `TENANCY_REFLEX` · a mobile composition canon · `PROJECT_CLASS` · promotion of the batch protocol.


## Fifth wave of v6.37 — contracts, routing law, and the language of the layer

**Both sides of every contract, across all 23 roles.** The rectification audit found 17 of 20 role-to-role handoffs written only in the file of the role that *demanded* them. Every role now carries **`ACTIVATES_CANONS`** (what to open on activation, in order), **`RECEIVES`** (artifact · from whom · what you must do · **what to do when it is missing**) and **`RETURNS`** (artifact · to whom · verdict form). The links that had no second side are now named on both: @DEV knows `DESIGN_SPEC`, the Component Map, Class A/B findings, the job and pipeline passports, the RAG passports and `MEDIA_PASSPORT` · @ARCH knows @PRINCIPLE, the semantic core and the "backend must GROW" requests · @QA_VISUAL knows that `MICRO_SPEC` is what its V7/V8 measure · @PENTEST knows @AUDITOR escalates to it · @SEC and @OPS know that @SEO TECH is a deploy blocker peer to @PENTEST · @DOMAIN_EXPERT knows its routes are the input to the domain model and the place where regulatory requirements enter · @PERF now has a return address at all.

**Law 5 — two rejected drafts before the right one, and the reason is the finding.** The audit reported Law 5 as the deadest rule in the constitution: no owner, no gate, "33 of 43 laws violate it". Both rewrites that followed made it worse. The first, *"constructive routing"*, collided with the system's own loaded term (TASK ROUTING · the Task Router · the per-role ROUTING sections) and ran to 1419 characters against 167 for Law 8. The second, *"name the next action"*, was shorter and still wrong — it was a **procedure**. **The real defect was in the diagnosis, not the law.** Law 5 is a **register law**: it does not describe a step, it sets the register every other rule is written in. It has no owner and no gate because it cannot have one, and the metric "33 laws violate it" counted negation words in laws — 38 and 40 — whose nature is prohibition. Restored close to its original brevity, in the owner's formulation: *a statement of what to do and how outranks a caution about what must not be done; a warning with no call to action is noise.* LAW PRECEDENCE now names the distinction, so the next audit does not "repair" it the same way: **procedural laws take owners and gates; register laws are made sharper and shorter, never given machinery.** A later pass then added the discriminator the law was missing — a warning carrying **basis · consequence · proposal** is a professional objection (Law 23 gives it its form), and noise is what remains when any of the three is absent. **Silence stayed with Law 13, deliberately:** withholding a known improvement is not a phrasing failure but quality seen and not acted on, and the objection Law 23 shapes is a sentence, never the deliverable. The three laws now divide cleanly by axis — **5 the register · 23 the form · 13 the action** — and Law 23's citation of Law 13 for silence, which an audit had flagged as broken, is correct exactly as written.

**Laws 13 and 17 operationalised.** Law 13 ("quality stops speed") had no owner and no gate for its whole life; it is now verified through the report — an improvement made appears as what changed, an improvement **seen and not made** appears in the `NOT DONE:` line, and silence about a seen improvement is the violation. Law 17's `[STUB]` had no register, so a stub was forgettable by design; every stub is now recorded in the wave ledger with what it fakes, what would make it real and who decides, and an unregistered stub is 🔴 even when correctly marked in code.

**The layer speaks one language.** `CRAFT_LINT_SPEC.md` and `QA_VISUAL_AESTHETE_SENSOR.md` were written in Russian while the other 127 files were English — and one of them is the machine floor that Law 39 makes blocking. Both fully translated with structural parity verified (line counts, every detector id, every threshold, every code fence byte-identical, emoji markers counted). `LIBRARY_TRANSFER_CRAFT_CANON.md` carried Russian product strings and one client's ADR numbers: strings translated, and a reading note marks the concrete routes and `ADR-0xx` references as one delivered product's example, never defaults for another project.

**Phantoms and misroutes closed.** `@PRE` — a role referenced twice by @LAWYER and existing nowhere — replaced by the real owners (@DOMAIN_EXPERT for the regulatory picture, @BIZ for the KILL SIGNAL). `AW-14` was cited as a law in a series that ends at AW-13; the rule belongs to `DATA_INTEGRITY_CANON` §3 and now says so. Seven broken `§` references repaired against the real section structure (`METRICS_PROTOCOL` has §2.1/2.2 and §3.1/3.2 — not the §2.4 and §3.3 three files cited). `DEV_PROMPTS` had three declared addresses; the binder has one, and a wave file may index it but is never a second binder. **@MEDIA_ENGINEER existed in the constitution, had a detailed role file, and was absent from the orchestrator** — @LEAD now carries its trigger, its mode requirement and the stop condition when a hero needs plates and no passport exists.

---

# UPGRADE v6.35 — "Production-readiness & drift cleanup" (2026-07-23)

> **One sentence:** the system now builds production-grade products by default (Law 41 — "MVP" is a delivery
> schedule, not a quality level; foundation complete / delivery phased; raw planning = a `# TODO` in the
> foundation), and the long-standing cross-file conflicts were resolved to single winners.

## What was added
- **Law 41 (production-readiness by default)** in `.cursorrules` + two canons: `roles/PRODUCTION_READINESS_CANON.md`
  (the bar) and `roles/PLANNING_MATURITY_CANON.md` (the self-audit loop DRAFT→RED-TEAM SELF-PASS→REFINE, the
  Completeness Ledger + spot-check, the three criteria responsibility/foresight/completeness).
- **FORESIGHT / COMPLETENESS GATE** in `ROLE_LEAD` (+ CHAIN step 0.8, + GATE-0/GATE-1 blockers in
  `LEAD_PRODUCT_GATE_PROTOCOL`): production rollup + ledger before @ARCH; concept lock before @MOTION/@DESIGN;
  foundation-touch trigger; rework-cost REFLEX.
- **`roles/CONFLICT_REGISTRY.md`** — single winners for C1–C8, all applied:
  C1 Law 33→38 (security numbering) · C2 @DESIGN scope = new pattern · C3 confirm/undo by action class ·
  C4 palette precedence (no project hex in universal roles) · C5 stiffness S1–S12 → ST1–ST12 (security S1–S12 kept) ·
  C6 aesthete catalogue A–H · C7 @SEC advisory / @PENTEST blocking · C8 SEO TECH on par with @PENTEST.
- **QA_ARCH Vector 19 (UI Contract & List Semantics)** — pagination/DST/concurrent-edit/partial-failure/
  permission-UI/CSV-injection/focus/skeleton/… (distinct from CRAFT_LINT V19 Toy-Graph).
- **ACTIVATES_CANONS** headers in LEAD/CREATOR/BIZ/ARCH/PRINCIPLE/DESIGN/MOTION.
- **Multi-wave foundation** note (@ARCH), **Foresight forward-questions** (@PRINCIPLE), production completeness
  rollup (@CREATOR/@BIZ).

## Applied (2026-07-23, cont.)
- **ENGINEERING_PLAN.md full rebase** (v3.0/v6.13 → v4.0/v6.35): state machine, transitions, escalation, Quality Gate and folder-structure resynced to current roles/gates (VISUAL CONCEPT · @SEO CORE · @MEDIA_ENGINEER · FORESIGHT/COMPLETENESS gate · @PRINCIPLE MODE:MODEL · @PENTEST S-0/S-Wave/S-Global · Law 40 publish-boundary). STALE header removed; carries an authority-note (SoT = `.cursorrules` CHAIN; a semantic mirror).
- **§5.2 PENTEST deep detail:** `ROLE_PENTEST` v2.1 NAMED LEAVES (30 greppable leaves) + `PENTEST_SCENARIOS` v1.1 §§10–15 (SSRF/CORS/file/IDOR-matrix/webhook/refresh-session) + `SECURITY_GATE_PROTOCOL` §3 v1.1 extended Security Contract (+ semantic mirror in ROLE_PENTEST MODE 0).
- **§5.3 DEV:** CONTRACT SHEET gains conditional mandatory blocks (FRONTEND CONTRACT / LIST-QUERY BOUNDS / MIGRATION SANITY / SECURITY CROSS-REF by leaf id) + a 15-recurring-mistake catalog mapped to sheet blocks (`ROLE_DEV`).
- **§5.4 DESIGN:** 7 mandatory DESIGN_SPEC subsections (Responsive Matrix · Intermediate states · i18n & overflow · Permission matrix · Motion detail · Focus & keyboard · List UI), blocking (present or N/A+reason); semantic mirror State Spec ↔ QA_ARCH Vector 3/19 (`ROLE_DESIGN`). **All §5 deep detail closed.**

## Known remaining (next waves)
- Phase 4 mirror-markers (incl. three `semantic` mirrors: Security Contract, routing graph, state vocabulary), Phase 5 hygiene (TPF move; **ROLE_DESIGN at 44.5 KB → offload candidate to FRONTEND_DESIGN_EXCELLENCE**), Phase 6 coda evidence-audit on a live project.
- TEMPLATE_BIZ_LOGIC.md `[FILL IN]` reconciliation check.
- Project `.cursorrules` overlays are pre-v6.35 snapshots (validation copies) — intentionally not synced.

Plan of record: `roles/SYSTEM_UPGRADE_MANIFEST.md`.

---

# UPGRADE v6.25 — "Pipeline Resilience" (2026-07-08)

> **One sentence:** real incident #2 (RAG ingest: rate limiter held itself full, retries bypassed it,
> heartbeat revived zombies, the task lifecycle had three time zones) was broken down into laws —
> these defects can no longer be committed because @DEV receives them resolved in the passport BEFORE code.

## What was added (ASYNC_WORKERS_CANON upgraded to v2.0 + addenda; no new files)
| Where | What |
|-------|------|
| `ASYNC_WORKERS_CANON.md` v2.0 | PART II: §9 incident #2 breakdown (9 defects D1–D9 → laws) · §10 AL-11 lease model (single clock, one alive predicate, reclaim ≤ TTL/2, recovery window as a number, **heartbeat = progress**, deadline on every await) · §11 AL-13 rate limiter at the wire (atomic check-and-consume, failure does not consume capacity, pacing default, concurrency = budget) · §12 AL-12 single retry owner + cross-cutting taxonomy (broad catch = AP-18) · §13 **PIPELINE PASSPORT** with template + "five questions before a pipeline" · §14 tests T-I1…I6 and AP-15…20 |
| `.cursorrules` | Law 30 extended with v2.0 block; PIPELINE_PASSPORT artifact in Layer W |
| `ROLE_ARCH` | "five questions before code" — mandatory pipeline entry; passport before @DEV |
| `ROLE_DEV` | external call checklist: rate limiter on every attempt, narrow catches, timeout on every await, T-I1/I3/I5 locally |
| `ROLE_QA_ARCH` | Liveness/Integration vector: greps AP-15…20, number cross-check passport↔config |
| `ROLE_PENTEST` | series T-I1…I6 (including mock-hung provider — regression D1) |

## Measurably changed
Rate limiter failure does not consume capacity (T-I1 as number) · retries do not break budget (T-I2: RPS ≤ limit) ·
dead worker releases slot within declared recovery window (T-I4) · zombie with live heartbeat
detected by stuck predicate (T-I6) · "which retry levels are disabled" — a passport line, not log archaeology.

---

# UPGRADE v6.24 — "Integrity Under Concurrency" (2026-07-07)

> **One sentence:** workers stopped hanging in v6.22 — now data stops lying too: every business invariant
> is protected by a layer that cannot be bypassed, not just an `if` in code.

## Diagnosis in one line
Double booking, oversell, duplicate payment, overwritten edits, and cross-tenant leaks — the entire class
"system is live but data lies" had no rules: invariants lived in code checks that fail under concurrency.

## New file (`roles/`)
| File | What it does |
|------|-------------|
| `DATA_INTEGRITY_CANON.md` | Protection hierarchy (§1: DB schema > lock/version > code hint); race catalog with SQL recipes (§2); transaction discipline (§3); Idempotency-Key + webhook inbox (§4); money as minor integers (§5); time as UTC/[start,end)/branch TZ (§6); tenant isolation by schema (§7); soft-delete and audit (§8); **INVARIANT LEDGER** in ADR (§9); race tests T-H1…H7 (§10); anti-patterns (§11) |

## Integrations
`.cursorrules`: **Law 32** + Layer P · `ROLE_ARCH`: ledger required before code, schema rules §5–§8 ·
`ROLE_DEV`: data checklist + local T-H run · `ROLE_QA_ARCH`: Data-Race vector (check-then-act,
float-money, naive-time, tenant-scope, ledger↔migration cross-check) · `ROLE_PENTEST`: T-H series in CRASH_TEST
(T-H5 — isolation IDOR matrix) · `MIGRATIONS_PLAYBOOK`: constraints on live tables (NOT VALID→VALIDATE,
CONCURRENTLY, tenant retrofit) · registries FILE_MAP/RAG_CANON.

## Measurably changed
Double booking/oversell impossible by schema (T-H1/H2 — exactly N successes as a number) · POST repeat is safe (T-H3) ·
edits are not lost (T-H4: 409 instead of overwrite) · tenants are isolated by matrix (T-H5: 0 leaks) ·
money does not float · every invariant is visible in ledger ADR.

---

# UPGRADE v6.23 — "Decision Backbone" (2026-07-07)

> **One sentence:** architectural knowledge was complete but scattered across six files and was not enforced
> at decision time — now every decision pulls them into 12 vertebrae AS NUMBERS before code, and the last two
> gaps (synchronous path timeouts and complexity discipline) are closed by sections §3–§4.

## Diagnosis in one line
DOMAIN_STANDARDS/EXCELLENCE_PASSPORT/SYSTEM_DESIGN knew about tenancy, idempotency, RBAC, money, UTC —
but nothing forced @ARCH to pull them into a specific ADR (hence "prolonged fine-tuning"); outbound call
timeouts were mentioned nowhere (0 matches across the system); "when to split, when not to" lived as taste, not numbers.

## New file: `roles/ARCH_SPINE_PROTOCOL.md`
12 vertebrae (tier · SLO · tenancy+leak test · timeouts for all dependencies · idempotency ·
constraints-first · additive-only contracts · JOB_PASSPORTS · capacity · failure/radius · DR with
restore-test date · STRIDE sketch) · **Complexity Ladder 0–4** with numeric trigger thresholds (anti-overengineering:
"future-proof" = 🔴, downgrade is legitimate) · **Timeouts/deadlines** (§4: default table, deadline propagation,
retry budget ≤1, grep-invariant T5) · one-page artifact template.

## Integrations
`.cursorrules` Law 31 + Layer P/W · `ROLE_ARCH` (STEP 0 += 4 lines; backbone with ADR; ladder — movement law;
timeout table → project config) · `ROLE_QA_ARCH` Spine vector (completeness, ladder as number,
grep T5, tenancy leak, restore-test freshness) · `SYSTEM_DESIGN_PROTOCOL` (steps map to vertebrae 8–10) · registries FILE_MAP/RAG_CANON.

## Measurably changed
No outbound call without a timeout (grep-🔴) · microservice/Kafka appear only with a numeric trigger ·
every decision has a page where tenancy, DR and contracts are answered before code · restore-test — with a date, not faith ·
@PENTEST receives vertebrae 3/11/12 as a target map.

---

# UPGRADE v6.22 — "Async Contour by Contract" (2026-07-06)

> **One sentence:** queues stopped being "put the function in — hopefully it runs": every task gets
> a passport-contract, management is separated from work, and the "task-supervisor" deadlock became
> impossible by construction.

## Diagnosis in one line
The system could choose a broker (DATA_STORE §2) and calculate sizes (SYSTEM_DESIGN Step 6, Celery-bias),
but did not define TASK SEMANTICS: cancellation, idempotency, plane separation, bulkhead — the agent
improvised, leading to a real incident (supervisor task in the data queue + closed event loop
with 2 slots = dead queue).

## New file: `roles/ASYNC_WORKERS_CANON.md`
§0 incident breakdown as canonical anti-example → 5 causes → 5 laws · §1 three planes
(data/control/supervision: worker never manages a worker) · §2 laws AL-1…10 · §3 pattern catalog
(cron-scheduler with lock, Chain/Flow, Saga, **Outbox**, external API rate limiter, batch,
dedupe window; BullMQ/Celery mapping; Postgres queue only via SKIP LOCKED) · §4 **JOB PASSPORT** —
task type contract (task cannot be written without a passport) · §5 topology and sizing (worker = separate
container; capacity formula) · §6 metrics/alerts M-ASYNC · §7 crash tests T-A…G · §8 anti-patterns AP-1…12.

## Integrations
`ROLE_ARCH` (ADR async contour + JOB_PASSPORTS_[PROJECT].md before code; lesson-canon) · `ROLE_DEV`
(AL checklist before delivery + self-check "if task waits for a task — stop") · `ROLE_PENTEST`
(CRASH_TEST += T-A…G) · `SYSTEM_DESIGN_PROTOCOL` Step 6 (pointer: without passports the step is not passed) ·
`.cursorrules` Layer P · registries.

## Measurably changed
Incident hang is caught three times: forbidden on review (AP-1), does not pass passport (checkpoints
required), and if it does — stalled detector returns the task (T-D). Task loss/duplication — testable
property (T-A/B/E), not luck. Slow wave no longer blocks fast operations (bulkhead, T-F).

---

# UPGRADE v6.21 — "Visibility by Design" (2026-07-06)

> **One sentence:** a site "worth millions" that does not appear in search results for the client's demand
> does not exist — the system gained an owner of search visibility (@SEO, gate-role modelled on @SEC) and
> a law about showcase rendering.

## Diagnosis in one line
18 roles — and not one owned visibility: no semantics at all (pages were invented from imagination,
not from demand), and the default Vite-SPA stack returned an empty `<div id="root">` to search engines — invisibility by design.

## New files (`roles/`)
| File | What it does |
|------|-------------|
| `ROLE_SEO.md` | Head of SEO: 4 modes — CORE (semantic core + page map BEFORE IA/design), ONPAGE (H-structure/meta/schema — entry to @DESIGN), TECH (deploy blocker for showcase, criterion "curl without JS returns content"), MONITOR (Search Console/Webmaster, M-SEO, quarterly cadence); boundaries with 9 roles; anti-manipulation prohibition is absolute |
| `SEO_CANON.md` | Knowledge: semantic core methodology (5 steps, intents, "1 cluster = 1 page"), **rendering table SSG/SSR** (showcase separate from SPA-app), tech minimum with budgets (CWV field, JS ≤150KB), title/description/H formulas, schema table by niche, local SEO (Google Business Profile, geo pages without duplicates), E-E-A-T, AI search (llms.txt, extractable answers), anti-patterns, monitoring |

## Integrations
`.cursorrules`: **Law 29** + @SEO in ROLE MAP + chain nodes (CORE after concept; ONPAGE in @DESIGN entry;
TECH next to @SEC before deploy; SEO artifacts in Layer W) · `ROLE_ARCH`: addendum — rendering ADR required
BEFORE showcase code · `ROLE_CREATOR`: "demand language ↔ delivery world" exchange in 5.5.A · `ROLE_DESIGN`: SPEC
entry for indexable page += SEO_ONPAGE · `ROLE_DEV`: SEO technical checklist line by line + self-check curl-without-JS ·
`ROLE_LEAD`: routing 4 points + "beauty vs semantics" conflict resolved by intent · registries.

## v6.20 refinements in the same wave
`VISUAL_CONCEPT_PROTOCOL` §2: PASS 1 migrated to anatomy constructor (preset — at ≥6 axes) —
"world choice vs constructor" conflict resolved · `LAYOUT_COMPOSITION` §5 += **G7 form grammar**
(one column, STACK label→field→error with height reserve — error does not shift fields) · `CONCEPT_ANATOMY` §4.2 +=
"competitor output = intent map, but aesthetic ANTI-reference".

## Measurably changed
Pages exist for real regional demand, not from imagination · showcase is readable by bot without JS (gate as number) ·
title/H1 use client words from keyword research · deploy without Search Console/Webmaster/sitemap is impossible ·
positions become metric M-SEO with quarterly cadence, not wishful thinking.

---

# UPGRADE v6.20 — "Concept Before Code" (2026-07-06)

> **One sentence of the upgrade:** taste moved from model weights to files — aesthetics are born once as a
> concept-world with executable recipes, and layout collisions gained an owner-paragraph and a sensor-number.

## Diagnosis in one line
Passports were `[hex]` slots without values → cheap models filled them with defaults (blue/Inter/grey SaaS),
expensive ones — with improvisation at $60–70; references — monoculture Linear/Stripe; Mantine — with factory face;
button collisions belonged to no rule.

## New files (`roles/`)
| File | What it does | Symptom closed |
|------|-------------|----------------|
| `CONCEPT_DNA_LIBRARY.md` | 12 concept worlds as executable recipes: hex palettes, font pairs (Cyrillic tested), CSS effect kits, motion personalities, Mantine skins, archetype affinities + Custom World Constructor + niche→world router | "tasteless colors and buttons", "cannot build a concept for the product meaning", "everything under Linear" |
| `VISUAL_CONCEPT_PROTOCOL.md` | "Concept before code": concept birth by @CREATOR (Step 5.5.A/B), TASTE GATE (cliché blacklist K1–K10 + "remove the logo" test), transfer map to 4 passports, RESKIN mode for @DESIGN, model economics | "concept must be created immediately", "@DESIGN should be able to change concept", "$60–70 per project" |
| `CONCEPT_ANATOMY.md` | Generative core: 8 DNA axes with variant spaces, axis coherence rules, **reference protocol** (format SOURCE→EXTRACT→TRANSFER→NOT TAKE, ≤1 SaaS, source typology), world constructor; 12 worlds reclassified as presets (≥6 axes) | "unclear reference scheme, no criteria for what a reference transfers", "don't want to stop at 5–6 references", "agent must UNDERSTAND what a concept consists of" |
| `LAYOUT_COMPOSITION.md` | Grammar of layout CONSTRUCTION: three space laws (children don't push each other · width from above/height from content · flow is law, absolute is license), algebra of 8 primitives, proximity law ×2, button group grammar G1–G6, overlap diagnosis protocol | "keeps messing up layout", "buttons, micro-bugs, one thing overlaps another" — overlap is now impossible by construction, and §12/V12 remain as insurance |

## Integrations (full texts — `roles/INTEGRATION_PATCHES_TASTE.md`)
- **`.cursorrules`:** Law 28 "Concept Before Code"; Law 26 extended with collisions; ROLE MAP (@CREATOR +VISUAL CONCEPT, @DESIGN +RESKIN/Tier 0, @MOTION +Step 0); Layer P +2 files; chain +concept node. Version 6.20.
- **`ROLE_CREATOR.md`:** Step 5.5 → 5.5.A (concept + TASTE GATE) and 5.5.B (passports by derivation, no `[hex]` placeholders); summary and handover extended.
- **`ROLE_DESIGN.md`:** MODE 4 RESKIN; Tier 0 "project world" + Tier 1.5 "references beyond SaaS"; showcase SPEC entry requires VISUAL_CONCEPT; addendum v6.20.
- **`FRONTEND_DESIGN_EXCELLENCE.md`:** new §8 MANTINE DE-BRANDING (createTheme from world, cssVariablesResolver, `theme/effects.css` layer, underutilised Mantine power map, showcase right to headless); pointer in §5; gate §6 +3 items.
- **`LAYOUT_INVARIANTS.md`:** new §12 COLLISION & STACKING (Z-scale, anchor positioning, interactive distance, deterministic criterion) + @DEV/@QA_VISUAL checklists.
- **`ROLE_QA_VISUAL.md`:** vector V12 (pairwiseIntersection == 0 px² · zIndexAudit · fixedCoverage) — in sensor core; concept-conformance in verdict.
- **`ROLE_MOTION.md` / `MOTION_AMBITION_DIAL.md`:** CONCEPT Step 0 — inheriting world motion personality; ban on easing degeneration (6-personality dictionary); linear — only restrained operational mode.
- **`ROLE_LEAD.md` / `ROLE_FRONTEND.md` / `ROLE_DEV.md` / `ROLE_QA_ARCH.md`:** addenda v6.20 — concept and RESKIN orchestration, Z-scale and de-branding ownership, §12 checklist, concept-conformance in gate.
- **Header pointers:** `DESIGN_DECISION_LIBRARY` (values → world), `PROJECT_VISUAL_BOOTSTRAP_PROTOCOL` (Step 0), `HERO_ARCHETYPES` (world affinity).
- **Registries:** `FILE_MAP`, `RAG_CANON`, `FRONTEND_CONSOLIDATION` (source map +2 lines).

## Deployment order
1. Two new files in `roles/`. 2. Collisions (§12 + V12) — independent, fix "overlapping buttons" immediately. 3. Concept layer (other integrations). 4. Pilot: one showcase through full cycle CONCEPT → passports → MOTION → SPEC → DEV → V1–V12.

## Measurably changed
- Palette/fonts stop being a lottery: copied from world recipe, replacement = one line with reason.
- "Everything under Linear" lifted: Tier 0 = project world; SaaS library — only operational patterns.
- Buttons stop overlapping: `pairwiseIntersection == 0 px²` — rule with an owner (§12) and a sensor (V12), not "fix it by eye".
- Mantine stops being a face: criterion "screen is not identified as Mantine docs" is in the gate.
- Character change = RESKIN by Change Map (skin), skeleton and baseline discipline preserved.
- Economics: strong model — 1–2 calls (custom world/FUSION); rest assembled by cheap models from recipes.

---

# UPGRADE v6.16 — "Closing the Frontend Control Loop"
# What was added, how it was integrated, in what order to install, what remains your decision.

> **One sentence of the upgrade:** the system gained its missing sense organ — the render sensor — and
> deterministic geometry rules that it measures; plus an "ambition axis" (archetypes + ambition dial)
> and an owner of micro-moments on operational screens.

---

## WHY (diagnosis in one line)

Before the upgrade the system **managed the recipe, not the dish**: backend — closed loop (code/SQL/tests
observable → stable), frontend — open loop (visual "gates" checked tokens, not rendered geometry; agent
self-signed them without rendering). Hence: jumping cards, "house of cards" (4–8 passes), buggy buttons,
Linear-hero monoculture, constrained motion, pre-production passing itself off as done.

The upgrade closes the loop and separates two axes: **stability** (geometry) and **ambition** (expressiveness).

---

## NEW FILES (placed in `roles/`)

| File | What it does | Symptom closed |
|------|-------------|----------------|
| `ROLE_QA_VISUAL.md` | Render sensor: Playwright harness, measurement tools, vectors V1–V10, baseline anchor, debug cycle. New mandatory gate after @QA_ARCH for any UI. | "pre-production passes itself off as done"; absence of visual feedback in principle |
| `LAYOUT_INVARIANTS.md` | 10 deterministic layout rules (equal-height, reserved height, min-width, aspect-ratio, zero-shift, no-layout-animation) + text of Law 26. | "jumping cards", "house of cards", "buggy buttons" |
| `HERO_ARCHETYPES.md` | 8 hero archetypes + selection protocol (Q1–Q3). Removes the "text left + mockup right" default. | "Linear everywhere", "hero = text+card", "text in hero is a blind spot" |
| `MOTION_AMBITION_DIAL.md` | Ambition dial (input to @MOTION CONCEPT, default `confident`) + MICRO mode (micro-moments for /admin·/app). | "constrained motion", "3D always last", "buttons bug out, nobody owns micro-moments" |
| `INTEGRATION_PATCHES.md` | 14 targeted integrations into existing files (copy-paste with anchors). | makes all of the above alive in the system |
| `SYSTEM_UPGRADE_MANIFEST.md` | This file. | upgrade navigation |

---

## AFFECTED FILES (via `INTEGRATION_PATCHES.md`)

| File | Patch | Integration summary |
|------|-------|---------------------|
| `.cursorrules` | 1 | Law 26; @QA_VISUAL in ROLE MAP; @MOTION +MICRO/ambition; chain +@QA_VISUAL; Layer P +4 files; QA_ARCH GATE delegates render-geometry; version 6.16 |
| `ROLE_QA_ARCH.md` | 2 | Vector 6: separation "code level (me) vs pixel level (@QA_VISUAL)" |
| `ROLE_DESIGN.md` | 3 | SPEC showcase — archetype from HERO_ARCHETYPES; Pixel Decisions — stability per LAYOUT_INVARIANTS |
| `ROLE_MOTION.md` | 4 | 5th mode MICRO; Step 0 Ambition; Step 2.5 Archetype |
| `ROLE_FRONTEND.md` | 5 | Pillar 9 +LAYOUT_INVARIANTS; Visual Quality Gate extended with @QA_VISUAL render check |
| `ROLE_QA.md` | 6 | requires 🟢 @QA_VISUAL before final UI pass |
| `ROLE_AUDITOR.md` | 7 | receives symptom from @QA_VISUAL; typical causes + layout invariant violations |
| `LEAD_PRODUCT_GATE_PROTOCOL.md` | 8 | GATE-4 + @QA_VISUAL blockers; release DoD +R8 |
| `ROLE_LEAD.md` | 9 | routing: @QA_VISUAL, ambition, @MOTION MICRO, escalations |
| `FILE_MAP.md` | 10 | new files, VISUAL_QA_REPORT/MICRO_SPEC artifacts, frontend/tests/visual |
| `RAG_CANON.md` | 11 | reading order +4 files |
| `ENGINEERING_PLAN.md` | 12 | artifact convention +visual; Quality Gate +@QA_VISUAL |

---

## BEFORE AND AFTER

```
BEFORE:
  @DEV → @QA_ARCH (reads code: tokens, states, mutations) → @QA
        visual "gates" = token checklist, self-signed without render → open loop

AFTER:
  @DEV → @QA_ARCH (code level 🟢)
       → @QA_VISUAL (render level: render→measure→compare; V1–V10; baseline) 🟢
       → @QA → @SEC
        output value (geometry) is measured as a number and diff → closed loop
```

Two axes separated:
- **Stability** — `LAYOUT_INVARIANTS` (rules) + `@QA_VISUAL` (measurement). Deterministic.
- **Ambition** — `HERO_ARCHETYPES` (composition) + `MOTION_AMBITION_DIAL` (boldness). Controlled by an explicit parameter, not inertia.

---

## INSTALLATION ORDER

1. Copy 6 new files into `roles/`.
2. Apply `INTEGRATION_PATCHES.md` Patches 1–12 (by anchors, in order). These are safe additive integrations (Law 1 — only what is touched).
3. Run system self-check (optional, via your `MIRROR_PROTOCOL`): pairs `@QA_ARCH→@QA_VISUAL` (gap: who sees the render) and `@DESIGN→@MOTION` (gap: micro-moments) — confirm gaps are closed.
4. On the first real UI module: create `frontend/tests/visual/specs/[module].visual.spec.ts` and the baseline — `@QA_VISUAL` runs for the first time and anchors the reference.
5. Decide on Patches 13–14 (see below).

---

## LEFT TO YOUR DECISION

| # | What | Recommendation |
|---|------|----------------|
| Patch 13 | Auto-layout of artifacts into sub-folders (ADR/waves/roadmap/qa) | Minor, you correctly deferred it. Enable after frontend stabilisation. |
| Patch 14 | Frontend canon consolidation (tokens duplicated in 5+ files — "textual proliferation") | Low-risk variant: declare `FRONTEND_DESIGN_EXCELLENCE.md` the single token canon + references in the others. Physical file merging — as a separate task with review. Your decision. |

---

## LINK VERIFICATION (completed)

- All `roles/*.md` links in new files resolve to existing system files (74) or new ones (6). **No broken links.**
- Artifact outputs (`docs/artifacts/VISUAL_QA_REPORT_*`, `MICRO_SPEC_*`) and code paths (`frontend/tests/visual/*`) — new conventions, registered in `FILE_MAP` (Patch 10) and `ENGINEERING_PLAN` (Patch 12).
- New files added to `RAG_CANON` (Patch 11) and `FILE_MAP` (Patch 10) — per the `.cursorrules` rule "new file in roles/ → add to RAG_CANON and FILE_MAP".

---

## WHAT WILL MEASURABLY CHANGE IN PRACTICE

- Cards stop jumping: `siblingHeightDelta == 0` on the `longtext` fixture — a rule, not an eye.
- "House of cards" is healed by baseline: a change in a neighbouring component is caught by diff against baseline before the whole screen drifts.
- Buttons stop "running off": `geometryShiftOnState == 0` (V7) + only transform/opacity (V8) — with an owner (@MOTION MICRO).
- Hero stops being one template: archetype is chosen by Q1–Q3, A is only one of eight.
- Motion opens up on request: ambition dial, default `confident`, 3D at `bold+` on equal footing.
- Pre-production becomes honest: visual gate with "fact, not intention" (number/diff), not a self-signed checklist.

---

Version: 6.25 | 2026-07-08
Composition v6.25: ASYNC_WORKERS_CANON v2.0 (PART II) + addenda ARCH/DEV/QA_ARCH/PENTEST + Law 30 v2.0 + PIPELINE_PASSPORT
**v6.38b — the enforcement half. A clean-context pass on v6.38 returned one sentence: *permission was fixed, enforcement was not.***
The first pass rescoped the invariant and wrote the craft canon, then declared the job done from intention rather than from grep. It was not. What the second pass found and this wave fixed:
- **`ROLE_QA_ARCH` carried a 🔴 that REJECTED floor-compliant motion** — "reveal in flow: opacity-only, not translateY" — and it runs *before* @QA_VISUAL, the role that was given the new detector. Everything upstream could now produce good motion and this one line sent it back. Highest-leverage single fix in the wave.
- **Law 39 — the only *blocking* craft law — was named as the cause and never edited.** It enumerated V15–V20 and stopped; V21 and M1–M12 were outside it, so nothing about motion was blocking and a stiff screen passed GATE-4 exactly as before. Law 39 now reads V15–V21 + X/ST/**M**, with the blocking status stated.
- **Ten surviving copies of the losing side**, three of them gates: `ROLE_DESIGN` (SPEC step 4, audit lens 10), `ROLE_MOTION` (the MOTION_SPEC template, and Principle 7 contradicting itself inside one paragraph), `ROLE_FRONTEND` (activation header, rule 14, pre-handoff checklist), `COMPONENT_REGISTRY`, `FRONTEND_DESIGN_EXCELLENCE` ×2. All rewritten.
- **`LAYOUT_INVARIANTS` contradicted itself inside the file that had just been rewritten:** the §10 *heading* still stated the banned reading two lines above the body refuting it; the **reference CSS had ✅ and ❌ inverted** — the 350 ms opacity fade this wave exists to delete was the ✅ example and the floor's own entrance was the ❌ one, in copy-paste form; "why it breaks" still asserted the factual error (`translateY` shifts content); the @DEV checklist still said opacity-only; and the reduced-motion sample clamped everything to `.001ms`, which is the canon's own **M12** verbatim.
- **`MOTION_REFLEX.md` — new.** Motion had no reflex while async has had one for versions. R1–R12: literal greps over the diff (`transition[^;]*opacity` with no transform · `.map(` with no per-sibling delay · fewer than two durations · `ease-in-out` · a scaling panel with no `transform-origin` · `key={status}` forcing remount · an entrance with no exit · layout properties · `.001ms` · a bare spinner · a success with no affirmative moment · tokens invented where a world exists), each with its stop-question, its fix, and the M-sign it catches. Run by @DEV/@FRONTEND before handoff, mirrored at @QA_VISUAL, one required report line.
- **V21 was defined and unrun**: absent from the CI `craft` job, from the ready-to-copy role inserts, and from every "V15–V20" reference in seven files — the role that owns it was nowhere told it exists. Now in all of them, plus a §4.0 role insert and two CI steps.
- **M1–M12 had an instruction to walk it and no artifact to carry the result.** `ROLE_QA_VISUAL`'s report format now holds a V21 metrics row and a twelve-cell M-table under the same enforcement clause the aesthete catalogue uses: every cell 🟢/🔴/⚪, silence = incomplete report = no 🟢.
- **Contradictions inside the new material.** The floor's containment rule (`≤10% of the element's height`) forbade the floor's own 8px entrance on the exact element class it told you to stagger — a 40px table row allowed 4px; now "8px or 10%, whichever is larger". `M1–M12` collided with `M1–M8` moment IDs in `TEMPLATE_MOTION_LANGUAGE` (renamed `PM-xx`, and `M` added to the ambiguous-letter list in the constitution). That template's token file declared **five** durations against the floor's four, and `MOTION_AMBITION_DIAL`'s MICRO catalogue kept hand-typed numbers under a rewritten template — both aligned to the floor's tokens. `MOTION_LIBRARY`'s boundary clause forbade `clip-path` outside an island while the new invariant offers `clip-path` as the sanctioned containment for a word reveal.
- **V21 would have failed a project faithfully implementing a canonical world** (the curatorial world declares a 500 ms opacity-only reveal). A narrow waiver added: the third threshold is waived where a declared world governs the surface **and the world and token are cited**; the first two hold regardless — a world sets the character of motion, never a licence for one duration and one easing.
- **`SECOND_PASS` TC-01** — the operational class, the one this wave says "forgot" motion, had no motion lens on the clean-context pass either. @MOTION and a motion criterion added. **TC-16** now routes M1–M12 directly instead of depending on a one-hop read.
- **`CONFLICT_REGISTRY` C12** claimed "✅ applied everywhere" with ten copies of the losing side still live, and its Affected list omitted four files. Corrected, with the overclaim left visible in the row as the reason its own "re-verify by grep" rule exists. Mirror-index rows added for the motion floor, M1–M12 and R1–R12; `MIRROR SOURCE` markers added to the canon — the new material had been violating the mirror doctrine this same batch made binding.
- **De-branding repairs:** a four-column table left with five cells, a verbless sentence fragment in `ROLE_DESIGN`'s persona, the **public-site** row of the design constitution still naming four products (the most load-bearing instance, untouched by the first pass), a hero summary table reduced to eight em-dashes (now the gesture each archetype commits to), a TPF module header that specified nothing, and `REFERENCE WALK` renamed in one place while four callers still cited the old name.

**v6.38 — MOTION: the discipline that had permission, an arsenal, a role — and no craft.**
The reported symptom was that everything this system animates is "point A, point B, an effect". A clean-context pass found two independent causes that reinforced each other, and three reasons it never self-corrected.
- **A stability invariant written without scope forbade the very thing @MOTION exists to produce.** `LAYOUT_INVARIANTS` §10/§11 was read across eight files as *no `transform` in the document flow*. But a transform does not participate in layout: the element keeps its box, neighbours do not move, `scrollY` does not change. The invariant protects **reflow and the reader's scroll position** — it was being applied as a ban on **movement**. Consequence: exactly one legal pattern existed (an opacity fade), it was pre-filled as the answer in `ROLE_DESIGN`'s own SPEC template, `ROLE_MOTION` conceded the conflict *in advance* ("§11 wins"), and `COMPONENT_REGISTRY` held six motion components, all of them containment devices — so Law 25 step (4) forbade everything else as unregistered. Scope now stated in the invariant, Law 26, and all eight downstream copies. Recorded as `CONFLICT_REGISTRY` **C12**.
- **Motion was never verified positively.** V7 (`geometryShift == 0`), V8 (`0 layout animations`), V11 (`ΔscrollY == 0`) all measure *harmlessness*: a page with `animation: none` scored perfect on every one. Neither blocking craft layer of Law 39 held a single motion item, and the one detector that could have caught stiffness — `EDITORIAL_CRAFT` Y12 — fires only above the dial's default. **An animation that did not exist passed every motion gate in the system, and no rule existed under which it failed.**
- **New: `roles/MOTION_CRAFT_CANON.md`** — the peer `VISUAL_CRAFT_CANON` always had and motion never did. §1 **THE MOTION FLOOR**: four durations, three easings by role plus one spring, three stagger steps, a floor entrance that is not a bare fade, and a reduced-motion rule — taken verbatim when no concept exists, exactly as the visual floor is. §2 **the grammar of the in-between** (G1 order · G2 offset · G3 overlap · G4 origin · G5 verb · G6 keyframe stops) — the part every artifact in the system omitted. §3 **M1–M12**, the stiffness catalogue, 3+ hits = 🔴.
- **`CRAFT_LINT_SPEC` V21** — the first motion vector that can fail on absence: ≥2 distinct durations, ≥2 distinct easings, ≥1 entrance carrying a transform.
- **Routing.** `ROLE_MOTION.md` was in no task class at all — the file holding the timing principles and the SPEC formats was unreachable until @MOTION had already been summoned. TC-01 routed no motion canon and named `MOTION_LIBRARY` in its OUT list; the MICRO catalogue was routed by nothing. Fixed across TC-01/02/03, and @LEAD's motion trigger changed from a bug report ("buttons twitch") to a gate on every operational screen.
- **Choreography columns** added to the three artifacts that carried endpoints only: the DESIGN_SPEC (a six-row block that must be answered), MICRO_SPEC, and `TEMPLATE_MOTION_LANGUAGE`. Across 129 files there had been 11 `@keyframes` blocks and exactly one with an intermediate stop.
- **`COMPONENT_REGISTRY` §4** now registers *expression* components (Stagger · Reveal · Sequence · StateTransition · Confirm), not only containment devices.
- The single instruction telling @MOTION not to clamp — `ROLE_MOTION`'s activation pointer to `PRODUCTION_READINESS_CANON` §6 — was a **dead reference**: that file contains no occurrence of "motion", "ambition" or "timid". Repointed at Law 41 and the new canon.
- **Named products removed from every template and checklist that demanded one.** The golden-library table, the screen-type reference table, the hero archetype references, the passport fields, the TPF module headers and the reference walk now name **the capability or behaviour to reach**, never a product to imitate — the world comes from the project's own concept (Tier 0), or from THE FLOOR. Negative mentions ("not the default world", "never the acceptance base") were kept: they argue the same point.

**v6.37 — eighth wave: three contradictions a clean context found, and the doctrine that had four owners.**
- **The LANGUAGE CONTRACT contradicted Law 44 and made it unenforceable.** The block sits 157 lines above the laws and still said *chat: Russian (default) · `docs/artifacts/*`: Russian*. Law 44 says the reply follows the human and artifacts are English unless `DOCS_LANGUAGE` says otherwise. Same two objects, opposite values, and no precedence handle — the block is not a law, so the ladder could not resolve it. The contract now states the operative summary and **names Law 44 as its owner**; no locale is hard-coded anywhere, and the reply language is detected, never assumed.
- **Law 34 reinstated the greenfield deadlock the fourth wave closed.** `FRONTEND_CAPABILITY_CANON` carried a greenfield clause, but Law 34 still said "No map → no handoff to @DESIGN" with no scope clause — and LAW PRECEDENCE rung 4 forbids a canon from narrowing an unscoped law, calling that reinterpretation "the failure". Formally, the first module of every new project had to stop. The exception is now **in the law**, where rung 4 requires it.
- **The duplication doctrine had four owners and no winner.** `CONFLICT_REGISTRY` ("verbatim in every mirror, **never** a pointer"), `FRONTEND_CONSOLIDATION` ("convert duplicates into references"), `RULE_INTEGRITY` T2 and LAW PRECEDENCE (b) — all four in the same six-file `TC-20` minimum, read one after another. Resolved by **naming T2 the owner** and scoping the other three to their own cases rather than deleting any: a decision has one home; it may be echoed in full where a missed pointer is costly; **every echo names its home**; the forbidden thing is the unowned copy. `CONFLICT_REGISTRY` keeps verbatim mirrors — they carry `MIRROR OF:`, which is what makes them legitimate — and `FRONTEND_CONSOLIDATION`'s sweep is scoped to **unmarked** duplicates, so it can no longer delete a marked mirror as noise.
- `CONFLICT_REGISTRY` grounded its doctrine three times on "`.cursorrules` §1 philosophy". **`.cursorrules` has no §1** — its sections are unnumbered. The sole cited authority for the verbatim-mirror rule did not exist; all three citations now point at T2.
- @LEAD's own activation block read `.cursorrules` **Laws 1–41** — three short of its own Law 42/43 gates and of Law 44.

**v6.37 — seventh wave: language, and the rule about repeating a rule.**
- **New Law 44 — the system writes in English; the reply speaks the user's language.** Everything on disk (canons, `docs/artifacts/*`, prompt series, batch headers, ADRs, code comments, commit messages) is **English by default**; everything said to the human in the chat is in **the language the human used**, because a reply is direct speech and not an artifact. The default is a **declared decision, not a fact of nature**: a project sets `DOCS_LANGUAGE` in `PROJECT_PROFILE.md` §1 and its `docs/` follow it, while `roles/` and `.cursorrules` stay English. The clause that matters most is the carve-out — **the product's own user-facing strings are never touched by this law**; an agent that "translates the interface to English" has inverted it.
- Law 40's refusal script no longer hardcodes Russian; it replies in the language of the request.
- **The Russian layer is gone.** `roles/niches/*` (six files, 485 lines) translated with verified structural parity; the Russian term for a bounded subsystem rendered as the canon's own **contour**, not "loop", so the niches and the rest of the system name the same object the same way. Placeholder `.gitkeep` notes under `docs/` translated. Zero Cyrillic remains in `.cursorrules`, `roles/` and `docs/`.
- **LAW PRECEDENCE (b) rewritten — the owned echo.** The old rule ("a resolution lives in one place, the other keeps no second copy") was true about drift and wrong about how a model reads: the agent does not always open a pointer. The rule is now about **ownership, not uniqueness** — one law owns the resolution and is *named*; restating it in full elsewhere is legitimate where the cost of a missed pointer is high, provided the restatement names the same owner. **The forbidden thing is the unowned copy** — two statements, neither pointing at the other, which come apart on the first edit with no way to tell which half is stale.
- **Law 25 kept whole, with owners stamped on it.** It restates Laws 28 · 33 · 34 · 26 · 39 and the `ROLE_DESIGN` Tier 0 rule at seven points. Under the old (b) that was six defects; under the new one it is legitimate, and it stays — this chain is read at the opening of every surface, and a step whose rule is only a link is a step that gets skipped. Each restating step now **names the law that owns it**, and the preamble says the echo is deliberate. Nothing was deleted: what changed is that a future editor now knows, at every one of those seven points, which file to change instead.
- Applied immediately: **Law 43 declared owner** of the 13↔43 resolution (drift is 43's goal), Law 13 reduced to a pointer that says so. **Detector-code qualification** kept in LAW PRECEDENCE as the owner *and* repeated in full as `RAG_CANON` §1.1 — a deliberate echo, marked as one, because it governs every citation the agent writes.

**v6.37 — sixth wave: semantic rectification (no new rules, no new files).**
A clean-context audit of the five preceding waves, run under `roles/RULE_INTEGRITY_PROTOCOL.md`: every correction had to either **reinforce** an object's own goal or mark its **contrast**, never add a claim. What it removed:
- **Two numbers for one threshold.** `ROLE_LEAD` §B carried its own E3/E4 bounds (`4–6 reopened`, `past 2× the tier`) that disagreed with Law 43 (`1–6`, `upper bound + 2`). The gate now points at the law and keeps no second copy — a countable threshold with two values is not countable.
- **A fourth ordering nobody had named.** `RAG_CANON` §1 ranks documents and placed `roles/` *below* `docs/artifacts/`, with `.cursorrules` absent — readable as "an artifact outranks a law". §1 is now explicitly a **source** ladder that the laws are not on, and LAW PRECEDENCE names four orderings instead of three.
- **A gate that ignored the register.** The @FRONTEND Visual Quality Gate applied the `instrument` checklist (`gray.0`, reference product named) to every surface — the collision `CONFLICT_REGISTRY` had already decided against. The gate now declares the register first, and the comparison base is the project passport, never an outside product.
- **A greenfield deadlock.** `ROLE_DESIGN` still said "no capability map → stop", against `FRONTEND_CAPABILITY_CANON`'s own greenfield clause in the same system. A module with no backend yet is mapped from the domain model with `PLANNED` rows, not blocked forever.
- **Self-contradiction inside single laws.** Law 39 named a CI job as owner while the same law says the executor may not exist; Law 22 named Jenkins as canon after Law 21 stopped mandating an engine; the CHAIN still demanded "a reference" before @DEV. All three now match the law they sit in.
- **A protocol that miscounted itself.** `RULE_INTEGRITY_PROTOCOL` has seven tests and called them six in three places, and — having written the rule that a canon the router does not know does not exist — was itself missing from `FILE_MAP` and `SYSTEM_FILES_MASTER`. Both fixed; `RAG_CANON` TC-20 renumbered and brought back under the six-file cap.
- **Coverage holes.** `SECOND_PASS` §4 had no audit set for TC-21 · `ROLE_PRINCIPLE` RETURNS did not name @DEV though @DEV's RECEIVES expects the model from it · `LAYOUT_INVARIANTS` §12 (collision) was missing from the @DESIGN and @QA_ARCH activation lists · V12 in `ROLE_DEV` hardcoded four viewports against Law 26's declared surfaces.
- **Language.** Russian residue removed from the canon layer (the scourge contract, placeholder examples, criterion glosses). Still outstanding and **not** touched: `roles/niches/*` (six files) and the "reply in Russian" clause in Law 40's refusal script.

Composition v6.24: DATA_INTEGRITY_CANON (+Law 32, integrations ARCH/DEV/QA_ARCH/PENTEST/MIGRATIONS, registries)
Composition v6.23: ARCH_SPINE_PROTOCOL (+Law 31, integrations ARCH/QA_ARCH/SYSTEM_DESIGN, registries)
Composition v6.22: ASYNC_WORKERS_CANON (+integrations ARCH/DEV/PENTEST/SYSTEM_DESIGN, registries)
Composition v6.21: ROLE_SEO · SEO_CANON (+Law 29, integrations in 8 files, v6.20 refinements)
Composition v6.20: CONCEPT_DNA_LIBRARY · VISUAL_CONCEPT_PROTOCOL · CONCEPT_ANATOMY · LAYOUT_COMPOSITION · INTEGRATION_PATCHES_TASTE (+integrations in 16 files)
Composition v6.16: ROLE_QA_VISUAL · LAYOUT_INVARIANTS · HERO_ARCHETYPES · MOTION_AMBITION_DIAL · INTEGRATION_PATCHES · SYSTEM_UPGRADE_MANIFEST
