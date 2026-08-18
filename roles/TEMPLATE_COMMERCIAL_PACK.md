# Commercial Package Template (after deploy)

> Filled for each new project after deploy. Activated by @BIZ: price, sales description, objections, security.
> Result — a selling text and an answer to: "Why do I want to buy this product?"

---

## 1. Purpose

The commercial package is needed to:

- Sell the product on Kwork (or another platform): one ready text and card structure.
- Close objections before the client voices them.
- Have arguments at hand: what the product can do, why it is needed, why it is safe.

**When to do it:** after deploy, before handoff to client (together with licence/contract, see DEPLOY_LICENSE_AND_PIRACY.md).

---

## 2. Package structure (fill in per project)

Create a file **COMMERCIAL_PACK_[ProjectName].md** (e.g. `COMMERCIAL_PACK_BOOKING.md`) and fill in the sections below.

### 2.1 One sentence about the product

- For whom and what it does.
- Example: "Telegram booking bot + web admin panel for small businesses: clients book themselves, you manage services and schedule with no monthly subscription."

### 2.2 Price and payback

- Recommended price (one-time / subscription).
- Comparison with alternatives (competitors, their price).
- ROI for client (in money or time): how quickly it pays off.

### 2.3 What the product can do (features)

- **For the end user** (bot client / visitor): step by step, what they can do.
- **For the owner/admin**: what is configurable, what is visible in the panel, what reports/notifications exist.

Bullet list without filler — concrete capabilities.

### 2.4 Admin panel (if present)

- Login (credentials, where it opens).
- Sections: what is in the menu, what is in each section.
- Key scenarios: creating a service, changing the schedule, viewing appointments, rolling back changes, etc.
- Backups, notification settings — briefly.

### 2.5 Why buy it (3–5 points)

- Time / money savings.
- Control and transparency.
- No monthly subscription (if applicable).
- Deploy in N minutes, own server, own data.
- Local hosting support (if applicable).

Phrased so the client can say: "Yes, I need this."

### 2.6 Kwork listing description (selling text)

- Headline (short, benefit-led).
- 1–2 paragraphs: client's problem → how the product solves it → what they get as a result.
- Feature list (bullets).
- What is included in delivery (files, instructions, what the client needs to provide).
- Price and delivery time.
- Call to action (write, discuss the task).

Plain language, no jargon. Length: to fit a Kwork order description (typically 1–2 screens).

### 2.7 Objections and answers (top 5)

In the format:

| Objection (how the client will say it) | Answer (1–2 sentences) | Evidence (what to show/give) |
|--------------------------------|--------------------------|------------------------------------|
| Too expensive | … | ROI calculator, subscription comparison |
| What if Telegram gets blocked | … | Fallback (VK/Email), local hosting |
| … | … | … |

Plus 1–2 "do not buy" objections (red flags): if the client says X — this is not our client, do not waste time.

### 2.8 Security and reliability (briefly)

- Where data is stored (hosting, location).
- Personal data: how processed, where stored (local data-protection law if applicable).
- Admin access: password, password change.
- Backups: how made, what to recommend to client.

2–4 points to remove the fear "will we lose data / will we be hacked".

### 2.9 What is included in delivery

- Project folder/archive.
- README (installation, first run).
- .env.example and how to fill it in.
- Deploy in N minutes (Docker / script).
- Optional: short video or screencast of first login.

---

## 3. Who fills it in

- **@BIZ** — price, competitors, ROI, objections, "why buy", text structure for Kwork.
- **@LEAD** — approval, completeness (nothing missed), transfer to roles_update when canonised.

Feature data is taken from BUSINESS_LOGIC.md, TECHNICAL_SPEC.md and the actual deploy package.

---

## 4. Connection to other documents

- **Before client handoff:** DEPLOY_LICENSE_AND_PIRACY.md (contract, LICENSE in folder).
- **Process:** PROCESS_LAUNCH.md — "After deploy" stage (commercial package + licence).
- **Role:** ROLE_LEAD.md — after deploy, prepare commercial package using this template.

---

*Template used for each new project after deploy. Example of a filled package: COMMERCIAL_PACK_BOOKING.md (in the main project, not copied to roles_update).*
