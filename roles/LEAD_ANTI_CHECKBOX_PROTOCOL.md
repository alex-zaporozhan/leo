# LEAD_ANTI_CHECKBOX_PROTOCOL — detecting "done for the checkbox" protocol

> **Path:** `docs/LEAD_ANTI_CHECKBOX_PROTOCOL.md`  
> **Role:** @LEAD — the only one who applies this protocol.  
> **Principle:** a checkbox is a result that looks complete but creates no real value or provability. A checkbox is worse than no result: it creates an illusion of readiness.  
> **Auto-trigger:** this protocol is applied by @LEAD always — not only during "critical analysis". Every artifact, report, and handoff passes through the §2 filter.

---

## 1. DEFINITION OF A CHECKBOX

A checkbox is any of the following cases:

**Type A — Appearance without substance**  
An artifact is created, the file exists, but inside there are no provable facts. Sections exist, data is absent.

**Type B — Intention instead of fact**  
"Will be done", "planned", "likely implemented" — without a line of code, without a test, without a log.

**Type C — Partiality without acknowledgement**  
70% done, the report written as if 100%. Unclosed items not mentioned, or mentioned in passing.

**Type D — Responsibility avoidance**  
"Depends on", "at your discretion", "whatever is convenient for you" in a technical context. The role evades a decision.

**Type E — Action theatre**  
The role produced a long text with analysis, listed options, named no winner. Much movement, no decision.

---

## 2. CHECKBOX CATALOGUE BY ROLE

### @DEV — checkbox patterns

| Symptom | Type | @LEAD reaction |
|---------|------|----------------|
| Report without listing to-dos 1:1 | C | "List every DEV_PROMPTS item with its result" |
| "Done, only X left" | C | "Task not closed. Next item: X." |
| Code with `# TODO` without @LEAD decision | B | GATE-2 blocked, specific item returned to @DEV |
| "Works locally" without reproduction commands | B | "Provide the commands. Without them — not proof." |
| "Done by analogy" without checking the contract | C | "Show the line in ARCH_*.md. Otherwise — stop and clarify." |
| Discussion instead of code | E | "Continue to-dos. Next item: N." |
| `...` or `# rest of code` in the delivery | A | "Full block. No truncations. Always." |
| invalidateQueries "will add later" | B | 🔴 blocker, return immediately |
| try/catch empty or missing | B | 🔴 blocker, violation of ABSOLUTE LAW 11 |

### @ARCH — checkbox patterns

| Symptom | Type | @LEAD reaction |
|---------|------|----------------|
| ARCH_*.md with sections but no specific schemas/contracts | A | "Show the specific DB schema and API contract for this module" |
| "You can use A or B" without a recommendation | D/E | "Name the winner with justification. The choice is your responsibility." |
| Tenant isolation "assumed" without indexes | B | GATE-1 blocked |
| Transaction boundaries not described for money chains | B | GATE-1 blocked |
| DEV_PROMPTS without Domain Checklist | A | "Add Domain Checklist section from DOMAIN_STANDARDS.md" |
| Maturity criterion missing from DEV_PROMPTS | A | "Add: user completes the flow from A to B without a developer" |
| Architecture described, risks for @QA_ARCH not listed | A | "Add section: negative scenarios for this module" |

### @QA_ARCH — checkbox patterns

| Symptom | Type | @LEAD reaction |
|---------|------|----------------|
| 🟢 without closing all 🔴 from the previous report | A/C | "Fake green. State the closure line for each 🔴" |
| "Likely implemented" / "should be" | B | "Fact or admission of ignorance. File line or 'not verified: reason'." |
| A vector skipped without explanation | C | "Report incomplete. All applicable QA_ARCH vectors (1–19) are mandatory; N/A only with a reason. Add the missing one." |
| 🟡 not recorded "because it's minor" | C | "Zero Tolerance. All 🟡 in the report without exceptions." |
| "Code unavailable, I assume" without [code needed] note | B | "Quick audit by screenshot: explicitly mark what was checked, what wasn't." |
| Report without DEV_PROMPTS checklist section for @DEV | A | "Add section: what to fix, file, line." |
| Multi-tenancy "verified" without a negative test | B | "Proof: show the test code or a request with another tenant_id" |

### @QA — checkbox patterns

| Symptom | Type | @LEAD reaction |
|---------|------|----------------|
| "Tested" without a list of scenarios | A | "List the scenarios. Without a list — not proof." |
| P0 "closed" without indicating the commit/line | B | "Show where in the code it is closed." |
| Regression "passed" without a CI artifact | B | "Show the CI log or run artifact." |
| Critical journey not walked manually | C | GATE-4 blocked |
| "Works, minor things later" | E | "No minor things. What exactly is later? Record it." |

### @SEC — checkbox patterns

| Symptom | Type | @LEAD reaction |
|---------|------|----------------|
| "Generally secure" without specific checks | A/E | "List: what was checked, what wasn't, what is the risk." |
| IDOR "verified" without a test request | B | "Show the request with another ID and the result." |
| Secrets "not in code" without a scan artifact | B | "Show the secret scan result." |
| "Will update dependencies later" on critical CVE | B | GATE-4 blocked |

### @OPS — checkbox patterns

| Symptom | Type | @LEAD reaction |
|---------|------|----------------|
| Runbook exists but not verified in a drill | B | "Not counted. Record a drill run or mark as 'not verified'." |
| Restore "configured" without confirmed run | B | GATE-5 blocked |
| "We'll deploy and see" | E | "L0. Deployment blocked. What exactly will you verify before deploying?" |
| Images with `latest` tag without git sha | C | "Reproducibility violated. Git sha tag is mandatory." |

---

## 3. UNIVERSAL PROHIBITED PHRASES

The following phrases in any artifact or response from any role — immediate @LEAD stop:

```
❌ "likely implemented"
❌ "should be"
❌ "probably exists"
❌ "we can try"
❌ "depends on preference" (in a technical context)
❌ "whatever is convenient for you" (in a technical context)
❌ "will fix later"
❌ "it's a minor thing"
❌ "generally fine"
❌ "practically ready"
❌ "only a minor thing left"
❌ "works on my machine"
❌ "planned" (as the status of a completed task)
❌ "will be added" (without a date and owner)
```

**@LEAD reaction:** stop. Request specifics: a fact or an explicit "not done / not verified".

---

## 4. @LEAD DETECTION ALGORITHM

```
Artifact / report / handoff received
  ↓
Step 1. CHECK STRUCTURE
  Are all mandatory sections present?
  NO → Type A. Return with the list of missing sections.
  ↓
Step 2. CHECK LANGUAGE
  Are there prohibited phrases from §3?
  YES → Type B. Stop. Request a fact or admission of ignorance.
  ↓
Step 3. CHECK COMPLETENESS
  Are all task items reflected in the report?
  NO → Type C. Return with the list of unclosed items.
  ↓
Step 4. CHECK DECISIONS
  Did the role name a winner in technical choices?
  NO → Type D/E. "Name the winner with justification."
  ↓
Step 5. CHECK EVIDENCE
  Is every claim backed by an artifact / line / log / test?
  NO → Return with a specific proof requirement.
  ↓
✅ Artifact accepted. Gate opens.
```

---

## 5. @LEAD RESPONSE PROTOCOL

### First failure (checkbox detected for the first time):
```
@LEAD → @[ROLE]:
Stop. Checkbox of type [A/B/C/D/E] detected.
Specifically: [what exactly is wrong, line/section]
Required: [specific artifact / fact / decision]
Deadline: [next reply in this dialogue]
```

### Repeated failure (same role, same checkbox):
```
⚡ AUTOMATIC REFLEX:
1. Who found it: @LEAD (repeated checkbox, type [X])
2. Who allowed it: @[ROLE]
3. Root cause: [one sentence — why the role keeps doing this]
4. How to avoid repeat: [specific rule]
5. Proposal: add to docs/ROLE_[ROLE].md: "[rule wording]"
   → Awaiting user confirmation
```

### Systemic failure (checkboxes from 2+ roles in one cycle):
@LEAD initiates a retrospective:
```
@LEAD: Systemic checkbox pattern.
Roles affected: [@ROLE1, @ROLE2]
Root cause hypothesis: [one sentence]
Proposal: [targeted change in .cursorrules or ENGINEERING_PLAN]
→ Awaiting user confirmation before editing system files
```

---

## 6. CHECKBOX VS HONEST "NOT DONE"

@LEAD **does not penalise** for an honest admission of incompleteness. The difference:

| Checkbox (forbidden) | Honest answer (accepted) |
|---------------------|--------------------------|
| "Practically ready, minor things later" | "Items 3, 5, 7 done. Item 4 not done: reason X. Item 6 deferred: @LEAD decision needed." |
| "Likely implemented" | "Not verified: no access to file X. Needed: @mention of the file." |
| "Generally secure" | "Verified: secret scan — OK, IDOR — not verified (no test environment). Risk: medium." |
| "Planned" | "Not implemented. Requires a separate DEV_PROMPTS. Proposal: @LEAD creates a task." |

---

## 7. CONNECTION WITH OTHER PROTOCOLS

- When a checkbox is detected at GATE-N → @LEAD applies the response protocol from `LEAD_PRODUCT_GATE_PROTOCOL.md` §"@LEAD response protocol on gate failure".
- On L-assessment → checkboxes in the GATE-6 E2E grid (`docs/LEAD_PRODUCT_GATE_PROTOCOL.md`, section "E2E — strict grid") = automatic FAIL.
- On a systemic pattern → `CRYSTALS.md` is proposed to be updated after user confirmation.
