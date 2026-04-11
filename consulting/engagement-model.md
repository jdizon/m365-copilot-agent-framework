# Engagement Model — M365 Copilot Agent Framework

**Maintained by:** Momentums | **Service:** M365 Agentic Workflow Implementation

---

## What we deliver

An end-to-end agentic workflow built on Microsoft 365 tools the customer already has:
- Copilot Studio agent (reasoning layer)
- Power Automate flows (execution layer)
- SharePoint knowledge base + audit trail
- Power BI operational metrics
- Version-controlled agent instructions (updateable without re-engagement)
- SOPs and runbooks so the customer owns and operates it independently

This is not a custom app. Everything lives inside the customer's M365 tenant.

---

## Engagement tiers

### Starter — Phases 0-3
**What's delivered:** Foundations through autonomous source-of-truth updates
**Duration:** 4-6 weeks
**Includes:**
- Discovery and scoping (OpenSpec)
- SharePoint list schemas + setup guide
- Copilot Studio agent (instructions, triggers, knowledge sources)
- Power Automate flows: Update-Project-Status, Update-Planner-Task, Create-Planner-Task, Send-Teams-Alert, Log-Audit-Entry, Log-NeedsReview
- Accuracy gate review (Phase 2 → Phase 3 promotion)
- SOP-01 to SOP-04
- Handover and training

### Standard — Phases 0-4
**What's delivered:** Starter + task capture, financial tracking, and Power BI dashboards
**Duration:** 6-8 weeks
**Adds:**
- **Executive Portfolio dashboard** — portfolio health, financial summary, communication intelligence
- **Team Operations dashboard** — task progress, workload, commitment tracker
- **Financial Tracking dashboard** — budget vs actuals, billing pipeline, invoice ageing
- **Accountability Scorecard** — weekly metric status per owner with automated Teams alerts
- Financial intent classifications (INVOICE_RECEIVED, BILLING_MILESTONE, BUDGET_ALERT, etc.)
- Financial Power Automate flows (Update-Financial-Status, Create-Invoice-Task, Log-PO-Entry, Flag-Budget-Overrun)
- FinancialRegister + AccountabilityScorecard SharePoint lists
- SOP-05 (weekly review) + first review facilitation
- Planner task creation flow with commitment tracking

### Full — Phases 0-6
**What's delivered:** Complete platform with knowledge enrichment and continuous improvement
**Duration:** 10-14 weeks
**Adds:**
- SharePoint document library as agent knowledge source
- Email style guide (built from customer sent mail patterns)
- Teams chat trigger + meeting transcript trigger
- Improvement pipeline: correction loop → instruction update cycle
- Power BI improvement dashboard (precision trends, correction frequency)
- Quarterly review framework

### Retainer — Ongoing Phase 6
**What's delivered:** Monthly continuous improvement partnership
**Cadence:** Monthly
**Includes:**
- Weekly review facilitation (or async review + async update)
- Monthly instruction update + deployment
- Quarterly knowledge base review
- Priority support for Normal/Emergency change events

---

## Customer prerequisites

Before kickoff, the customer must have:

| Prerequisite | Why |
|---|---|
| M365 tenant with Exchange Online | Email trigger origin |
| Copilot Studio licence (or M365 Copilot add-on) | Agent creation |
| Power Automate Premium licence | HTTP-triggered flows |
| Azure AD App Registration (for deployment) | `pac` CLI access |
| System Administrator in Power Platform environment | Solution deployment |
| SharePoint Online site designated for this project | Registry + knowledge base |

See [`prerequisites-checklist.md`](prerequisites-checklist.md) for the full discovery checklist.

---

## What the customer owns after delivery

- All agent configuration files (YAML/JSON) in their own git repository
- All Power Automate flow definitions
- All SharePoint schemas and SOP documents
- The `pac` CLI deployment script — they can update and redeploy independently
- Running agent in their own Copilot Studio environment
- Power BI dashboard connected to their own SharePoint audit log

**No vendor lock-in.** The customer can update the agent in Copilot Studio UI or via code. We train them to do both.

---

## Differentiation

| Feature | This framework | Typical automation project |
|---------|---------------|--------------------------|
| Agent reasons, doesn't just route | Yes | No — rule-based |
| Updates sources of truth, not just folders | Yes | Rarely |
| Built-in continuous improvement loop | Yes | No |
| Version-controlled instructions | Yes | No |
| Customer-owned and operable | Yes | Varies |
| Evidence-based accuracy gates | Yes | No |
| Expands to Teams + meetings | Yes (Phase 5) | No |
