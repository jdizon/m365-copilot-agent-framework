# OpenSpec — M365 Copilot Agent
## [Customer Name] — [Agent Name]

**Version:** v1.0 | **Owner:** [Consultant Name] | **Customer:** [Customer Name]
**Date:** [Date]

---

## Evidence basis
This specification is grounded in:
1. Customer's internal artefacts (provided during discovery)
2. Microsoft's publicly documented platform capabilities
3. The M365 Copilot Agent Framework evidence pack

Recommendations are clearly labelled as recommendations — not claims about vendor capabilities.

---

## 1. Scope
[Agent name] is an autonomous triage agent for [scope: personal inbox / shared mailbox / Teams channel]. It:
- Reviews incoming [emails / messages / transcripts]
- Matches signals to active projects and tasks
- Updates sources of truth via Power Automate action flows
- Routes low-confidence signals to a human review queue

---

## 2. Goals
- [ ] Autonomous activation when [new email arrives / message posted / meeting ends]
- [ ] Consistent classification into agreed intent categories
- [ ] Source-of-truth updates in [SharePoint / Planner / Loop / OneNote — select applicable]
- [ ] [Urgent-only / agreed threshold] Teams alerts
- [ ] Full audit trail in SharePoint log list
- [ ] Team-operable SOPs so the system survives beyond one person

---

## 3. Non-goals (v1)
- No autonomous external email sending without approval
- No autonomous calendar edits without approval
- No permanent deletion of email or records
- No actions above agreed confidence threshold without human review

---

## 4. Microsoft 365 applications used

| Application | Role |
|------------|------|
| Outlook | Inbox source; trigger origin |
| Microsoft Copilot Studio | Agent reasoning, intent classification, action orchestration |
| Power Automate | Event trigger plumbing; action flow execution |
| Microsoft Teams | Urgent alerts; [optional: chat trigger input] |
| SharePoint Online | Project/task registry; knowledge base; audit log |
| Planner / To Do | Task registry; task creation and updates |
| Loop | [Optional] Collaborative page updates |
| OneNote | [Optional] Note additions |
| Power BI | Operational metrics and improvement dashboard |

---

## 5. Intent classification schema

| Intent | Description | Action |
|--------|-------------|--------|
| `STATUS_UPDATE` | Project/task status changed | Update SharePoint + Planner |
| `ACTION_ITEM` | New task or commitment identified | Create Planner task |
| `BLOCKER` | Something is blocked | Update status + urgent Teams alert |
| `DECISION` | Decision recorded | Add OneNote note + Loop update |
| `COMPLETION` | Task/project complete | Update Planner + SharePoint |
| `FYI` | Informational only | Log only |
| `NEEDS_REVIEW` | Low confidence | Log to NeedsReview queue |

*Customise these categories for the customer's context during Phase 0.*

---

## 6. Key prerequisites

- [ ] Outlook folders/categories exist with consistent naming
- [ ] SharePoint project registry list created and populated
- [ ] Power Automate connections established with least privilege
- [ ] SharePoint audit log list schema created
- [ ] Urgent criteria defined and agreed (prevents Teams alert fatigue)
- [ ] Privacy notice updated to disclose automated processing
- [ ] M365 data residency confirmed (admin centre)
- [ ] Solution-aware cloud flow sharing enabled (Power Platform admin)

---

## 7. Governance

Treat agent instruction changes, model changes, and routing rules as changes to a production system.

| Change type | Examples | Required steps |
|-------------|----------|----------------|
| Standard | Prompt tweaks, minor taxonomy edits | Log only |
| Normal | New category, routing logic change, threshold update | Review + rollback plan |
| Emergency | Disable routing after incident | Restore safe state + post-change review |

---

## 8. Acceptance criteria

- [ ] All trigger executions visible in activity log + SharePoint audit list
- [ ] Zero external sends performed autonomously
- [ ] Classification precision ≥90% over 2 weeks before Phase 3 autonomous actions
- [ ] Urgent alerts have <10% false positive rate
- [ ] NeedsReview queue reviewed weekly

---

## 9. Evidence links

- [M365 Copilot Agent Framework](https://github.com/jdizon/m365-copilot-agent-framework)
- [Evidence research pack](evidence-research.md)
- [Microsoft autonomous agent guidance](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/autonomous-agents)
