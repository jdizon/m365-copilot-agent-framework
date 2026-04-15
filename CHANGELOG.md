# Changelog

## [2.0.0] — April 2026

### Breaking changes
- Framework restructured from Studio-only to **dual-path**: Option A (Cowork-first) and Option B (Studio-first)
- `runbooks/setup-checklist.md` fully rewritten — Studio agent build steps replaced with Cowork evaluation baseline (Phase 1) and PA governance flows (Phase 2)
- `consulting/engagement-model.md` fully rewritten — deliverables now vary by architecture path; prerequisites split by Option A / Option B
- `consulting/prerequisites-checklist.md` restructured — Cowork prerequisites + compliance gates added as Option A; Studio/Graph App Registration retained as Option B
- `templates/agent/instructions-template.md` annotated as Option B only; Cowork prompt templates now primary for Option A
- `runbooks/weekly-review.md` rewritten — `AgentAuditLog` / `pac solution import` references replaced with EmailTriageQueue + SharePoint prompt template versioning (Option A path documented alongside Option B)

### Added
- `docs/cowork-integration.md` — Cowork overview, licensing, admin enablement, data residency/APP compliance, 13 skills reference, approval gate guidance, PA integration pattern, known limitations
- `runbooks/power-automate-flows.md` — full step-by-step build guide for all 8 PA flows (4 core Phase 2 + 4 financial Phase 3), with exact Power Automate expressions, field mappings, and testing checklist
- `templates/cowork/prompt-templates.md` — full prompt library: Phase 1 triage, Phase 2+ refined triage, daily briefing, morning catch-up, pre-meeting prep, 4 reply types, inbox cleanup, project search, financial intent triage; version log table for SOP-04

### Changed
- `README.md` — v2.0 dual-path architecture overview; both option diagrams; updated repo structure; updated prerequisites by path
- `docs/architecture.md` — full rewrite: Option A vs B comparison table; both architecture diagrams with exact component flows; shared intent schema; shared SharePoint system of record; compliance notes table
- `docs/open-spec-template.md` — architecture option checkboxes added; prerequisites split by Option A / Option B; updated acceptance criteria
- `docs/evidence-research.md` — Section 5 (Cowork) expanded with GA dates, licensing, Anthropic processing detail, and 6 official Learn URLs; sources index updated from 16 to 22

---

## [1.2.0] — April 2026

### Added
- `runbooks/graph-access-setup.md` — step-by-step Microsoft Graph App Registration setup guide: Entra ID app creation, client secret management, 10-permission table (delegated + application), admin consent, Power Automate connection, Copilot Studio knowledge source connection, governance/rotation/compliance, troubleshooting table, Phase 0 exit gate
- `consulting/prerequisites-checklist.md` — new **Microsoft Graph App Registration** section with incremental permission guidance (Phase 2 minimum set vs Phase 3 additions) and vault storage requirement

### Changed
- `README.md` — repository structure updated to include `runbooks/graph-access-setup.md`; prerequisites section links to the new runbook

---

## [1.1.0] — April 2026

### Added
- `docs/metrics-and-accountability.md` — full accountability framework: 5 dashboards, metric definitions, owners, Green/Amber/Red thresholds, escalation rules, review cadence, commitment tracking model
- `templates/powerbi/dashboard-spec.md` — complete Power BI build specification: visuals, DAX measures, data sources, refresh cadence for all 5 dashboards
- Financial intent classifications added to architecture: INVOICE_RECEIVED, PAYMENT_CONFIRMED, PO_APPROVED, BUDGET_ALERT, BILLING_MILESTONE
- Financial columns added to ProjectRegistry schema (Budget, ActualSpend, BilledToDate, FinancialStatus, RAGStatus)
- FinancialRegister SharePoint list schema (invoices, POs, expenses, billing milestones)
- AccountabilityScorecard SharePoint list schema (weekly metric state for Power BI)
- README updated with metrics and accountability summary section
- Engagement model Standard tier updated to include financial tracking and all 5 dashboards

---

## [1.0.0] — April 2026

Initial release.

### Added
- README with architecture overview and engagement model summary
- `docs/architecture.md` — full architecture, intent classification schema, knowledge layers, security model
- `docs/evidence-research.md` — 16-source evidence pack (accuracy benchmarks, platform constraints, licensing, compliance)
- `docs/open-spec-template.md` — customer-ready OpenSpec template
- `templates/agent/instructions-template.md` — agent instructions template (fill in per customer)
- `templates/sharepoint/project-registry-schema.md` — ProjectRegistry, AuditLog, NeedsReviewQueue schemas
- `templates/knowledge-base/email-style-guide-template.md` — email style guide template
- `runbooks/setup-checklist.md` — full Phase 0-6 build checklist
- `runbooks/weekly-review.md` — SOP-05: weekly review and continuous improvement process
- `consulting/engagement-model.md` — Starter / Standard / Full / Retainer tiers
- `consulting/prerequisites-checklist.md` — customer readiness checklist + discovery questions
