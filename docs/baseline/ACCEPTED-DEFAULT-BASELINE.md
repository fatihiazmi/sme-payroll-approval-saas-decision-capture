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
- Payroll evidence pack and audit timeline: ZIP evidence pack with PDF summary plus CSV/JSON attachments, retained for 7 years by default
- Payroll journal preview/export, not a full accounting ledger
- Role-based access control with assignment boundaries
- Audit logging for sensitive payroll actions
- Sensitive field controls for salary and bank details
- Basic dashboards for payroll readiness and approval status

### Deferred from MVP

- Full ERP suite
- Full statutory payroll calculation engine for EPF/SOCSO/EIS/PCB unless pilot requires it
- Full GL/AP/AR/accounting modules
- Bank reconciliation
- Invoice processing
- Advanced finance automation
- AI training on customer payroll data
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
- Customer data must not be used to train AI models by default.

---

## 4. Accepted Finance/Audit Defaults

- The product is an evidence and approval system first, not accounting software.
- MVP should produce a payroll audit pack.
- MVP should support payroll journal preview/export.
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

**Out of MVP:** full approval pack PDF on the approval page, full employee-by-employee drilldown on the same page, variance analytics dashboard, evidence file previewer, owner-side editing, and AI risk scoring.

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

**Decision:** For MVP, export a configurable generic CSV payment file after payroll approval, then require manual payment proof upload.

**Scope:**

- Include employee name, bank name, bank account number, net pay amount, and payment reference.
- Block export until the payroll run is approved.
- Record exporter, timestamp, row count, and exported total in the audit timeline.
- Do not build direct bank integration or bank-specific file formats in MVP.
- Expand to specific Malaysian bank formats after pilot validation.

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
