# SME Payroll Approval SaaS — MVP PRD

**Status:** MVP baseline  
**Context:** Malaysian SME payroll approval and audit-evidence workflow  
**Product stance:** Focused payroll verification and approval SaaS, not full ERP, full accounting, or full statutory payroll engine.

---

## Problem

SMEs and service providers often manage payroll across spreadsheets, email, messaging apps, payroll tools, bank portals, and shared drives. This creates recurring problems:

- Payroll status is hard to track across multiple companies and pay cycles.
- OT, unpaid leave, claims, allowances, and statutory-sensitive changes are reviewed manually and inconsistently.
- SME owners approve payroll without a clear snapshot of exceptions, changes, and payment implications.
- Payment exports, payment proof, statutory summaries, and payroll evidence are scattered.
- Audit support is slow because source files, approvals, reviewer notes, payment proof, and final payroll outputs are not packaged together.
- Salary, bank, identity, and payroll evidence data require PDPA-aware access controls and audit logging.

The MVP solves the narrow but high-value gap: **multi-company payroll verification, OT exception review, SME owner approval, payment export/proof capture, and audit evidence.**

---

## Target Users

Primary target: Malaysian SME payroll operators and service providers that manage payroll for multiple small companies.

- **Service-provider / payroll bureau operator:** Needs one control tower for many SME payroll runs.
- **SME owner / director / authorized approver:** Needs a simple approval view before payment.
- **Payroll processor:** Imports or prepares payroll, resolves validation issues, and requests review.
- **Payroll reviewer / manager:** Checks payroll changes, OT exceptions, evidence completeness, and approval readiness.
- **Accountant / auditor / finance reviewer:** Needs payroll summaries, journal export, payment proof, and audit evidence pack.

Secondary target: individual SMEs with enough payroll complexity to need approval discipline, but not a full ERP rollout.

---

## Goals / Non-Goals

### Goals

- Provide a multi-company workspace for tracking payroll readiness, blockers, exceptions, approvals, exports, and evidence.
- Support payroll import or manual entry sufficient for payroll verification and approval.
- Highlight OT and payroll exceptions before SME approval.
- Enforce SME owner approval before payment export by default.
- Capture payment export records and payment proof. MVP payment export is a controlled generic CSV for manual bank upload (`DEC-2026-05-17-2313-controlled-generic-payment-csv`); bank-specific formats and direct bank integration are deferred.
- Generate a payroll audit evidence pack per payroll run.
- Maintain a structured append-only audit timeline for payroll lifecycle, sensitive access, export/download, and denied sensitive/lifecycle actions; audit logs must not store raw full salary or bank values (`DEC-2026-05-17-2341-structured-append-only-audit-timeline`).
- Protect salary, bank, identity, and evidence data with role-based access and audit logging.
- Enforce sensitive salary/bank visibility as a mandatory default-masked server-side authorization policy, not a tenant/customer feature flag (`DEC-2026-05-17-2337-strict-sensitive-data-masking`).
- Support accountant-ready payroll journal preview/export without becoming the accounting ledger.

### Non-Goals

- Full ERP suite.
- Full HRIS, leave management, attendance, scheduling, or claims workflow.
- Full statutory payroll engine for EPF/KWSP, SOCSO/PERKESO, EIS, PCB/MTD on day one unless pilot scope requires it.
- Direct statutory portal submission.
- Full accounting system, GL, AP, AR, bank reconciliation, financial statements, or month-end close engine.
- Native bank reconciliation.
- Deep third-party integrations beyond import/export in MVP.
- Customer- or tenant-configurable feature flag to disable sensitive salary/bank masking.
- AI training on customer payroll data.
- Replacing human approval, accountant judgment, or statutory/legal responsibility.

---

## MVP Scope

### In Scope

1. **Multi-company workspace**
   - Provider tenant and multiple SME company workspaces.
   - MVP company workspaces are created by Service Provider Admin users; SME owner self-service registration is deferred.
   - Company/payroll-period dashboard with status, blockers, pending approver, exception count, and evidence completeness.

2. **Payroll run setup and lifecycle**
   - Create/import payroll run by company and pay period; MVP displays a simple month/year label while storing explicit `period_start`, `period_end`, and `pay_date`.
   - Support baseline lifecycle: Draft / Imported → Validation Issues → Ready for Review → OT / Exception Review → Pending SME Approval → Approved for Payment → Payment Exported → Payment Proof Uploaded → Closed / Archived.
   - Controlled Reopened / Correction Required path with mandatory reason and audit trail.
   - Owner return-for-correction uses the accepted structured correction rule (`DEC-2026-05-17-2258-owner-return-structured-correction`): only an authorized SME approver can return a Pending SME Approval run; return requires one reason category plus a required comment; return invalidates the submitted approval snapshot; correction creates a new payroll run version that must be revalidated/reviewed and re-submitted before approval.
   - Owner approval page uses the accepted decision-ready readiness summary (`DEC-2026-05-17-2306-owner-readiness-summary`): employee count, gross total, net total, payment-ready total, validation status, blocking issue count, warning count, OT/exception status, evidence readiness, submitted by, submission time, run version, stale snapshot blocking, and Approve / Return for correction / View details actions.
   - Payment export uses the accepted controlled generic CSV rule (`DEC-2026-05-17-2313-controlled-generic-payment-csv`): export only from Approved for Payment run version; require payment export permission; include employee/payment fields; record checksum, totals, row count, format version, exporter, and timestamp; audit denied export attempts.
   - Payment proof upload uses the accepted controlled evidence-capture rule (`DEC-2026-05-17-2317-controlled-payment-proof-upload`): require payment/proof permission, proof file, proof type, payment date, payment reference/note, file validation, checksum, linked payroll run version, linked export record if available, and audit logging; it does not verify bank-side payment success.

3. **Payroll data import / manual entry**
   - Employee master import or manual entry.
   - MVP payroll import template uses practical approval/payment/evidence columns: `employee_identifier`, `employee_name`, `ic_or_passport_last4`, `department`, `basic_pay`, `allowances`, `deductions`, `overtime_amount`, `net_pay`, `bank_name`, `bank_account`, `payment_reference`, and `remarks`.
   - Import flow uses a two-step preview then confirm pattern: upload file → validate and show valid/error rows → commit rows only after user confirmation.
   - Manual payroll row add/edit is allowed only while the payroll run is in Draft / Imported; after validation/review/approval starts, edits require returning/reopening the run to Draft / Imported with an audit reason.
   - Payroll components for salary, allowance, deduction, unpaid leave, OT, bonus/commission, and approved claims totals.
   - Payroll run summary shows employee count, basic pay total, allowances total, overtime total, gross pay total, deductions total, net pay total, payment-ready total, and rows with issues count.
   - Validation report uses the accepted practical payroll/payment readiness checklist: required employee identifier, required employee name, duplicate employee identifier, valid numeric pay fields, non-negative pay values, gross pay consistency, net pay consistency, missing bank name/account when net pay > 0, rows missing payment reference when payment export is expected, and zero blocking issue count before submission.

4. **Verification checklist**
   - Configurable payroll readiness checklist.
   - Mandatory baseline: payroll snapshot, salary register, change log, statutory contribution summary or imported values, approval record, payment export/proof placeholder.
   - Conditional evidence for OT, attendance, claims, bonus/commission, joiner/resignation, or material changes.

5. **OT exception review**
   - OT exception queue with affected employee, exception type, reason, severity, payroll impact, required action, and evidence reference if available.
   - Initial exception types: excessive OT above configured threshold, missing OT evidence, public holiday/rest day mismatch, employee not OT-eligible, unusual multiplier, manual override.
   - Reviewer decision: accept, reject, adjust, request evidence, or escalate to SME approval.
   - Blocking OT exceptions prevent SME approval submission unless explicitly escalated to SME approval with reviewer note and audit trail.
   - MVP does not implement a full attendance, shift-rule, leave, or statutory/legal interpretation engine.

6. **SME owner approval**
   - Approval request with locked payroll snapshot, exception summary, net payment total, variance summary, and comments.
   - Submission to owner requires practical approval-readiness: latest validation report has zero blocking issues, blocking OT exceptions are resolved or explicitly escalated, required pre-approval evidence placeholders/checklist items are present or formally waived, payroll totals snapshot is generated, sensitive salary/bank access is checked server-side, and submission audit event is recorded.
   - Owner approval requires an authorized SME approver, unchanged run version since submission, visible locked totals/exception/evidence summary, and explicit acceptance of an approval statement.
   - Approval event records approver, timestamp, run version, totals snapshot, exception summary, evidence readiness, approval statement version, and access context.
   - Approve/reject/request changes.
   - Recalculation after approval invalidates approval or requires re-approval.
   - Payment export blocked until approval unless logged override permission is used.

7. **Payment export and proof**
   - Salary payment listing/export from approved payroll.
   - Optional pilot-specific bank file export only if format is confirmed.
   - Upload payment proof linked to payroll run/payment batch.
   - Track exported by/at, approved snapshot used, file/version, and proof status.

8. **Audit evidence pack and audit timeline**
   - Generate a ZIP evidence pack containing a human-readable PDF summary plus structured CSV/JSON attachments.
   - Include payroll summary, salary register according to permission, validation results, OT/exception resolutions, approval record, source file references, payment export/proof, statutory summaries/proof references, journal preview/export, reviewer notes, document index, and audit timeline.
   - Maintain structured append-only audit events for company/user/role actions, payroll lifecycle actions, imports, edits, validations, exception decisions, approvals/returns, payment exports, payment proof uploads, evidence pack generation/download, sensitive data reveal/export/download, and denied sensitive/lifecycle attempts.
   - Audit events record actor, company, payroll run, action, timestamp, run version, from/to status where applicable, source IP/user agent where available, and safe metadata/snapshot references.
   - Audit events must not store raw full salary, bank account, identity, or unrestricted evidence contents; store masked values, references, checksums, counts, reason codes, and version IDs instead.
   - Evidence files are versioned, hashed, permissioned, retained for 7 years by default, and event-logged for generation/download.

9. **Payroll journal preview/export**
   - Limited accounting export mapping, not full chart of accounts.
   - Preview payroll journal lines for salary expense, allowances, OT, employer statutory costs, deductions/payables, net salary payable, statutory payable, and optional cost centre/department.
   - Export CSV/XLSX/PDF for accountant handoff.

10. **RBAC, tenancy, and PDPA-aware controls**
    - MVP RBAC uses fixed role bundles with an explicit permission matrix: Owner / Approver, Payroll Operator, Payment / Journal User, Auditor / Read-only Reviewer, and Platform Admin.
    - Permissions are represented as stable permission keys so the model can evolve into tenant-defined custom roles later without rewriting authorization logic (`DEC-2026-05-17-2331-fixed-mvp-rbac-permission-matrix`).
    - MVP supports both email invitations and Service Provider Admin-created user records for controlled onboarding; manual users must be scoped to company roles and require account activation/password setup before access.
    - Users restricted by tenant, assigned company, role, explicit permission key, resource state, and sensitive-field policy.
    - Authorization must be enforced server-side for UI, API, exports, evidence access, approval actions, and sensitive-field reveal; UI hiding is convenience only.
    - Salary, deduction, net pay, bank account, identity, payment proof, and evidence file details are default-masked unless an explicit permission grants reveal for the current company, workflow state, and use case.
    - Owner / Approver and Payroll Operator may see salary data for preparation/approval; Payment / Journal User may see approved bank/payment data only for export/proof/journal work; Auditor / Read-only Reviewer sees masked data unless explicitly granted.
    - Audit logs for sensitive reveal/export/download/edit/approval/evidence actions and denied attempts.
    - MFA required for privileged roles and strongly encouraged for all approvers.

### Explicitly Out of MVP

- Full payroll statutory calculation engine unless narrowed for a pilot.
- Full GL/accounting ledger, AP/AR, bank reconciliation, financial statements.
- Attendance device integrations, HRIS integrations, accounting API integrations, banking API integrations.
- Claims approval workflow, leave workflow, scheduling, employee self-service portal.
- Multi-level enterprise approval matrix, e-signature workflow, auditor portal.
- Line-by-line approval annotations, chat thread per payroll correction, multi-round dispute workflow, formal rejection letter/PDF, and owner-side payroll editing.
- Full approval pack PDF on the approval page, full employee-by-employee drilldown on the same page, variance analytics dashboard, evidence file previewer, and AI risk scoring.
- Native mobile apps.
- AI automation or AI analysis of payroll data.
- Maybank/CIMB/bank-specific payment file formats, direct bank API, payment status reconciliation, multi-bank batch splitting, automatic payment release, and encryption/signing of bank files.
- Maker-checker proof verification, OCR bank receipt reading, automatic bank reconciliation, bank payment success validation, multiple proof approval workflow, and evidence redaction workflow.
- Full SIEM integration, tamper-evident blockchain-style audit chain, advanced audit search/reporting, legal hold workflow, and audit event redaction workflow.
- Tenant-configurable custom role builder, custom permission editor, role templates marketplace, per-field custom permission editor, and per-tenant approval matrix builder. These are Phase 2+ evolution paths; MVP must only preserve the permission-key foundation.

---

## Personas

### 1. Payroll Processor

- **Goal:** Prepare payroll runs quickly and avoid missing inputs.
- **Pain:** Chases files, reconciles spreadsheets, and manually tracks changes.
- **MVP needs:** Import data, see validation issues, upload evidence, resolve exceptions, request review.

### 2. Payroll Reviewer / Manager

- **Goal:** Ensure payroll is correct before sending to SME owner.
- **Pain:** High-risk items are buried in spreadsheets and email threads.
- **MVP needs:** Exception queue, checklist, variance view, audit trail, approval readiness status.

### 3. SME Owner / Authorized Approver

- **Goal:** Approve payroll confidently before money moves.
- **Pain:** Receives unclear summaries and must trust back-and-forth messages.
- **MVP needs:** Simple approval snapshot, key totals, exception summary, variance vs previous run, reject/request-change flow.

### 4. Service-Provider Admin

- **Goal:** Manage many company payroll runs with clear accountability.
- **Pain:** No portfolio view of who is blocked, overdue, approved, or missing evidence.
- **MVP needs:** Multi-company dashboard, role assignment, company access boundaries, status reporting.

### 5. Accountant / Auditor / Finance Reviewer

- **Goal:** Retrieve complete payroll support without reconstructing history.
- **Pain:** Evidence is scattered across folders, emails, bank portals, and spreadsheets.
- **MVP needs:** Audit pack, payment proof, journal export, source references, immutable timeline.

---

## Core Workflows

### 1. Monthly payroll preparation

1. Processor creates payroll run for company and period.
2. Processor imports employee/payroll data or enters it manually.
3. System validates required employee identifiers/names, duplicate identifiers, numeric/non-negative pay values, gross/net pay consistency, payment readiness fields, and evidence checklist.
4. Payroll run moves to Validation Issues or Ready for Review.

### 2. OT and exception review

1. System flags OT/payroll exceptions.
2. Reviewer opens exception queue and reviews severity, evidence, and payroll impact.
3. Reviewer accepts, adjusts, rejects, requests evidence, or escalates.
4. All decisions and notes are written to audit timeline.
5. Payroll moves to Pending SME Approval only after validation, exception, evidence, totals snapshot, authorization, and submission audit checks pass.

### 3. SME owner approval

1. System sends approval request to SME approver.
2. Approver reviews locked payroll snapshot, total payment amount, variance, exceptions, evidence readiness, and notes.
3. Approver accepts an explicit approval statement and approves, or rejects/requests changes.
4. Approval locks the approved run version and approval statement version; later recalculation requires re-submission and re-approval.

### 4. Payment export and proof capture

1. Approved payroll becomes eligible for payment export.
2. Processor exports salary payment listing or configured payment file.
3. Processor uploads payment proof after payment is completed externally.
4. System links export and proof to approved payroll snapshot.

### 5. Audit pack generation

1. User generates audit pack for a payroll run.
2. System packages payroll summaries, source files, exception decisions, approval history, exports, payment proof, journal preview/export, and audit timeline.
3. Evidence pack is permissioned and reproducible for the payroll run version.

### 6. Reopen / correction path

1. Authorized user requests reopen with mandatory reason.
2. System records reopen event and marks prior approval/export state as superseded where applicable.
3. Corrections are made as new events, not silent overwrites.
4. Corrected payroll must pass review and approval again before payment/export closure.

---

## Success Metrics

### Pilot success metrics

- At least 3–5 SME companies processed through full payroll approval cycle in pilot.
- 80%+ payroll runs reach approval without offline reconstruction of evidence.
- 90%+ approved payroll runs have payment export/proof attached before closure.
- 100% of approved payroll runs have an audit timeline and evidence checklist status.
- SME approval turnaround reduced by at least 30% from baseline.
- Exception resolution time reduced by at least 30% from baseline.
- Payroll rework/error incidents reduced versus baseline manual workflow.

### Product health metrics

- Active companies processed per month.
- Payroll runs created, approved, exported, and closed.
- Median time from Draft/Imported to Approved for Payment.
- Median time from Pending SME Approval to Approved/Rejected.
- Exceptions per payroll run and resolution rate.
- Evidence pack generation count.
- Sensitive data access/export events reviewed with no unauthorized-access incidents.

---

## Risks / Assumptions

### Assumptions

- Initial customers accept import/export-first workflow instead of deep integrations.
- Service providers or SMEs already have payroll data sources and can provide sample Excel/CSV formats.
- Human review and SME approval remain final authority for payroll correctness.
- MVP can use statutory summaries/readiness checks and proof references without claiming full statutory engine responsibility.
- Payment execution remains outside the product; MVP captures export/proof rather than moving money.
- Malaysian SME users value audit evidence and approval discipline enough to adopt a focused SaaS.

### Risks

- **Scope creep:** Users may ask for full ERP, HRIS, statutory engine, accounting, banking, and claims modules.
- **Compliance liability:** Payroll/statutory calculations may be perceived as guaranteed even when framed as verification.
- **PDPA exposure:** Salary, bank, identity, and payroll evidence data require strong access controls, audit logs, retention, and deletion policies.
- **Adoption friction:** SMEs may resist structured evidence and approval steps if the workflow is too strict.
- **Integration expectations:** Users may expect bank, accounting, HR, or statutory portal integrations earlier than planned.
- **Evidence trust:** Audit pack value depends on versioning, hashing, access logs, and reproducible snapshots.
- **Buyer ambiguity:** Payroll bureau, accounting firm, CoSec firm, and direct SME buyer needs may diverge.

---

## Release Gates

### Gate 1 — PRD and scope baseline

Release planning may proceed when:

- MVP wedge is accepted as payroll verification/approval/evidence, not ERP/accounting/statutory engine.
- In-scope and out-of-scope boundaries are documented.
- Target buyer/user model is accepted for initial pilot.
- Pilot success metrics are agreed.

### Gate 2 — Prototype readiness

Prototype is ready for pilot-user walkthrough when:

- Multi-company dashboard flow is clickable or demonstrable.
- Payroll run lifecycle and state transitions are represented.
- OT exception review, SME approval, payment proof, and audit pack flows are covered.
- Role/access assumptions are visible in the prototype.
- Sample payroll import and evidence examples are available.

### Gate 3 — MVP build readiness

Development may start when:

- Payroll run state machine is finalized.
- Core data model covers tenant, company, payroll run, employee, component, exception, approval, payment export, evidence document, audit event, and journal export mapping.
- RBAC and sensitive-field rules are specified.
- Import/export formats for pilot are confirmed.
- PDPA-aware data handling, retention, and audit logging requirements are accepted.

### Gate 4 — Pilot launch readiness

Pilot can launch when:

- At least one complete payroll run can move from import/manual entry to closed/archive.
- Approval-before-payment-export works by default.
- Recalculation after approval requires re-approval.
- Payment export/proof can be linked to the approved payroll snapshot.
- Audit pack generation works for a closed payroll run.
- Sensitive data access/export events are audit-logged.
- Provider and SME user roles have been tested against company access boundaries.

### Gate 5 — MVP success / continue decision

Proceed beyond MVP when:

- Pilot success metrics show reduced approval turnaround, exception resolution time, or payroll rework.
- Pilot users complete at least one full monthly payroll cycle without reverting to offline evidence reconstruction.
- Users identify the product as valuable for payroll control and audit readiness, not merely another file repository.
- Top Phase 2 requests are validated and prioritized against the MVP boundary.
