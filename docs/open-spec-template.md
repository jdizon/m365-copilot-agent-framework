# OpenSpec — M365 Copilot Agent
## [Customer Name] — [Agent Name]

**Version:** v1.0 | **Owner:** [Consultant Name] | **Customer:** [Customer Name]  
**Date:** [Date] | **Architecture:** [ ] Option A — Cowork-first | [ ] Option B — Studio-first

---

## Evidence basis

This specification is grounded in:
1. Customer's internal artefacts (provided during discovery)
2. Microsoft's publicly documented platform capabilities
3. The M365 Copilot Agent Framework evidence pack ([`docs/evidence-research.md`](evidence-research.md))

Recommendations are clearly labelled. This document does not make claims about vendor capabilities not confirmed in official Microsoft documentation.

---

## 1. Scope

[Agent name] is an AI-powered triage workflow for [scope: personal inbox / shared mailbox / Teams channel]. It:

- Classifies incoming [emails / messages / transcripts] using [Copilot Cowork / Copilot Studio agent]
- Routes signals into predefined categories: DO / FYI / IGNORE / NeedsReview / Urgent (customise as needed)
- Writes structured records to SharePoint (system of record) via Power Automate flows
- Raises Teams alerts for urgent signals
- Creates Planner tasks for actioned items
- Provides Power BI dashboards for triage accuracy, task completion, and financial health

**Architecture option selected:** [ ] Option A — Cowork-first | [ ] Option B — Studio-first  
*(Document the reason for the choice based on customer licence and Cowork availability)*

---

## 2. Goals

- [ ] Email classified and routed into predefined folders using [Cowork native triage / Studio agent]
- [ ] Every triage decision written to SharePoint as an auditable record
- [ ] Planner tasks auto-created for DO items via Power Automate
- [ ] Urgent signals trigger Teams alert via Power Automate
- [ ] Financial intent signals (invoices, POs, payments) identified and logged to FinancialRegister *(Phase 3)*
- [ ] Power BI dashboards provide accuracy and accountability visibility *(Phase 4)*
- [ ] System is team-operable: SOPs documented, change control applied, weekly review cadence

---

## 3. Non-goals (v1)

- No autonomous external email sending without approval
- No autonomous calendar edits without approval
- No permanent deletion of email as part of automation
- No signals from sources outside agreed scope ([adjust as needed])
- *(Option A only)* No Copilot Studio custom classification agent unless Cowork precision falls below 90%

---

## 4. Microsoft 365 applications used

| Application | Role | Required |
|------------|------|---------|
| Outlook | Inbox source; triage folders | Yes |
| **Copilot Cowork** | Native triage layer | Option A only |
| **Copilot Studio** | Custom agent reasoning layer | Option B only |
| Power Automate | Governance — log, Planner tasks, Teams alerts | Yes |
| Microsoft Teams | Urgent notifications, accountability alerts | Yes |
| SharePoint Online | System of record — EmailTriageQueue, ProjectRegistry, etc. | Yes |
| Planner | Action register | Yes |
| Power BI | Operational dashboards | Phase 4 |
| Loop / OneNote | Decision logs, meeting notes | Optional |

---

## 5. Intent classification schema

| Category | Definition | Action |
|--------|-------------|--------|
| `DO` | Requires direct action from the user | Planner task + pin/flag |
| `FYI` | Informational only | Mark read; log |
| `IGNORE` | Newsletter, automated notification, no value | Archive |
| `URGENT` | Action required today or escalation | Teams alert + Planner task |
| `NEEDS_REVIEW` | Low confidence — human decides | Log to EmailTriageQueue; no action |
| `INVOICE_RECEIVED` | Invoice or bill received | FinancialRegister + Planner task |
| `PAYMENT_CONFIRMED` | Payment processed | Update FinancialRegister |
| `PO_APPROVED` | Purchase order approved | FinancialRegister |
| `BUDGET_ALERT` | Budget risk signal | Update ProjectRegistry + Teams alert |
| `BILLING_MILESTONE` | Stage complete — invoice can be raised | Planner task + Teams alert |

*Customise categories for the customer's context during Phase 0.*

---

## 6. Key prerequisites

### Option A — Cowork-first
- [ ] Copilot Cowork accessible at m365.cloud.microsoft
- [ ] Anthropic processing region confirmed in M365 Admin Centre — result documented
- [ ] M365 data residency confirmed (relevant region)
- [ ] Privacy notice updated to disclose AI triage AND Cowork/Anthropic cross-border processing
- [ ] Power Automate Premium licence active
- [ ] Solution-aware cloud flow sharing enabled

### Option B — Studio-first
- [ ] Copilot Studio licence confirmed (maker role)
- [ ] Power Automate Premium licence active
- [ ] Azure AD App Registration created — see [`runbooks/graph-access-setup.md`](../runbooks/graph-access-setup.md)
- [ ] System Administrator role in Power Platform environment
- [ ] Solution-aware cloud flow sharing enabled
- [ ] Privacy notice updated to disclose AI-assisted triage
- [ ] M365 data residency confirmed

### Both paths
- [ ] Outlook folders created: DO / FYI / IGNORE / NeedsReview / Urgent (or customer's taxonomy)
- [ ] SharePoint EmailTriageQueue / AgentAuditLog list created with full schema
- [ ] SharePoint ProjectRegistry list created
- [ ] Planner plan created with agreed bucket structure
- [ ] Urgent criteria defined and agreed
- [ ] Existing Outlook Rules and Quick Steps audited (SOP-00)

---

## 7. Governance

All changes to classification logic (Cowork prompt templates or Studio agent instructions), Outlook taxonomy, PA flow logic, and accuracy thresholds are treated as changes to a production system.

| Change type | Examples | Required steps |
|-------------|----------|----------------|
| Standard | Prompt tweaks, minor taxonomy edits | Document in SharePoint change log only |
| Normal | New category/folder, routing logic, threshold change | Review + rollback plan before applying |
| Emergency | Disable routing after incident | Restore safe state + post-change review |

---

## 8. Acceptance criteria

- [ ] All triage decisions logged (100% PA flow coverage — every folder move creates a SharePoint record)
- [ ] No external email send occurs without explicit user approval
- [ ] Classification precision ≥90% across all categories over 2 weeks before reducing approval frequency (Option A) or enabling Phase 3 autonomous updates (Option B)
- [ ] Urgent Teams alerts fire for 100% of Urgent-folder moves; false-positive rate ≤10%
- [ ] Planner tasks created for 100% of DO folder moves (PA flow coverage)
- [ ] Financial intents logged to FinancialRegister for 100% of matching emails (Phase 3)
- [ ] All 5 Power BI dashboards published and reviewed weekly (Phase 4)
- [ ] SOPs 00–05 documented and stored in SharePoint SOP library

---

## 9. Reference links

- [M365 Copilot Agent Framework](https://github.com/jdizon/m365-copilot-agent-framework)
- [Evidence research pack](evidence-research.md)
- [Cowork integration guide](cowork-integration.md)
- [Cowork overview — Microsoft Learn](https://learn.microsoft.com/en-us/microsoft-365/copilot/cowork/)
- [Cowork responsible AI FAQ](https://learn.microsoft.com/en-us/microsoft-365/copilot/responsible-ai/cowork-responsible-ai-faq)
- [Microsoft autonomous agent guidance](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/autonomous-agents) *(Option B)*
