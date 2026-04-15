# M365 Copilot Agent Framework

A consulting and implementation framework for building intelligent, governed workflows on Microsoft 365 — connecting Copilot Cowork and Copilot Studio to SharePoint, Planner, and Power BI.

**Maintained by:** [Momentums](https://momentums.com.au)  
**Version:** 2.0.0 | **Updated:** April 2026

---

## What this is

A production-ready framework for deploying an AI-powered inbox triage and workflow automation system on Microsoft 365. Two architecture paths are supported depending on the customer's licence:

### Option A — Cowork-first (recommended for M365 Copilot / E7 customers)
- **Copilot Cowork** handles email classification, routing, and triage natively with built-in HITL approval
- **Power Automate flows** write structured records to SharePoint, create Planner tasks, and fire Teams alerts
- No Copilot Studio custom classification agent is required unless Cowork precision falls below 90%
- Faster to deploy; lower build complexity; built-in approval gates

### Option B — Studio-first (for customers without Cowork access)
- **Copilot Studio agent** handles reasoning, project matching, and intent classification via event triggers
- **Power Automate flows** execute actions across SharePoint, Planner, Loop, OneNote, and Teams
- Full control over classification logic; supports custom intent categories and multi-source signals

Both paths share the same system-of-record layer (SharePoint + Planner), Power BI accountability framework, and continuous improvement model. The architecture decision is made at Phase 0 based on the customer's licence and Cowork availability.

---

## Architecture

### Option A — Cowork-first

```
Email arrives in Outlook inbox
        ↓
Copilot Cowork — native triage (classify, route, folder move)
        ↓  [User approves in Cowork dialog]
Email moved to triage folder (DO / FYI / IGNORE / NeedsReview / Urgent)
        ↓
Power Automate — governance layer (triggered by folder move)
        ├── Log-Audit-Entry        → SharePoint EmailTriageQueue
        ├── Create-Planner-Task    → Planner PITA Action Register   (DO folder)
        ├── Send-Teams-Alert       → Teams urgent channel            (Urgent folder)
        ├── Log-NeedsReview        → EmailTriageQueue, IsCorrect=null (NeedsReview folder)
        └── Detect-Financial-Intent → FinancialRegister              (Phase 3)
```

### Option B — Studio-first

```
Signal arrives (email / Teams chat / meeting transcript)
        ↓
Power Automate: event trigger → payload
        ↓
Copilot Studio Agent
    ├── Classifies intent + project match
    ├── Assesses confidence
    └── Calls Power Automate action flow
            ├── Update-Project-Status  → SharePoint list
            ├── Create-Planner-Task    → Planner
            ├── Send-Teams-Alert       → Teams channel/chat
            ├── Log-NeedsReview        → Human review queue
            └── Log-Audit-Entry        → SharePoint audit list
```

---

## Phases

| Phase | Name | What it delivers |
|-------|------|-----------------|
| 0 | Foundations | Outlook taxonomy, SharePoint schemas, Planner setup, compliance gates |
| 1 | Baseline evaluation | Cowork-first: 2-week supervised triage + precision measurement. Studio-first: manual baseline + SOP drafting |
| 2 | Governance layer | PA flows automate logging, Planner task creation, Teams alerts |
| 3 | Scale + financial layer | Financial intent detection; accountability lists; reduced approval friction |
| 4 | Power BI dashboards | 5 dashboards: Agent Performance, Team Ops, Financial, Portfolio, Accountability |
| 5 | Knowledge enrichment | Reply drafting; Teams + meeting transcript signals |
| 6 | Continuous improvement | Prompt/instruction versioning; weekly review; quarterly audit |

---

## Repository structure

```
├── docs/
│   ├── architecture.md              Full architecture for both Option A and Option B
│   ├── cowork-integration.md        Copilot Cowork setup, capabilities, and integration guide
│   ├── evidence-research.md         Evidence base: accuracy benchmarks, licensing, compliance
│   ├── metrics-and-accountability.md Full accountability framework and Power BI specs
│   └── open-spec-template.md        OpenSpec template for scoping with customers
├── runbooks/
│   ├── setup-checklist.md           Full build checklist (Phases 0-6, Cowork-first path)
│   ├── power-automate-flows.md      PA flow build guide: all flows step-by-step
│   ├── graph-access-setup.md        Microsoft Graph App Registration — step-by-step
│   ├── change-control.md            Standard / Normal / Emergency change process
│   └── weekly-review.md             SOP-05: weekly review and improvement cadence
├── templates/
│   ├── cowork/
│   │   └── prompt-templates.md      Cowork prompt templates (fill in per customer)
│   ├── agent/
│   │   └── instructions-template.md Copilot Studio agent instructions (Option B / Studio-first)
│   ├── sharepoint/
│   │   └── project-registry-schema.md  All SharePoint list schemas
│   ├── powerbi/
│   │   └── dashboard-spec.md        Power BI build specifications
│   └── knowledge-base/
│       └── email-style-guide-template.md
├── consulting/
│   ├── engagement-model.md          Delivery model, tiers, and customer value proposition
│   └── prerequisites-checklist.md  Customer readiness checklist + discovery questions
└── CHANGELOG.md
```

---

## Prerequisites (per customer)

**For Cowork-first (Option A):**
- Microsoft 365 tenant with Exchange Online
- Microsoft 365 Copilot licence (for Cowork access via Frontier programme) or M365 E7 licence (GA 1 May 2026)
- Power Automate Premium licence
- SharePoint Online site for project registry and audit log
- Anthropic subprocessor accepted in M365 Admin Centre (for Cowork data processing)

**For Studio-first (Option B):**
- Microsoft 365 tenant with Exchange Online
- Microsoft Copilot Studio licence or M365 Copilot add-on
- Power Automate Premium licence
- Azure AD App Registration with Graph API permissions (see [`runbooks/graph-access-setup.md`](runbooks/graph-access-setup.md))
- System Administrator role in the target Power Platform environment
- SharePoint Online site for project registry and knowledge base

---

## Engagement model

See [`consulting/engagement-model.md`](consulting/engagement-model.md) for the full delivery model.

| Engagement type | Scope | Cowork-first | Studio-first |
|---|---|---|---|
| **Starter** | Phases 0-2 (foundations + governance layer) | 3-4 weeks | 4-6 weeks |
| **Standard** | Phases 0-4 (add financial tracking + dashboards) | 5-7 weeks | 6-8 weeks |
| **Full** | Phases 0-6 (complete with knowledge + improvement) | 8-12 weeks | 10-14 weeks |
| **Retainer** | Ongoing Phase 6 (weekly review + prompt/instruction updates) | Monthly | Monthly |

Cowork-first engagements are shorter because the classification agent build (the most complex part of the Studio-first path) is replaced by Cowork's native triage capabilities.

---

## Metrics and accountability

Five Power BI dashboards provide full visibility:

| Dashboard | Audience | What it shows |
|-----------|----------|---------------|
| **Agent Performance** | IT/ops | Cowork/Studio precision trends, NeedsReview rate, improvement timeline |
| **Team Operations** | Project managers | Task progress, workload, commitment tracking |
| **Financial Tracking** | Finance, account managers | Budget vs actuals, billing pipeline, invoice ageing |
| **Executive Portfolio** | Leadership | Portfolio health, financial summary, communication intelligence |
| **Accountability Scorecard** | All | Weekly metric status per owner — Green/Amber/Red with Teams alerts |

Every metric has an owner, a threshold, and an automated Teams escalation. See [`docs/metrics-and-accountability.md`](docs/metrics-and-accountability.md).

---

## Evidence base

All implementation decisions are evidence-grounded. See [`docs/evidence-research.md`](docs/evidence-research.md):

- Email workload: 121 emails/day average; 28% of knowledge worker time on triage
- AI classification accuracy: F1 ~0.84 baseline; HITL 97-99.9% with tuning
- Copilot Cowork capabilities: native email triage, 13 built-in skills, built-in HITL approval (March 2026)
- Platform constraints: Cowork data residency (Anthropic/US), Studio trigger auth, billing, action chain limits
- Licensing: Cowork (E7/$99/user), Studio path (~$54/user), Power Automate Premium
- Australian Privacy Act: APP 3/6/8/10 compliance requirements

---

## License

MIT — use freely for customer engagements. Attribution appreciated.
