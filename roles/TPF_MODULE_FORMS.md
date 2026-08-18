# TPF_MODULE_FORMS — Forms (Paperless)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.7), `REV_RAG_MAP_INNOVATIONS.md` (section 8).

---

## 0. Design Reference and Visual Standard

**Reference:** Typeform admin — template list; Notion databases — simple table with actions
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

Forms are the tool for collecting data without paper. The administrator rarely visits this section — but when they do, they need to quickly find the right template and send it.

Three key feelings:
1. **Template list — dense and clear** — name, type, date — all in one row
2. **Sending in two clicks** — from the patient or booking context
3. **Editor — not intimidating** — simple builder, clear fields

Frequent actions: find a template, send a form to a patient, create a new template.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Template table: compact, verticalSpacing="sm"
- [ ] ActionMenu: Edit / Duplicate / Delete
- [ ] EmptyState: form icon + "Create first template"
- [ ] Editor Drawer: size="lg", closes on Escape
- [ ] Confirm on delete: Modal (not Drawer)
- [ ] Template type indicator: badge next to name

---

## 1. Purpose

Form template builder and sending forms in the context of a patient or booking: generate a unique link and deliver via WhatsApp/SMS.

---

## 2. Screen structure

### 2.1. Template list

- Table (or cards) of templates: name, type, last modified date. Data Density, ActionMenu per row (Edit, Duplicate, Delete).
- "Create template" button opens a Drawer with a builder (fields by JSON schema or visual editor).

### 2.2. Template editor (Drawer)

- Fields per selected schema (e.g. health questionnaire, consent form). Saved as DigitalFormTemplate.

### 2.3. Send form from context

- In patient or booking card ActionMenu — "Send form" item. Select template → generate unique URL → send via WhatsApp/SMS (or copy link). Backend creates a send record and returns the URL.

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Templates | GET/POST/PUT/DELETE form_templates | CRUD. |
| Send form | POST form/send-link (patient_id or booking_id, template_id) | Returns URL; optionally triggers delivery to channel. |

---

## 4. UI Rules

- EmptyState: "No templates" with CTA "Create template".
- Drawer for creating/editing a template. Modal for delete confirmation.
- In chat (OmniChat), the "Questionnaire" button from the Action Bar uses the same form-sending mechanism.

---

## 5. References

- Page: Settings section or `/admin/forms`. Entities: DigitalFormTemplate, Patient, Booking; integration with Omnichannel for link delivery.
