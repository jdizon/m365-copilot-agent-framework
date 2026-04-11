# SOP-05 — Weekly Review and Continuous Improvement

**Cadence:** Weekly (recommend: same day/time each week)
**Duration:** 30-45 minutes
**Owner:** [Head of IT / Operations Lead]
**Participants:** IT Support (optional)

---

## Purpose

The weekly review is the continuous improvement engine for the agent. It converts the previous week's audit data into instruction improvements, knowledge updates, and accuracy gains.

Without this review, the agent stays static. With it, the agent improves every week.

---

## Step 1 — Pull the week's metrics from Power BI (10 min)

Review the AgentAuditLog dashboard for the past 7 days:

| Metric | Review question |
|--------|----------------|
| Total signals processed | Is volume as expected? Any unusual spikes? |
| Classification breakdown by intent | Any category unexpectedly high or low? |
| NeedsReview rate | Is it trending down? If rising, why? |
| False positive rate per category | Which categories are most error-prone? |
| Action distribution | Which flows were called most? Any failures? |

Note any anomalies. These drive the review.

---

## Step 2 — Review NeedsReview queue (10 min)

Open `NeedsReviewQueue` in SharePoint. Filter by `ReviewStatus = Pending`.

For each item:
1. Read the signal excerpt and the agent's reason for routing to NeedsReview
2. Decide: correct category + project match
3. Fill in `CorrectProject`, `CorrectIntent`, and `CorrectionNotes`
4. Set `ReviewStatus = Reviewed`

**Pattern watch:** If 3+ items share the same misclassification reason, it's an instruction gap — flag for Step 3.

---

## Step 3 — Update agent instructions (10 min)

For each instruction gap identified:

1. Open the agent instruction file in git (`templates/agent/instructions-template.md`)
2. Make the targeted improvement:
   - Add a clarifying example for the confused category
   - Tighten the project matching criteria
   - Adjust the confidence threshold for a specific pattern
   - Add a new keyword to the project registry
3. Commit the change with a note: `Update: [what changed] — reason: [why]`
4. Deploy via `pac solution import` or update directly in Copilot Studio
5. Note the instruction version in `AgentVersion` column for future correlation

---

## Step 4 — Update knowledge base (5 min, as needed)

- New project started this week? Add to ProjectRegistry + create project brief in SOP Library.
- Project completed? Update status to Complete in ProjectRegistry.
- New stakeholder or client? Add to project brief document.
- Agent drafted an email that was significantly edited? Update Email Style Guide with the pattern.

---

## Step 5 — Record the review (5 min)

Log the review outcome in SharePoint (or a Loop page):

```
Date: [date]
Reviewer: [name]
Signals processed this week: [n]
NeedsReview items resolved: [n]
Instruction changes made: [yes/no — summary]
Knowledge base updates: [yes/no — summary]
Notes: [anything unusual]
Next week watch: [any category or pattern to monitor]
```

---

## Escalation criteria

Escalate to change control (Normal change) if:
- An instruction change affects more than one category
- A new intent category needs to be added
- Confidence threshold needs adjustment
- A flow is failing or producing incorrect results

---

## Improvement trajectory targets

| Timeframe | Target |
|-----------|--------|
| End of Phase 2 (2 weeks) | ≥90% precision; <10% false positive rate |
| Month 2 | NeedsReview rate declining week-on-week |
| Month 3 | Agent draft acceptance rate >50% |
| Month 6 | NeedsReview rate <10%; agent flags at-risk projects proactively |
