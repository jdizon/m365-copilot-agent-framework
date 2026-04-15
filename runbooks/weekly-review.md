# SOP-05 — Weekly Review and Continuous Improvement

**Cadence:** Weekly (recommend: same day/time each week)  
**Duration:** 30–45 minutes  
**Owner:** [Head of IT / Operations Lead]  
**Participants:** IT Support (optional)

---

## Purpose

The weekly review is the continuous improvement engine for the agent. It converts the previous week's audit data into prompt improvements (Option A) or instruction updates (Option B), knowledge updates, and accuracy gains.

Without this review, the system stays static. With it, precision improves every week.

---

## Step 1 — Pull the week's metrics from Power BI (10 min)

Review the **Agent Performance** dashboard (source: EmailTriageQueue) for the past 7 days:

| Metric | Review question |
|--------|----------------|
| Total emails processed | Is volume as expected? Any unusual spikes? |
| Classification breakdown by category | Any category unexpectedly high or low? |
| NeedsReview rate | Is it trending down? If rising, why? |
| IsCorrect rate per category | Which categories are most error-prone? |
| False positive rate | Which categories have the most corrections? |

Note any anomalies. These drive Steps 2 and 3.

---

## Step 2 — Review NeedsReview queue (10 min)

Open **EmailTriageQueue** in SharePoint. Filter by `IsCorrect = (blank)` (NeedsReview items pending human classification).

For each item:
1. Read the email subject, sender, and the category Cowork/Studio assigned
2. Decide: what was the correct category?
3. Fill in `ConfirmedCategory`, `ConfirmedBy`, `ConfirmedDate`, and `IsCorrect` (Yes/No)
4. Add a brief note in `Notes` if the reason for misclassification is instructive

**Pattern watch:** If 3+ items share the same misclassification reason, it is a prompt/instruction gap — flag for Step 3.

---

## Step 3 — Update prompt templates or agent instructions (10 min)

### Option A — Cowork-first

For each gap identified in Step 2:

1. Open the **Prompt_Templates** document library in SharePoint
2. Open the current version of the affected template (e.g., `Daily_Triage_v1.2.md`)
3. Make the targeted improvement:
   - Add a clarifying rule for the confused category
   - Add a specific sender or domain to the rules block
   - Tighten or loosen a keyword condition
4. Save as a new version (increment the version number)
5. Add a row to the **Version Log** table in the template with: version, date, change summary, precision before, precision after (update "after" at next week's review)
6. Log the change in the SharePoint change log per **SOP-04**

### Option B — Studio-first

For each gap identified in Step 2:

1. Open the agent instructions in Copilot Studio (or `templates/agent/instructions-template.md` in git)
2. Make the targeted improvement:
   - Add a clarifying example for the confused category
   - Adjust a rule in the customer-specific rules block
   - Add a new keyword or sender to the classification logic
3. Save and deploy the updated instructions
4. Note the instruction version in the SharePoint change log per **SOP-04**

---

## Step 4 — Update knowledge base (5 min, as needed)

- New project started this week? Add to ProjectRegistry.
- Project completed? Update status to Complete in ProjectRegistry.
- New stakeholder or client? Add to customer-specific rules in the prompt template (Option A) or agent instructions (Option B).
- Agent drafted an email that was significantly edited? Update Email Style Guide with the pattern.

---

## Step 5 — Record the review (5 min)

Log the review outcome in SharePoint (change log or a dedicated Weekly Review list):

```
Date: [date]
Reviewer: [name]
Emails processed this week: [n]
NeedsReview items resolved: [n]
Prompt/instruction changes made: [yes/no — summary]
Knowledge base updates: [yes/no — summary]
Notes: [anything unusual]
Next week watch: [any category or pattern to monitor]
```

---

## Escalation criteria

Escalate to change control (Normal change — see SOP-04) if:
- A prompt/instruction change affects more than one category
- A new intent category needs to be added
- The urgency threshold needs adjustment
- A PA flow is failing or producing incorrect results

---

## Improvement trajectory targets

| Timeframe | Target |
|-----------|--------|
| End of Phase 1 (2 weeks) | ≥90% precision across all categories |
| Month 1 | NeedsReview rate declining week-on-week |
| Month 2 | NeedsReview rate <15%; corrections becoming rare |
| Month 3 | ≥95% precision in high-volume categories; "skip approvals" enabled for those (Option A) |
| Month 6 | NeedsReview rate <10%; prompt/instruction changes < 1 per month |
