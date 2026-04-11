# Metrics and Accountability Framework
## M365 Copilot Agent — Executive and Team Reporting

**Purpose:** Define what is measured, who owns it, what thresholds trigger action, and how accountability is maintained across the organisation.

Dashboards without accountability are wallpaper. This framework ties every metric to an owner, a threshold, and an escalation path.

---

## Accountability model

Every metric has four properties:

| Property | Description |
|----------|-------------|
| **Metric** | What is being measured |
| **Owner** | Who is responsible for the number (from RACI) |
| **Accountable** | Who is answerable if the metric goes red |
| **Threshold** | Green / Amber / Red definitions |
| **Escalation** | What happens automatically when threshold is breached |

The agent enforces escalation thresholds automatically — when a metric crosses from Amber to Red, a Power Automate flow triggers a Teams alert to the accountable person. Dashboards show the trend; the agent acts on the breach.

---

## Dashboard structure — audience and cadence

| Dashboard | Audience | Refresh | Primary use |
|-----------|----------|---------|-------------|
| **Executive Portfolio** | Leadership, directors | Daily | Strategic decisions, portfolio health, financial overview |
| **Team Operations** | Project managers, team leads | Real-time | Task management, workload, blockers |
| **Agent Performance** | IT/ops, administrator | Weekly | Continuous improvement, classification accuracy |
| **Financial Tracking** | Finance, account managers | Daily | Budget vs actuals, billing pipeline, overrun flags |
| **Accountability Scorecard** | All levels | Weekly | Commitment tracking, SLA compliance, review cadence |

---

## 1. Executive Portfolio Dashboard

### Purpose
Give leadership a single view of portfolio health, financial standing, and team performance — without needing to open SharePoint or Planner.

### Metrics

#### Portfolio health
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| % projects On Track | Project Manager | Head of IT / Ops Director | ≥85% | 70-84% | <70% |
| Projects At Risk | Project Manager | Ops Director | 0 | 1-2 | ≥3 |
| Overdue milestones | Project Manager | Head of IT | 0 | 1-2 | ≥3 |
| Projects with open Blockers | Project Manager | Ops Director | 0 | 1 | ≥2 |
| Average project age (days) | Project Manager | Head of IT | ≤target | +20% | +50% |

#### Financial summary
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Portfolio budget variance | Finance / PM | Ops Director | ≤5% over | 5-15% over | >15% over |
| Unbilled completed milestones | Finance | Ops Director | 0 | 1-2 | ≥3 |
| Overdue invoices | Finance | Ops Director | 0 | 1 | ≥2 |
| Revenue billed this month vs target | Finance | Director | ≥100% | 85-99% | <85% |

#### Communication intelligence
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Urgent signals unresolved >24hrs | IT Support | Head of IT | 0 | 1 | ≥2 |
| Escalation (BLOCKER) signals this week | Project Manager | Ops Director | 0 | 1-2 | ≥3 |
| NeedsReview queue age (oldest item) | IT Support | Head of IT | <24hrs | 1-2 days | >2 days |

### Key visuals
- **Portfolio health matrix** — project name × status colour (On Track/At Risk/Blocked/Complete)
- **Financial waterfall** — budget → spend to date → remaining → billed
- **Trend lines** — projects at risk over time (are we improving?)
- **Urgent signals heatmap** — which projects are generating the most urgent comms

---

## 2. Team Operations Dashboard

### Purpose
Give project managers and team leads real-time visibility over task progress, workload, and blockers. Replaces status meetings.

### Metrics

#### Task performance
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Tasks completed this week | Assignee | Head of IT | ≥target | 80% of target | <80% |
| Overdue tasks (count) | Assignee | Project Manager | 0 | 1-3 | ≥4 |
| Tasks created vs completed ratio | Project Manager | Ops Director | ≤1.2 | 1.2-1.5 | >1.5 |
| Average task age (days open) | Assignee | Project Manager | ≤5 days | 5-10 days | >10 days |
| Blockers unresolved >48hrs | Project Manager | Ops Director | 0 | 1 | ≥2 |

#### Workload distribution
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Tasks per person this sprint | IT Support | Head of IT | Balanced ±20% | ±30% | ±50%+ |
| Unassigned tasks | Project Manager | Head of IT | 0 | 1-2 | ≥3 |

#### Commitment tracking
Commitments are captured by the agent from email/Teams signals (ACTION_ITEM intent → Planner task with due date and assignee).

| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Commitments due this week — met | Assignee | Project Manager | ≥90% | 75-89% | <75% |
| Commitments past due (no update) | Assignee | Project Manager | 0 | 1-2 | ≥3 |
| Commitments with no Planner task | IT Support | Head of IT | 0 | — | Any |

### Key visuals
- **Team Kanban summary** — task count by status per person
- **Burndown by project** — tasks created vs. completed over time
- **Commitment tracker** — table of commitments made (source, who, what, by when, met Y/N)
- **Workload heatmap** — tasks per person per week

---

## 3. Agent Performance Dashboard

### Purpose
Track whether the agent is getting smarter. This is the continuous improvement engine's instrument panel.

### Metrics

| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Classification precision (overall) | IT Support | Head of IT | ≥90% | 80-89% | <80% |
| Precision per intent category | IT Support | Head of IT | ≥90% | 80-89% | <80% |
| NeedsReview rate | IT Support | Head of IT | <10% | 10-20% | >20% |
| NeedsReview resolution time | IT Support | Head of IT | <24hrs | 24-48hrs | >48hrs |
| Correction frequency (per week) | IT Support | Head of IT | Declining | Flat | Increasing |
| Draft acceptance rate (Phase 5+) | IT Support | Head of IT | ≥70% | 50-69% | <50% |
| Instruction version deployed | IT Support | Head of IT | Current | 1 week old | >2 weeks old |

### Key visuals
- **Precision trend line** — classification accuracy per category over 12 weeks
- **NeedsReview trend** — weekly NeedsReview rate (should decline over time)
- **Correction heatmap** — which categories generate the most corrections
- **Instruction update timeline** — when instructions were updated and effect on accuracy

---

## 4. Financial Tracking Dashboard

### Purpose
Single view of project financials, billing pipeline, and spend status for finance and account managers.

### Metrics

#### Per-project financials
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Budget variance per project | Project Manager | Ops Director | ≤5% | 5-15% | >15% |
| % budget consumed | Project Manager | Ops Director | On track | 80% budget, <80% complete | Over budget |
| Days since last invoice | Finance | Ops Director | ≤30 days | 30-60 days | >60 days |
| Outstanding invoice value | Finance | Ops Director | $0 | Low | High |

#### Portfolio financials
| Metric | Owner | Accountable | Green | Amber | Red |
|--------|-------|-------------|-------|-------|-----|
| Total unbilled completed work | Finance | Director | $0 | <$5k | >$5k |
| Billing milestones due this month | Finance | Ops Director | All raised | 1 missed | ≥2 missed |
| POs awaiting approval | Finance | Ops Director | 0 | 1-2 | ≥3 |

### Key visuals
- **Budget vs actuals bar chart** — per project, current period
- **Billing pipeline** — upcoming milestones with dates and values
- **Invoice ageing table** — invoices by age (0-30 / 30-60 / 60-90 / 90+ days)
- **Revenue recognised vs target** — month-to-date and full year trend

---

## 5. Accountability Scorecard

### Purpose
A weekly single-page view that shows whether the people responsible for things are actually doing them. Not about blame — about visibility.

### Structure

Each row = one commitment or metric. Each column = status this week.

| Item | Owner | Due | Status | Trend | Notes |
|------|-------|-----|--------|-------|-------|
| Weekly review completed | Head of IT | Weekly | ✅ | — | |
| NeedsReview queue cleared | IT Support | Weekly | ⚠️ | → | 2 items >48hrs old |
| Phase 3 accuracy gate reviewed | IT Support | Ongoing | ✅ | ↑ | 91% this week |
| All overdue tasks addressed | Assignees | Weekly | ❌ | ↓ | 4 tasks >10 days |
| Billing milestones raised on time | Finance | Monthly | ✅ | — | |
| Agent instructions updated | IT Support | As needed | ✅ | — | v1.4 deployed |

### Escalation rules (automated via Power Automate)

| Condition | Action | Target |
|-----------|--------|--------|
| Any metric goes Red | Teams alert sent immediately | Accountable person |
| Red metric unresolved after 48hrs | Escalation alert sent | Director / Ops Director |
| NeedsReview queue age >48hrs | Teams reminder | IT Support + Head of IT |
| Overdue task count ≥4 | Weekly digest highlights | Project Manager |
| Budget variance >15% | Alert + flag on dashboard | Finance + Ops Director |
| Classification precision drops below 80% | Alert to IT Support | Head of IT |

---

## Review cadence

| Cadence | Forum | Dashboard used | Who attends |
|---------|-------|---------------|-------------|
| Daily | Async — dashboard reviewed independently | Team Operations | Project managers |
| Weekly | 30-min team check-in | Accountability Scorecard + Team Operations | All team |
| Weekly | Agent review (SOP-05) | Agent Performance | IT Support + Head of IT |
| Monthly | Executive review | Executive Portfolio + Financial | Leadership |
| Quarterly | Retrospective | All dashboards | All team |

---

## Power BI data sources

| Dashboard | Primary data source | Refresh method |
|-----------|--------------------|--------------  |
| Executive Portfolio | SharePoint ProjectRegistry | Power BI dataflow — daily |
| Team Operations | Planner (via Graph connector) | DirectQuery or daily import |
| Agent Performance | SharePoint AgentAuditLog + NeedsReviewQueue | Daily import |
| Financial Tracking | SharePoint ProjectRegistry (financial columns) + HaloPSA (via API) | Daily |
| Accountability Scorecard | All of the above + manual scorecard list | Daily |

---

## Commitment tracking — how it works

The agent captures commitments automatically:

```
Email/Teams message contains commitment signal:
"I'll follow up by Friday"
"We'll have this resolved by [date]"
"[Name] will send the report by [date]"
        ↓
Agent classifies as ACTION_ITEM
        ↓
Create-Planner-Task flow creates task:
  Title: "[commitment text]"
  Due date: [extracted date]
  Assigned to: [extracted person]
  Source: [email subject + sender]
        ↓
Power BI Commitment Tracker shows:
  - Commitment made (date, source, what)
  - Owner
  - Due date
  - Met: Yes / No / Overdue
```

Commitments that are not met surface automatically — no manual tracking required.
