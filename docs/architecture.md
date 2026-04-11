# Architecture — M365 Copilot Agent Framework

## Design principle

The agent **reasons**. Power Automate **executes**. Humans **approve** consequential actions.

This separation is deliberate:
- Copilot Studio handles generative reasoning, project matching, and intent classification
- Power Automate handles deterministic execution (API calls, list updates, notifications)
- Every execution is logged for audit and improvement

---

## Full architecture

```
┌─────────────────────────────────────────────────────────┐
│                     INPUT SIGNALS                        │
│  Email (Outlook) │ Teams Chat │ Meeting Transcript       │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│              POWER AUTOMATE — EVENT TRIGGERS             │
│  "When new email arrives"                                │
│  "When message posted in channel"                        │
│  "When meeting transcript is ready"                      │
│                                                          │
│  Payload: sender, subject, body excerpt,                 │
│           thread context, timestamp                      │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│            COPILOT STUDIO AGENT (the brain)              │
│                                                          │
│  1. Reads signal payload                                 │
│  2. Queries project/task registry (SharePoint)           │
│  3. Queries knowledge sources (docs, style guide)        │
│  4. Classifies:                                          │
│     - Which project does this relate to?                 │
│     - What type of signal? (status update / action item  │
│       / blocker / decision / FYI / completion)           │
│     - Which system to update?                            │
│     - What field/content to write?                       │
│  5. Assesses confidence:                                 │
│     - High → calls action flow autonomously              │
│     - Low  → routes to NeedsReview queue                 │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│         POWER AUTOMATE — ACTION FLOWS (the hands)        │
│                                                          │
│  Update-Project-Status    → SharePoint list              │
│  Update-Planner-Task      → Planner (existing task)      │
│  Create-Planner-Task      → Planner (new task)           │
│  Update-Loop-Page         → Loop workspace page          │
│  Add-OneNote-Note         → OneNote section              │
│  Send-Teams-Alert         → Teams channel/chat           │
│  Log-NeedsReview          → Human review queue           │
│  Log-Audit-Entry          → SharePoint audit list        │
│         (called by every action flow)                    │
└──────────────────────────────────────────────────────────┘
```

---

## Knowledge layers

The agent's reasoning quality depends on what it can access. Three layers:

### Layer 1 — Structured knowledge (SharePoint lists)
Required from Phase 0. The agent queries these to match signals to work.

| List | Purpose |
|------|---------|
| Project Registry | Active projects, codes, owners, Planner IDs, Loop/OneNote URLs |
| Task Registry | Active tasks, assignees, due dates, Planner task IDs |
| Audit Log | Every agent action — classification, confidence, outcome |
| NeedsReview Queue | Low-confidence items awaiting human triage |

### Layer 2 — Document knowledge (SharePoint knowledge sources)
Added in Phase 5. The agent reasons over these documents.

- Project briefs and background context
- SOPs and process documentation
- Email style guide (tone, salutations, sign-offs by recipient type)
- Past decisions and change logs
- Stakeholder maps

### Layer 3 — Semantic Index (M365 Copilot)
Optional. Requires M365 Copilot licence. Indexes all org content automatically.

When active, the agent can answer: *"What is the current status of Project X based on all communications this week?"* — not just what the SharePoint list says, but what was said in emails, Teams chats, and meetings.

---

## Power BI reporting layer

Power BI sits above all data sources and provides the accountability and insight layer. It is not optional — it is how the organisation knows the system is working.

```
SharePoint Lists          Planner           HaloPSA API
(ProjectRegistry,         (Tasks,           (Tickets,
 AuditLog,                 Assignments,      Time,
 NeedsReviewQueue,         Due dates)        Invoices)
 FinancialRegister,
 AccountabilityScorecard)
        ↓                     ↓                  ↓
        └──────────── Power BI ──────────────────┘
                          ↓
        ┌─────────────────────────────────────────┐
        │  Executive Portfolio (daily)             │
        │  Team Operations (real-time)             │
        │  Agent Performance (weekly)              │
        │  Financial Tracking (daily)              │
        │  Accountability Scorecard (weekly)       │
        └─────────────────────────────────────────┘
                          ↓
        Power Automate alert flows
        (metric crosses threshold → Teams alert → accountable person)
```

Five dashboards serve different audiences. See [`docs/metrics-and-accountability.md`](metrics-and-accountability.md) for the full metric definitions, owners, thresholds, and escalation rules. See [`templates/powerbi/dashboard-spec.md`](../templates/powerbi/dashboard-spec.md) for visual and DAX specifications.

---

## Agent intent classification schema

Beyond simple priority (DO/FYI/IGNORE), the agent classifies intent:

| Intent | Description | Primary action |
|--------|-------------|----------------|
| `STATUS_UPDATE` | Signal indicates a project/task status has changed | Update SharePoint + Planner |
| `ACTION_ITEM` | Signal contains a new task or commitment | Create Planner task (commitment tracking) |
| `BLOCKER` | Signal indicates something is blocked | Update status + urgent Teams alert |
| `DECISION` | Signal records a decision | Add OneNote note + Loop page update |
| `COMPLETION` | Signal indicates task/project is done | Update Planner + SharePoint |
| `FYI` | Informational only, no action needed | Log only |
| `NEEDS_REVIEW` | Low confidence — human should decide | Log to NeedsReview queue |
| `INVOICE_RECEIVED` | Invoice or bill received | Log to FinancialRegister; alert finance |
| `PAYMENT_CONFIRMED` | Payment processed | Update FinancialRegister + ProjectRegistry |
| `PO_APPROVED` | Purchase order approved | Update FinancialRegister; trigger next step |
| `BUDGET_ALERT` | Signal indicates budget risk | Update FinancialStatus + urgent alert |
| `BILLING_MILESTONE` | Stage complete — invoice can be raised | Create billing task in Planner; alert finance |

---

## Continuous improvement loop

```
Signal arrives
    ↓
Agent classifies + acts (or routes to NeedsReview)
    ↓
Every action logged to SharePoint audit list
    ↓
NeedsReview items: human reviews + logs correct category
    ↓
Weekly review (SOP-05):
    - Query audit list for corrections this week
    - Identify which categories had highest misclassification
    - Update agent instructions for those categories
    - Commit updated instructions to git
    ↓
Power BI dashboard surfaces trends:
    - Classification precision per category over time
    - NeedsReview rate trend
    - Correction frequency by category
    - Draft acceptance rate (Phase 5+)
```

---

## Phase gate: Phase 2 → Phase 3

Do not enable autonomous source-of-truth updates until:
- ≥90% classification precision over 2 weeks of logged Phase 2 runs
- <10% false positive rate per category
- NeedsReview rate understood and declining

Evidence basis: ML email classifiers baseline at F1 ~0.84; HITL systems reach 97-99.9% with tuning. 90% is a conservative, achievable gate. (Source: InfoWorld/Springer, 2025-2026)

---

## Security model

| Principle | Implementation |
|-----------|----------------|
| Least privilege | Each Power Automate flow has minimum required Graph permissions |
| Maker credentials | Event triggers run under the agent maker's credentials (platform constraint, Feb 2026) |
| Audit trail | Every execution logged — trigger, classification, confidence, action, outcome |
| Human gates | Low confidence → NeedsReview; external sends → always require approval |
| Change control | All instruction updates version-controlled; rollback via `pac solution import` |

---

## Deployment approach

The agent and all flows are defined as code (Power Platform solution files) and deployed via the Power Platform CLI (`pac`). This enables:

- Version control (git) for all agent configuration
- Repeatable deployment across customer environments
- Rollback on accuracy degradation
- CI/CD pipeline integration (optional)

See [`runbooks/setup-checklist.md`](../runbooks/setup-checklist.md) for the full build sequence.
