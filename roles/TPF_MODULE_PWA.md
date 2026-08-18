# TPF_MODULE_PWA — Patient PWA (Client App)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.12), `REV_RAG_MAP_INNOVATIONS.md` (section 15), TECH_PASSPORT_FRONTEND_UI_LOGIC (sections 6, PWA).

---

## 0. Design Reference and Visual Standard

**Reference:** Revolut mobile — bottom nav + cards; Apple Health — tickets and progress
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

The PWA is the clinic's face to the patient. Opened on a phone, it must feel like a native app, not a "mobile website".

Three key feelings:
1. **Visit ticket — the main element on the home screen** — patient opens and immediately sees the next booking
2. **Book in 3 taps** — select doctor → select date → confirm
3. **Loyalty wallet like Apple Wallet** — beautiful cards, progress, not just numbers

Frequent actions: view a booking, book, check balance.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Bottom Navigation: fixed, safe-area-inset-bottom
- [ ] Next Visit Ticket: gradient, border-radius 16px+, QR if needed
- [ ] Touch targets: min 44×44px for all buttons
- [ ] Stories bar: round avatars, horizontal scroll without scrollbar
- [ ] Booking Wizard: doctor photo large, date carousel horizontal
- [ ] Pull-to-Refresh: present on home screen and in chat
- [ ] prefers-color-scheme: light/dark theme follows system

---

## 1. Purpose

The PWA is the clinic's face to the patient. Must feel like a native app: bottom navigation, visit ticket, stories, book in 3 taps, loyalty wallet.

---

## 2. App Shell (PWA shell)

- **Bottom Navigation Bar:** Fixed panel at the bottom with 4–5 icons: Home, Book, Chat, Profile. Active — accent colour. No top hamburger menu.
- **Safe Areas:** `padding-bottom: env(safe-area-inset-bottom)` for screen notches (iPhone etc.).
- **Pull-to-Refresh:** On booking and chat pages — pull down to refresh.
- Meta tags: `apple-mobile-web-app-capable`, `theme-color` for blending with the system bar.

---

## 3. Home screen

- **Greeting:** "Good morning, [Name]!" (or time of day).
- **Next Visit Ticket:** If a future booking exists — a ticket card (Pass) with rounded corners, gradient: date, time, doctor, service, "Cancel/Reschedule" and "Add to calendar" buttons, QR if needed. If no bookings — EmptyState with "Book now" button.
- **Stories Bar:** Horizontal scroll with round avatars (like Instagram Stories). Tap — full-screen story (promotions, news). Data: PromoPost / Story.

---

## 4. Booking Wizard

- **Visual First:** Doctor/practitioner selection — large photos and rating/reviews.
- **Date carousel:** Horizontal scroll of days (Mon 12, Tue 13…), slots below (Chips). Selecting a date immediately updates the slots.
- **Upsell (optional):** Before confirmation — "Usually taken with this service: X (+£5). Add?".
- Minimum steps; confirmation and prepayment if needed per current flow.

---

## 5. Profile and loyalty (Wallet)

- **Digital Wallet:** Virtual clinic card, cashback balance large, progress to next level (e.g. "2 visits left until VIP").
- **Referral button:** "Gift a friend £10 (and get £10 yourself)" — Web Share API to send link via messengers.

---

## 6. Chat (Patient)

- Message history, text sending. Matches the current patient chat API. Pull-to-Refresh.

---

## 7. Endpoints (pointers)

- Current patient, bookings: `GET /api/v1/patient/bookings`, create/cancel.
- Slots: `GET /api/v1/doctors/{id}/schedule`.
- Stories/Promo: public_marketing.
- Loyalty/balance: per Loyalty module (if implemented).

---

## 8. UI Rules

- No top global menu; Bottom Nav only.
- Next Visit Ticket — the most prominent block on the home screen.
- EmptyState everywhere there is no data (no bookings, no stories, etc.).
- Support for light/dark theme per system (prefers-color-scheme).
