# Cowork Prompt Templates

**Version:** 1.0.0 | **Framework:** M365 Copilot Agent Framework  
**Applies to:** Option A (Cowork-first) engagements

Store versioned copies of these templates in the customer's SharePoint `Prompt_Templates` library. Update with version number and precision notes after each change.

---

## Template structure

Effective Cowork triage prompts follow this pattern:

```
[Action instruction]. [Rules block]. [Fallback instruction].
```

- **Action instruction:** what you want Cowork to do
- **Rules block:** customer-specific classification rules (replace placeholders)
- **Fallback instruction:** always include — prevents confident mis-classification

---

## Phase 1: Daily triage (starter)

Use this in the first two weeks while approving every action manually.

```
Triage my inbox. For each unread email:
- If it requires a direct action from me: move to DO
- If it is purely informational with no action needed: move to FYI
- If it is a newsletter, marketing email, automated notification, or low-value update: move to IGNORE
- If the email is from a client or senior contact and requires action today: move to Urgent
- If you are unsure: move to NeedsReview — do not guess

Rules:
- [CLIENT_DOMAIN] emails → always DO unless clearly informational
- Emails with "invoice", "PO", or "purchase order" in the subject → always DO
- Emails from [NEWSLETTER_DOMAINS] → always IGNORE
- Emails with "unsubscribe" in the footer → IGNORE
- Emails from [EXECUTIVE_NAMES] → always treat as at least FYI

Do not skip the approval dialog for any action.
```

*Replace: `[CLIENT_DOMAIN]`, `[NEWSLETTER_DOMAINS]`, `[EXECUTIVE_NAMES]`*

---

## Phase 1: Morning catch-up

```
Catch me up on emails I received since 5 PM yesterday.
- Summarise any threads that need a reply
- Flag anything that looks urgent
- Tell me which emails you are not sure how to classify
Do not take any actions yet — just summarise.
```

---

## Phase 2+: Daily triage (refined after first precision review)

Update this template after Week 2 precision gate — add specific criteria for any category that had <95% precision.

```
Triage my inbox. Apply these rules:

URGENT → move to Urgent:
- Any email from [CLIENT_NAMES] requiring action today
- Any email mentioning: outage, breach, missed deadline, escalation, executive complaint
- Any email with "urgent" or "ASAP" in the subject from a known contact

DO → move to DO:
- Any email requiring a direct response or action from me
- Invoices, purchase orders, contracts needing review or approval
- Project updates requiring acknowledgement or decision
- Emails from [TEAM_MEMBERS] requiring input

FYI → move to FYI:
- Project updates that are informational only
- CC'd emails where no action is expected of me
- Internal announcements

IGNORE → move to IGNORE and archive:
- Newsletters and marketing emails
- Automated system notifications that don't require action (e.g., calendar reminders, build notifications)
- Emails from [KNOWN_IGNORE_SENDERS]

NeedsReview → if you are not confident in any classification, move to NeedsReview. Do not guess.
```

---

## Daily briefing (set up as scheduled prompt)

```
Give me a daily briefing for today. Include:
1. Urgent emails I received since yesterday at 5 PM — summarise each in one sentence
2. My meetings today with relevant email context (any recent emails from those attendees)
3. Emails in my DO folder that are more than 24 hours old and not yet actioned — list them
4. One key thing I should focus on first today

Keep it concise. No more than 10 items total.
```

*Set this as a scheduled Cowork prompt: Cowork > Scheduled tab > New scheduled prompt > Every weekday at 8:30 AM*

---

## Pre-meeting prep

```
Prepare me for my meeting with [ATTENDEE_NAME / COMPANY] at [TIME].
Find all emails between us from the last 3 weeks.
Summarise:
- What we last discussed
- Any outstanding commitments or action items
- Anything I need to follow up on
Keep it to 5 bullet points maximum.
```

---

## Reply drafting templates

### Acknowledgement reply

```
Draft a reply to [SENDER]'s email about [TOPIC].
Say: we have received their [request/inquiry/document] and will [respond/action/review] by [DATE].
Tone: professional and warm. Keep it under 3 sentences.
```

### Status update reply

```
Draft a reply to [SENDER] updating them on [PROJECT/TOPIC].
Current status: [STATUS_DESCRIPTION].
Next milestone: [NEXT_MILESTONE] by [DATE].
If there are any blockers, mention: [BLOCKER or "none at this stage"].
Tone: clear and direct. Under 150 words.
```

### Invoice confirmation

```
Draft a reply to [SENDER] confirming receipt of their invoice [INVOICE_NUMBER / DESCRIPTION].
Say: we have received invoice [number] dated [date] for [amount if known].
Confirm expected payment date or say we will process within our standard [N]-day payment terms.
Tone: professional. Under 3 sentences.
```

### Escalation reply

```
Draft a reply to [SENDER] responding to their escalation about [ISSUE].
Acknowledge the issue. Confirm we are treating it as priority.
State: we will [ACTION] by [DATE] and provide an update by [DATE].
Do not make commitments I have not confirmed. Use placeholder [TBC] where I need to fill in specifics.
Tone: empathetic, accountable, and direct.
```

---

## Inbox cleanup (periodic)

```
Clean up my inbox. 
1. Find all unread emails older than 30 days. Show me a summary grouped by sender — ask before archiving any.
2. Find all newsletters and marketing emails. List the senders — ask before bulk deleting.
3. Find emails from [SPECIFIC_SENDER] and move them all to [FOLDER].
Show me each group and ask for approval before taking any action.
```

---

## Project-based search

```
Find all emails about [PROJECT_NAME / TOPIC] from the last [N] weeks.
Summarise:
- Key decisions made
- Outstanding action items
- Any blockers mentioned
- Most recent status update
Return as a short bullet list.
```

---

## Financial intent triage (Phase 3+)

```
Triage my inbox. Apply standard rules (see Daily Triage template) PLUS:

FINANCIAL PRIORITY:
- Any email with "invoice", "tax invoice", or "bill" → move to DO immediately + note "INVOICE" in the subject
- Any email with "purchase order", "PO approved" → move to DO + note "PO"
- Any email mentioning "overdue", "past due", "payment reminder" → move to Urgent
- Any email from [FINANCE_CONTACT_NAMES] → treat as at least DO

If an email contains a financial amount ($ or AUD), flag it in the NeedsReview comment.
```

---

## Version log

| Version | Date | Change | Precision before | Precision after |
|---|---|---|---|---|
| 1.0 | [Date] | Initial template | — | — |
| | | | | |

*Update this table after every prompt change per SOP-04.*
