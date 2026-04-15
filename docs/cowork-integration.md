# Copilot Cowork Integration Guide

**Version:** 1.0.0 | **Updated:** April 2026  
**Applies to:** Option A (Cowork-first) engagements

---

## What is Copilot Cowork?

Copilot Cowork is Microsoft's action/execution layer for M365 Copilot. It performs multi-step tasks over minutes to hours, with built-in human approval gates before consequential actions. It is not a sidebar chatbot — it is a task engine.

- **Announced:** 9 March 2026 (Microsoft Frontier Transformation event)
- **Frontier programme availability:** 30 March 2026
- **General Availability:** 1 May 2026 (via M365 E7 suite, $99 USD/user/month)
- **Powered by:** Anthropic Claude (running inside M365 security boundary)

**Official resources:**
- [Cowork overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/cowork/)
- [Get started with Cowork](https://learn.microsoft.com/en-us/microsoft-365/copilot/cowork/get-started)
- [Use Cowork](https://learn.microsoft.com/en-us/microsoft-365/copilot/cowork/use-cowork)
- [Cowork FAQ](https://learn.microsoft.com/en-us/microsoft-365/copilot/cowork/cowork-faq)
- [Responsible AI FAQ](https://learn.microsoft.com/en-us/microsoft-365/copilot/responsible-ai/cowork-responsible-ai-faq)

---

## Licencing

| Path | Licence | Cost (approx USD) |
|---|---|---|
| **Cowork via Frontier** | M365 Copilot add-on + Frontier enrolment | ~$30/user/month (add-on) |
| **Cowork via E7** | M365 E7 (bundles M365 E5 + Copilot + Cowork + Entra Suite) | $99/user/month |
| **Studio-first (no Cowork)** | Copilot Studio + Power Automate Premium | ~$25/user/month |

Power Automate Premium (~$15/user/month) is still required in addition to E7 if PA flows need premium connectors.

---

## Admin enablement

1. Confirm tenant is enrolled in Frontier programme: **admin.microsoft.com > Copilot settings**
2. Verify Anthropic is enabled as a subprocessor (on by default — check under Copilot settings)
3. Navigate to: **admin.microsoft.com > Copilot > Agents > All agents > Cowork**
4. Select: "Deploy to entire organisation" or "Specific users/groups" (recommended for pilot)
5. **Note for EU tenants:** Cowork is OFF by default due to Anthropic data boundary requirements — must be explicitly enabled

**Official admin reference:** [Manage Copilot Cowork for your organisation](https://learn.microsoft.com/en-us/copilot/microsoft-365/cowork/cowork-admin-governance)

---

## Data residency (critical for Australian engagements)

Cowork uses Anthropic Claude models. Processing may occur in the **US/Global datazone** — not Australia East/Southeast.

**Australian Privacy Act implications:**

| APP | Requirement | Cowork implication |
|---|---|---|
| APP 3 | Only collect information reasonably necessary | Include only necessary email context in triage prompts |
| APP 6 | Use/disclose only for stated purpose | Cowork processes for inbox triage — confirm this aligns with stated business purpose |
| APP 8 | Cross-border disclosure obligations | Anthropic (US) receiving email content = cross-border disclosure. Customer must accept Anthropic as subprocessor via Microsoft's agreement |
| APP 10 | Accuracy of personal information | Cowork approval gates = accuracy control |

**Required action before Phase 1:**
1. Open M365 Admin Centre > Copilot > Settings
2. Confirm Anthropic is listed as a subprocessor; note regional restrictions shown
3. Update customer's privacy notice to disclose: *"We use Microsoft Copilot Cowork, which processes email content using Anthropic's AI models. This may involve transfer of data to the United States."*
4. Document compliance gate as cleared in project tracker

**Official data residency reference:** [Copilot and Anthropic apps in Microsoft 365](https://learn.microsoft.com/en-us/microsoft-365/copilot/copilot-anthropic-apps)

---

## Cowork's 13 built-in skills

| Skill | PITA relevance |
|---|---|
| **Email** | Core — classify, route, draft replies, archive, flag | ★★★ |
| **Scheduling** | Link DO emails to calendar follow-ups | ★★ |
| **Calendar Management** | Coordinate inbox with meeting load | ★★ |
| **Meetings** | Create meetings from email action items | ★★ |
| **Daily Briefing** | Morning triage summary | ★★★ |
| **Enterprise Search** | Find project context across M365 | ★★★ |
| **Communications** | Post Teams alerts for urgent items | ★★ |
| **Deep Research** | Background research for complex emails | ★ |
| **Word** | Document drafting from email threads | ★ |
| **Excel** | Financial follow-up tracking | ★ |
| **PowerPoint** | Proposal follow-ups | ★ |
| **PDF** | Read/create PDF attachments | ★ |
| **Adaptive Cards** | Structured Teams notifications | ★ |

Skills combine automatically within a single Cowork task.

---

## What Cowork can and cannot do (as of April 2026)

| Action | Supported |
|---|---|
| Move email to folder | ✅ (with approval) |
| Pin / flag / archive / mark read | ✅ (with approval) |
| Delete emails (bulk newsletters) | ✅ (with approval) |
| Reply / draft reply | ✅ (draft shown for approval) |
| Create Outlook folder | ✅ (with approval) |
| Post Teams message | ✅ (with approval) |
| Create calendar event | ✅ (with approval) |
| Write to SharePoint list | ❌ — use Power Automate flow |
| Create Planner task | ❌ — use Power Automate flow |
| Access local files | ❌ — OneDrive/SharePoint only |
| Delete SharePoint/OneDrive files | ❌ |
| Mobile support | ❌ (Frontier preview) |
| Persistent custom instructions across sessions | ❌ — include rules in each prompt |

---

## How Cowork approval gates work

Before Cowork takes any consequential action it shows an approval dialog containing:

- What action it wants to take
- A preview of the content (email draft, folder destination, meeting details)
- A risk level indicator: Low / Medium / High
- Three options: **Approve**, **Cancel**, **Skip future approvals for this type**

**Best practice for engagement delivery:**

- In Phase 1 (evaluation baseline): instruct the customer to **never skip approvals**. Every approval/rejection is a data point for precision measurement.
- In Phase 2: review which categories have consistently ≥95% precision before enabling "skip future approvals" for those categories.
- Keep manual approval permanently for: external email replies, Urgent classification, bulk deletes.

---

## Prompt template structure

Effective Cowork triage prompts follow this structure:

```
[Action verb] my inbox. [Rules block]. [Fallback instruction].

Rules:
- Treat emails from [domain/sender] as [category]
- Treat emails with "[keyword]" in the subject as [category]
- Archive emails from [newsletter senders] without asking
- Flag anything from [client/contact names] as [category]
- Move anything I haven't read in [n] days and is from a newsletter to IGNORE

Fallback: If you are unsure, move the email to NeedsReview — do not guess.
```

See [`templates/cowork/prompt-templates.md`](../templates/cowork/prompt-templates.md) for a full library of starter templates.

---

## Integration with Power Automate

Cowork and Power Automate connect through **Outlook folder events**, not direct API calls:

1. Cowork moves an email to a triage folder (e.g., "DO") after user approval
2. Power Automate trigger fires: "When an email is moved to a folder"
3. PA flow creates the SharePoint record, Planner task, or Teams alert

This is simpler and more reliable than Studio event triggers because:
- No Copilot Credits billing per email (PA folder trigger is included in PA Premium)
- No maker credential scope issues
- No 15-action chain limit risk
- Each PA flow is independently testable

**Trigger setup in Power Automate:**
- Connector: **Office 365 Outlook**
- Trigger: "When an email is moved to a folder"
- Folder: select the specific triage folder
- Note: folder names are **case-sensitive** in the Outlook connector

See [`runbooks/power-automate-flows.md`](../runbooks/power-automate-flows.md) for full build steps.

---

## Known limitations (Frontier preview, April 2026)

| Limitation | Workaround |
|---|---|
| No persistent custom instructions between sessions | Save prompt templates in SharePoint; paste at start of each session |
| No native Planner task creation | Power Automate flow |
| No native SharePoint list writes | Power Automate flow |
| Data processed in US/Global (not Australia) | Compliance gate before production |
| No mobile support | Web browser on mobile as interim |
| Custom skills not validated by Microsoft | Review all outputs carefully |
| File attachment max 200 MB | Use SharePoint links for large files |
