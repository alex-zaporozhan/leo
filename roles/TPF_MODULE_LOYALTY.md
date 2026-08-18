# TPF_MODULE_LOYALTY — Memberships & Loyalty (Loyalty Engine)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.8), `BUSINESS_LOGIC_V2.md` (§ 2.10), `ARCH_FRONTEND_BUSINESS_OS_UX.md` (§ 3.10), `REV_RAG_MAP_INNOVATIONS.md` (section 7b). Backend: `docs/dev_artifacts/DEV_ARTIFACT_BACKEND_IMPLEMENTATION.md` (Phase B6).

## 0. Design Reference and Visual Standard

**Reference:** Apple Wallet — card stack; Revolut — loyalty progress bar
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

Loyalty is the emotional centre of the PWA and a value instrument for the clinic. The patient must feel: "I have something valuable".

Three key feelings:
1. **Membership as a valuable card** — gradient, large progress bar, expiry date — not just text
2. **Progress visible instantly** — "2 visits left until VIP" — number large
3. **Checkout Auto-detect — effortless** — membership checkbox appears automatically, no need to search

Frequent actions (PWA): check balance, book using membership.
Frequent actions (Admin): sell membership, check Liability Dashboard.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Digital Pass card: gradient background, not a white card with text
- [ ] Progress bar: large (height 8px+), accent colour, "X of Y" label
- [ ] Expiry date: red text if < 7 days
- [ ] In Checkout: membership checkbox is explicit, not hidden in a dropdown
- [ ] Family member: "Access: [owner name]" badge on the card
- [ ] Liability Dashboard: "Money in the air" metric — xl fw={700}
- [ ] EmptyState PWA: "Buy membership" as CTA

---

## 1. Purpose

**Loyalty & Wealth Engine:** not "just a number 10→9", but the level of top fitness apps and premium clinics. Retention through service packages and deposits, family sharing, Zero-Click payment from membership, AI burn reminders, liability dashboard.

---

## 2. Interface blocks

### 2.1. Digital Pass (Apple Wallet Style in PWA)

- **Where:** Patient app (PWA), "My Memberships" / profile section.
- **Visual:** Memberships — a stack of gradient cards (like bank cards, with visual NFC chip). On the card: package name, large progress bar "5 of 10", expiry date.
- **Action:** Button on card `[Book using membership]` → opens Booking Wizard with filtered package services; at checkout — "Paid by membership".
- **API:** `GET /api/v1/patient/loyalty/subscriptions` (or equivalent) with fields: name, remaining/total (visits or amount), expires_at, services_included — for service filter in the booking wizard.

### 2.2. Family Sharing (admin)

- **Where:** Patient card Drawer → **"Memberships"** tab.
- **Logic:** If the patient has a package (deposit), a `[+ Add family member]` button is displayed. Opens Spotlight search over the patient database; selected patient is linked to the package (FamilyLink). In the linked person's PWA, the membership card shows "Access granted: [Owner Name]".
- **API:** CRUD for FamilyLink relationships; when a visit is deducted, check: visit patient = package owner OR in the shared_with list.

### 2.3. Auto-Checkout (Checkout Hub)

- **Where:** When moving a booking to "Completed", Checkout Hub opens (payment block).
- **Smart Detection:** When checkout opens, `GET booking/{id}/checkout-info` (or as part of booking) returns a list of suitable active packages (package_id, name, remaining_visits/amount). If a package covers the visit service — the "Payment" block shows a checkbox: `✅ "Massage Course" membership available. [Use 1 visit]`. Admin clicks "Confirm"; duplicate payment by card/cash is not allowed.
- **API:** When complete_booking, optional parameter `use_subscription_id`; backend calls use_subscription_for_booking only when present.

### 2.4. Liability Dashboard

- **Where:** "Finance" section — tab or block for the owner.
- **Metric:** "Money in the air" (Unearned Revenue) — the amount clients paid for memberships but have not yet used (clinic's debt to clients).
- **API:** `GET /admin/clinics/{id}/finance/liability` or a block in the report; aggregate over balances of active CustomerSubscription (remaining_visits × notional price or remaining_amount).

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Patient packages (PWA) | GET patient/loyalty/subscriptions | name, remaining/total, expires_at, services_included. |
| Eligible packages for visit | GET booking/{id}/checkout-info or part of booking | List of eligible subscriptions for Checkout Hub. |
| Complete with membership | POST booking complete | Body: use_subscription_id (optional). |
| Family by package | GET/POST/DELETE admin/loyalty/packages/{id}/family | FamilyLink CRUD. |
| Liability | GET admin/clinics/{id}/finance/liability | Unearned Revenue. |

---

## 4. UI Rules

- Loyalty section in BUSINESS menu: `/admin/loyalty` — list of packages (templates), sold memberships per clinic; subsections if needed.
- In Patient Drawer, "Memberships" tab: list of purchased packages + "+ Add family member" button for deposits/packages with sharing.
- Checkout Hub: when a suitable membership is available — explicit checkbox "Charge from membership"; no manual package search.
- EmptyState: "No memberships" with CTA "Create package" (admin) / "Buy package" (PWA by context).

---

## 5. References

- **Pages:** `/admin/loyalty`, Patient Drawer ("Memberships" tab), Checkout Hub, Finance section (Liability). PWA: profile / "My Memberships".
- **Entities (backend):** SubscriptionPackage, CustomerSubscription, SubscriptionUsage, FamilyLink (see BUSINESS_LOGIC_V2.md § 2.10, DEV_ARTIFACT_BACKEND_IMPLEMENTATION.md B6).
