# CONFLICT_REGISTRY — resolved cross-file conflicts and their single winners

> **What this is:** the closed ledger of every conflict between system files (a rule/value stated differently in two places) and the single winner chosen for it — **and** the central index of every mirror set in the system (conflict-born or intentional accent; see "Mirror sets beyond conflicts" + "Marker model" below).
> **Why it exists:** the system grows additively (new Law + new canon + mirror in a role). Without a registry, copies drift into contradiction (e.g. Law 33 meaning both Security and Craft across files). A drifted repeated-accent drives two conflicting trajectories at once — strictly worse than one.
> **Rule:** any `@EVOLVE` that introduces an overlap (the same concept stated in ≥2 files) must add a row here, pick the winner, and synchronise every mirror verbatim. **A mirror is a full-text repeated accent that carries a `MIRROR OF:` marker naming its home** — it is not thinned to a bare "see section X" pointer, because the reader does not reliably follow one, and it is not left as an unmarked copy either, because an unmarked copy is what drifts.
> **Doctrine owner: `roles/RULE_INTEGRITY_PROTOCOL.md` T2** (decision versus meaning, and when a full echo is legitimate). This registry applies that test to one case — a **conflict winner**, which is always a decision — and keeps no second version of the doctrine. If the doctrine changes, it changes in T2 first.
> **Verification:** before applying any row, re-verify it by grep in the living files. The registry is a hypothesis; the living file is truth.

---

## Legend

- **Winner** — the single source-of-truth decision. It has **one home**, and its value may be written **verbatim in every mirror location** — each mirror naming that home (`MIRROR OF:`). A verbatim mirror with a named home is legitimate; a verbatim copy without one is the drift this registry exists to prevent (`RULE_INTEGRITY_PROTOCOL` T2).
- **Status** — `✅ applied` · `🟡 pending` · `🔎 verify` (needs a fresh grep before touching).

---

## Registry

### C1 — Security law numbering
- **Conflict:** files referenced "Law 33" for the **security** gate, while `.cursorrules` v6.34+ has Law 33 = Craft, Law 38 = Security. Residue of the v6.26→later renumbering (security was Law 33 at v6.26).
- **Winner:** `.cursorrules` numbering is source of truth — **Security = Law 38, Craft = Law 33**.
- **Affected (security-context "Law 33" → "Law 38"):** `ROLE_LEAD.md` (2) · `ROLE_DEV.md` (1) · `LEAD_PRODUCT_GATE_PROTOCOL.md` (1) · `ROLE_ARCH.md` (2) · `ROLE_PRINCIPLE.md` (1) · `ROLE_PENTEST.md` (4) · `ROLE_QA_ARCH.md` (1) · `SECURITY_GATE_PROTOCOL.md` (3) · `ROLE_DESIGN.md` (1). Total 16.
- **Left untouched (craft-context "Law 33", correct):** `MEDIA_SYNTHESIS_CANON.md`, `ROLE_MEDIA_ENGINEER.md`. Changelog `INTEGRATION_PATCHES_SECURITY.md` is historical (records v6.26 state) — not rewritten.
- **Status:** ✅ applied (2026-07-23). Verify: `grep "Law 33"` in the security files = 0.

### C2 — @DESIGN scope trigger
- **Conflict (historical wording, laws since rewritten in v6.37):** the former Law 25 said "any new screen", the former Law 19 said "UI-heavy", `ROLE_DESIGN` said "CRUD by pattern — skip". Laws 19 and 25 no longer carry the trigger at all — 19 now governs *editing the frontend from decisions* and 25 *the order design is produced in*; the trigger itself lives in `ROLE_DESIGN` and in Law 25 step 4.
- **Winner:** trigger = **NEW PATTERN / COMPOSITION**, not "any screen". New pattern → SPEC mandatory; a screen by an existing pattern (CRUD, tech page) → skip.
- **Affected:** `.cursorrules` Law 19 / Law 25 · `ROLE_DESIGN.md` trigger + header · `ROLE_LEAD.md` step 1.6. Same decision (new-pattern trigger + the existing-pattern skip list) mirrored in each.
- **Status:** ✅ applied (2026-07-23). ROLE MAP (`.cursorrules` line 139) also aligned to "new pattern/composition" (it earlier said bare "ANY new screen"). Chain-rules line 354 already carries "Exception — editing an existing component without a new pattern" — consistent, left as-is.
- **Mirror completeness note (C3 lesson applied):** the confirm/undo winner also had to be aligned in the QA_ARCH gate copies — `.cursorrules` QA_ARCH GATE + `ROLE_QA_ARCH.md` (Vector 9 checklist + the crime list) — else @QA_ARCH would flag a valid undo as 🔴. When aligning a UX rule, grep ALL of: `.cursorrules` laws + gate, the owning canon, the template, AND the QA_ARCH checklist.

### C3 — confirm vs undo
- **Conflict:** Law 15 + `TEMPLATE_ADMIN_UI_UX.md` require confirm; `INTERFACE_CRAFT_CANON.md` says "undo instead of confirm".
- **Winner:** **by action class.** Irreversible / money / PII / cross-user → **confirm** (Law 15 holds). Reversible, single-entity, within session → **undo + toast**.
- **Affected:** `.cursorrules` Law 15 · `TEMPLATE_ADMIN_UI_UX.md`. Winner already lived in `INTERFACE_CRAFT_CANON.md` (I4/ST2: reversible→Undo; irreversible/wide-blast→typed-confirm) — that is the source, unchanged; the two blanket-confirm mirrors were aligned to it.
- **Status:** ✅ applied (2026-07-23).

### C4 — palette token source
- **Conflict:** `FRONTEND_DESIGN_EXCELLENCE.md` (generic tokens) vs `TEMPLATE_ADMIN_UI_UX.md` → `TECH_PASSPORT_FRONTEND_UI_LOGIC.md` (different hex).
- **Winner:** **generic canon = floor/fallback; project VISUAL_CONCEPT/passport = authoritative for that project** (validated by Glacier/Spectrum). Precedence rule written full-text in both; concrete hex are never captured into universal `roles/`.
- **Affected:** `FRONTEND_DESIGN_EXCELLENCE.md` · `TEMPLATE_ADMIN_UI_UX.md` · `SYSTEM_FILES_MASTER.md` SSOT table.
- **Status:** ✅ applied (2026-07-23). `TEMPLATE_ADMIN_UI_UX.md` no longer hard-codes project hex (`#1c2e45`/`#f4f6f8`) — it states the precedence and points to the project passport for concrete values.

### C5 — S1–S12 namespace collision
- **Conflict:** `SECURITY_GATE_PROTOCOL.md` uses **S1–S12** for the SECURITY SURFACE; `INTERFACE_CRAFT_CANON.md` §7 uses **S1–S12** for the stiffness detector signs (verified: `INTERFACE_CRAFT_CANON.md:186-192`).
- **Winner:** **SECURITY keeps S1–S12; stiffness → ST1–ST12.**
- **Affected:** `INTERFACE_CRAFT_CANON.md` §7 + everywhere the stiffness signs are cited (`ROLE_QA_VISUAL.md` calls the whole detector "vector V14" — that wrapper name stays; only the internal sign labels S1..S12 → ST1..ST12).
- **Status:** ✅ applied (2026-07-23). Renamed in `INTERFACE_CRAFT_CANON.md` §7 (12 signs + header + note) and synced across all 9 mirror citations: `.cursorrules` Law 33 + Layer-P description · `TEMPLATE_ADMIN_UI_UX.md` · `ROLE_LEAD.md` (×2) · `QA_VISUAL_AESTHETE_SENSOR.md` (also fixed a stray "I1–I12" typo) · `TEMPLATE_UI_COMPOSITION_PASSPORT.md` (×2) · `ROLE_DESIGN.md` (full list). SECURITY S1–S12 untouched.

### C6 — aesthete catalogue letters
- **Conflict:** `ROLE_QA_VISUAL.md` prose and `QA_VISUAL_AESTHETE_SENSOR.md` §J said "A–G"; `.cursorrules` Law 39 and the sensor's own verdict table §I use **A–H** (block H = canon detectors X/I/Y).
- **Winner:** **A–H canonical** (superset; the §I verdict table already had 8 rows).
- **Affected:** `QA_VISUAL_AESTHETE_SENSOR.md` §J prose (A–G→A–H) + fixed a duplicate `## H.` header (severity-scale block de-lettered) · `ROLE_QA_VISUAL.md` two references.
- **Status:** ✅ applied (2026-07-23).

### C7 — @SEC vs @PENTEST verdict ownership (asymmetric mirror, not a dispute)
- **Conflict:** `ROLE_PENTEST.md` already states "@SEC advisory / @PENTEST holds the block"; `ROLE_SEC.md` is silent on advisory/blocking and carries a stale ordering ("@PENTEST after major waves").
- **Winner:** **@PENTEST = blocking verdict owner; @SEC = advisory checklist.**
- **Affected:** add the mirror line to `ROLE_SEC.md` + fix `ROLE_SEC.md` "after major waves" → the S-0 / S-Wave / S-Global cadence of `SECURITY_GATE_PROTOCOL.md`. This is completing the mirror, not resolving a fight.
- **Status:** ✅ applied (2026-07-23). ROLE_SEC now states advisory role + the three checkpoints; version 2.1.

### C8 — SEO TECH gate parity
- **Conflict:** Law 29 says SEO TECH is a blocker "on par with @SEC", but @SEC is now advisory (C7), so the parity anchors to the wrong (non-blocking) role.
- **Winner:** **SEO TECH — blocking gate on par with @PENTEST** (the actual blocking security contour).
- **Affected:** `.cursorrules` Law 29 · `ROLE_LEAD.md` (×2: chain step 5 + gate list).
- **Status:** ✅ applied (2026-07-23). "on par with @SEC" → "on par with @PENTEST (the blocking contour; @SEC is advisory)" in all three spots. `LEAD_PRODUCT_GATE_PROTOCOL.md` GATE-4 already lists SEO TECH as a blocker — consistent.

---

### C9 — Which visual checklist applies to which surface
- **Conflict:** the `.cursorrules` QA_ARCH gate embedded the `FRONTEND_DESIGN_EXCELLENCE` §6 checklist (`gray.0` background, white cards with a 1px hairline, Drawer for forms, three-dot row menu, Tabler `stroke={1.5}`, 150ms hover) as a **universal** acceptance criterion, while Law 33 splits craft into two registers with **partly opposite** laws and `EDITORIAL_CRAFT_CANON` states them. A landing measured by the instrument list fails for being a landing; passing that list on a showcase is itself the defect.
- **Winner:** **the REGISTER is declared before any visual verdict.** `instrument` → `VISUAL_CRAFT_CANON` (+ the §6 list, whose concrete values are superseded by the project design passport wherever it is filled). `statement` → `EDITORIAL_CRAFT_CANON` (timidity Y1–Y12) + the project world. **Register unstated → no visual verdict is issued.**
- **Affected:** `.cursorrules` QA_ARCH GATE (register line added above the checklist) · `ROLE_FRONTEND.md` (contour table by register; DESIGN SOLUTION Step 0) · `RAG_CANON.md` TC-01/TC-02/TC-03 (each class names its register and its OUT list).
- **Remaining:** the §6 values themselves are still written as absolutes inside `FRONTEND_DESIGN_EXCELLENCE`. Moving them into a project passport is a separate, planned change.
- **Status:** ✅ applied (2026-09-03), partial as noted.

### C10 — Where the viewport set comes from
- **Conflict:** `ROLE_QA_VISUAL` fixed the measurement viewports at 360/768/1280/1920 as a constant of the system, while a product's real surfaces (PWA, native, embed, an internal tool that is desktop-only) are a per-project decision. A project breakpoint outside the four was measured nowhere.
- **Winner:** the viewport set comes from **the project's declared surfaces** — `FRONTEND_PASSPORT_[PROJECT].md` §Surfaces, fixed by @FRONTEND with @ARCH before the first screen. The four values remain **the default when nothing is declared**, not the law.
- **Affected:** `ROLE_QA_VISUAL.md` (fixtures table) · `ROLE_FRONTEND.md` (§MOBILE AND PLATFORM COMPOSITION — who declares it and where it lives).
- **Status:** ✅ applied (2026-09-03).

### C11 — Reference precedence: the project world vs the golden library
- **Conflict:** Laws 19 and 25 name a golden library of seven external products (and name it differently in each of the two laws), while `ROLE_DESIGN` Tier 0 and `VISUAL_CONCEPT_PROTOCOL` make the project's own world the primary reference. `ROLE_FRONTEND` opened with "every screen must feel like a Linear/Stripe/Notion-grade product", which reads as the opposite instruction.
- **Winner:** **Tier 0 — the project world (`docs/artifacts/VISUAL_CONCEPT_[PROJECT].md`) outranks every external reference.** The SaaS golden library is a **fallback for the `instrument` register only**, never the source of a public site's aesthetic. Absent a world: THE FLOOR (`VISUAL_CRAFT_CANON` §11) for instrument, a stop to @CREATOR for statement.
- **Affected:** `ROLE_FRONTEND.md` (visual standard rewritten; DESIGN SOLUTION Steps 1–2) · `RAG_CANON.md` TC-02/TC-03/TC-05 (the world is item 1 of the minimum, and the library is out of scope for statement).
- **Closed:** Laws 19 and 25 were rewritten in v6.37 and no longer enumerate an external library at all; Law 25 names `ROLE_DESIGN` Tier 0 as the owner of the reference rule.
- **Status:** ✅ applied everywhere.

### C12 — "opacity-only in the flow" vs the motion arsenal
- **Conflict:** `LAYOUT_INVARIANTS` §10/§11 was read across eight files as *no `transform` in the document flow*, while `MOTION_LIBRARY` T1–T6, I1–I3 and `MOTION_AMBITION_DIAL` all describe techniques that are transforms on elements in the flow. Every downstream file copied the broad reading; `ROLE_MOTION` conceded it in advance ("§11 wins"); `ROLE_DESIGN` shipped the answer pre-filled in its own SPEC template; `COMPONENT_REGISTRY` registered six motion components, all of them containment devices. The result was one legal pattern — an opacity fade — and a system that could not produce anything else.
- **Winner:** **the invariant owns reflow and the reader's scroll position, and nothing else.** A `transform` does not participate in layout: the element keeps its box, neighbours do not move, `scrollY` does not change. Therefore `transform` + `opacity` + `filter` are **permitted in the document flow**, inside the element's own reserved space; animating layout properties is forbidden everywhere; a **motion island is required only where the motion needs its own scroll or overflow context** (carousel, strip, pinned scrub, canvas).
- **Affected (all verified by grep, not by intention):** `.cursorrules` Law 26 and Law 39 · `LAYOUT_INVARIANTS` §10 heading, §10 table, §11 architecture table, the reference CSS, the reduced-motion sample, the @DEV checklist · `ROLE_QA_ARCH` (its reveal line was a 🔴 that **rejected** floor-compliant motion — the gate that ran before the role holding the new detector) · `ROLE_MOTION` (Principle 7, the ScrollTrigger note, the Motion Quality Gate, the MOTION_SPEC template, both conflict clauses) · `ROLE_FRONTEND` (activation header, Pillar 9, rule 14, the pre-handoff checklist) · `ROLE_DESIGN` (SPEC production step 4, audit lens 10, the DESIGN_SPEC motion block) · `COMPONENT_REGISTRY` (§4 and the first-screen checklist) · `FRONTEND_DESIGN_EXCELLENCE` (the reveal CSS comment and the public-site checklist) · `MOTION_LIBRARY` (the `clip-path` boundary clause) · `MOTION_AMBITION_DIAL` (safety rails and the MICRO catalogue).
- **Also fixed with it:** motion had no craft canon and no floor while every other discipline had both — `roles/MOTION_CRAFT_CANON.md` now supplies the floor (§1), the grammar of the in-between (§2) and the M1–M12 stiffness catalogue (§3), and `CRAFT_LINT_SPEC` V21 is the first motion vector in the system that can fail on absence.
- **Status:** ✅ applied. *The first pass at this row claimed "applied everywhere" while ten copies of the losing side were still live, three of them gates — the claim was written from intention rather than from grep. Re-verify by grep before trusting this line (the rule at the top of this file), and that is exactly why that rule is there.*

---

## Mirror sets beyond conflicts (Law 41 / gates / semantic echoes)

> These are not resolved fights — they are **intentional repeated accents** (a rule stated in several roles on purpose, per `roles/RULE_INTEGRITY_PROTOCOL.md` T2). They still need a drift-guard: change the SoT → sync the accents. Listed here so one grep of this file finds **every** mirror set in the system, conflict or not.

| Set | SoT (source of truth) | Accents (mirror locations) | Kind |
|-----|----------------------|----------------------------|------|
| The motion floor (durations · easings · stagger · the floor entrance · reduced-motion) | `MOTION_CRAFT_CANON.md §1` | `TEMPLATE_MOTION_LANGUAGE.md §2` · `MOTION_AMBITION_DIAL.md` MICRO catalogue · `LAYOUT_INVARIANTS.md §10` reference CSS · `CRAFT_LINT_SPEC.md §1d` | verbatim tokens |
| M1–M12 motion stiffness | `MOTION_CRAFT_CANON.md §3` | `ROLE_QA_VISUAL.md` report format · `CRAFT_LINT_SPEC.md §1d` · `MOTION_REFLEX.md` · `.cursorrules` Law 39 | reference |
| R1–R12 motion reflex | `MOTION_REFLEX.md §1` | `ROLE_DEV.md` · `ROLE_FRONTEND.md` · `ROLE_QA_VISUAL.md` · `CRAFT_LINT_SPEC.md §4.0` | reference |
| Foundation-completeness rollup (Law 41) | `PLANNING_MATURITY_CANON.md §3` | `ROLE_CREATOR.md` · `ROLE_BIZ.md` · `TEMPLATE_BIZ_LOGIC.md §3 anchor` | verbatim rollup |
| Foresight forward-questions | `PLANNING_MATURITY_CANON.md` | `ROLE_PRINCIPLE.md` | verbatim |
| FORESIGHT / COMPLETENESS gate | `.cursorrules` CHAIN step 0.8 | `ROLE_LEAD.md` (gate section + CHAIN) · `LEAD_PRODUCT_GATE_PROTOCOL.md` GATE-0/GATE-1 | verbatim |
| Security Contract template | `SECURITY_GATE_PROTOCOL.md §3` (full) | `ROLE_PENTEST.md` MODE 0 (compact echo) | **semantic** |
| Routing graph | `.cursorrules` CHAIN PROTOCOL | `ENGINEERING_PLAN.md §1` state machine | **semantic** |
| State vocabulary | `ROLE_DESIGN.md` State Spec + Intermediate states | `ROLE_QA_ARCH.md` Vector 3 / Vector 19 | **semantic** |

**semantic** = same *requirement set*, worded for each role (compact vs full, design-intent vs audit-check). The drift-guard verifies the **set of items**, not character-for-character text. Each semantic pair also carries a prose note in both files.

## Marker model — where the inline mirror comment goes (and where it does not)

Two greppable keywords, placed **at the block**, so a local editor sees the accent without leaving the file:
- `<!-- MIRROR SOURCE (SoT): <what> | cited in <list> | index: CONFLICT_REGISTRY.md -->` — on the **source-of-truth block** (e.g. the stiffness ST1–ST12 list, the Security Contract full template, the State Spec). Tells the editor: change here → sync the citations/echoes.
- `<!-- MIRROR OF: <SoT> | keep verbatim (or: semantic) | index: CONFLICT_REGISTRY.md -->` — on a **mirror/echo block** (a duplicated paragraph/table, or a semantic echo like ROLE_PENTEST MODE 0). Tells the editor: this follows another file.

Where each applies:
- **Block mirrors** (a duplicated paragraph/table/template — the Security Contract, state vocabulary, Law 41 rollup, foresight-gate, routing graph, and the C3/C5 **source** lists): carry one of the two markers above at the block.
- **Token mirrors** (a single value inside prose — a law number like "Law 38" C1, "on par with @PENTEST" C8, or a scattered "ST1–ST12" / "confirm-vs-undo" citation C3/C5): an inline comment per mention would be noise, not a guard. These are guarded **centrally, here** — the drift-guard is `grep` of the concept across the Affected list above. No inline marker (the source block still carries `MIRROR SOURCE`, which names every citation site).

**Grep the whole mirror surface:** `MIRROR OF|MIRROR SOURCE` (block markers, both keywords) + this file (token + block index) = complete.

---

## How to add a row (for `@EVOLVE`)

1. State the conflict: the concept + the two (or more) places it is stated differently, with a grep-verified quote.
2. Pick ONE winner (a value / rule / reference).
3. List every mirror location; write the winner's value **verbatim** in each (repeated accent preserved).
4. Run the drift guard: `grep` the concept across the mirrors → 0 divergences.
5. Set status and date.

---

Reference: `roles/SYSTEM_UPGRADE_MANIFEST.md` (historical origin only — this registry is self-contained and does not depend on that transient plan) · `roles/RULE_INTEGRITY_PROTOCOL.md` T2 (a decision has one home; a full-text mirror is legitimate when it names that home) · `roles/SYSTEM_FILES_MASTER.md` (Single Sources of Truth table) · `roles/SYSTEM_EVOLUTION_PROTOCOL.md`
Version: 1.0 | 2026-07-23
