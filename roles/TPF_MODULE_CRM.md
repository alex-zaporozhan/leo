# TPF_MODULE_CRM — CRM & Sales (Kanban Funnel)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.4), `REV_RAG_MAP_INNOVATIONS.md` (section 5).

---

## 0. Design Reference and Visual Standard

**Reference:** Linear Kanban — cards + Bitrix24 funnel — column totals
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

CRM is a visualisation of money. The manager looks at the board and must instantly see: where money is stuck and what needs to be done to move it.

Three key feelings:
1. **Money is visible immediately** — budget totals in each column header, large
2. **Lead Rotting is unmissable** — an overdue card turns red, no need to search
3. **Action in one click** — open chat, generate a prepayment link from the Lead Drawer

Frequent actions: drag a lead to the next stage, open chat, create a payment link.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Column header: name + count + total — three elements, readable in a second
- [ ] Lead Rotting: border 2px red.5 on the card (not the whole background)
- [ ] Drag ghost: ghost card, drop zone — grey highlight
- [ ] Empty column: "Drag here" — not an empty space
- [ ] Lead Drawer: opens on the right, does not cover the entire Kanban
- [ ] Lead source: channel icon (TG/WA/Site) — 16px, with tooltip
- [ ] AI Active tag: blue badge, not a coloured background for the whole card

---

## 1. Purpose

Visualise money: leads as cards by funnel stage, with column totals and quick access to chat and prepayment.

---

## 2. Screen structure

### 2.1. Layout

- Three-column: filters/metrics on the left when needed, Kanban in the centre, Drawer with selected lead details on the right.
- Horizontal scroll. Columns = funnel stages (LeadStage).

### 2.2. Columns

- Each column header: stage name, card count, budget total. Example: "Thinking (5) — £15,000".
- Drag-and-Drop cards between columns. On drop — optimistic update and PATCH lead_cards/{id} (stage_id).

### 2.3. Lead card

- Display: Name (contact), Budget (expected amount), source icon (TG, WA, Site), last contact date, "AI Active" tag (if bot is handling the conversation).
- **Lead Rotting:** if a card has been in the "Thinking" stage (or equivalent) for more than N days (e.g. 2), the card visually "reddens" (border or background).
- Clicking a card opens a Drawer on the right: chat history + "Generate prepayment link" button. Optional: clicking the WhatsApp icon on the card opens the same Drawer with focus on chat.

### 2.4. Lead Drawer

- Tabs or sections: Info (contact, source, budget), Chat (embedded window or link to OmniChat with this contact), Tasks, Notes.
- "Generate prepayment link" button → enter amount → send link to the selected channel (e.g. WhatsApp).

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Funnel stages | `GET /api/v1/admin/lead-stages` or from config | List of LeadStage for columns. |
| Lead cards | `GET /api/v1/admin/lead-cards` (filter by stage, clinic) | With fields: contact, budget, source channel, last_contact_at, ai_active. |
| Move | `PATCH /api/v1/admin/lead-cards/{id}` (stage_id) | Idempotent for Optimistic UI. |
| Stage aggregates | Same API with aggregation or a separate endpoint | Budget totals per stage. |
| Chat, payments | Omnichannel + Payments | As in TPF_MODULE_OMNICHAT. |

---

## 4. UI Rules

- EmptyState: if a stage has no cards — not an empty column, but "Drag here" or a minimal placeholder.
- Empty board — EmptyState "No leads" with a hint about sources (chat, website).
- Skeleton on first column and card load.
- Drawer opens on the right, not covering the entire Kanban (or covering only the right portion).

---

## 5. References

- Page: `/admin/crm`.
- Entities: LeadStage, LeadCard, OmnichannelChat/Contact, Payment (prepayment).
