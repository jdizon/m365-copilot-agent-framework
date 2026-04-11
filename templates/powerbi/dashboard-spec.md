# Power BI Dashboard Specification
## M365 Copilot Agent Framework

Use this spec to build each dashboard. Each section defines the report pages, visuals, data sources, and measures required.

---

## Global setup

### Data sources to connect
1. **SharePoint — ProjectRegistry list** (`/sites/[site]/lists/ProjectRegistry`)
2. **SharePoint — AgentAuditLog list** (`/sites/[site]/lists/AgentAuditLog`)
3. **SharePoint — NeedsReviewQueue list** (`/sites/[site]/lists/NeedsReviewQueue`)
4. **Planner** — via Microsoft Graph connector or export to SharePoint via Power Automate
5. **HaloPSA** — via REST API connector (OAuth2; see HaloPSA reference docs)

### Shared DAX measures (create in a dedicated Measures table)

```dax
-- Project counts by status
Projects On Track = CALCULATE(COUNTROWS(ProjectRegistry), ProjectRegistry[Status] = "Active", ProjectRegistry[FinancialStatus] <> "Over Budget")

Projects At Risk = CALCULATE(COUNTROWS(ProjectRegistry), ProjectRegistry[RAGStatus] = "Amber")

Projects Blocked = CALCULATE(COUNTROWS(ProjectRegistry), ProjectRegistry[RAGStatus] = "Red")

-- Agent performance
Classification Precision % =
    DIVIDE(
        CALCULATE(COUNTROWS(AgentAuditLog), AgentAuditLog[CorrectionMade] = FALSE()),
        COUNTROWS(AgentAuditLog)
    ) * 100

NeedsReview Rate % =
    DIVIDE(
        CALCULATE(COUNTROWS(AgentAuditLog), AgentAuditLog[IntentClassified] = "NEEDS_REVIEW"),
        COUNTROWS(AgentAuditLog)
    ) * 100

-- Commitment tracking
Commitments Met % =
    DIVIDE(
        CALCULATE(COUNTROWS(Planner), Planner[CompletedDate] <= Planner[DueDate], Planner[Status] = "Completed"),
        CALCULATE(COUNTROWS(Planner), Planner[DueDate] <= TODAY())
    ) * 100

-- Financial
Budget Variance % =
    DIVIDE(
        SUM(ProjectRegistry[ActualSpend]) - SUM(ProjectRegistry[Budget]),
        SUM(ProjectRegistry[Budget])
    ) * 100

Unbilled Completed Work =
    CALCULATE(
        SUMX(ProjectRegistry, ProjectRegistry[Budget] - ProjectRegistry[BilledToDate]),
        ProjectRegistry[Status] = "Active"
    )
```

---

## Dashboard 1 — Executive Portfolio

**Report name:** `Exec_Portfolio`
**Refresh:** Daily at 06:00
**Audience:** Directors, leadership team

### Page 1: Portfolio Health

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Health scorecard | Card row | Projects On Track %, At Risk count, Blocked count | Conditional colour: green/amber/red |
| Project health matrix | Table | ProjectName, RAGStatus, Owner, NextMilestone, FinancialStatus | Sort by RAGStatus desc |
| Projects at risk trend | Line chart | Week, Projects At Risk count | Last 12 weeks |
| Milestone timeline | Gantt / table | ProjectName, Milestone, DueDate, Status | Filter: next 30 days |

**Slicer:** Date range, Project Owner, Status

### Page 2: Financial Overview

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Revenue this month | Card | BilledToDate (current month) vs target | KPI visual |
| Budget vs actuals | Clustered bar | ProjectName, Budget, ActualSpend | Per project |
| Billing pipeline | Table | ProjectName, NextBillingMilestone, EstimatedValue, DueDate | Sort by DueDate |
| Unbilled work | Card | Unbilled Completed Work | Red if >$5k |
| Invoice ageing | Stacked bar | Age bucket (0-30/30-60/60-90/90+), Value | |

### Page 3: Communication Intelligence

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Signals this week | Card | Count of AgentAuditLog rows (current week) | |
| Urgent signals by project | Bar chart | ProjectMatched, count where Intent = BLOCKER | |
| NeedsReview queue age | Table | NeedsReviewQueue items, Age in hours | Red highlight if >48hrs |
| Signal intent breakdown | Donut chart | IntentClassified, count | |

---

## Dashboard 2 — Team Operations

**Report name:** `Team_Operations`
**Refresh:** Every 4 hours (or DirectQuery)
**Audience:** Project managers, team leads

### Page 1: Task Overview

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Tasks due today | Card | Count Planner tasks due today | Red if >5 |
| Overdue tasks | Card | Count Planner tasks past due, incomplete | Red if ≥4 |
| Task Kanban summary | Matrix | Assignee (rows), Status (cols), Count | |
| Tasks created vs completed | Line chart | Week, Created count, Completed count | Last 8 weeks |
| Average task age | Card | Avg days open (incomplete tasks) | Red if >10 |

**Slicer:** Project, Assignee, Date range

### Page 2: Workload Distribution

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Tasks per person | Bar chart | Assignee, Task count (open) | Highlight imbalance >±30% |
| Overdue by person | Bar chart | Assignee, Overdue task count | Red if ≥4 |
| Blockers table | Table | TaskTitle, Assignee, DaysOpen, Project | Filter: Intent = BLOCKER |
| Unassigned tasks | Card + table | Count unassigned + list | Red if >0 |

### Page 3: Commitment Tracker

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Commitments met this week % | KPI card | Commitments Met % | Green ≥90%, Amber 75-89%, Red <75% |
| Commitments table | Table | TaskTitle, Assignee, DueDate, Status, SourceEmail | Sort by DueDate |
| Past-due commitments | Table | Filter: DueDate < today, Status ≠ Complete | Highlight in red |
| Commitment trend | Line chart | Week, Commitments Met % | Last 12 weeks |

---

## Dashboard 3 — Agent Performance

**Report name:** `Agent_Performance`
**Refresh:** Daily
**Audience:** IT Support, Head of IT

### Page 1: Classification Accuracy

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Overall precision % | KPI card | Classification Precision % | Green ≥90%, Amber 80-89%, Red <80% |
| Precision by category | Bar chart | IntentClassified, Precision % per category | |
| NeedsReview rate trend | Line chart | Week, NeedsReview Rate % | Target: declining |
| Correction frequency | Bar chart | Week, Corrections count | Target: declining |

### Page 2: Signal Volume and Distribution

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Signals this week | Card | Total count | |
| Signals by source | Donut | SignalSource (Email/Teams/Meeting) | |
| Signals by intent | Bar chart | IntentClassified, count | |
| Signals by project | Bar chart | ProjectMatched, count | Top 10 |
| Daily signal volume | Line chart | Date, count | Last 30 days |

### Page 3: Improvement Timeline

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Instruction version history | Table | AgentVersion, DeployDate, PrecisionBefore, PrecisionAfter | |
| Precision before/after updates | Line + annotation | Precision trend with instruction update markers | |
| NeedsReview resolution time | Bar chart | Week, Avg hours to resolve | Target: <24hrs |
| Draft acceptance rate | KPI card | Phase 5+ only | Green ≥70%, Amber 50-69%, Red <50% |

---

## Dashboard 4 — Financial Tracking

**Report name:** `Financial_Tracking`
**Refresh:** Daily
**Audience:** Finance, account managers, Ops Director

### Page 1: Portfolio Financials

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Total budget (active) | Card | SUM Budget | |
| Total actual spend | Card | SUM ActualSpend | |
| Budget variance % | KPI card | Budget Variance % | Red if >15% |
| Unbilled completed work | Card | Unbilled Completed Work | Red if >$5k |
| Budget vs actuals per project | Clustered bar | ProjectName, Budget, ActualSpend, BilledToDate | |

### Page 2: Billing Pipeline

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Milestones due this month | Table | ProjectName, Milestone, Value, DueDate, Status | |
| Revenue recognised (MTD) | Card | BilledToDate (current month) | vs target |
| Invoice ageing | Stacked bar | Age buckets, $ value | |
| Overdue invoices | Table | ProjectName, InvoiceDate, Amount, DaysOverdue | |

### Page 3: HaloPSA Integration (if active)

| Visual | Type | Fields | Notes |
|--------|------|--------|-------|
| Ticket status by project | Bar chart | Project, Open/Closed ticket count | From HaloPSA |
| Time logged vs estimated | Bar chart | Project, Estimated hours, Actual hours | |
| Billable hours this month | Card | Sum billable hours | |
| Unbilled time | Card | Hours logged but not invoiced | |

---

## Dashboard 5 — Accountability Scorecard

**Report name:** `Accountability_Scorecard`
**Refresh:** Daily
**Audience:** All team, reviewed weekly

### Page 1: Weekly Scorecard

Build as a **formatted table** with conditional formatting:

| Item | Owner | Status | Trend | Last Updated |
|------|-------|--------|-------|--------------|
| Weekly review completed | Head of IT | ✅/⚠️/❌ | → | [date] |
| NeedsReview queue cleared (<24hrs) | IT Support | | | |
| Classification precision ≥90% | IT Support | | | |
| All overdue tasks resolved | Assignees | | | |
| Billing milestones raised on time | Finance | | | |
| Agent instructions current | IT Support | | | |
| Budget variance within threshold | Project Managers | | | |

**Conditional formatting rules:**
- ✅ Green = threshold met
- ⚠️ Amber = approaching threshold
- ❌ Red = threshold breached → Teams alert triggered

### Page 2: Trend View

| Visual | Type | Notes |
|--------|------|-------|
| Scorecard trend matrix | Heat table | All metrics × last 12 weeks. Green/amber/red cells show history at a glance |
| Metrics currently Red | Card + table | Count of red metrics + list. Should be 0 |
| Time in Red per metric | Bar chart | How many weeks has each metric been red historically |

---

## Automated alerts — Power Automate flows

Create one Power Automate flow per alert rule. Each flow:
1. Runs on a schedule (daily or real-time based on SharePoint trigger)
2. Evaluates the metric against the threshold
3. Posts to Teams if threshold breached
4. Logs the alert to the AccountabilityScorecard list

| Alert | Trigger | Message target |
|-------|---------|---------------|
| Projects At Risk ≥3 | Daily check | Ops Director + Teams channel |
| Budget variance >15% | Daily check | Finance + Ops Director |
| NeedsReview age >48hrs | Hourly SharePoint check | IT Support + Head of IT |
| Classification precision <80% | Daily check | IT Support + Head of IT |
| Commitments met <75% | Weekly | Project Manager + Ops Director |
| Overdue tasks ≥4 | Daily check | Project Manager |
| Unbilled work >$5k | Daily check | Finance + Ops Director |
| Overdue invoice | Daily check | Finance + Ops Director |
