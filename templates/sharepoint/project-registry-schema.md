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

## 4. SOP Library (document library, not list)

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
