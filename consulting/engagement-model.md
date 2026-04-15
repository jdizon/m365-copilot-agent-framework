# Engagement Model — M365 Copilot Agent Framework

**Maintained by:** Momentums | **Service:** M365 Agentic Workflow Implementation

---

## What we deliver

An end-to-end agentic workflow built on Microsoft 365 tools the customer already has:

- **Option A (Cowork-first):** Copilot Cowork as the reasoning and triage layer; Power Automate flows for governance and downstream actions
- **Option B (Studio-first):** Copilot Studio agent as the reasoning layer; Power Automate flows for execution
- SharePoint knowledge base + audit trail (both paths)
- Power BI operational metrics (both paths)
- SOPs and runbooks so the customer owns and operates it independently

The architecture option is confirmed at scoping based on customer licence. See [`docs/architecture.md`](../docs/architecture.md) for the full comparison.

This is not a custom app. Everything lives inside the customer's M365 tenant.

---

## Architecture selection

| Customer profile | Recommended path |
|---|---|
| M365 E7 licence or M365 Copilot add-on with Frontier access | **Option A — Cowork-first** |
| M365 Copilot Studio licence; no Cowork access | **Option B — Studio-first** |
| Cowork access but precision <90% in specific categories | **Option A + Studio hybrid** (Studio flows for gap categories only) |

---

## Engagement tiers

### Starter — Phases 0–3
**What's delivered:** Foundations through automated governance layer  
**Duration:** 4–6 weeks  
**Includes:**
- Discovery and scoping (OpenSpec)
- SharePoint list schemas + setup (EmailTriageQueue, ProjectRegistry)
- Option A: Cowork prompt library (versioned in SharePoint); 2-week evaluation baseline
- Option B: Copilot Studio agent (instructions, triggers)
- Power Automate flows: Log-Audit-Entry, Create-Planner-Task, Send-Teams-Alert, Log-NeedsReview
- Precision gate review (Phase 1 → Phase 2 promotion)
- SOP-00 to SOP-05
- Handover and training

### Standard — Phases 0–4
**What's delivered:** Starter + financial tracking and Power BI dashboards  
**Duration:** 6–8 weeks  
**Adds:**
- **Agent Performance dashboard** — triage precision per category, NeedsReview rate, prompt version history
- **Team Operations dashboard** — task status, workload, commitment tracker
- **Financial Tracking dashboard** — budget vs actuals, billing pipeline, invoice ageing
- **Executive Portfolio dashboard** — portfolio health, financial summary
- **Accountability Scorecard** — weekly metric status per owner with automated Friday Teams alerts
- Financial intent classifications (INVOICE_RECEIVED, BILLING_MILESTONE, BUDGET_ALERT, etc.)
- Financial Power Automate flows (Detect-Financial-Intent, Create-Invoice-Task, Log-PO-Entry, Flag-Budget-Overrun)
- FinancialRegister + AccountabilityScorecard SharePoint lists
- SOP-05 (weekly review) + first review facilitation

### Full — Phases 0–6
**What's delivered:** Complete platform with knowledge enrichment and continuous improvement  
**Duration:** 10–14 weeks  
**Adds:**
- Email style guide (built from customer sent mail patterns)
- Teams chat weekly summary + meeting transcript action item capture
- Improvement pipeline: NeedsReview correction loop → prompt/instruction update cycle
- Power BI improvement view (precision trends, correction frequency)
- Quarterly review framework

### Retainer — Ongoing Phase 6
**What's delivered:** Monthly continuous improvement partnership  
**Cadence:** Monthly  
**Includes:**
- Weekly review facilitation (or async review + async update)
- Monthly prompt template update (Option A) or instruction update + deployment (Option B)
- Quarterly knowledge base review
- Priority support for Normal/Emergency change events

---

## Customer prerequisites

Before kickoff, the customer must have:

### Option A — Cowork-first

| Prerequisite | Why |
|---|---|
| M365 E7 or M365 Copilot add-on + Frontier enrolment | Cowork access |
| Copilot Cowork accessible at m365.cloud.microsoft | Agent triage layer |
| Power Automate Premium licence | PA flows — premium connectors |
| SharePoint Online site designated for this project | Audit log + registry |
| Anthropic processing region documented in M365 Admin Centre | APP 8 compliance gate |

### Option B — Studio-first

| Prerequisite | Why |
|---|---|
| Copilot Studio licence (maker role) | Agent creation |
| Power Automate Premium licence | HTTP-triggered flows |
| Azure AD App Registration (client ID + secret) | `pac` CLI / trigger credentials |
| System Administrator in Power Platform environment | Solution deployment |
| SharePoint Online site designated for this project | Knowledge base + audit log |

See [`consulting/prerequisites-checklist.md`](prerequisites-checklist.md) for the full discovery checklist.

---

## What the customer owns after delivery

- All Cowork prompt templates (versioned in SharePoint Prompt_Templates library) — Option A
- All Copilot Studio agent configuration files — Option B
- All Power Automate flow definitions
- All SharePoint schemas and SOP documents
- Power BI dashboards connected to their own SharePoint audit log
- Running system in their own M365 tenant — no external dependency

**No vendor lock-in.** The customer can update Cowork prompts in SharePoint (Option A) or update Studio agent instructions in the UI (Option B). We train them to do both.

---

## Differentiation

| Feature | This framework | Typical automation project |
|---------|---------------|--------------------------|
| Agent reasons, doesn't just route | Yes | No — rule-based |
| HITL approval gates before consequential actions | Yes (Cowork native + PA) | No |
| Updates sources of truth, not just folders | Yes | Rarely |
| Built-in continuous improvement loop | Yes | No |
| Precision-gated phase progression | Yes | No |
| Version-controlled prompt templates / instructions | Yes | No |
| Customer-owned and operable | Yes | Varies |
| Evidence-based accuracy gates | Yes | No |
| Expands to Teams + meetings | Yes (Phase 5) | No |
