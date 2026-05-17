# Initial Agile Backlog — SME Payroll Approval SaaS

## MVP Scope Assumptions

Accepted MVP baseline:
- Multi-company payroll verification for SME owners and payroll operators.
- Payroll import and manual entry.
- Validation checklist before approval.
- Overtime exception review.
- SME owner approval workflow.
- Payment export/proof capture.
- Audit evidence pack and timeline.
- Journal preview/export.
- RBAC, audit logs, sensitive salary/bank controls.
- Basic dashboards.

Explicitly deferred:
- Full ERP/accounting suite.
- Full statutory payroll calculation engine.
- Full tax/EPF/SOCSO/EIS filing automation.
- Deep accounting reconciliation and bank integration.

## Personas

- **SME Owner / Approver**: legally or operationally responsible for approving payroll and payments.
- **Payroll Operator**: HR/payroll staff, outsourced payroll preparer, or the SME owner themselves when operating solo.
- **Payment / Journal User**: optional role for whoever exports payment files, uploads proof, or exports journal entries. In a small SME this can be the same person as the owner or payroll operator.
- **Auditor / Admin**: user who needs controlled access to evidence and audit history.

**Role note:** These are capabilities, not mandatory separate people. For the first MVP/pilot, one SME owner may perform all roles. The invitation story exists so the product can support delegation once a company has staff, outsourced payroll, or accountant involvement.

## Priorities

- **P0**: Required for MVP approval path.
- **P1**: Important for MVP trust, usability, and operational completeness.
- **P2**: Valuable follow-up after the core approval loop is proven.

## Epic A — Company Workspace and Payroll Run Setup

Technical risk: Tenant isolation must be enforced consistently across all payroll data, evidence, exports, dashboards, and audit logs.

### PAY-001: Create a company workspace

**As a** Service Provider Admin, **I want to** create SME company workspaces, **so that** each client's payroll data, users, exports, and evidence are separated by company.

**Acceptance Criteria**:
- **Given** I am a Service Provider Admin, **When** I create a company with required name and registration identifier, **Then** the SME company workspace is created under my service-provider tenant.
- **Given** I create a company workspace, **When** I assign the SME owner/approver and optional payroll operator/payment roles, **Then** those memberships are scoped to that company.
- **Given** I belong to one company, **When** I view the company switcher or settings, **Then** I only see companies where I have membership.
- **Given** a company exists, **When** another unrelated user requests company data, **Then** no company payroll data is returned.
- **Given** an SME owner without service-provider admin access, **When** they attempt to self-create a company workspace in MVP, **Then** the action is not available or is denied.

**Story Points**: 5  
**Dependencies**: None  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-002: Invite team members and assign payroll roles

**As a** Service Provider Admin or SME owner, **I want to** invite or manually create company users and assign payroll roles, **so that** payroll preparation, payment export, and evidence review can be delegated only when needed.

**Acceptance Criteria**:
- **Given** I am a Service Provider Admin or authorized company owner, **When** I invite a user by email and assign one or more roles, **Then** the invitation is recorded with those roles and company.
- **Given** an invited user accepts the invitation, **When** they sign in, **Then** they can access only the invited company.
- **Given** I am a Service Provider Admin, **When** I manually create a user record for a company, **Then** the user is created in pending activation status with assigned company roles and cannot access the system until activation/password setup is completed.
- **Given** a user needs multiple responsibilities, **When** I assign roles, **Then** the user can hold multiple roles in the same company, such as SME Owner plus Payment/Journal User.
- **Given** I am not an authorized owner or admin, **When** I try to invite, manually create, or assign a user role, **Then** the system denies the action.
- **Given** any invitation, manual creation, activation, or role assignment occurs, **When** the action completes, **Then** an audit event records actor, target user, company, roles, timestamp, and action type.

**Story Points**: 5  
**Dependencies**: PAY-001  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-003: Create a payroll run for a pay period

**As a** payroll operator, **I want to** create a payroll run for a specific pay period, **so that** payroll can be prepared and approved as a controlled batch.

**Acceptance Criteria**:
- **Given** I have payroll operator access, **When** I create a payroll run with month, year, pay date, and company, **Then** the run is created in Draft status.
- **Given** a payroll run already exists for the same company and period, **When** I create another run for that period, **Then** the system prevents duplication unless I choose a permitted correction run type.
- **Given** a run is created, **When** I view the run list, **Then** I can see period, pay date, status, gross total, net total, and last updated time.

**Story Points**: 3  
**Dependencies**: PAY-001  
**Priority**: P0  
**GitHub Issue**: Yes

## Epic B — Payroll Data Capture

Technical risk: Imported spreadsheets may vary across SMEs; the MVP must support a constrained template and clear validation errors instead of attempting universal parsing.

### PAY-004: Download payroll import template

**As a** payroll operator, **I want to** download a standard payroll template, **so that** I can prepare data in the expected format.

**Acceptance Criteria**:
- **Given** I am on a Draft payroll run, **When** I download the import template, **Then** the file contains required columns for employee identifier, employee name, basic pay, allowances, deductions, overtime amount, bank name, bank account, and net pay.
- **Given** the template is downloaded, **When** I open it, **Then** required columns are present with sample guidance rows or column notes.

**Story Points**: 2  
**Dependencies**: PAY-003  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-005: Import payroll rows from template

**As a** payroll operator, **I want to** import payroll rows from the template, **so that** I do not need to enter every employee manually.

**Acceptance Criteria**:
- **Given** I have a Draft payroll run, **When** I upload a valid template file, **Then** payroll rows are created with imported pay, deduction, overtime, bank, and net pay values.
- **Given** the uploaded file is missing required columns, **When** import is attempted, **Then** no payroll rows are saved and the missing columns are shown.
- **Given** some rows contain invalid numeric values, **When** import is attempted, **Then** valid rows are not committed and row-level validation errors are displayed.

**Story Points**: 8  
**Dependencies**: PAY-004  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-006: Manually add or edit a payroll row

**As a** payroll operator, **I want to** add or edit payroll rows manually, **so that** exceptions and small payrolls can be handled without a spreadsheet.

**Acceptance Criteria**:
- **Given** a payroll run is in Draft status, **When** I add a payroll row with required employee and pay fields, **Then** the row appears in the run totals.
- **Given** a payroll row exists in Draft status, **When** I edit pay or bank fields, **Then** the row and run totals are recalculated.
- **Given** a payroll run is submitted for approval, **When** I attempt to edit a payroll row, **Then** the system prevents edits unless the run is returned to Draft.

**Story Points**: 5  
**Dependencies**: PAY-003  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-007: View payroll run totals and employee summary

**As a** payroll operator, **I want to** view payroll totals and employee rows, **so that** I can verify the run before submission.

**Acceptance Criteria**:
- **Given** a payroll run has rows, **When** I open the run summary, **Then** I see employee count, gross total, total deductions, overtime total, and net pay total.
- **Given** a payroll row has missing or invalid fields, **When** I view the summary, **Then** the row is marked as requiring attention.
- **Given** salary-sensitive values are displayed, **When** a user lacks salary access, **Then** amounts are masked or hidden according to their role.

**Story Points**: 5  
**Dependencies**: PAY-005, PAY-006, PAY-016  
**Priority**: P0  
**GitHub Issue**: Yes

## Epic C — Validation Checklist and Exception Review

Technical risk: Validation must be explainable and deterministic; hidden rules or false positives will reduce owner trust.

### PAY-008: Run required payroll validation checklist

**As a** payroll operator, **I want to** run a validation checklist, **so that** payroll issues are found before owner approval.

**Acceptance Criteria**:
- **Given** a payroll run has rows, **When** I run validation, **Then** the system checks required employee fields, non-negative pay values, net pay consistency, duplicate employee identifiers, and missing bank details.
- **Given** validation finds issues, **When** results are shown, **Then** each issue includes row reference, field, severity, and suggested action.
- **Given** validation has blocking errors, **When** I submit for approval, **Then** the system prevents submission.

**Story Points**: 8  
**Dependencies**: PAY-005, PAY-006  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-009: Review overtime exceptions

**As a** SME owner, **I want to** see overtime exceptions separately, **so that** I can focus on unusual overtime before approving payroll.

**Acceptance Criteria**:
- **Given** a payroll run contains overtime amounts, **When** I open the overtime exception view, **Then** rows with overtime above the configured threshold are listed.
- **Given** an overtime exception exists, **When** I mark it reviewed with a note, **Then** the review status, reviewer, note, and timestamp are saved.
- **Given** unreviewed overtime exceptions exist, **When** I attempt to approve the payroll run, **Then** the system warns me and requires explicit confirmation or completion according to company setting.

**Story Points**: 5  
**Dependencies**: PAY-007, PAY-008  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-010: Submit payroll run for owner approval

**As a** payroll operator, **I want to** submit a validated payroll run for approval, **so that** the owner can approve payment.

**Acceptance Criteria**:
- **Given** a Draft payroll run has no blocking validation errors, **When** I submit it, **Then** the run status changes to Pending Approval.
- **Given** the run is Pending Approval, **When** payroll rows are viewed by the operator, **Then** editing controls are disabled.
- **Given** submission occurs, **When** the audit timeline is viewed, **Then** the submitter, timestamp, validation result, and totals snapshot are recorded.

**Story Points**: 3  
**Dependencies**: PAY-008, PAY-018  
**Priority**: P0  
**GitHub Issue**: Yes

## Epic D — Owner Approval Workflow

Technical risk: Approval records must be immutable enough for audit evidence while still allowing a controlled reject-and-correct workflow.

### PAY-011: Owner approves payroll run

**As a** SME owner, **I want to** approve a payroll run, **so that** finance can proceed with payment export.

**Acceptance Criteria**:
- **Given** I am an owner and the payroll run is Pending Approval, **When** I approve the run, **Then** the status changes to Approved and approval timestamp is recorded.
- **Given** I approve the run, **When** the audit timeline is viewed, **Then** the approval event includes approver, timestamp, totals snapshot, and reviewed exception status.
- **Given** I am not authorized to approve, **When** I attempt approval, **Then** the action is denied and no status change occurs.

**Story Points**: 5  
**Dependencies**: PAY-010, PAY-016, PAY-018  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-012: Owner returns payroll run for correction

**As a** SME owner, **I want to** return a payroll run with comments, **so that** payroll staff can correct issues before approval.

**Acceptance Criteria**:
- **Given** a payroll run is Pending Approval, **When** I return it for correction with a required comment, **Then** the status changes to Changes Requested.
- **Given** a run has Changes Requested status, **When** the payroll operator opens it, **Then** the owner comment is visible and editing is allowed.
- **Given** the run is resubmitted, **When** the audit timeline is viewed, **Then** both the return comment and resubmission event are preserved.

**Story Points**: 3  
**Dependencies**: PAY-010, PAY-018  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-013: Show approval readiness summary to owner

**As a** SME owner, **I want to** see a concise readiness summary, **so that** I can make an informed approval decision quickly.

**Acceptance Criteria**:
- **Given** a payroll run is Pending Approval, **When** I open the approval page, **Then** I see employee count, gross total, net total, validation status, overtime exception status, and last submission time.
- **Given** validation warnings exist, **When** I view the readiness summary, **Then** warning counts and links to details are shown.
- **Given** the run was changed after a previous submission, **When** I view the summary, **Then** the latest submission snapshot is shown.

**Story Points**: 5  
**Dependencies**: PAY-007, PAY-008, PAY-009, PAY-010  
**Priority**: P0  
**GitHub Issue**: Yes

## Epic E — Payment Export and Proof

Technical risk: Bank/payment file formats vary; MVP should use a configurable CSV export and proof upload rather than direct bank integration.

### PAY-014: Export approved payment file

**As a** payment/journal user, **I want to** export an approved payroll payment file, **so that** payments can be uploaded to banking software.

**Acceptance Criteria**:
- **Given** a payroll run is Approved, **When** I export payment CSV, **Then** the file includes employee name, bank name, bank account, payment reference, and net pay amount.
- **Given** a payroll run is not Approved, **When** I attempt payment export, **Then** the export is blocked.
- **Given** payment export is generated, **When** the audit timeline is viewed, **Then** exporter, timestamp, and exported total are recorded.
- **Given** the MVP export is generated, **When** I inspect the file, **Then** it uses the generic CSV format rather than a bank-specific integration format.

**Story Points**: 5  
**Dependencies**: PAY-011, PAY-018  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-015: Upload payment proof

**As a** payment/journal user, **I want to** upload payment proof, **so that** the payroll run has evidence that payment was made.

**Acceptance Criteria**:
- **Given** a payroll run is Approved, **When** I upload an accepted proof file, **Then** the proof is attached to the payroll run with uploader and timestamp.
- **Given** a proof file is uploaded, **When** I open the payroll run, **Then** I can view file name, upload time, uploader, and download link if authorized.
- **Given** payment proof is uploaded, **When** the audit timeline is viewed, **Then** the upload event is recorded.

**Story Points**: 5  
**Dependencies**: PAY-011, PAY-018, PAY-016  
**Priority**: P0  
**GitHub Issue**: Yes

## Epic F — Security, RBAC, and Auditability

Technical risk: Sensitive salary and bank data creates high privacy risk; access controls and audit logging must be implemented before exposing real payroll data.

### PAY-016: Enforce role-based access to payroll actions

**As a** SME owner, **I want to** control user permissions by role, **so that** only authorized users can view, prepare, approve, or export payroll.

**Acceptance Criteria**:
- **Given** a user has Owner role, **When** they access payroll features, **Then** they can invite users, view sensitive payroll data, approve runs, export payments, and view audit evidence.
- **Given** a user has Payroll Operator role, **When** they access payroll features, **Then** they can create, import, edit, validate, submit runs, and view salary/payroll details needed for preparation, but cannot approve payment.
- **Given** a user has Finance or Payment/Journal role, **When** they access payroll features, **Then** they can view approved bank/payment data and export/upload proof but cannot view salary details beyond payment totals, change payroll rows, or approve runs.
- **Given** a user lacks a required permission, **When** they attempt that action through UI or API, **Then** the action is denied.

**Story Points**: 8  
**Dependencies**: PAY-001, PAY-002  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-017: Mask sensitive salary and bank data by role

**As a** SME owner, **I want to** restrict visibility of salary and bank details, **so that** sensitive employee data is protected.

**Acceptance Criteria**:
- **Given** a user lacks salary detail permission, **When** they view payroll rows, **Then** salary, deduction, and net pay amounts are masked or hidden.
- **Given** a user lacks bank detail permission, **When** they view payroll rows or payment proof, **Then** bank account values are masked except for permitted last digits.
- **Given** a user has Owner or Payroll Operator role, **When** they view payroll preparation screens, **Then** salary/payroll details are visible according to their company assignment and the reveal is auditable.
- **Given** a user has Payment/Journal role, **When** they prepare payment export or journal workflow after approval, **Then** approved bank/payment details needed for that workflow are visible, while salary preparation details remain masked unless explicitly granted by role.
- **Given** a user exports data, **When** the export is generated, **Then** only fields permitted for that role are included.
- **Given** the application is configured for a tenant, **When** sensitive salary/bank masking is evaluated, **Then** masking cannot be disabled by a customer- or tenant-facing feature flag; access changes require explicit role/permission assignment and audit logging.

**Story Points**: 5  
**Dependencies**: PAY-016, PAY-007, PAY-014  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-018: Record audit events for payroll lifecycle

**As an** auditor, **I want to** see an audit trail of payroll actions, **so that** approval evidence is traceable.

**Acceptance Criteria**:
- **Given** a payroll run is created, imported, edited, validated, submitted, approved, returned, exported, or proof-uploaded, **When** the action completes, **Then** an audit event is recorded with actor, company, run, action, timestamp, and relevant metadata.
- **Given** a user views audit history for a payroll run, **When** they are authorized, **Then** events are shown in chronological order.
- **Given** audit events exist, **When** payroll row values later change, **Then** prior audit metadata snapshots remain unchanged.

**Story Points**: 8  
**Dependencies**: PAY-001, PAY-003, PAY-016  
**Priority**: P0  
**GitHub Issue**: Yes

### PAY-019: Generate audit evidence pack

**As an** SME owner, **I want to** generate an evidence pack for an approved payroll run, **so that** I can answer audit or management questions later.

**Acceptance Criteria**:
- **Given** a payroll run is Approved or Paid Evidence Uploaded, **When** I generate the evidence pack, **Then** the pack includes run summary, approval record, validation results, overtime reviews, export record, payment proof reference, and audit timeline.
- **Given** the evidence pack is generated, **When** the file is created, **Then** it is a ZIP containing a PDF summary plus CSV/JSON evidence attachments.
- **Given** I lack audit evidence permission, **When** I request an evidence pack, **Then** the request is denied.
- **Given** the evidence pack is generated, **When** I download it, **Then** the file name includes company identifier, pay period, and generation timestamp.
- **Given** an evidence pack is generated, **When** the pack record is saved, **Then** the system stores file hash, generator, timestamp, sensitivity markers, and a default retention-until date of 7 years after generation.

**Story Points**: 8  
**Dependencies**: PAY-009, PAY-011, PAY-014, PAY-015, PAY-018  
**Priority**: P1  
**GitHub Issue**: Yes

## Epic G — Journal Preview and Export

Technical risk: Accounting mappings differ by company; MVP should support simple configurable account codes, not deep accounting package integration.

### PAY-020: Configure payroll journal account mappings

**As a** payment/journal user, **I want to** configure basic payroll journal mappings, **so that** journal exports use our account codes.

**Acceptance Criteria**:
- **Given** I have finance or owner access, **When** I configure account codes for salary expense, deductions payable, and cash/bank, **Then** the mappings are saved for the company.
- **Given** required mappings are missing, **When** I preview a journal, **Then** the system identifies missing mappings.
- **Given** I lack finance configuration permission, **When** I try to change mappings, **Then** the action is denied.

**Story Points**: 5  
**Dependencies**: PAY-016  
**Priority**: P1  
**GitHub Issue**: Yes

### PAY-021: Preview payroll journal entries

**As a** payment/journal user, **I want to** preview journal entries for a payroll run, **so that** I can check accounting impact before export.

**Acceptance Criteria**:
- **Given** a payroll run has payroll totals and account mappings, **When** I preview the journal, **Then** debit and credit lines are displayed with account code, description, and amount.
- **Given** debits and credits do not balance, **When** the preview is shown, **Then** the imbalance is highlighted and export is blocked.
- **Given** the payroll run changes, **When** I refresh the preview, **Then** the journal values reflect the latest approved or draft totals according to run status.

**Story Points**: 5  
**Dependencies**: PAY-007, PAY-020  
**Priority**: P1  
**GitHub Issue**: Yes

### PAY-022: Export payroll journal CSV

**As a** payment/journal user, **I want to** export journal entries as CSV, **so that** I can import them into accounting software manually.

**Acceptance Criteria**:
- **Given** a journal preview is balanced, **When** I export journal CSV, **Then** the CSV includes date, account code, description, debit, credit, company, and payroll period.
- **Given** a journal preview has missing mappings or imbalance, **When** I attempt export, **Then** the export is blocked with reasons.
- **Given** journal export is generated, **When** the audit timeline is viewed, **Then** exporter and timestamp are recorded.

**Story Points**: 3  
**Dependencies**: PAY-018, PAY-021  
**Priority**: P1  
**GitHub Issue**: Yes

## Epic H — Dashboards and Operational Visibility

Technical risk: Dashboard counts must reflect permission scope and payroll status transitions accurately.

### PAY-023: Show owner payroll approval dashboard

**As a** SME owner, **I want to** see payroll runs awaiting my approval, **so that** I can act before pay day.

**Acceptance Criteria**:
- **Given** I am a company owner, **When** I open the dashboard, **Then** I see payroll runs grouped by Draft, Pending Approval, Approved, and Paid Evidence Uploaded.
- **Given** one or more runs are Pending Approval, **When** I view the dashboard, **Then** each run shows period, pay date, employee count, net total, and days until pay date.
- **Given** I select a dashboard item, **When** I click it, **Then** I am taken to the payroll run approval or detail page.

**Story Points**: 5  
**Dependencies**: PAY-003, PAY-011, PAY-015, PAY-016  
**Priority**: P1  
**GitHub Issue**: Yes

### PAY-024: Show payroll operator work queue

**As a** payroll operator, **I want to** see payroll runs needing preparation or correction, **so that** I can prioritize my work.

**Acceptance Criteria**:
- **Given** I am a payroll operator, **When** I open my work queue, **Then** I see Draft and Changes Requested payroll runs for companies I can access.
- **Given** a run has validation errors or owner comments, **When** it appears in the queue, **Then** the issue count or latest comment is visible.
- **Given** I select a work queue item, **When** I click it, **Then** I am taken to the run detail page.

**Story Points**: 3  
**Dependencies**: PAY-003, PAY-008, PAY-012, PAY-016  
**Priority**: P1  
**GitHub Issue**: Yes

## Discovery / Spike Items

These are not user stories but should be tracked as planning issues before implementation choices harden.

### SPIKE-001: Confirm payment export format for first target bank(s) — Resolved

**Goal**: Decide whether MVP CSV needs a specific bank-compatible layout or a generic payment list.

**Timebox**: 1–2 days  
**Priority**: P0  
**GitHub Issue**: Yes

**SPIKE-001 decision:** Use a configurable generic CSV payment export for MVP, followed by manual payment proof upload. Do not build direct bank integration or bank-specific formats until after pilot validation.

### SPIKE-002: Confirm evidence pack format and retention expectation — Resolved

**Goal**: Decide whether evidence pack should initially be PDF, ZIP of CSV/PDF files, or a web-only timeline export.

**SPIKE-002 decision:** Use a ZIP evidence pack for MVP, containing a readable PDF summary plus structured CSV/JSON attachments and payment proof/reference files where available. Evidence packs and audit timeline records are retained for 7 years by default. MVP records the retention date and file hash; complex auto-purge, legal hold, and per-company retention engines are deferred.

**Timebox**: 1 day  
**Priority**: P1  
**GitHub Issue**: Yes

### SPIKE-003: Confirm salary/bank masking policy by role — Resolved

**Goal**: Confirm exact masking rules and export permissions for owner, operator, finance, and auditor roles.

**SPIKE-003 decision:** Use Option 3. Owner and Payroll Operator can see salary/payroll details required for preparation. Payment/Journal User can see approved bank/payment details required for payment export and journal workflow. Access is enforced by server-side role/permission policy with audit logging. Do not provide a customer- or tenant-facing feature flag to disable sensitive-field masking; pilot exceptions must be explicit role/permission assignments.

**Timebox**: 1 day  
**Priority**: P0  
**GitHub Issue**: Yes

## Recommended Sprint Plan

### Sprint 0 — Product and Delivery Foundation

Recommended focus:
- Confirm MVP flows, roles, and non-goals.
- Prepare environments, CI/CD, test data strategy, and security baseline.
- Resolve high-risk policy/format spikes before coding locks in assumptions.

Candidate issues:
- SPIKE-001: Confirm payment export format for first target bank(s)
- SPIKE-003: Confirm salary/bank masking policy by role
- PAY-001: Create a company workspace
- PAY-002: Invite team members and assign payroll roles
- PAY-016: Enforce role-based access to payroll actions
- PAY-018: Record audit events for payroll lifecycle

Notes:
- PAY-016 and PAY-018 may start as vertical slices that support only the first implemented actions, then expand with each subsequent story.
- Avoid building payroll calculation or statutory filing infrastructure in Sprint 0.

### Sprint 1 — Core Payroll Preparation and Validation

Recommended focus:
- Let payroll operators create a run, capture rows, validate, and submit.
- Deliver a thin end-to-end path to Pending Approval.

Candidate issues:
- PAY-003: Create a payroll run for a pay period
- PAY-004: Download payroll import template
- PAY-005: Import payroll rows from template
- PAY-006: Manually add or edit a payroll row
- PAY-007: View payroll run totals and employee summary
- PAY-008: Run required payroll validation checklist
- PAY-010: Submit payroll run for owner approval
- PAY-017: Mask sensitive salary and bank data by role

### Sprint 2 — Approval, Payment Evidence, and Finance Outputs

Recommended focus:
- Complete owner approval loop and post-approval operational artifacts.
- Provide enough audit and export capability for a credible MVP demo/pilot.

Candidate issues:
- PAY-009: Review overtime exceptions
- PAY-011: Owner approves payroll run
- PAY-012: Owner returns payroll run for correction
- PAY-013: Show approval readiness summary to owner
- PAY-014: Export approved payment file
- PAY-015: Upload payment proof
- PAY-020: Configure payroll journal account mappings
- PAY-021: Preview payroll journal entries
- PAY-022: Export payroll journal CSV

Recommended post-Sprint-2 / hardening:
- PAY-019: Generate audit evidence pack
- PAY-023: Show owner payroll approval dashboard
- PAY-024: Show payroll operator work queue
- SPIKE-002: Confirm evidence pack format and retention expectation

## GitHub Issue Creation List

Create issues for all P0/P1 user-visible stories and planning spikes. Suggested labels:
- `type:story`
- `type:spike`
- `priority:P0`, `priority:P1`, `priority:P2`
- `epic:workspace`, `epic:data-capture`, `epic:validation`, `epic:approval`, `epic:payment`, `epic:security-audit`, `epic:journal`, `epic:dashboard`
- `mvp`

Suggested first batch:
1. PAY-001 — Create a company workspace — P0 — 5 pts
2. PAY-002 — Invite team members and assign payroll roles — P0 — 5 pts
3. PAY-016 — Enforce role-based access to payroll actions — P0 — 8 pts
4. PAY-018 — Record audit events for payroll lifecycle — P0 — 8 pts
5. SPIKE-001 — Confirm payment export format for first target bank(s) — P0
6. SPIKE-003 — Confirm salary/bank masking policy by role — P0
7. PAY-003 — Create a payroll run for a pay period — P0 — 3 pts
8. PAY-004 — Download payroll import template — P0 — 2 pts
9. PAY-005 — Import payroll rows from template — P0 — 8 pts
10. PAY-006 — Manually add or edit a payroll row — P0 — 5 pts
11. PAY-007 — View payroll run totals and employee summary — P0 — 5 pts
12. PAY-008 — Run required payroll validation checklist — P0 — 8 pts
13. PAY-010 — Submit payroll run for owner approval — P0 — 3 pts
14. PAY-009 — Review overtime exceptions — P0 — 5 pts
15. PAY-011 — Owner approves payroll run — P0 — 5 pts
16. PAY-012 — Owner returns payroll run for correction — P0 — 3 pts
17. PAY-013 — Show approval readiness summary to owner — P0 — 5 pts
18. PAY-014 — Export approved payment file — P0 — 5 pts
19. PAY-015 — Upload payment proof — P0 — 5 pts
20. PAY-017 — Mask sensitive salary and bank data by role — P0 — 5 pts
21. PAY-020 — Configure payroll journal account mappings — P1 — 5 pts
22. PAY-021 — Preview payroll journal entries — P1 — 5 pts
23. PAY-022 — Export payroll journal CSV — P1 — 3 pts
24. PAY-019 — Generate audit evidence pack — P1 — 8 pts
25. PAY-023 — Show owner payroll approval dashboard — P1 — 5 pts
26. PAY-024 — Show payroll operator work queue — P1 — 3 pts
27. SPIKE-002 — Confirm evidence pack format and retention expectation — P1

## Backlog Health Check

- **Independent**: Stories are grouped by epic but can mostly be delivered as vertical slices; dependencies are explicit.
- **Negotiable**: Acceptance criteria define observable behavior without prescribing internal implementation.
- **Valuable**: Each story produces user-visible SaaS behavior or a required planning decision.
- **Estimable**: Each story has clear scope and Fibonacci points.
- **Small**: No user story exceeds 8 points.
- **Testable**: Each story includes Gherkin-style acceptance criteria with observable outcomes.
