# Setup Checklist — M365 Copilot Agent

**Version:** v1.2 | **Framework:** M365 Copilot Agent Framework

---

## Prerequisites (do not skip)

### Access
- [ ] Access to Microsoft Copilot Studio (maker role)
- [ ] Access to Power Automate (Premium licence)
- [ ] Azure AD App Registration — client ID + secret for `pac` CLI deployment
- [ ] System Administrator role in the target Power Platform environment
- [ ] Admin confirmation: solution-aware cloud flow sharing enabled in Power Platform Admin Centre

### Compliance
- [ ] Privacy notice updated to disclose AI-assisted signal processing
- [ ] M365 data residency confirmed (AU East/Southeast or relevant region) in Admin Centre
- [ ] Maker account access scope reviewed — triggers run under maker credentials (platform constraint)

### Foundations
- [ ] Outlook folders/categories created (folder-first — routing depends on this)
- [ ] SharePoint lists created: ProjectRegistry, AgentAuditLog, NeedsReviewQueue (see schemas)
- [ ] SharePoint SOP Library document library created
- [ ] Project registry populated with at least active projects
- [ ] Urgent criteria defined and agreed (prevents Teams alert fatigue)
- [ ] Planner plan created with agreed bucket structure

---

## Phase 0 — Foundations

- [ ] Complete all prerequisites above
- [ ] Train team on taxonomy (folder names, intent categories, urgency definition)
- [ ] Define Teams channel/chat for urgent alerts

**Exit criteria:** Team can triage manually using taxonomy; SharePoint log ready to receive entries.

---

## Phase 1 — Manual baseline (1-2 weeks)

- [ ] Route signals manually using the agreed taxonomy
- [ ] Log sample decisions in SharePoint AgentAuditLog (manual entries)
- [ ] Draft SOP-01 to SOP-05 (see templates)
- [ ] Confirm urgency criteria are stable and low-noise

**Exit criteria:** Taxonomy stable; SOP set drafted; baseline accuracy measurable.

---

## Phase 2 — Assisted automation (classify + log, no autonomous updates)

### Build the agent
- [ ] Create agent in Copilot Studio named: **[Agent Name]**
- [ ] Set description: define scope, exclude external sends / calendar / deletion
- [ ] Enable **generative orchestration** (required for event triggers)
- [ ] Add agent instructions from `templates/agent/instructions-template.md`
- [ ] Add knowledge sources:
  - [ ] ProjectRegistry SharePoint list
  - [ ] SOP Library document library
- [ ] Add event trigger: "When new email arrives" (Outlook connector — Joseph's inbox)
- [ ] Test trigger payload: run triggering event once; verify payload fields before publishing

### Build Phase 2 flows (log + label only — no source-of-truth updates yet)
- [ ] `Log-Audit-Entry` — writes classification result to AgentAuditLog (no action taken)
- [ ] `Log-NeedsReview` — routes low-confidence signals to NeedsReviewQueue

### Test
- [ ] Test categories: STATUS_UPDATE / ACTION_ITEM / BLOCKER / FYI / NEEDS_REVIEW
- [ ] Test against 50+ real emails before going live (industry benchmark)
- [ ] Confirm 100% of triggered runs appear in AgentAuditLog

**Exit criteria:** Classification precision ≥90% over 2 weeks of logged runs. False positive rate <10%.

---

## Phase 3 — Autonomous routing (source-of-truth updates)

### Build action flows
- [ ] `Update-Project-Status` — PATCH SharePoint ProjectRegistry item
- [ ] `Update-Planner-Task` — PATCH Planner task + add comment
- [ ] `Create-Planner-Task` — POST new Planner task
- [ ] `Update-Loop-Page` — append to Loop workspace page
- [ ] `Add-OneNote-Note` — POST to OneNote section
- [ ] `Send-Teams-Alert` — POST to Teams channel (urgent only)

### Enable agent actions
- [ ] Register each flow as a callable action in the Copilot Studio agent
- [ ] Update agent instructions: add action selection criteria per intent category
- [ ] Test each action flow with the agent in sandbox before publishing

**Exit criteria:** Routing accuracy meets Phase 2 gate; no alert fatigue reported; NeedsReview lane working.

---

## Phase 4 — Task capture + dashboards

- [ ] Confirm `Create-Planner-Task` flow in production
- [ ] Build Power BI dashboard connected to AgentAuditLog:
  - Volume: signals processed per day/week
  - Classification breakdown by intent
  - NeedsReview rate
  - Action distribution (which flows called most)
- [ ] Weekly review cadence formalised (SOP-05)

---

## Phase 5 — Knowledge enrichment

- [ ] Add project brief documents to SOP Library; refresh agent knowledge source
- [ ] Build `Email-Style-Guide.md` in SOP Library (see template)
- [ ] Confirm agent can retrieve project context and draft responses
- [ ] Confirm M365 Copilot licence active → Semantic Index available
- [ ] Add Teams chat trigger: "When message posted in channel"
- [ ] Add meeting transcript trigger: "When meeting ends / transcript ready"

---

## Phase 6 — Continuous improvement

- [ ] Add CorrectedCategory + CorrectionReason columns to NeedsReviewQueue
- [ ] Formalise weekly review → instruction update → git commit cycle
- [ ] Update Power BI dashboard: add precision trend, correction frequency, NeedsReview trend
- [ ] Conduct quarterly knowledge base review (project registry, style guide, SOPs)

---

## Change control reference

| Type | Trigger | Process |
|------|---------|---------|
| Standard | Prompt tweaks, minor taxonomy edits | Log in audit |
| Normal | New category, routing logic, threshold | Review + rollback plan |
| Emergency | Disable routing after incident | Restore safe state → post-change review |
