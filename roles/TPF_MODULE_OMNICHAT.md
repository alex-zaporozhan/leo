# TPF_MODULE_OMNICHAT — Omnichannel Chat (Smart Hub)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.3), `REV_RAG_MAP_INNOVATIONS.md` (section 4).

---

## 0. Design Reference and Visual Standard

**Reference:** Intercom Inbox — three-column layout + Linear Issue — right inspector panel
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

Chat is the operational communication centre. The administrator handles 20–50 conversations per day without leaving the page.

Three key feelings:
1. **Each chat's status is visible in the list** — "waiting for reply", "AI handling", "draft" — without opening
2. **Reply and create booking — from one place** — Action Bar below input, Drawer over chat
3. **Client context on the right — always** — next visit, LTV, funnel stage without extra clicks

Frequent actions: reply to a message, create a booking from chat, change funnel stage.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Three columns: inbox / conversation / inspector — no horizontal page scroll
- [ ] Status badges in inbox: red/blue/yellow — distinguishable without hover
- [ ] Incoming bubble: grey background; outgoing: accent colour
- [ ] Timestamp under bubble: xs, dimmed — does not compete with text
- [ ] Action Bar icons: equal size, with tooltip
- [ ] AI Suggestions: horizontal chips, not a full-width block
- [ ] Right column: collapses to icons (Ctrl+Shift+L)

---

## 1. Purpose

Chat is the operational environment. The administrator must not leave the page to book a client, send an invoice, or send a form. Everything is accessible from the three-column interface.

---

## 2. Screen structure (three columns)

### 2.1. Left column — Smart Inbox

- Chat list with badges:
  - `[Waiting for reply]` (red) — last message from client.
  - `[AI handling]` (blue) — autopilot is enabled.
  - `[Draft]` (yellow) — admin started typing but did not send.
- Filter chips at the top: All, Unanswered, VIP, Payment error (etc.).
- Preview below the name — not only text, but also system events: "🔄 AI booked for 14:00", "💳 Payment £500 received".

### 2.2. Centre column — Chat (Canvas)

- Message feed (bubbles): client / admin / AI with label.
- **Above input field:** AI Magic Suggestions — up to 3 buttons with response options based on the last message context. Click inserts text into the field (or sends it).
- **Rich Bubbles:** Payment link displayed as a card: "Invoice #123 for £500 [PAID / PENDING]" with real-time status update.
- **Action Bar below input:**
  - Book (Calendar) → opens Booking creation Drawer with pre-filled patient phone.
  - Invoice (CreditCard) → modal/mini-Drawer: amount → "Send link to chat".
  - Questionnaire (FileText) → select form and send link to chat.
  - AI on/off (Robot) — autopilot toggle for this chat.
- Hotkeys: Cmd+J — focus chat search, Cmd+Enter — send message.

### 2.3. Right column — Client Context (Command Center)

- Block 1: Identification — avatar, full name, loyalty level (LTV, visit count), "Frequently cancels" badge if applicable.
- Block 2: Timeline — next visit (date, time, doctor) with "Change time", "Cancel" buttons; if no visit — last visit + "Book again".
- Block 3: Funnel stage (LeadStage) — dropdown for quick transfer (Lead → Thinking → Booked etc.).
- Block 4: Internal notes — text field with yellow background (sticky note). Admin-only, client cannot see.
- **"Create booking"** button — opens Drawer with pre-filled phone/patient.

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Chat list | Existing omnichannel/chats | With flags: last_from_client, ai_enabled, draft. |
| Messages | Existing messages | + system type for events (booking created, payment). |
| AI Suggestions | `POST /api/v1/ai/suggest-replies` or in chat context | Context from last messages. |
| Payment status | Webhook or poll for Rich Bubbles | Update card "Pending" → "Paid". |
| Patient, visits, lead | Patient, Bookings (next), LeadCard | Right column. |
| Create booking | `POST /api/v1/admin/bookings` | With patient_id/phone from context. |
| Payment link | Payment API (e.g. Stripe) | Generate and send to chat. |
| Send form | Paperless API | Form link to chat. |
| AI on/off | Chat/contact settings | OmnichannelAiSettings or per-chat flag. |

---

## 4. UI Rules

- Booking Drawer and invoice/form modals do not navigate away from the chat page — they open over or beside it.
- EmptyState: if no chats — "No conversations" with a hint on connecting channels.
- Skeleton on load of chat list and message history.
- Escape closes the open Drawer, not the chat itself.

---

## 5. References

- Page: `/admin/chat` (or per project routing).
- Entities: OmnichannelChat, OmnichannelMessage, OmnichannelContact, Conversation, Patient, Booking, LeadCard, Payment.
