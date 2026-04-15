# Customer Prerequisites Checklist

Use this during discovery/scoping to confirm readiness before committing to a start date.

---

## Step 1 — Select architecture path

Confirm licence before scoping. This determines everything downstream.

| Customer has | Path |
|---|---|
| M365 E7 or M365 Copilot add-on + Frontier programme enrolment | **Option A — Cowork-first** |
| Copilot Studio licence (no Cowork) | **Option B — Studio-first** |

If unsure: check `admin.microsoft.com > Copilot settings`. If "Cowork" appears under Agents, Option A is available.

---

## Option A — Cowork-first prerequisites

### Licences
- [ ] M365 E7 (includes Cowork) OR M365 Copilot add-on + Frontier programme confirmed
- [ ] Copilot Cowork accessible at https://m365.cloud.microsoft — "Organize my inbox" prompt appears
- [ ] Power Automate Premium licence confirmed (at minimum 1 licence for the account running PA flows)

### Compliance gates (mandatory before Phase 1)
- [ ] **Anthropic processing region confirmed** in M365 Admin Centre — Copilot Settings — document the result (Australian Privacy Act APP 8)
- [ ] M365 data residency region confirmed (AU East / AU Southeast or relevant region)
- [ ] Privacy notice updated to disclose: AI-assisted triage AND Cowork/Anthropic cross-border processing
- [ ] Compliance gate outcome documented in project tracker — **do not begin Phase 1 until cleared**

### Tenant / environment
- [ ] Solution-aware cloud flow sharing enabled in Power Platform Admin Centre
- [ ] DLP policies do not block Outlook, SharePoint, Planner, or Teams connectors

---

## Option B — Studio-first prerequisites

### Licences
- [ ] Copilot Studio licence (maker role) confirmed
- [ ] Power Automate Premium licence confirmed

### Tenant / environment access
- [ ] Azure AD App Registration can be created (or already exists) — need client ID + secret
- [ ] Power Platform environment exists (not default trial — production or sandbox type)
- [ ] System Administrator role available for the implementation account
- [ ] Solution-aware cloud flow sharing enabled in Power Platform Admin Centre
- [ ] DLP policies do not block Outlook, SharePoint, Planner, Teams, or OneNote connectors

### Microsoft Graph App Registration (Phase 0 prerequisite)

This must be completed before Phase 0 exit. See [`runbooks/graph-access-setup.md`](../runbooks/graph-access-setup.md) for the full step-by-step guide.

- [ ] Entra ID App Registration created — client ID and tenant ID documented
- [ ] Client secret created — stored in secrets vault, expiry date calendared
- [ ] Admin consent granted for all required Graph API permissions
- [ ] Phase 2 minimum permissions confirmed: `Mail.Read`, `Sites.ReadWrite.All`, `User.Read`, `offline_access`
- [ ] Phase 3 permissions added when ready: `Mail.ReadWrite`, `Tasks.ReadWrite`, `ChannelMessage.Send`
- [ ] Power Automate connection tested: read mail ✅, write SharePoint ✅
- [ ] Copilot Studio knowledge source connected to SharePoint ✅
- [ ] Client secret **not** stored in any document, email, or flow variable — vault only

### Compliance gates
- [ ] M365 data residency region confirmed
- [ ] Privacy notice updated to disclose AI-assisted triage

---

## Both paths — shared prerequisites

### SharePoint
- [ ] SharePoint Online site designated for this project
- [ ] Permission to create lists and document libraries in that site
- [ ] EmailTriageQueue list can be created with full schema (see `runbooks/setup-checklist.md`)
- [ ] ProjectRegistry list can be created and populated before Phase 2

### Outlook
- [ ] Target mailbox confirmed (personal inbox / shared mailbox)
- [ ] Folder taxonomy agreed: **DO / FYI / IGNORE / NeedsReview / Urgent** (or customer's taxonomy)
- [ ] Folders can be created in the target mailbox
- [ ] Existing Outlook Rules and Quick Steps inventoried (SOP-00 input)

### Teams
- [ ] Target channel for urgent alerts identified (name + ID — needed for PA flow)
- [ ] Permission to post to that channel

### Planner
- [ ] Planner plan created (or can be created): **PITA Action Register**
- [ ] Bucket structure agreed: DO – This Week / DO – Upcoming / Waiting / NeedsReview / Done

---

## Discovery questions

Ask these during the scoping call:

1. **Do you have M365 E7 or M365 Copilot with Frontier access?** *(Architecture path decision)*
2. **What are your 5 most active projects right now?** *(Needed to seed the project registry)*
3. **What does a typical urgent email look like?** *(Needed to define the urgency threshold — SOP-02 input)*
4. **Where do you currently record project status?** *(Confirms which systems the agent should update)*
5. **Do you have a naming convention for emails related to projects?** *(e.g., subject line codes — prompt rule input)*
6. **What happens today when an important email is missed?** *(Helps define the NeedsReview escalation path)*
7. **Who will run the weekly review?** *(Critical for Phase 6 — without an owner, improvement stalls)*
8. **Are there senders or domains that should always be treated as priority?** *(Prompt template rules — Option A / instructions — Option B)*
