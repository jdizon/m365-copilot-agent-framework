# Customer Prerequisites Checklist

Use this during discovery/scoping to confirm readiness before committing to a start date.

---

## Licences (confirm before kickoff)

- [ ] Microsoft 365 tenant — Exchange Online included
- [ ] Copilot Studio licence OR Microsoft 365 Copilot add-on (per user making the agent)
- [ ] Power Automate Premium — at minimum 1 licence for the service account running flows
- [ ] *(Phase 5 optional)* Microsoft 365 Copilot per-user licence — required for Semantic Index

---

## Tenant / environment access

- [ ] Azure AD App Registration can be created (or already exists) — need client ID + secret
- [ ] Power Platform environment exists (not default trial — production or sandbox type)
- [ ] System Administrator role available for the implementation account
- [ ] Solution-aware cloud flow sharing is enabled (check Power Platform Admin Centre)
- [ ] DLP policies do not block Outlook, SharePoint, Planner, Teams, or OneNote connectors

---

## SharePoint

- [ ] SharePoint Online site designated for this project
- [ ] Permission to create lists and document libraries in that site
- [ ] ProjectRegistry list can be created and populated before Phase 2

---

## Outlook

- [ ] Target mailbox confirmed (personal inbox / shared mailbox)
- [ ] Folder taxonomy agreed (folder names match the intent categories)
- [ ] Folders can be created in the target mailbox

---

## Teams

- [ ] Target channel or chat for urgent alerts identified
- [ ] Permission to post to that channel/chat

---

## Planner / OneNote / Loop

- [ ] Planner plan created (or can be created) for the customer's projects
- [ ] OneNote notebook and section structure agreed (if OneNote notes are in scope)
- [ ] Loop workspace exists (if Loop updates are in scope)

---

## Data and compliance

- [ ] Privacy notice can be updated to disclose automated processing
- [ ] M365 data residency region confirmed (relevant for GDPR / Australian Privacy Act)
- [ ] Legal or privacy review completed if processing client communications

---

## Discovery questions

Ask these during the scoping call:

1. **What are your 5 most active projects right now?** *(Needed to seed the project registry)*
2. **What does a typical urgent email look like?** *(Needed to define the urgency threshold)*
3. **Where do you currently record project status?** *(Confirms which systems the agent should update)*
4. **Who would be the agent maker?** *(The account whose credentials the triggers run under)*
5. **Do you have a naming convention for emails related to projects?** *(e.g., subject line codes)*
6. **What happens today when an important email is missed?** *(Helps define the NeedsReview escalation path)*
7. **Who owns the weekly review?** *(Critical for Phase 6 — without an owner, improvement stalls)*
