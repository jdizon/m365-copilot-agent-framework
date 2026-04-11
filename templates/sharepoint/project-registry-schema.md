# SharePoint List Schemas — M365 Copilot Agent Framework

---

## 1. Project Registry

**List name:** `ProjectRegistry`
**Purpose:** The agent queries this list to match incoming signals to active projects.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| ProjectName | Single line text | Yes | Human-readable name the agent matches against |
| ProjectCode | Single line text | Yes | Short code used in subject lines e.g. `[PROJ-01]` |
| Status | Choice | Yes | Active / On Hold / Complete / Archived |
| Owner | Person | Yes | Project owner — used for routing and alerts |
| Description | Multi-line text | No | Brief context helps agent understand project scope |
| PlannerPlanID | Single line text | No | For Planner task linking |
| LoopPageURL | Hyperlink | No | For Loop page updates |
| OneNoteSectionURL | Hyperlink | No | For OneNote note additions |
| TeamChannelID | Single line text | No | For Teams alerts specific to this project |
| Keywords | Multi-line text | No | Additional terms the agent should match (e.g. client name, product name) |
| StartDate | Date | No | |
| TargetEndDate | Date | No | |
| **Financial columns** | | | |
| Budget | Currency | No | Agreed project budget |
| ActualSpend | Currency | No | Running actual cost — updated by Update-Financial-Status flow |
| BilledToDate | Currency | No | Cumulative invoiced amount |
| NextBillingMilestone | Single line text | No | Description of next billing trigger |
| PONumber | Single line text | No | Associated purchase order reference |
| FinancialStatus | Choice | No | On Budget / At Risk / Over Budget |
| RAGStatus | Choice | No | Green / Amber / Red — overall project health (set by agent or manually) |
| LastInvoiceDate | Date | No | Date of most recent invoice raised |

---

## 2. Audit Log

**List name:** `AgentAuditLog`
**Purpose:** Every agent action is logged here. Used for weekly review, Power BI, and compliance.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| SignalSource | Choice | Yes | Email / TeamsChat / MeetingTranscript |
| Sender | Single line text | Yes | Email address or Teams user |
| Subject | Single line text | Yes | Email subject or message preview |
| ProjectMatched | Lookup (ProjectRegistry) | No | Blank if unmatched |
| IntentClassified | Choice | Yes | STATUS_UPDATE / ACTION_ITEM / BLOCKER / DECISION / COMPLETION / FYI / NEEDS_REVIEW |
| ConfidenceLevel | Choice | Yes | High / Medium / Low |
| ActionTaken | Single line text | Yes | Which flow was called, or "NeedsReview" |
| ActionDetail | Multi-line text | No | What was updated (field, value, task title, etc.) |
| ProcessedDate | Date/Time | Yes | Timestamp of agent action |
| AgentVersion | Single line text | No | Track which instruction version was active |

---

## 3. NeedsReview Queue

**List name:** `NeedsReviewQueue`
**Purpose:** Low-confidence signals awaiting human review. Human corrections feed the improvement loop.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| SignalSource | Choice | Yes | Email / TeamsChat / MeetingTranscript |
| Sender | Single line text | Yes | |
| Subject | Single line text | Yes | |
| BodyExcerpt | Multi-line text | Yes | First ~500 chars of signal |
| AgentReason | Multi-line text | Yes | Why the agent routed to NeedsReview |
| AgentSuggestedProject | Lookup (ProjectRegistry) | No | Agent's best guess (if any) |
| AgentSuggestedIntent | Choice | No | Agent's best guess intent |
| ReviewStatus | Choice | Yes | Pending / Reviewed / Escalated |
| ReviewedBy | Person | No | Who reviewed it |
| ReviewedDate | Date/Time | No | |
| CorrectProject | Lookup (ProjectRegistry) | No | Human's correct answer — feeds improvement loop |
| CorrectIntent | Choice | No | Human's correct answer |
| CorrectionNotes | Multi-line text | No | Why the agent got it wrong — used in weekly review |

---

## 4. Financial Register

**List name:** `FinancialRegister`
**Purpose:** Tracks invoices, purchase orders, and financial events captured by the agent. Feeds Power BI Financial Tracking dashboard.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| ProjectRef | Lookup (ProjectRegistry) | Yes | Which project this relates to |
| RecordType | Choice | Yes | Invoice / PO / Expense / BillingMilestone / Payment |
| Reference | Single line text | Yes | Invoice number, PO number, expense claim ID |
| Amount | Currency | Yes | Value of the record |
| Status | Choice | Yes | Pending / Approved / Paid / Overdue / Cancelled |
| DueDate | Date | No | Payment due date or milestone date |
| ReceivedDate | Date | No | When the document arrived |
| SignalSource | Single line text | No | Email subject / Teams message that triggered this record |
| CreatedByAgent | Yes/No | Yes | Was this logged by the agent or manually? |
| Notes | Multi-line text | No | |

---

## 5. Accountability Scorecard

**List name:** `AccountabilityScorecard`
**Purpose:** Stores the weekly scorecard state. Power BI reads this to render the scorecard and trend history. Power Automate writes to it when running alert checks.

| Column | Type | Required | Notes |
|--------|------|----------|-------|
| WeekStarting | Date | Yes | Monday of the review week |
| MetricName | Single line text | Yes | e.g. "NeedsReview queue cleared" |
| Owner | Person | Yes | Responsible person |
| Status | Choice | Yes | Green / Amber / Red |
| MetricValue | Number | No | Actual value (e.g. 91 for 91%) |
| Threshold | Single line text | No | e.g. "≥90%" |
| AlertSent | Yes/No | Yes | Was a Teams alert triggered? |
| Notes | Multi-line text | No | Context for the review |

---

## 6. SOP Library (document library, not list)

**Library name:** `SOPLibrary`
**Purpose:** Agent knowledge source — add this library to the Copilot Studio agent's knowledge sources.

Recommended folder structure:
```
SOPLibrary/
├── SOP-01-Inbox-Taxonomy.docx
├── SOP-02-Urgent-Definition.docx
├── SOP-03-Exception-Handling.docx
├── SOP-04-Change-Management.docx
├── SOP-05-Weekly-Review.docx
├── Email-Style-Guide.md
└── Project-Briefs/
    └── [one document per active project]
```
