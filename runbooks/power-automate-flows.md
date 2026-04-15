# Power Automate Flows — Build Guide

**Version:** 1.0.0 | **Framework:** M365 Copilot Agent Framework  
**Applies to:** Option A (Cowork-first). These flows are triggered by Cowork's folder actions, not by Studio event triggers.

---

## Overview

Four core flows in Phase 2, four financial flows in Phase 3. All use the same trigger pattern: **"When an email is moved to a folder" (Office 365 Outlook connector)**.

| Flow | Phase | Trigger folder | Primary action |
|---|---|---|---|
| Log-Audit-Entry | 2 | All triage folders | Create item in EmailTriageQueue |
| Create-Planner-Task | 2 | DO | Create task in PITA Action Register |
| Send-Teams-Alert | 2 | Urgent | Post to Teams channel |
| Log-NeedsReview | 2 | NeedsReview | Create item in EmailTriageQueue (IsCorrect = null) |
| Detect-Financial-Intent | 3 | DO (conditional) | Write to FinancialRegister |
| Create-Invoice-Task | 3 | FinancialRegister trigger | Create Planner task with financial metadata |
| Log-PO-Entry | 3 | FinancialRegister trigger | Update FinancialRegister |
| Flag-Budget-Overrun | 3 | FinancialRegister trigger | Teams alert + update ProjectRegistry |

---

## Important: folder names are case-sensitive

The Outlook connector's "When an email is moved to a folder" trigger uses exact folder name matching. **DO** is different from **Do**. Set folder names in Outlook first, then copy the exact names into each flow trigger.

---

## Phase 2 Flows

---

### Flow 1 — Log-Audit-Entry

**Purpose:** Automatically log every Cowork-approved triage action to EmailTriageQueue. Replaces the manual logging from Phase 1.

**Build steps:**

1. Go to [make.powerautomate.com](https://make.powerautomate.com) > Create > Automated cloud flow
2. Name: `PITA - Log-Audit-Entry`
3. **Trigger:** Office 365 Outlook — "When an email is moved to a folder"
   - Mailbox: customer's mailbox
   - Folder: create a **parallel branch** condition to handle all folders, OR build one flow per folder (simpler to debug)

4. **Action:** SharePoint — "Create item"
   - Site: your SharePoint site
   - List: EmailTriageQueue
   - Field mapping:

   | SharePoint column | Value (from trigger or expression) |
   |---|---|
   | Title | `triggerOutputs()?['body/Subject']` |
   | Subject | `triggerOutputs()?['body/Subject']` |
   | Sender | `triggerOutputs()?['body/From']` |
   | ReceivedDate | `triggerOutputs()?['body/DateTimeReceived']` |
   | CoworkCategory | Name of the folder (hardcode per flow, or use dynamic folder name) |
   | Action | `moved` |
   | ConfirmedDate | `utcNow()` |

5. **Test:** Move a real email to the DO folder manually. Confirm a row appears in EmailTriageQueue within ~30 seconds.

**Troubleshooting:**
- Flow not firing → check solution-aware cloud flow sharing is enabled in Power Platform Admin Centre
- SharePoint item not created → re-authenticate the SharePoint connection; verify column names match exactly

---

### Flow 2 — Create-Planner-Task

**Purpose:** Create a Planner task for every email moved to the DO folder (by Cowork or manually).

**Build steps:**

1. Create new automated cloud flow
2. Name: `PITA - Create-Planner-Task`
3. **Trigger:** Office 365 Outlook — "When an email is moved to a folder"
   - Folder: **DO**

4. **Action 1:** SharePoint — "Create item" in EmailTriageQueue (same as Log-Audit-Entry — you can call the Log-Audit-Entry flow as a child flow, or duplicate the action)

5. **Action 2:** Planner — "Create a task"
   - Plan ID: PITA Action Register plan ID (find in Planner URL)
   - Bucket ID: DO – This Week bucket ID
   - Title: `triggerOutputs()?['body/Subject']`
   - Due date: `addDays(utcNow(), 3)` (default +3 business days; customise if needed)
   - Description / Notes: compose expression — sender + email link:
     ```
     concat('From: ', triggerOutputs()?['body/From'], ' | ', triggerOutputs()?['body/WebLink'])
     ```

6. **Test:** Move a test email to DO folder. Confirm:
   - Planner task appears in "DO – This Week" bucket
   - Task title = email subject
   - Description contains sender and link

**Troubleshooting:**
- Plan ID / Bucket ID not found → open Planner, click into the plan, copy the ID from the URL
- Task created in wrong bucket → verify Bucket ID; bucket IDs are GUIDs, not names

---

### Flow 3 — Send-Teams-Alert

**Purpose:** Post a Teams message to the urgent alerts channel for every email moved to the Urgent folder.

**Build steps:**

1. Create new automated cloud flow
2. Name: `PITA - Send-Teams-Alert`
3. **Trigger:** Office 365 Outlook — "When an email is moved to a folder"
   - Folder: **Urgent**

4. **Action:** Microsoft Teams — "Post message in a chat or channel"
   - Post as: Flow bot (or a service account — agree with customer)
   - Post in: Channel
   - Team: select the customer's team
   - Channel: select the designated urgent alerts channel (agreed in SOP-02)
   - Message: compose with HTML formatting:
     ```
     🚨 URGENT EMAIL<br>
     <b>From:</b> [sender]<br>
     <b>Subject:</b> [subject]<br>
     <b>Received:</b> [received date/time]<br>
     <b>Link:</b> <a href="[weblink]">Open email</a>
     ```

5. **Test:** Move a test email to Urgent folder. Confirm Teams message appears with correct content.

**Troubleshooting:**
- Teams message not posting → confirm the connection account has permission to post to that channel
- Wrong team/channel → verify Team ID and Channel ID in the flow action

---

### Flow 4 — Log-NeedsReview

**Purpose:** Log emails that Cowork could not classify confidently. IsCorrect is left blank — customer fills this during weekly review.

**Build steps:**

1. Create new automated cloud flow
2. Name: `PITA - Log-NeedsReview`
3. **Trigger:** Office 365 Outlook — "When an email is moved to a folder"
   - Folder: **NeedsReview**

4. **Action:** SharePoint — "Create item" in EmailTriageQueue
   - Field mapping same as Log-Audit-Entry EXCEPT:
   - CoworkCategory: `NeedsReview`
   - IsCorrect: leave blank (null — this is populated during weekly review)
   - Notes: `Pending human review`

5. **Test:** Move a test email to NeedsReview. Confirm row created with blank IsCorrect.

---

## Phase 3 Financial Flows

---

### Flow 5 — Detect-Financial-Intent

**Purpose:** Identify emails with financial intent (invoices, POs, payments, budget alerts) and log them to FinancialRegister.

**Build steps:**

1. Trigger: email moved to DO folder (same as Create-Planner-Task)
2. **Condition:** check if subject or body contains financial keywords:
   ```
   contains(triggerOutputs()?['body/Subject'], 'invoice') OR
   contains(triggerOutputs()?['body/Subject'], 'PO') OR
   contains(triggerOutputs()?['body/Subject'], 'payment') OR
   contains(triggerOutputs()?['body/Subject'], 'budget') OR
   contains(triggerOutputs()?['body/Subject'], 'milestone')
   ```
   *(customise keyword list for customer's context)*

3. **If yes:** Classify intent type using a Switch condition:
   - `invoice` or `tax invoice` → `INVOICE_RECEIVED`
   - `payment received` or `payment confirmed` → `PAYMENT_CONFIRMED`
   - `purchase order` or `PO approved` → `PO_APPROVED`
   - `budget alert` or `over budget` → `BUDGET_ALERT`
   - `milestone` or `stage complete` → `BILLING_MILESTONE`

4. **Action:** SharePoint — "Create item" in FinancialRegister
   - EmailID: email subject + received date
   - IntentType: classified value from Switch
   - ProjectCode: *(optional — extract from subject if customer uses project codes)*
   - Date: `triggerOutputs()?['body/DateTimeReceived']`
   - Status: `New`

---

### Flow 6 — Create-Invoice-Task

**Purpose:** Create a Planner task when an invoice is received, so it doesn't get lost.

**Trigger:** SharePoint item created in FinancialRegister where IntentType = `INVOICE_RECEIVED`

**Action:** Planner — "Create a task"
- Bucket: DO – This Week
- Title: `Invoice: [EmailID from FinancialRegister]`
- Due date: `addDays(triggerOutputs()?['body/Date'], 14)` (14-day payment terms default — adjust)
- Notes: Amount (if extracted), sender, email link

---

### Flow 7 — Log-PO-Entry

**Purpose:** Confirm PO approval is recorded in FinancialRegister with status update.

**Trigger:** SharePoint item created in FinancialRegister where IntentType = `PO_APPROVED`

**Action:** SharePoint — "Update item" in FinancialRegister
- Status: `Confirmed`

*(Extend to update ProjectRegistry if needed for the customer.)*

---

### Flow 8 — Flag-Budget-Overrun

**Purpose:** Alert the team and update project RAG status when a budget alert email is received.

**Trigger:** SharePoint item created in FinancialRegister where IntentType = `BUDGET_ALERT`

**Action 1:** Microsoft Teams — "Post message in a chat or channel"
- Channel: urgent alerts channel (or a dedicated finance alerts channel)
- Message: *"⚠️ BUDGET ALERT — [EmailID] — review FinancialRegister for details"*

**Action 2:** SharePoint — "Update item" in ProjectRegistry
- Find item by ProjectCode (if available)
- Set FinancialStatus: `At Risk`
- Set RAGStatus: `Amber`

---

## Weekly accountability alert (Phase 4)

**Purpose:** Every Friday, alert metric owners in Teams for any AccountabilityScorecard metric that has turned Red.

**Trigger:** Recurrence — every Friday at 5 PM (or end of business day for the customer)

**Action:** SharePoint — "Get items" from AccountabilityScorecard where RAGStatus = `Red` AND WeekEnding = this week

**For each item:** Teams — "Post message" to the MetricOwner (use @mention if supported):
- *"📊 ACCOUNTABILITY ALERT — [MetricName] is Red this week. Owner: [MetricOwner]. Please update the scorecard with corrective action."*

---

## Testing checklist

Before marking any phase complete, confirm:

- [ ] All Phase 2 flows tested with real emails (not just test emails — at least 5 real emails per folder)
- [ ] EmailTriageQueue rows contain correct metadata (not blank fields)
- [ ] Planner tasks created with correct bucket, title, and due date
- [ ] Teams alerts post to the correct channel with readable content
- [ ] NeedsReview rows have blank IsCorrect (not null error)
- [ ] Financial flows fire on real keyword matches, not test data only
- [ ] Friday accountability alert fires (trigger manually from Power Automate to test before scheduling)
