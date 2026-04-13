# M365 Copilot Agent Framework

A consulting and implementation framework for building intelligent, agentic workflows on Microsoft 365 using Copilot Studio, Power Automate, and the Microsoft Graph.

**Maintained by:** [Momentums](https://momentums.com.au)

---

## What this is

A production-ready framework for deploying a **Copilot Studio agent** that:

- Monitors incoming signals (email, Teams chats, meeting transcripts)
- Reasons over content using generative AI
- Matches signals to active projects and tasks
- Updates sources of truth (SharePoint, Planner, Loop, OneNote) via Power Automate flows
- Learns and improves over time through a structured feedback loop

This is not a rule-based automation. The agent reasons. Power Automate executes. Your systems stay current without manual intervention.

---

## Architecture

```
Signal arrives (email / Teams chat / meeting transcript)
        ↓
Power Automate: event trigger
        ↓ payload
Copilot Studio Agent
    ├── Reads signal content
    ├── Queries project/task registry (SharePoint knowledge source)
    ├── Classifies: project match + intent + confidence
    └── Calls Power Automate action flow
            ├── Update-Project-Status   → SharePoint list
            ├── Update-Planner-Task     → Planner
            ├── Create-Planner-Task     → Planner
            ├── Update-Loop-Page        → Loop workspace
            ├── Add-OneNote-Note        → OneNote
            ├── Send-Teams-Alert        → Teams channel/chat
            └── Log-NeedsReview         → Human review queue
                    ↓
            Log-Audit-Entry             → SharePoint audit list (every action)
```

---

## Phases

| Phase | Name | What it delivers |
|-------|------|-----------------|
| 0 | Foundations | Outlook taxonomy, SharePoint schemas, Planner setup, compliance pre-flight |
| 1 | Manual baseline | Prove taxonomy; draft SOPs; capture baseline accuracy |
| 2 | Assisted automation | Classify + label + log; no autonomous moves until ≥90% precision |
| 3 | Autonomous routing | Agent updates sources of truth; NeedsReview for low confidence |
| 4 | Task capture + dashboards | Planner tasks from signal; Power BI operational metrics |
| 5 | Knowledge enrichment | Project docs + style guide as knowledge sources; Teams + meetings triggers |
| 6 | Continuous improvement | Correction loop; versioned instructions; improvement dashboards |

---

## Repository structure

```
├── docs/
│   ├── architecture.md          Full architecture + design decisions
│   ├── evidence-research.md     Evidence base: accuracy benchmarks, licensing, compliance
│   └── open-spec-template.md    OpenSpec template for scoping with customers
├── implementation/
│   ├── phase-0-foundations.md
│   ├── phase-1-manual-baseline.md
│   ├── phase-2-assisted-automation.md
│   ├── phase-3-autonomous-routing.md
│   ├── phase-4-task-capture.md
│   ├── phase-5-knowledge-enrichment.md
│   └── phase-6-continuous-improvement.md
├── runbooks/
│   ├── setup-checklist.md       Full build checklist (Phases 0-6)
│   ├── graph-access-setup.md    Microsoft Graph App Registration — step-by-step setup guide
│   ├── change-control.md        Standard / Normal / Emergency change process
│   └── weekly-review.md         SOP-05: weekly review and improvement cadence
├── templates/
│   ├── agent/
│   │   ├── instructions-template.md   Agent instructions (fill in for each customer)
│   │   └── trigger-config.md          Event trigger configuration reference
│   ├── sharepoint/
│   │   ├── project-registry-schema.md  SharePoint list column definitions
│   │   └── audit-log-schema.md         Audit list column definitions
│   ├── power-automate/
│   │   └── flows/                      Flow JSON definitions (one per action type)
│   └── knowledge-base/
│       ├── project-registry-template.md
│       └── email-style-guide-template.md
├── consulting/
│   ├── engagement-model.md      How to scope and deliver this for customers
│   ├── prerequisites-checklist.md  What the customer must have before kickoff
│   └── customer-onboarding.md   Onboarding questions and discovery template
└── CHANGELOG.md
```

---

## Prerequisites (per customer)

- Microsoft 365 tenant with Exchange Online
- Microsoft Copilot Studio licence (or M365 Copilot add-on)
- Power Automate Premium licence
- Azure AD App Registration with Graph API permissions (see [`runbooks/graph-access-setup.md`](runbooks/graph-access-setup.md))
- System Administrator role in the target Power Platform environment
- SharePoint Online site for project registry + knowledge base

Optional (enables Phase 5 Semantic Index):
- Microsoft 365 Copilot licence per user

---

## Engagement model

See [`consulting/engagement-model.md`](consulting/engagement-model.md) for the full delivery model.

| Engagement type | Scope | Typical duration |
|---|---|---|
| **Starter** | Phases 0-3 (foundations through autonomous routing) | 4-6 weeks |
| **Standard** | Phases 0-4 (add task capture + dashboards) | 6-8 weeks |
| **Full** | Phases 0-6 (complete with knowledge + improvement pipeline) | 10-14 weeks |
| **Retainer** | Ongoing Phase 6 (weekly review + instruction updates) | Monthly |

---

## Metrics and accountability

Five Power BI dashboards provide full visibility across the organisation:

| Dashboard | Audience | What it shows |
|-----------|----------|---------------|
| **Executive Portfolio** | Leadership | Portfolio health, financial summary, communication intelligence |
| **Team Operations** | Project managers | Task progress, workload distribution, commitment tracking |
| **Agent Performance** | IT/ops | Classification accuracy, NeedsReview trends, improvement timeline |
| **Financial Tracking** | Finance, account managers | Budget vs actuals, billing pipeline, invoice ageing |
| **Accountability Scorecard** | All | Weekly metric status per owner — Green/Amber/Red with Teams alerts |

Every metric has an owner, a threshold, and an automated escalation path. When a metric goes Red, Power Automate sends a Teams alert to the accountable person — no manual monitoring required.

See [`docs/metrics-and-accountability.md`](docs/metrics-and-accountability.md) for the full framework and [`templates/powerbi/dashboard-spec.md`](templates/powerbi/dashboard-spec.md) for build specifications.

---

## Evidence base

All implementation decisions in this framework are evidence-grounded. See [`docs/evidence-research.md`](docs/evidence-research.md) for the full research pack including:

- Email workload statistics (121 emails/day; 28% of knowledge worker time)
- AI email classification accuracy benchmarks (F1 ~0.84 baseline; HITL 97-99.9%)
- Microsoft platform constraints (trigger auth, billing, action chain limits)
- Licensing cost comparison (Copilot vs Power Automate Premium)
- Australian Privacy Act compliance requirements

---

## License

MIT — use freely for customer engagements. Attribution appreciated.
