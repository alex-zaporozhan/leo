# TPF_MODULE_DASHBOARD — Dashboard (Action Center)

> **[v6.16] PROJECT EXAMPLE (dental Business OS), NOT a universal canon.** Universal module type direction — `roles/TEMPLATE_MODULE_DEV.md §2`; screen business minimum — `roles/DOMAIN_STANDARDS.md`; tokens — `roles/FRONTEND_DESIGN_EXCELLENCE.md`; geometry — `roles/LAYOUT_INVARIANTS.md`; business logic — project specification + `docs/artifacts/BUSINESS_LOGIC.md`. Not created by default for a new project. Target repository location: `docs/artifacts/reference/tpf/`.

> **Prefix TPF_** — Tech Passport Frontend. Module tech passport.  
> **Connections:** `TPF_MASTER.md` (sect. 4.1), `REV_RAG_MAP_INNOVATIONS.md` (section 3).

---

## 0. Design Reference and Visual Standard

**Reference:** Stripe Dashboard — metric widgets + Vercel Analytics — event timeline
**Constitution:** `roles/FRONTEND_DESIGN_EXCELLENCE.md` §2 (operational contour)

**Implementation chain:**
→ @DESIGN creates `DESIGN_SPEC_[MODULE].md` using this file as the source of business requirements
→ @FRONTEND passes Visual Quality Gate §6 before handing to @DEV
→ @DEV implements per DESIGN_SPEC + DEV_PROMPTS

**This file:** business requirements + functional map + Visual Intent.
Design decisions → `DESIGN_SPEC_*.md`. API contracts → `ARCH_MODULE_*.md`.

### Visual Intent — what the user should feel

The dashboard is the first thing the administrator sees in the morning. Within 10 seconds they must understand: "Is everything okay?" and "What needs my attention right now?".

Three key feelings:
1. **Instant pulse of the day** — four metrics at the top give a picture in a second
2. **Attention Feed as a task queue** — not "information to review", but concrete cards with action buttons
3. **Timeline is live** — bookings change status in real time, the administrator sees "who is in the chair right now"

Frequent actions: check metrics, take an Attention Feed alert into work, click a booking in the timeline.

### QA Visual Criteria — verified by @QA_ARCH

- [ ] Metrics: white cards with border, NOT a coloured fill for the whole block
- [ ] Trend: green arrow ↑ on growth, red ↓ on decline — no dash
- [ ] Attention Feed: "All quiet" empty state — not an empty column
- [ ] Feed cards: action button visible without hover (not hidden)
- [ ] Timeline: status indicator (green circle = in chair) is visible
- [ ] Loading skeleton: mimics the shape of metric widgets and columns
- [ ] Sparkline under metric: 32px height, no axes, accent colour

---

## 1. Purpose

The dashboard is not just statistics, it is the shift management centre: metrics, a feed of events requiring action, and a timeline of upcoming bookings.

---

## 2. Screen structure

### 2.1. Top Bar

Four metric widgets:

1. **Bookings today** — count + dynamics (e.g. +5% vs yesterday), Sparkline when needed.
2. **Revenue** — amount for the day/period + dynamics.
3. **New leads** — count + dynamics.
4. **NPS / Cancellations** — compact indicator and trend.

Under each metric — micro-chart (Sparkline) and percentage growth vs last week. Green arrow on growth, red on decline.

### 2.2. Left column (~60%) — Attention Feed

Feed of event cards requiring attention:

- Types (with colour coding):
  - Critical (red): patient cancelled a booking for today → button "Offer to waitlist".
  - Finance (orange): visit completed but checkout not closed → "Open checkout".
  - AI Assistant (indigo): AI is handling a conversation with a conflicting tone → "Take over the conversation".
  - Stock (yellow): stock below critical level → "Create purchase task".
- Each card has a "Take into work" button (move to My Focus).

### 2.3. Right column (~40%) — Schedule Timeline

Vertical list of today's bookings:

- Cards: time, patient, doctor, service. Status indicator (waiting / in chair / completed).
- Green circle — in chair, grey — waiting. Click on a card opens the Booking Drawer.

### 2.4. Optional: "Revenue saved (AI)" widget

If AI Revenue Hunter is implemented: "Revenue saved by AI overnight" block (e.g. +£500).

---

## 3. Endpoints

| Data | Method/path | Note |
|------|-------------|------|
| Metrics (bookings, revenue, leads, cancellations) | Aggregates per clinic for period | Existing reports or new summary endpoints. |
| Attention Feed | `GET /api/v1/admin/attention-feed` or equivalent | List of events with type and payload for buttons. |
| Today's timeline | `GET /api/v1/admin/clinics/{id}/schedule?date=today` | Same schedule with day filter. |
| Claim alert | `PATCH /api/v1/admin/attention-feed/{id}/claim` or via task | Move to My Focus (assign to current admin). |

---

## 4. UI Rules

- When Attention Feed is empty — EmptyState with text "All quiet for now" (not an empty column).
- When timeline is empty — EmptyState "No bookings today".
- Loading: Skeleton for metric widgets and both columns, no full-screen Loader.
- Feed card buttons lead to a specific action (open Booking Drawer, checkout, chat, task).

---

## 5. References

- Page: `/admin` or home after login.
- Components: metrics, Feed cards, timeline. Context: current clinic from AdminClinicContext.
