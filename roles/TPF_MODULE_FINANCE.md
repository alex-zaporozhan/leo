# TPF_MODULE_FINANCE — Finance & Inventory (ERP)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.6), `REV_RAG_MAP_INNOVATIONS.md` (section 7).

---

## 0. Design Reference and Visual Standard

**The standard to reach:** transaction table; cash registers and balances
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

Finance is the zone of strictness and trust. The administrator works with money — any error is painful.

Three key feelings:
1. **Each cash register balance is the main number** — large, fw={700}, visible immediately on opening
2. **The transaction form will not let you make a mistake** — required fields clearly marked, submit blocked without them
3. **Checkout Hub — the final point of a visit** — everything in one place: services, payment, inventory

Frequent actions: deposit to a register, create a transaction, complete a visit through Checkout.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Register balance: Text size="xl" fw={700} — most prominent number in the row
- [ ] Transaction form: unfilled required fields — border red.5 on submit
- [ ] Transaction type: colour indicator (green = income, red = expense)
- [ ] Checkout Hub: three sections with visual divider (not three separate screens)
- [ ] Amount to pay: large in the total, before the confirm button
- [ ] Drawer for all forms — not modals (except confirm)
- [ ] ActionMenu in register row: Deposit / Withdraw / Transfer

---

## 1. Purpose

Strict control: cash registers with Deposit/Withdraw/Transfer operations; transaction form with required fields; tech cards in the service card; when completing a visit — Checkout Hub (receipt, cash register, materials write-off).

---

## 2. Screen structure

### 2.1. Cash registers (table)

- Columns: register name, current balance. ActionMenu per row: "Deposit", "Withdraw", "Transfer between registers".
- Each action opens a Drawer with a transaction form: cashbox_id (or from/to for transfer), amount, type (income/expense/transfer), category. Required fields — do not save without them (Relational Integrity).

### 2.2. Transaction form (Drawer)

- Fields: register (select), amount, type (Income/Expense), category. For transfer — source register and destination register.
- Validation on frontend and backend.

### 2.3. Tech cards (in service card)

- In service editing Drawer — "Consumables" tab. Dynamic list: select Product from dropdown, quantity (Amount). Example: Syringe — 1 unit, Anaesthesia — 2 ml. CRUD rows in tech card.

### 2.4. Checkout Hub (visit completion)

- Opens when a booking is moved to "Completed" status (or via "Open checkout" button from Attention Feed).
- **Block 1 — Receipt:** list of services rendered, "+ Add item" button, discount % field, total amount.
- **Block 2 — Payment:** select register/method (Cash, Card terminal, Instant transfer, Charge from membership). Enter payment amount.
- **Block 3 — Inventory:** Accordion "Write off materials". System pre-fills consumables per tech card for selected services; admin can adjust (e.g. 2 syringes instead of 1). Confirm write-off.

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Cash registers | GET/POST/PUT cashboxes | With balance (computed or field). |
| Transactions | POST transactions (cashbox_id, amount, type, category) | Required field validation. |
| Tech cards | Part of Service or separate service_consumables | Product + amount. |
| Complete visit | POST booking complete + payments + consumables write-off | Single flow or multiple calls. |

---

## 4. UI Rules

- EmptyState for register list: "Add first register".
- Drawer for all forms (transaction, service editing with consumables). Modal only for confirmation ("Write off materials?").
- Checkout Hub — either full-screen step, or large Drawer/modal with tabs/sections.

---

## 5. References

- Pages: `/admin/finance`, registers; services — from the services section, "Consumables" tab; Checkout — from booking or Attention Feed. Entities: Cashbox, Transaction, Service, Product, ServiceConsumable, Booking, Payment.
