# Setup Checklist — M365 Copilot Agent (Cowork-first)

**Version:** 2.0 | **Framework:** M365 Copilot Agent Framework  
**Path:** Option A — Cowork-first. For Option B (Studio-first), see `templates/agent/instructions-template.md` and the v1.x checklist in git history.

---

## Prerequisites (do not skip)

### Access and licencing
- [ ] Copilot Cowork accessible at https://m365.cloud.microsoft — confirm "Organize my inbox" prompt appears
- [ ] Power Automate Premium licence confirmed
- [ ] SharePoint Online site available for list creation
- [ ] Planner access confirmed

### Compliance gates (mandatory before Phase 1)
- [ ] **Anthropic processing region confirmed** in M365 Admin Centre (Australian Privacy Act APP 8 gate — document the result; do not proceed until documented)
- [ ] M365 data residency confirmed (AU East/Southeast or relevant region) in Admin Centre
- [ ] Privacy notice updated to disclose AI triage AND Cowork/Anthropic cross-border processing (APP 3/6/8)
- [ ] Architecture option recorded: **Option A confirmed**

### Foundations
- [ ] Outlook folders created: **DO / FYI / IGNORE / NeedsReview / Urgent**
- [ ] Category definitions agreed and documented (input for SOP-01)
- [ ] Urgent criteria defined and agreed (prevents Teams alert fatigue — input for SOP-02)
- [ ] Existing Outlook Rules and Quick Steps audited: each item marked **Retire / Noise-gate only / Translate to Cowork prompt rule** (SOP-00)
- [ ] SharePoint EmailTriageQueue list created (see schema below)
- [ ] SharePoint ProjectRegistry list created
- [ ] Planner plan created: **PITA Action Register** (buckets: DO – This Week / DO – Upcoming / Waiting / NeedsReview / Done)
- [ ] Solution-aware cloud flow sharing enabled in Power Platform Admin Centre

### EmailTriageQueue schema

| Column | Type | Notes |
|---|---|---|
| Title (EmailID) | Single line | Outlook message ID or subject + date |
| Subject | Single line | |
| Sender | Single line | |
| ReceivedDate | Date | |
| CoworkCategory | Choice | DO / FYI / IGNORE / NeedsReview / Urgent |
| ConfirmedCategory | Choice | Same options |
| ConfirmedBy | Person | |
| ConfirmedDate | Date | |
| IsCorrect | Yes/No | |
| Action | Single line | What was done |
| ProjectCode | Single line | |
| Notes | Multi-line | Correction notes |

---

## Phase 0 — Foundations

- [ ] Complete all prerequisites above
- [ ] Train customer on Cowork interface (see [`docs/cowork-integration.md`](../docs/cowork-integration.md) and `Cowork_Best_Practice_Guide_v1.docx`)
- [ ] Confirm Teams channel for urgent alerts (name, ID — needed for PA flow)

**Exit criteria:** All prerequisites checked; compliance gates documented; Outlook folders, SharePoint lists, and Planner created.

---

## Phase 1 — Cowork evaluation baseline (2 weeks)

**Goal:** Establish whether Cowork native triage precision meets the 90% gate on the customer's real inbox. Every action approved manually. Log every decision.

### Daily sessions
- [ ] Open https://m365.cloud.microsoft
- [ ] Use starter prompt (see [`templates/cowork/prompt-templates.md`](../templates/cowork/prompt-templates.md) — Daily Triage template)
- [ ] Approve or reject every Cowork classification — do **not** skip any approvals
- [ ] After session: add rows to EmailTriageQueue for each email actioned (CoworkCategory + ConfirmedCategory + IsCorrect)

### SOP drafting (complete by end of Phase 1)
- [ ] **SOP-00:** Rules/Quick Steps inventory — Retire / Noise-gate / Cowork rule decision per item
- [ ] **SOP-01:** Inbox taxonomy — folder names, category definitions, one example each
- [ ] **SOP-02:** Urgent definition — exact criteria + Teams channel destination
- [ ] **SOP-03:** Exception handling — what goes to NeedsReview; how resolved in weekly review
- [ ] **SOP-04:** Change management — how to update Cowork prompt templates (version, test, log)
- [ ] **SOP-05:** Weekly review — what to measure, who reviews, what triggers a change

### Precision gate (end of Week 2)

Query EmailTriageQueue: (IsCorrect = Yes) ÷ (total rows per category)

| Result | Action |
|---|---|
| All categories ≥90% | → Phase 2 Cowork path |
| 1–2 categories <90% | → Build targeted Copilot Studio flows for those categories; retest; then Phase 2 |
| Most categories <90% | → Extend Phase 1 one week; refine prompt templates; re-measure |

**Exit criteria:** ≥90% precision across all categories confirmed from EmailTriageQueue data.

---

## Phase 2 — Power Automate governance layer

**Goal:** Automate the SharePoint logging and downstream actions that were manual in Phase 1.

See [`runbooks/power-automate-flows.md`](power-automate-flows.md) for full step-by-step build instructions for each flow.

### Flow 1 — Log-Audit-Entry
- [ ] Build flow: trigger = email moved to any triage folder → create item in EmailTriageQueue
- [ ] Test: move test email to each folder → confirm row appears in SharePoint

### Flow 2 — Create-Planner-Task
- [ ] Build flow: trigger = email moved to DO folder → create task in PITA Action Register (DO – This Week bucket)
- [ ] Test: confirm Planner task created with correct title, bucket, and due date

### Flow 3 — Send-Teams-Alert
- [ ] Build flow: trigger = email moved to Urgent folder → post to Teams urgent channel
- [ ] Test: confirm Teams message posts with sender, subject, and email link

### Flow 4 — Log-NeedsReview
- [ ] Build flow: trigger = email moved to NeedsReview folder → create item in EmailTriageQueue with IsCorrect = null
- [ ] Test: confirm row created with blank IsCorrect

### Weekly review cadence begins
- [ ] Schedule recurring weekly review session (SOP-05 — see [`runbooks/weekly-review.md`](weekly-review.md))
- [ ] Review EmailTriageQueue IsCorrect distribution; update Cowork prompt templates for gap categories

**Exit criteria:** All 4 flows deployed and tested; manual logging retired; weekly review completed at least once.

---

## Phase 3 — Financial layer + scale

**Gate:** ≥90% precision confirmed from Phase 2 PA log data (not manual data).

### SharePoint list expansion
- [ ] Add columns to ProjectRegistry: Budget, ActualSpend, BilledToDate, FinancialStatus, RAGStatus
- [ ] Create list: **FinancialRegister** — columns: EmailID, IntentType (Choice: INVOICE_RECEIVED / PAYMENT_CONFIRMED / PO_APPROVED / BUDGET_ALERT / BILLING_MILESTONE), Amount, ProjectCode, Status, Date
- [ ] Create list: **AccountabilityScorecard** — columns: MetricOwner (Person), MetricName, RAGStatus (Choice: Green/Amber/Red), WeekEnding (Date), Notes

### Financial PA flows (see [`runbooks/power-automate-flows.md`](power-automate-flows.md))
- [ ] **Detect-Financial-Intent** — DO folder + financial keyword → classify intent → write to FinancialRegister
- [ ] **Create-Invoice-Task** — INVOICE_RECEIVED → Planner task with amount + due date
- [ ] **Log-PO-Entry** — PO_APPROVED → FinancialRegister row
- [ ] **Flag-Budget-Overrun** — BUDGET_ALERT → Teams alert + update ProjectRegistry RAGStatus

### Scale Cowork
- [ ] Set up scheduled Cowork daily triage prompt (8:30 AM weekdays via Cowork Scheduled tab)
- [ ] For categories consistently ≥95% precision: enable "skip future approvals" for those categories
- [ ] Retain manual approval for: external replies, Urgent classification, bulk deletes

**Exit criteria:** Financial flows live; FinancialRegister populating; scheduled Cowork triage running.

---

## Phase 4 — Power BI dashboards

See [`templates/powerbi/dashboard-spec.md`](../templates/powerbi/dashboard-spec.md) for visual and DAX specs.

- [ ] **Agent Performance** — source: EmailTriageQueue; metrics: Cowork precision per category (trend), NeedsReview rate (trend), correction frequency, prompt version history; refresh: weekly
- [ ] **Team Operations** — source: EmailTriageQueue, Planner; metrics: task status, overdue, commitment rate; refresh: real-time
- [ ] **Financial Tracking** — source: FinancialRegister, ProjectRegistry; metrics: invoice ageing, budget vs actuals, billing pipeline; refresh: daily
- [ ] **Executive Portfolio** — source: ProjectRegistry, EmailTriageQueue; metrics: project RAG, financial summary; refresh: daily
- [ ] **Accountability Scorecard** — source: AccountabilityScorecard; metrics: RAG per metric owner per week; refresh: weekly
- [ ] **Automated Friday alert** — PA flow: weekly schedule → AccountabilityScorecard → Teams message for any RAGStatus = Red

**Exit criteria:** All 5 dashboards published; reviewed in at least one weekly review session.

---

## Phase 5 — Knowledge enrichment + expanded signals

- [ ] Create Email Style Guide in SharePoint (see [`templates/knowledge-base/email-style-guide-template.md`](../templates/knowledge-base/email-style-guide-template.md))
- [ ] Build Cowork prompt templates for reply types: acknowledgement / escalation / status update / invoice confirmation (see [`templates/cowork/prompt-templates.md`](../templates/cowork/prompt-templates.md))
- [ ] Test reply drafting: Cowork drafts reply → accepted without significant edits ≥80% of the time
- [ ] Teams chat triage: weekly Cowork prompt — "Find action items from Teams messages this week and create Planner tasks for any not already tracked"
- [ ] Meeting transcript flow: PA trigger on Teams recording ready → Cowork extracts action items → Create-Planner-Task

---

## Phase 6 — Continuous improvement

- [ ] Formalise NeedsReview correction loop: resolved items → ConfirmedCategory logged → feeds weekly review
- [ ] Prompt templates versioned in SharePoint with before/after precision notes per version
- [ ] Agent Performance dashboard: precision trends + NeedsReview rate trajectory
- [ ] Quarterly knowledge review: ProjectRegistry cleanup, SOP updates, Style Guide refresh, prompt template audit

---

## Change control reference

| Type | Trigger | Process |
|------|---------|---------|
| Standard | Prompt tweaks, minor taxonomy edits | Log in SharePoint change log |
| Normal | New category, routing logic, threshold | Review + rollback plan before applying |
| Emergency | Disable routing after incident | Restore safe state → post-change review |
