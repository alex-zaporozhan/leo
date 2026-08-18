# Copy Protection and Third-Party Handover

> What to do before handing the package to a client. For every project after deploy.

---

## 1. Honest answer: there is no technical protection in the package

If you hand the client a folder (e.g. `booking_deploy`) with code, Docker images and instructions — **technically** they can copy it and pass it to another party. The application does not "phone home" and does not check a license. This is normal for the "one-time delivery + client's own server" model.

**Conclusion:** the primary protection is the **contract and licence wording**, not the code inside the folder.

---

## 2. What to do before handover

### 2.1 Contract or offer

Before handing over the archive/folder access, the client must accept the terms. Minimum:

- **Right of use** — for one instance only (one organisation / one bot / one site, as agreed).
- **No transfer** — the client may not transfer, copy or resell the folder, archive or access to third parties.
- **Liability** — in case of violation (passing it to a "friend"), you are entitled to consider the contract breached and claim compensation under the contract.

It is best to formalise the wording in a **service agreement** or **licence agreement** (offer + acceptance act). Agree on the legal form (individual entrepreneur / LLC, website offer) with a lawyer for your jurisdiction.

### 2.2 LICENSE file in the deploy folder (optional)

In the root of the folder you hand to the client, you can place a short **LICENSE.txt** (or **LICENSE**) file, for example:

```
Licence for Use

You are granted the right to use this software
for one instance only (one bot / one organisation) in accordance
with the agreement dated [date] No. [number].

Transfer, copying or distribution of the materials
to third parties without the written consent of the rights holder is prohibited.

© [Year] [Your name/company]. All rights reserved.
```

This does not replace the contract, but records your position and reminds the client of the restrictions.

---

## 3. Optional: technical protection in the future

If the product becomes more expensive or the number of clients grows, you can add **key-based activation**:

- On first launch the application requests a key check from your server (client domain/ID + key).
- The key is tied to a domain or one instance (e.g. to an ID on first launch).
- Without a valid key the bot/admin panel does not work or works in a restricted mode.

Such a mechanism requires code changes and a small key-verification service (your backend). For one-time deliveries up to ~$500–1000, a contract + LICENSE in the folder is usually sufficient.

---

## 4. Checklist before client handover

- [ ] Contract signed or offer accepted (right of use, prohibition of transfer).
- [ ] LICENSE.txt (or equivalent) is in the deploy folder with a prohibition on transfer to third parties.
- [ ] Correspondence/contract explicitly states: "materials are delivered in a single copy for use by the customer only".

---

**Preparation of the contract/offer and LICENSE.txt is performed by the @LAWYER role** (docs/ROLE_LAWYER.md). Call @LAWYER with the request: "Before client handover: draw up contract/offer, place LICENSE.txt in the deploy folder per the template from DEPLOY_LICENSE_AND_PIRACY.md".

*This document is used at the "after deploy, before client handover" stage. See PROCESS_LAUNCH.md and ROLE_LEAD.md.*
