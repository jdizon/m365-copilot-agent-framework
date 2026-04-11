# Changelog

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
