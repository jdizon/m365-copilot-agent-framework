# Architecture — M365 Copilot Agent Framework

**Version:** 2.0.0 | **Updated:** April 2026

---

## Design principle

**The agent classifies. Power Automate executes. Humans approve. SharePoint records.**

This separation is deliberate — it allows the triage layer (Cowork or Studio) to be updated independently from the execution layer (PA flows) and the system of record (SharePoint).

---

## Architecture options

Two options are available based on the customer's M365 licence:

| | **Option A — Cowork-first** | **Option B — Studio-first** |
|---|---|---|
| **Triage layer** | Copilot Cowork (native, 13 built-in skills) | Copilot Studio agent (custom, event-triggered) |
| **Execution layer** | Power Automate flows (folder-move trigger) | Power Automate flows (HTTP-trigger from Studio) |
| **Licence required** | M365 Copilot + Frontier, or M365 E7 | Copilot Studio or M365 Copilot add-on |
| **Approval model** | Built into Cowork — pauses before consequential actions | Agent routes low-confidence to NeedsReview queue |
| **Classification control** | Cowork native + prompt rules | Full custom instruction set in Studio |
| **Build complexity** | Lower — no Studio agent to build | Higher — agent instructions, triggers, knowledge sources |
| **Best for** | Customers with M365 Copilot / E7 access | Customers without Cowork access; complex custom logic |

Both paths share the same SharePoint schemas, Power BI dashboards, and continuous improvement model.

---

## Option A — Cowork-first (full architecture)

```
┌─────────────────────────────────────────────────────────┐
│                     INPUT SIGNAL                         │
│               Email arrives in Outlook inbox             │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│              COPILOT COWORK — TRIAGE LAYER               │
│                                                          │
│  1. User triggers: "Organize my inbox" (manual)          │
│     OR scheduled daily triage prompt (Phase 3+)          │
│  2. Cowork classifies each email using 13 built-in skills│
│  3. Cowork presents approval dialog before any action    │
│  4. User approves → email moved to triage folder         │
│     • DO / FYI / IGNORE / NeedsReview / Urgent          │
│  Built on Anthropic Claude within M365 security boundary │
└──────────────────────────────────────────────────────────┘
                          ↓
             [Email moved to triage folder]
                          ↓
┌─────────────────────────────────────────────────────────┐
│         POWER AUTOMATE — GOVERNANCE LAYER                │
│     Trigger: "When email moved to folder" (Outlook)      │
│                                                          │
│  Log-Audit-Entry      → EmailTriageQueue (SharePoint)    │
│  Create-Planner-Task  → PITA Action Register  [DO only]  │
│  Send-Teams-Alert     → Urgent channel        [Urgent]   │
│  Log-NeedsReview      → EmailTriageQueue      [NeedsRev] │
│  Detect-Financial-Intent → FinancialRegister  [Phase 3]  │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│              SHAREPOINT — SYSTEM OF RECORD               │
│                                                          │
│  EmailTriageQueue — every triage decision logged         │
│  ProjectRegistry  — active projects, owners, RAG status  │
│  FinancialRegister — invoices, POs, payments, milestones │
│  AccountabilityScorecard — weekly RAG per metric owner   │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                   POWER BI — ACCOUNTABILITY              │
│  Agent Performance │ Team Operations │ Financial         │
│  Executive Portfolio │ Accountability Scorecard          │
└──────────────────────────────────────────────────────────┘
```

---

## Option B — Studio-first (full architecture)

```
┌─────────────────────────────────────────────────────────┐
│                     INPUT SIGNALS                        │
│  Email (Outlook) │ Teams Chat │ Meeting Transcript       │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│          POWER AUTOMATE — EVENT TRIGGERS                 │
│  "When new email arrives"                                │
│  "When message posted in channel"                        │
│  "When meeting transcript is ready"                      │
│  Payload: sender, subject, body excerpt, timestamp       │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│            COPILOT STUDIO AGENT — REASONING LAYER        │
│                                                          │
│  1. Reads signal payload                                 │
│  2. Queries ProjectRegistry (SharePoint knowledge source)│
│  3. Classifies: project match + intent + confidence      │
│  4. High confidence → calls action flow autonomously     │
│     Low confidence → routes to NeedsReview queue         │
└──────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│         POWER AUTOMATE — ACTION FLOWS                    │
│                                                          │
│  Update-Project-Status  → SharePoint ProjectRegistry     │
│  Update-Planner-Task    → Planner (existing task)        │
│  Create-Planner-Task    → Planner (new task)             │
│  Update-Loop-Page       → Loop workspace page            │
│  Add-OneNote-Note       → OneNote section                │
│  Send-Teams-Alert       → Teams channel/chat             │
│  Log-NeedsReview        → Human review queue             │
│  Log-Audit-Entry        → SharePoint audit list          │
└──────────────────────────────────────────────────────────┘
```

---

## Shared: intent classification schema

Both paths use the same intent taxonomy. Option A uses Cowork prompt rules to drive this. Option B uses Studio agent instructions.

| Intent | Description | Primary action |
|--------|-------------|----------------|
| `DO` / `ACTION_ITEM` | Requires action from Joseph | Planner task + pin/flag |
| `FYI` | Informational — read but no action | Mark read; log |
| `IGNORE` | No value — newsletter, auto-notification | Archive |
| `URGENT` / `BLOCKER` | Action required today OR escalation | Teams alert + Planner task |
| `NEEDS_REVIEW` | Low confidence; human decides | Log to EmailTriageQueue; no action |
| `INVOICE_RECEIVED` | Invoice or bill received | FinancialRegister; Planner task |
| `PAYMENT_CONFIRMED` | Payment processed | Update FinancialRegister |
| `PO_APPROVED` | Purchase order approved | FinancialRegister |
| `BUDGET_ALERT` | Budget risk signal | Update ProjectRegistry RAGStatus; Teams alert |
| `BILLING_MILESTONE` | Stage complete — invoice can be raised | Planner task; Teams alert |

---

## SharePoint system of record (shared by both paths)

### EmailTriageQueue (Option A primary audit log)
Every triage decision written here. Source of truth for precision measurement and continuous improvement.

| Column | Purpose |
|---|---|
| EmailID / Title | Outlook message identifier |
| Subject, Sender, ReceivedDate | Email metadata |
| CoworkCategory / AgentCategory | What the AI classified it as |
| ConfirmedCategory | What Joseph confirmed it should be |
| IsCorrect | Yes/No — drives precision calculation |
| Action | What was done |
| ProjectCode | If applicable |
| Notes | Correction notes |

### ProjectRegistry
Active projects, owners, status, financial fields.

### FinancialRegister (Phase 3+)
All financial intent emails: type, amount, project code, status.

### AccountabilityScorecard (Phase 4+)
Weekly RAG status per metric owner. Source for Accountability Scorecard Power BI dashboard.

---

## Power BI reporting layer (shared by both paths)

```
SharePoint Lists          Planner           HaloPSA (optional)
(EmailTriageQueue,         (Tasks,           (Tickets, Time,
 ProjectRegistry,           Assignments,      Invoices)
 FinancialRegister,         Due dates)
 AccountabilityScorecard)
        ↓                     ↓                   ↓
        └──────────── Power BI ──────────────────┘
                          ↓
        Agent Performance │ Team Operations │ Financial
        Executive Portfolio │ Accountability Scorecard
                          ↓
        PA alert flows: metric crosses threshold → Teams alert
```

See [`docs/metrics-and-accountability.md`](metrics-and-accountability.md) for metric definitions and [`templates/powerbi/dashboard-spec.md`](../templates/powerbi/dashboard-spec.md) for build specs.

---

## Continuous improvement loop (both paths)

```
Signal processed by Cowork or Studio
        ↓
PA flow logs to EmailTriageQueue / AgentAuditLog
        ↓
NeedsReview items: human reviews → logs ConfirmedCategory + IsCorrect
        ↓
Weekly review (SOP-05):
    - Query log for corrections this week
    - Identify categories with systematic misclassification
    - Option A: update Cowork prompt templates (version in SharePoint)
    - Option B: update Studio agent instructions (version-control in git)
        ↓
Agent Performance dashboard:
    - Precision per category over time
    - NeedsReview rate trend
    - Correction frequency by category
```

---

## Accuracy gate: Phase 1 → Phase 2

Do not automate system-of-record writes (build PA flows) until:
- ≥90% classification precision across all categories over 2 weeks of manual evaluation
- <10% false positive rate per category
- NeedsReview rate understood and documented

**Evidence basis:** ML email classifiers baseline at F1 ~0.84; HITL systems reach 97–99.9% with tuning. 90% is a conservative, achievable threshold. (Source: InfoWorld/Springer, 2025–2026)

---

## Compliance notes (both paths)

| Requirement | Option A (Cowork) | Option B (Studio) |
|---|---|---|
| Data residency | Confirm Anthropic processing region in M365 Admin Centre (Cowork uses Anthropic Claude in US/Global) | Confirm M365 data residency = AU East/Southeast |
| Cross-border disclosure | APP 8 — Anthropic is a US subprocessor; document acceptance | Not applicable unless using external AI connectors |
| Privacy notice | Must disclose AI triage AND Anthropic cross-border processing | Must disclose AI-assisted triage |
| Audit trail | Microsoft Purview captures all Cowork interactions + PA flows capture all folder moves | Studio activity log + PA flow audit + SharePoint log |
| Maker credentials | Not applicable | Studio event triggers run under maker credentials — scope must be reviewed before publishing |

---

## Security model (Option B — Studio-first specific)

| Principle | Implementation |
|-----------|----------------|
| Least privilege | Each PA flow has minimum required Graph permissions |
| Maker credentials | Event triggers run under maker's credentials (platform constraint, Feb 2026) |
| Audit trail | Every execution logged — intent, confidence, action, outcome |
| Human gates | Low confidence → NeedsReview; external sends → always require approval |
| Version control | All instruction updates in git; rollback via `pac solution import` |
