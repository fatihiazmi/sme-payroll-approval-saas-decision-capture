# Accepted Default Baseline — SME Payroll Approval SaaS

**Decision date:** 2026-05-17T16:35:38+08:00
**Decision maker:** Nik
**Status:** Accepted baseline for MVP planning

Nik reviewed the decision-capture documents and chose to proceed with the default recommendations. This file converts the recommended defaults into the current product baseline for the next PRD, backlog, architecture, and prototype work.

---

## 1. Product Positioning

Build a focused SaaS workflow for SME payroll verification and approval, aimed at SMEs and service providers managing payroll operations across multiple companies.

The MVP is **not** a full ERP, full payroll statutory engine, or full accounting system.

Primary wedge:

> Multi-company payroll verification, OT exception review, SME owner approval, payment export/proof, and audit evidence.

---

## 2. Accepted MVP Scope

### Included in MVP

- Multi-company workspace for SMEs/service-provider operation
- Payroll import or manual entry
- Payroll verification checklist
- Overtime exception review and approval workflow
- Payroll run status lifecycle
- SME owner approval before payment
- Payment export/proof upload
- Payroll evidence pack and audit timeline: versioned, permission-filtered ZIP evidence pack with PDF summary plus CSV/JSON attachments, retained for 7 years by default (`DEC-2026-05-18-0033-versioned-permissioned-evidence-pack`)
- Payroll journal preview/export using practical company-level payroll mapping buckets, not a full accounting ledger or full COA module (`DEC-2026-05-18-0039-practical-journal-mapping-with-future-coa-path`); preview is controlled, balanced, version-bound, and export-blocking when invalid (`DEC-2026-05-18-0050-controlled-balanced-journal-preview`); CSV export is generated only from a locked valid preview (`DEC-2026-05-18-0055-controlled-journal-csv-export`)
- Role-based access control with assignment boundaries
- Audit logging for sensitive payroll actions
- Sensitive field controls for salary and bank details
- Owner analytics dashboard for payroll readiness, approval status, urgency, trends, variance, cashflow forecast, and advisory risk/AI alerts (`DEC-2026-05-18-0059-owner-analytics-dashboard`)

### Deferred from MVP

- Full ERP suite
- Full statutory payroll calculation engine for EPF/SOCSO/EIS/PCB unless pilot requires it
- Full GL/AP/AR/accounting modules
- Bank reconciliation
- Invoice processing
- Advanced finance automation
- AI training on customer payroll data; PAY-023 advisory dashboard alerts do not permit customer payroll data training by default
- Deep third-party integrations beyond import/export unless required for pilot

---

## 3. Accepted SaaS Architecture Defaults

- Hybrid tenant hierarchy:
  - Platform account
  - Service-provider/CoSec-style tenant
  - SME company workspace
- MVP company workspaces are created by Service Provider Admin users; SME owner self-service registration is deferred to Phase 2.
- Every request must carry explicit tenant and company context.
- Payroll runs display a month/year label for MVP usability but store explicit `period_start`, `period_end`, and `pay_date` for audit and future non-monthly cycles.
- Use logical tenant isolation for MVP with strict authorization and audit controls.
- Restrict service-provider staff to assigned SMEs only.
- MVP user onboarding supports both email invites and Service Provider Admin manual user creation; manually created users remain pending until activation/password setup and all role assignments are company-scoped and audited.
- Sensitive salary, bank, identity, and payroll evidence fields require explicit permission and audit logging.
- Salary visibility defaults to Owner + Payroll Operator; bank/payment visibility defaults to Owner + Payment/Journal role for approved payment workflows; customer/tenant feature flags must not disable masking.
- MFA should be mandatory for privileged users and strongly encouraged/required for sensitive roles.
- Customer data must not be used to train AI models by default. PAY-023 AI/risk alerts are advisory dashboard signals only, not automatic payroll decisions.

---

## 4. Accepted Finance/Audit Defaults

- The product is an evidence and approval system first, not accounting software.
- MVP should produce a payroll audit pack.
- MVP should support payroll journal preview/export using practical mapping buckets; the mapping model should preserve a future path to full chart-of-accounts/COA integration (`DEC-2026-05-18-0039-practical-journal-mapping-with-future-coa-path`). Journal preview must be balanced, tied to a payroll run version, permission-filtered, and block export when invalid (`DEC-2026-05-18-0050-controlled-balanced-journal-preview`). Journal CSV export must come from a locked valid preview and record checksum/format/version metadata (`DEC-2026-05-18-0055-controlled-journal-csv-export`).
- Audit timeline should be append-only; corrections should be new events, not silent edits.
- Payment proof and statutory proof can be uploaded for MVP rather than deeply integrated.

---

## 5. Accepted Workflow Defaults

Recommended payroll lifecycle:

1. Draft / Imported
2. Validation Issues
3. Ready for Review
4. OT / Exception Review
5. Pending SME Approval
6. Approved for Payment
7. Payment Exported
8. Payment Proof Uploaded
9. Closed / Archived
10. Reopened / Correction Required, controlled exception path

---


## 6. Accepted Owner Return / Correction Decision

**Decision:** For MVP, owner return-for-correction uses a structured reason category plus a required comment, not free-text only and not line-level annotation.

**Decision ID:** `DEC-2026-05-17-2258-owner-return-structured-correction`

**Scope:**

- Return is allowed only from Pending SME Approval by an authorized SME approver.
- Return requires one reason category: Wrong amount, Missing employee, OT issue, Bank detail issue, Missing evidence, or Other.
- Return requires an owner comment and changes the run to Reopened / Correction Required.
- Return invalidates the submitted approval snapshot; corrections create a new payroll run version.
- The corrected run must pass validation/review and be re-submitted before approval.
- Audit trail must preserve return reason, owner comment, actor, timestamp, prior submitted version, correction event, new run version, and resubmission event.

**Out of MVP:** line-by-line annotation, chat thread per correction, multi-round dispute workflow, formal rejection letter/PDF, and owner editing payroll directly.

---

## 7. Accepted Owner Readiness Summary Decision

**Decision:** For MVP, the owner approval page shows a decision-ready readiness summary bound to the submitted payroll run version.

**Decision ID:** `DEC-2026-05-17-2306-owner-readiness-summary`

**Scope:**

- Summary shows employee count, gross total, net total, payment-ready total, validation status, blocking issue count, warning count, OT/exception status, evidence readiness, submitted by, submission time, and run version.
- Summary provides clear Approve, Return for correction, and View details actions for authorized approvers.
- Warnings, exceptions, and incomplete evidence show counts and links to detail pages.
- If the current payroll run version differs from the submitted approval snapshot, approval is blocked until the latest submitted snapshot is viewed.
- Sensitive totals and row details remain protected by role and sensitive-field policy.

**Out of MVP for the approval page:** full approval pack PDF on the approval page, full employee-by-employee drilldown on the same page, evidence file previewer, and owner-side editing. PAY-023 owns owner dashboard analytics/risk scope.

---


## 12. Accepted RBAC Scope Decision

**Decision:** Use fixed MVP roles with an explicit permission matrix for PAY-016, while designing the authorization model so it can evolve into a future custom role builder.

**Decision ID:** `DEC-2026-05-17-2331-fixed-mvp-rbac-permission-matrix`

**Scope:**

- Fixed MVP role bundles only: Owner / Approver, Payroll Operator, Payment / Journal User, Auditor / Read-only Reviewer, and Platform Admin.
- Users may hold multiple fixed roles within one company when real SME staffing overlaps.
- Every grant is company-scoped unless it is an operational platform-support permission, and platform-support access requires reason and audit logging.
- Authorization checks must use stable permission keys, not informal role-name checks scattered through application code.
- Sensitive salary, bank, identity, evidence, export, approval, and audit-pack access remains default-deny unless the permission matrix grants it.
- UI hiding is not a security boundary; UI and API paths must both call the same server-side authorization policy.
- Lifecycle-changing and sensitive denied attempts must be audit-logged.

**Future evolution guardrail:** Fixed roles are stored as role bundles over stable permission keys so Phase 2+ can add tenant-defined custom roles without rewriting payroll workflow, export, evidence, or sensitive-field authorization logic.

**Out of MVP:** tenant-created custom roles, tenant-facing custom role builder UI, custom permission editor, role templates marketplace, per-field custom permission editor, complex multi-level approval matrix, and customer-controlled disabling of sensitive-data masking.

---

## 13. Accepted Sensitive Data Masking Decision

**Decision:** Use strict default masking with explicit permission-based reveal for PAY-017.

**Decision ID:** `DEC-2026-05-17-2337-strict-sensitive-data-masking`

**Scope:**

- Salary, deduction, net pay, bank account, identity, payment proof, and evidence file details are masked by default.
- Reveal requires active company membership, company assignment where applicable, explicit permission key, valid workflow state, and server-side sensitive-field policy approval.
- Owner / Approver and Payroll Operator can see salary/payroll data for preparation and approval where granted by the permission matrix.
- Payment / Journal User can see approved bank/payment details only for payment export, proof upload, and journal handoff workflows.
- Auditor / Read-only Reviewer sees masked data unless explicitly granted by role/permission.
- Every sensitive reveal, export, download, and denied sensitive access attempt is audit-logged.
- Customer- or tenant-facing feature flags must not disable masking.

**Out of MVP:** customer-configurable masking rules, field-by-field tenant configuration, temporary reveal request workflow, manager-by-department salary visibility, evidence redaction workflow, and customer-controlled masking disablement.

---

## 14. Accepted Audit Timeline Decision

**Decision:** Use a structured append-only audit timeline for PAY-018.

**Decision ID:** `DEC-2026-05-17-2341-structured-append-only-audit-timeline`

**Scope:**

- Audit events are append-only; previous events are never edited or deleted by normal product workflows.
- Corrections, supersessions, returns, reopens, evidence replacement, and permission changes create new audit events.
- MVP records structured events for company creation, user invitation/manual creation, role assignment/change, payroll run creation, import preview, import commit, payroll row edit, validation run, exception review/resolution, submission, approval, return for correction, payment export, payment proof upload, evidence pack generation/download, sensitive data reveal/export/download, and denied sensitive/lifecycle attempts.
- Each event records actor, company, payroll run/resource reference, action/event type, timestamp, run version where applicable, from/to status where applicable, source IP/user agent where available, and safe metadata.
- Audit metadata must not store raw full salary, bank account, identity, or unrestricted evidence contents; use masked values, references, checksums, counts, reason codes, and version IDs.
- Authorized audit history is displayed chronologically for the payroll run and included in the audit evidence pack.

**Out of MVP:** full SIEM integration, tamper-evident blockchain-style audit chain, advanced audit search/reporting, legal hold workflow, audit event redaction workflow, and blockchain/notary-style external attestation.

---


## 15. Accepted Evidence Pack Decision

**Decision:** Use a versioned, permission-filtered ZIP evidence pack for PAY-019.

**Decision ID:** `DEC-2026-05-18-0033-versioned-permissioned-evidence-pack`

**Scope:**

- Evidence pack generation is available only for Approved for Payment, Payment Exported, Payment Proof Uploaded, or Closed / Archived payroll runs.
- The generated file is a ZIP containing a PDF summary plus structured CSV/JSON attachments.
- Pack contents include payroll run summary, approval record, validation results, OT/exception reviews, export record, payment proof reference, journal preview/export where available, document index, and append-only audit timeline.
- Pack generation is server-side permission-filtered: salary, bank, identity, payment proof, and evidence contents follow the sensitive-field policy; unauthorized fields are masked, omitted, or represented by safe references/checksums.
- Each pack record stores payroll run version, pack version, file hash/checksum, generator, timestamp, sensitivity markers, source artifact/version references, and `retention_until = generated_at + 7 years`.
- Generation, denied generation, and download events are audit-logged.

**Out of MVP:** legal hold workflow, automatic purge engine, auditor portal, external e-signature/certification, evidence redaction workflow, and tamper-proof external notarization.

---

## 16. Accepted Payroll Journal Mapping Decision

**Decision:** Use practical company-level payroll journal mapping buckets for PAY-020, with a future path to full chart-of-accounts/COA integration.

**Decision ID:** `DEC-2026-05-18-0039-practical-journal-mapping-with-future-coa-path`

**Scope:**

- MVP supports company-level account code mappings for common payroll journal buckets: `salary_expense`, `allowance_expense`, `overtime_expense`, `employer_statutory_expense`, `employee_deduction_payable`, `epf_kwsp_payable`, `socso_perkeso_payable`, `eis_payable`, `pcb_mtd_payable`, `net_salary_payable`, `cash_bank_clearing`, `rounding_adjustment`, and optional `department_or_cost_centre`.
- Required mappings must exist before journal preview/export; missing, inactive, or invalid mappings block preview/export with clear reasons.
- Only authorized Owner / Approver or Payment / Journal users can create or change mappings.
- Mapping changes are audit-logged with actor, timestamp, company, bucket key, old/new account code or label, and reason/note where supplied.
- Stable payroll mapping bucket keys must be stored separately from account code labels so the model can later connect to a full COA/chart-of-accounts or accounting package without rewriting journal generation.

**Out of MVP:** full chart-of-accounts/COA management, per-employee account rules, multi-entity consolidation, accounting package sync, multi-currency accounting, and complex cost allocation engine.

---

## 17. Accepted Payroll Journal Preview Decision

**Decision:** Use a controlled balanced journal preview for PAY-021 before any journal export.

**Decision ID:** `DEC-2026-05-18-0050-controlled-balanced-journal-preview`

**Scope:**

- Preview debit and credit journal lines from payroll totals and PAY-020 mapping buckets with account code, description, debit/credit side, amount, source mapping bucket, and payroll run version.
- Required mappings must exist and be active/valid before preview/export; missing or invalid mappings block preview/export with clear reasons.
- Total debits must equal total credits; any imbalance is highlighted with the imbalance amount and export is blocked.
- Preview is tied to the payroll run version. Before approval, preview may use status-appropriate draft totals where the workflow allows it; after approval/payment handoff, preview/export uses the approved run version.
- Refreshing preview recalculates against the latest valid payroll totals for the current run state.
- Sensitive totals and line details follow server-side role/permission masking; unauthorized reveal/export attempts are denied and audit-logged.
- Journal export must use the previewed balanced version.

**Out of MVP:** manual journal adjustments, posting periods, reversal entries, accruals, multi-currency accounting, branch/entity consolidation, direct GL ledger posting, and full accounting journal engine behavior.

---

## 18. Accepted Payroll Journal CSV Export Decision

**Decision:** Export payroll journal CSV only from a locked valid PAY-021 preview.

**Decision ID:** `DEC-2026-05-18-0055-controlled-journal-csv-export`

**Scope:**

- CSV export is allowed only from a valid balanced journal preview.
- Export is blocked if the preview is stale, invalid, missing required mappings, or imbalanced.
- Export records `export_id`, `payroll_run_id`, `payroll_run_version`, `preview_id`, `preview_version`, company, payroll period, journal date, currency, exported by/at, file checksum, and format version.
- CSV lines include account code, account description, debit, credit, mapping bucket, and line description.
- Export follows sensitive-field permissions and is audit-logged.
- Evidence packs reference the exported file/checksum/version where available.

**Out of MVP:** Xero/QuickBooks/SQL Accounting/AutoCount-specific formats, direct accounting API sync, posting into GL, multi-format export builder, and package-specific validation rules.

---

## 19. Accepted Owner Analytics Dashboard Decision

**Decision:** Use analytics dashboard scope for PAY-023.

**Decision ID:** `DEC-2026-05-18-0059-owner-analytics-dashboard`

**Scope:**

- Owner dashboard shows assigned-company payroll runs grouped by Draft, Pending Approval, Approved, Payment Exported, Payment Proof Uploaded, and Closed / Archived.
- Each item shows status, pay period, pay date, days until pay date, employee count, net total where permitted, blocking issue count, pending owner action, latest return/comment, proof/evidence status, and quick link to approval/detail page.
- Dashboard includes charts, monthly trends, variance analytics, cashflow forecast, and advisory risk/AI alerts.
- Urgent items appear first: overdue/near pay date, pending owner action, blocking issues, missing proof/evidence.
- Sensitive totals follow role/permission masking. Denied access is audit-logged.
- AI/risk alerts are advisory only. They cannot approve, reject, edit, or replace human/accountant judgment.
- Customer payroll data is not used to train AI models by default.

**Out of MVP:** auto-approval, automatic payroll changes, AI-generated statutory/accounting advice, external BI warehouse, customizable dashboard builder, and mobile push notification engine.

---

## 8. Next Artifacts to Produce

1. MVP PRD
2. Domain model and ubiquitous language
3. Payroll workflow state machine
4. SaaS tenancy and RBAC model
5. Initial Agile backlog using INVEST stories
6. Prototype/demo flow script

---

## 9. Source Decision Documents

- `docs/decision-capture/payroll-cosec-workflow.md`
- `docs/decision-capture/finance-audit-evidence.md`
- `docs/decision-capture/saas-product-architecture.md`

## 10. Accepted Payment Export Decision

**Decision:** For MVP, export a controlled generic CSV payment file after payroll approval, then require manual payment proof upload.

**Decision ID:** `DEC-2026-05-17-2313-controlled-generic-payment-csv`

**Scope:**

- Generate export only from an Approved for Payment payroll run version.
- Require payment export permission before exposing bank/salary payment data.
- Include `employee_identifier`, `employee_name`, `bank_name`, `bank_account`, `payment_reference`, `net_pay_amount`, `currency`, and `pay_date`.
- Record exporter, timestamp, run version, row count, exported total, file checksum, and export format version in the audit timeline.
- Block export until the payroll run is approved.
- Audit denied export attempts for pre-approval or unauthorized users.
- Do not build Maybank/CIMB/bank-specific formats, direct bank API, payment status reconciliation, multi-bank batch splitting, automatic payment release, or encryption/signing of bank files in MVP.
- Expand to specific Malaysian bank formats after pilot validation.

---


## 11. Accepted Payment Proof Upload Decision

**Decision:** For MVP, payment proof upload is controlled evidence capture, not bank-side payment verification.

**Decision ID:** `DEC-2026-05-17-2317-controlled-payment-proof-upload`

**Scope:**

- Allow proof upload for Approved for Payment or Payment Exported payroll runs.
- Require payment/proof permission before upload or download.
- Require proof file, proof type, payment date, and payment reference or note.
- Validate accepted file type and size; record malware-scan placeholder/status.
- Record uploader, timestamp, file name, file type, file size, file checksum, linked payroll run version, and linked payment export record if available.
- Provide authorized download/view of proof metadata and file.
- Audit upload, download, validation failure, and denied attempts.
- Do not claim payment success verification from the bank in MVP.

**Out of MVP:** maker-checker proof verification, OCR bank receipt reading, automatic bank reconciliation, bank payment success validation, multiple proof approval workflow, and evidence redaction workflow.

---

## 9. Accepted Payroll Import Template Decision

**Decision:** For MVP, use the practical payroll/payment/evidence import template rather than a minimal payroll-only template or a full employee/payroll master template.

**Required columns:**

- `employee_identifier`
- `employee_name`
- `ic_or_passport_last4`
- `department`
- `basic_pay`
- `allowances`
- `deductions`
- `overtime_amount`
- `net_pay`
- `bank_name`
- `bank_account`
- `payment_reference`
- `remarks`

**Scope:**

- Include enough fields for payroll approval, payment export, and evidence review.
- Use `ic_or_passport_last4` only for disambiguation; do not request full IC/passport number in the MVP template.
- Defer full employee master import, statutory calculation input depth, and HRIS-style employee profiles.

---

## 10. Accepted Payroll Import Error Handling Decision

**Decision:** For MVP, payroll import uses a two-step preview then confirm flow.

**Flow:**

1. Payroll operator uploads the template file.
2. System validates required columns and row values.
3. System shows an import preview with valid rows, row-level errors, totals, and blocking issues.
4. Payroll operator confirms the import only when there are no blocking errors.
5. System commits payroll rows to the Draft payroll run after confirmation.

**Scope:**

- Missing required columns block confirmation.
- Invalid numeric, identity, duplicate, or bank/payment values block confirmation.
- Canceling the preview commits nothing.
- Partial import is deferred; the MVP prioritizes clear user review and audit-safe commitment.

---

## 11. Accepted Manual Payroll Row Edit Decision

**Decision:** For MVP, manual payroll row add/edit is allowed only while the payroll run is in Draft / Imported.

**Scope:**

- Payroll operators may add or edit rows only before validation/review/approval workflow has started.
- Once the run leaves Draft / Imported, row edits are blocked.
- To correct data later, the run must be returned or reopened to Draft / Imported through the controlled correction path.
- Correction edits are audit-logged with actor, timestamp, changed fields, and reason where applicable.
- Anytime edit with correction versioning is deferred.

---

## 12. Accepted Payroll Summary Totals Decision

**Decision:** For MVP, PAY-007 uses the practical payroll summary totals set rather than minimal totals or full statutory/finance breakdown.

**Required summary totals:**

- employee count
- basic pay total
- allowances total
- overtime total
- gross pay total
- deductions total
- net pay total
- payment-ready total
- rows with issues count

**Scope:**

- Summary totals support payroll operator review, owner confidence, and later payment export reconciliation.
- `payment-ready total` is the amount expected to flow into payment export once the run is approved.
- Full EPF/SOCSO/EIS/PCB/employer contribution/cost-centre breakdown remains deferred unless a pilot requires it.
- Sensitive salary/payment amounts remain subject to role-based masking.

## 9. Accepted Payroll Validation Checklist Decision

**Decision:** For MVP, use practical payroll/payment readiness checks rather than only basic field checks or full statutory/finance validation.

**Mandatory checks:**

- required employee identifier
- required employee name
- duplicate employee identifier
- valid numeric pay fields
- non-negative pay values
- gross pay consistency
- net pay consistency
- missing bank name/account when net pay > 0
- rows missing payment reference when payment export is expected
- rows with blocking issues count must be zero before submission

**Scope boundary:** MVP validation is deterministic payroll/payment readiness validation. Full statutory contribution calculation, PCB/MTD validation, employer contribution validation, cost-centre accounting validation, and bank-specific file validation remain out of MVP unless a pilot explicitly requires them.

## 10. Accepted OT Exception Review Decision

**Decision:** For MVP, use a practical SME OT review set rather than threshold-only checks or a full attendance/payroll-rule engine.

**Initial exception types:**

- excessive OT above configured threshold
- missing OT evidence
- public holiday/rest day mismatch
- employee not OT-eligible
- unusual multiplier
- manual override

**Required review data:** affected employee, exception type, reason, severity, payroll impact amount, required action, reviewer decision, reviewer note, timestamp, and evidence reference if available.

**Scope boundary:** MVP OT exception review is an approval-confidence workflow, not a full attendance/shift-rule engine. Deep attendance matching, leave integration, statutory/legal interpretation, and complex shift policy calculation remain out of MVP unless a pilot explicitly requires them.

## 11. Accepted Owner Approval Submission Gate Decision

**Decision:** For MVP, use a practical approval-readiness gate before a payroll run can be submitted to the SME owner.

**Submission requirements:**

- latest validation report has zero blocking issues
- blocking OT exceptions are resolved or explicitly escalated to SME approval
- required evidence placeholders/checklist items are present or formally waived
- payroll totals snapshot is generated
- sensitive salary/bank access is checked server-side
- submission audit event is recorded

**Scope boundary:** MVP submission does not require full internal reviewer/manager sign-off or a complete generated approval pack before owner submission. Those controls can be added later if pilot customers require maker-checker governance.

## 12. Accepted Owner Approval Strength Decision

**Decision:** For MVP, owner approval uses an explicit approval statement with a locked payroll snapshot rather than a simple approve button or formal digital signing flow.

**Approval requirements:**

- authorized SME approver
- payroll run is Pending SME Approval
- run version is unchanged since submission
- owner sees locked totals, exception, and evidence summary
- owner accepts an explicit approval statement
- approval event records approver, timestamp, run version, totals snapshot, exception summary, evidence readiness, approval statement version, and access context
- status changes to Approved for Payment

**Scope boundary:** MVP owner approval does not require OTP/MFA step-up, e-signature certificate generation, or dual approver countersignature unless pilot customers explicitly require formal signing controls.
