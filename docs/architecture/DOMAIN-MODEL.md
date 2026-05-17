# Domain Model — SME Payroll Approval SaaS

**Status:** Draft architecture artifact  
**Baseline source:** `docs/baseline/ACCEPTED-DEFAULT-BASELINE.md`  
**Scope:** MVP domain model for multi-company payroll verification, exception review, SME approval, payment export/proof, and audit evidence.

---

## 1. Domain Framing

SME Payroll Approval SaaS is a **payroll verification, approval, evidence, and export workflow** for service providers and SMEs. The MVP does **not** own the full ERP, accounting ledger, bank reconciliation, or statutory payroll engine.

The core product value is controlling the monthly payroll approval lifecycle across many SME companies:

1. import or prepare draft payroll;
2. validate payroll and source evidence;
3. review OT/exceptions;
4. obtain SME approval;
5. export payment/accounting artifacts;
6. retain proof and an immutable audit timeline.

---

## 2. Strategic DDD Overview

### Core Domain

#### Payroll Run Approval Workflow

This is the differentiating domain. It coordinates payroll readiness, exceptions, internal review, SME approval, export, payment proof, reopening, and closure across multiple companies.

Why core:

- It is the product wedge and primary user workflow.
- It creates portfolio-level control for service providers.
- It reduces payroll approval risk and missing evidence.
- It defines the language, state machine, and audit trail that competitors would need to replicate.

### Supporting Subdomains

#### Payroll Import and Validation

Responsible for receiving imported/manual payroll data, normalizing it into payroll run records, detecting issues, and generating validation findings. Important, but not the final competitive core unless the product later becomes a statutory payroll engine.

#### OT and Exception Review

Responsible for overtime, unusual payroll components, missing evidence, variance checks, and reviewer resolution. High-value supporting subdomain closely tied to the core workflow.

#### Evidence and Audit Pack

Responsible for evidence checklist rules, document attachments, file hashes, event timeline, and reproducible audit pack generation.

#### Payment and Export Handoff

Responsible for payment listing, payment export file records, payroll journal preview/export, statutory summary export artifacts, and payment proof upload. It is a handoff model, not a banking/payment execution model.

#### Tenancy, Assignment, and Authorization

Responsible for platform account, service-provider tenant, SME company workspace, role, assignment, and permission policies. It protects all domain operations.

#### Notification and Task Follow-Up

Responsible for reminders, blocked status alerts, approval requests, and evidence request notifications.

### Generic Subdomains

These should be bought, reused, or kept thin behind adapters:

- Authentication, MFA, password reset, session management.
- Email/SMS/WhatsApp delivery.
- Object storage and antivirus scanning.
- PDF/XLSX/CSV generation libraries.
- Payment gateway or bank file format libraries, if added later.
- Generic analytics/telemetry.
- Support ticketing and billing/subscription management.

---

## 3. Bounded Contexts

### 3.1 Tenant and Access Context

**Purpose:** Defines who can operate on which SME company and which sensitive payroll fields/actions they may access.

**Primary language:** Platform Account, Service Provider Tenant, SME Company Workspace, Membership, Assignment, Role, Permission Policy, Sensitive Field Access, Break-Glass Access.

**Key model:**

- `ServiceProviderTenant`
- `SmeCompany`
- `SmeServiceRelationship`
- `Membership`
- `CompanyAssignment`
- `RoleGrant`
- `PermissionPolicy`

**Integration style:** Open host service for authorization decisions. Other contexts ask: “Can actor X perform action Y on resource Z within company C?”

### 3.2 Payroll Workflow Context — Core

**Purpose:** Owns the payroll run lifecycle and enforces the approval state machine.

**Primary language:** Payroll Run, Payroll Period, Draft, Validation Issues, Ready for Review, Exception Review, Pending SME Approval, Approved for Payment, Payment Exported, Payment Proof Uploaded, Closed, Reopened, Correction Required.

**Key model:**

- `PayrollRun` aggregate root
- `PayrollRunLine`
- `ApprovalRequest`
- `ReviewDecision`
- `WorkflowTransition`
- `PayrollRunStatus`

**Integration style:** Publishes domain events consumed by Evidence, Export, Notification, and Portfolio Dashboard contexts.

### 3.3 Payroll Import and Validation Context

**Purpose:** Converts source input into normalized payroll run data and findings.

**Primary language:** Import Batch, Source File, Import Mapping, Validation Finding, Blocking Issue, Warning, Payroll Component, Employee Payroll Snapshot.

**Key model:**

- `ImportBatch`
- `SourceFile`
- `ImportMapping`
- `ImportTemplate`
- `TemplateColumn`
- `ValidationReport`
- `ValidationFinding`

**MVP import template columns:** `employee_identifier`, `employee_name`, `ic_or_passport_last4`, `department`, `basic_pay`, `allowances`, `deductions`, `overtime_amount`, `net_pay`, `bank_name`, `bank_account`, `payment_reference`, `remarks`.

**Integration style:** Anti-corruption layer for spreadsheet formats and future external payroll/HR systems. Emits validation results into Payroll Workflow.

### 3.4 Exception Review Context

**Purpose:** Manages OT, variance, missing evidence, unsupported employee/category, and unusual component exceptions.

**Primary language:** Exception Case, OT Exception, Severity, Resolution, Reviewer Note, Required Action, Exception Queue.

**Key model:**

- `ExceptionCase`
- `ExceptionRule`
- `ExceptionResolution`
- `OvertimeEvidenceRef`

**Integration style:** Customer/supplier relationship with Payroll Workflow; Payroll Workflow cannot advance beyond exception review while blocking exception cases remain unresolved.

### 3.5 Evidence and Audit Context

**Purpose:** Maintains immutable evidence timeline and generates reproducible audit packs.

**Primary language:** Evidence Item, Evidence Checklist, Audit Timeline, Audit Pack, Document Index, File Hash, Snapshot, Proof.

**Key model:**

- `EvidenceChecklist`
- `EvidenceItem`
- `AuditTimelineEntry`
- `AuditPack`
- `DocumentReference`

**Integration style:** Subscribes to domain events from Payroll Workflow, Import, Exception Review, and Export. It should append evidence/timeline facts, not mutate workflow state directly.

### 3.6 Export and Payment Handoff Context

**Purpose:** Produces export artifacts after approval and tracks proof upload.

**Primary language:** Payment Export, Payment Listing, Export Artifact, Statutory Summary, Payroll Journal Export, Export Template, Payment Proof.

**Key model:**

- `PaymentExport`
- `ExportArtifact`
- `PayrollJournalPreview`
- `PaymentProof`

**Integration style:** Consumes `PayrollRunApprovedForPayment`; emits `PaymentExported`, `PaymentProofUploaded`.

### 3.7 Portfolio Operations Context

**Purpose:** Gives service-provider users a multi-company control tower.

**Primary language:** Portfolio Status, Blocker, Pending Approver, Payroll Month, Overdue, Risk, Readiness.

**Key model:** Read models/projections, not rich write aggregates:

- `CompanyPayrollStatusProjection`
- `PortfolioDashboardProjection`
- `OverdueApprovalProjection`

**Integration style:** CQRS-style projection from workflow and exception events.

---

## 4. Context Map

```mermaid
flowchart LR
  TA[Tenant & Access Context]
  PW[Payroll Workflow Context\nCore Domain]
  IV[Import & Validation Context]
  EX[Exception Review Context]
  EA[Evidence & Audit Context]
  EP[Export & Payment Handoff Context]
  PO[Portfolio Operations Context]
  AU[Auth/MFA Provider\nGeneric]
  OS[Object Storage\nGeneric]
  NT[Notification Provider\nGeneric]

  AU -->|Conformist / adapter| TA
  TA -->|Authorization service| PW
  TA -->|Authorization service| IV
  TA -->|Authorization service| EX
  TA -->|Authorization service| EA
  IV -->|ValidationReportReady| PW
  EX -->|ExceptionsResolved / BlockingExceptionRaised| PW
  PW -->|Domain events| EA
  PW -->|PayrollRunApprovedForPayment| EP
  PW -->|Status events| PO
  EX -->|Exception events| PO
  EP -->|Export/proof events| EA
  EP -->|Export/proof events| PO
  OS -->|Document storage adapter| EA
  PW -->|Approval/reminder events| NT
```

---

## 5. Ubiquitous Language

Terms are scoped to the bounded context where they are used. Avoid leaking external spreadsheet, payroll software, bank, or accounting-system terms into the core model without translation.

### Tenant and Access Language

- **Platform Account:** The SaaS operator boundary.
- **Service Provider Tenant:** The commercial workspace for a firm managing payroll/admin work for SME companies. Use service-provider-neutral language; do not hard-code “CoSec” into the core domain.
- **SME Company Workspace:** The operational and data ownership boundary for one SME company.
- **SME Service Relationship:** The active or historical relationship between a service provider and SME company.
- **Membership:** A user’s affiliation with a tenant or SME company.
- **Assignment:** Explicit permission for a service-provider user to work on an SME company.
- **Sensitive Field Access:** Permission to view or export salary, bank, identity, and statutory identifiers.
- **Break-Glass Access:** Time-limited support access requiring reason, ticket, expiry, and audit trail.

### Payroll Workflow Language

- **Payroll Run:** One payroll cycle for one SME company and one payroll period. It is the aggregate that moves through the approval lifecycle.
- **Payroll Period:** The user-facing month/year label plus explicit `period_start`, `period_end`, and `pay_date` covered by a payroll run.
- **Draft / Imported:** Payroll data exists but is not yet clean enough for review.
- **Validation Issues:** The run has blocking validation findings requiring correction.
- **Ready for Review:** Required validation checks have passed; reviewers can assess exceptions.
- **OT / Exception Review:** Reviewers resolve OT, variance, unsupported category, missing evidence, or unusual component exceptions.
- **Pending SME Approval:** Payroll has passed provider-side review and awaits authorized SME approval.
- **Approved for Payment:** SME has approved payroll for payment/export. Payroll amounts become locked except via controlled reopen/correction.
- **Payment Exported:** Payment listing/file/report has been generated after approval.
- **Payment Proof Uploaded:** Proof of salary/statutory payment or placeholder evidence has been attached.
- **Closed / Archived:** Payroll run is complete, evidence pack is retained, and routine changes are blocked.
- **Reopened / Correction Required:** Controlled exception state for correcting an approved/exported/closed run.

### Import and Validation Language

- **Import Batch:** A single import attempt from file/manual entry/API.
- **Source File:** Original uploaded input file retained as evidence.
- **Import Mapping:** Column-to-domain mapping used to interpret a source file.
- **Validation Finding:** A detected issue, warning, or informational message.
- **Blocking Issue:** A validation finding that prevents state progression.
- **Payroll Component:** A classified pay/deduction item such as salary, allowance, overtime, bonus, unpaid leave, claim reimbursement, employee deduction, employer contribution.
- **Employee Payroll Snapshot:** Payroll-relevant employee attributes captured for this run, including category and statutory treatment flags.

### Exception Review Language

- **Exception Case:** A review item requiring human action before payroll can proceed.
- **OT Exception:** Exception related to overtime eligibility, hours, multiplier, missing evidence, or day type.
- **Severity:** Risk level used to prioritize review: info, warning, blocking, critical.
- **Resolution:** Reviewer decision and reason that closes or escalates an exception.
- **Reviewer Note:** Human explanation retained in the audit timeline.

### Evidence and Audit Language

- **Evidence Checklist:** Required and conditional evidence rules for a payroll run.
- **Evidence Item:** Document, generated artifact, comment, approval record, or external proof attached to a payroll run.
- **Audit Timeline:** Append-only sequence of domain facts and sensitive actions.
- **Audit Pack:** Reproducible export package for a payroll run, including documents, approvals, exports, and timeline.
- **Document Reference:** Metadata and storage reference for a file, including hash/checksum and classification.
- **Calculation Snapshot:** Frozen summary of draft/final payroll values and rules/mappings used.

### Export and Payment Language

- **Payment Export:** Generated salary payment listing, bank file, or payment report artifact.
- **Export Artifact:** Any generated file/report retained as evidence.
- **Payroll Journal Preview:** Accountant-ready journal view derived from payroll totals and mapping rules; not a ledger posting.
- **Statutory Summary:** Export-ready summary of statutory amounts. MVP may verify/report rather than guarantee final statutory submission.
- **Payment Proof:** Uploaded evidence that salary/statutory payment has been made or scheduled.

---

## 6. Tactical Domain Model

### 6.1 Aggregate: PayrollRun — Core Aggregate Root

**Identity:** `PayrollRunId`

**Purpose:** Enforces lifecycle, locking, approval, and correction invariants for one SME company’s payroll period.

**Entities inside aggregate:**

- `PayrollRunLine` — employee-level payroll summary for the run.
- `PayrollComponentLine` — component-level amounts within an employee line.
- `ApprovalRequest` — request sent to SME approver.
- `ReviewDecision` — provider/SME decision record.
- `WorkflowTransition` — state transition record, with actor/reason.

**Value objects:**

- `PayrollRunId`
- `SmeCompanyId`
- `ServiceProviderTenantId`
- `PayrollPeriod`
- `PayDate`
- `PayrollRunStatus`
- `Money`
- `CurrencyCode`
- `PayrollComponentCode`
- `StatutoryTreatment`
- `EmployeeSnapshotRef`
- `ActorRef`
- `ApprovalDecision`
- `TransitionReason`
- `Version`

**Key commands:**

- `CreateDraftPayrollRun`
- `ImportPayrollData`
- `RecordValidationReport`
- `MarkReadyForReview`
- `StartExceptionReview`
- `ResolveExceptionReview`
- `SubmitForSmeApproval`
- `ApprovePayrollForPayment`
- `RejectPayrollApproval`
- `RecordPaymentExported`
- `RecordPaymentProofUploaded`
- `ClosePayrollRun`
- `ReopenForCorrection`
- `ApplyCorrection`

**Core invariants:**

- A payroll run belongs to exactly one SME company and one payroll period.
- A payroll period stores a display month/year label plus explicit `period_start`, `period_end`, and `pay_date`; monthly payroll defaults to the calendar month but the stored dates are authoritative.
- Only one active non-void payroll run should exist per SME company/payroll period/payroll cycle.
- Payroll amounts cannot be exported before SME approval.
- Payroll lines cannot be changed after `ApprovedForPayment` except through `ReopenedCorrectionRequired`.
- Every state transition must include actor, timestamp, prior state, next state, command, and reason where required.
- Sensitive data access is not authorized by the aggregate; it must be checked by Tenant and Access before loading/displaying sensitive fields.
- Closing requires either payment proof uploaded or an explicit authorized proof waiver/placeholder, depending on configured evidence policy.
- Reopening requires reason, authority, and audit event; previous approved/exported artifacts remain retained and are superseded, not deleted.

### 6.2 Aggregate: ImportBatch

**Identity:** `ImportBatchId`

**Purpose:** Tracks source import, mapping, parsing, normalization, and import validation results.

**Entities:**

- `SourceFile`
- `ParsedRow`
- `ImportError`
- `ImportMappingVersion`

**Value objects:**

- `FileHash`
- `FileName`
- `MimeType`
- `ImportSourceType`
- `ColumnMapping`
- `RowNumber`
- `ValidationSeverity`

**Invariants:**

- Original source file is retained once used to create/replace payroll data.
- Import mapping version is recorded for reproducibility.
- Failed imports cannot overwrite an existing payroll run.
- Bulk overwrite requires maker-checker or privileged authorization if enabled.

### 6.3 Aggregate: ValidationReport

**Identity:** `ValidationReportId`

**Purpose:** Captures validation findings for a payroll run and determines whether blocking issues exist.

**Entities:**

- `ValidationFinding`

**Value objects:**

- `ValidationRuleCode`
- `ValidationSeverity`
- `AffectedRecordRef`
- `FindingMessageCode`

**Invariants:**

- A report is immutable after being attached to a payroll run state transition.
- Blocking findings prevent `ReadyForReview`.
- Findings reference domain concepts, not raw spreadsheet cells only.

### 6.4 Aggregate: ExceptionCase

**Identity:** `ExceptionCaseId`

**Purpose:** Represents one human-reviewable payroll exception.

**Entities:**

- `ExceptionResolution`
- `ReviewerNote`
- `EvidenceRequirementRef`

**Value objects:**

- `ExceptionType`
- `ExceptionSeverity`
- `PayrollImpactAmount`
- `RequiredAction`
- `ResolutionReason`

**Invariants:**

- Blocking exception cases must be resolved before SME approval submission.
- Resolution requires actor, timestamp, decision, and reason.
- If an exception is waived, waiver reason and authority are mandatory.
- Critical exceptions may require reviewer approval separate from processor action.

### 6.5 Aggregate: EvidenceChecklist

**Identity:** `EvidenceChecklistId`

**Purpose:** Defines required, conditional, and optional evidence for a payroll run.

**Entities:**

- `EvidenceRequirement`
- `EvidenceItemLink`

**Value objects:**

- `EvidenceRuleCode`
- `EvidenceRequirementStatus`
- `EvidenceCategory`
- `DocumentReference`
- `FileHash`

**Invariants:**

- Mandatory evidence must be satisfied before close unless waived by authorized role.
- Conditional evidence rules evaluate against payroll run facts, e.g. “OT approval required if overtime exists.”
- Evidence item links are append-only; replacement creates a superseding link.

### 6.6 Aggregate: AuditPack

**Identity:** `AuditPackId`

**Purpose:** Reproducible packaged evidence for one payroll run.

**Entities:**

- `AuditPackSection`
- `DocumentIndexEntry`
- `GeneratedArtifactRef`

**Value objects:**

- `AuditPackVersion`
- `GeneratedAt`
- `FileHash`
- `PackScope`

**Invariants:**

- A generated audit pack must reference exact versions of artifacts/documents included.
- Regenerating creates a new version; prior pack versions remain available subject to retention.
- Audit pack generation must be logged as sensitive export if it includes payroll/bank data.

### 6.7 Aggregate: PaymentExport

**Identity:** `PaymentExportId`

**Purpose:** Tracks generated payment/export artifacts from an approved payroll run.

**Entities:**

- `ExportArtifact`
- `ExportAttempt`

**Value objects:**

- `ExportFormat`
- `ExportTemplateVersion`
- `ExportChecksum`
- `PaymentBatchReference`

**Invariants:**

- Export can only be generated from an `ApprovedForPayment` or later payroll run.
- Export artifact must store the payroll run version used.
- Regeneration after correction creates a new export version; old exports are retained and marked superseded where applicable.

### 6.8 Aggregate: PaymentProof

**Identity:** `PaymentProofId`

**Purpose:** Captures proof that payroll/statutory payment was made, scheduled, or externally handled.

**Entities:**

- `ProofDocument`
- `ProofReview`

**Value objects:**

- `PaymentProofType`
- `PaymentReference`
- `ProofStatus`
- `FileHash`

**Invariants:**

- Proof must reference a payment export or approved payroll run.
- Proof upload after closure requires reopen or append-only post-close evidence policy.
- Proof containing bank details is sensitive and must be access logged.

### 6.9 Aggregate: ServiceProviderTenant

**Identity:** `ServiceProviderTenantId`

**Purpose:** Commercial and operational workspace for a service provider.

**Entities:**

- `Membership`
- `RoleGrant`
- `CompanyAssignment`

**Value objects:**

- `TenantName`
- `MembershipStatus`
- `RoleCode`
- `PermissionCode`

**Invariants:**

- Non-admin service-provider users can only access explicitly assigned SME companies.
- Privileged role changes must be audited.
- Disabled memberships cannot approve, export, or view sensitive data.

### 6.10 Aggregate: SmeCompany

**Identity:** `SmeCompanyId`

**Purpose:** SME company workspace and data owner boundary.

**Entities:**

- `SmeServiceRelationship`
- `CompanyUserMembership`
- `PayrollCalendar`

**Value objects:**

- `CompanyRegistrationNumber`
- `CompanyStatus`
- `PayrollCycleConfig`
- `RetentionPolicy`

**Invariants:**

- Payroll runs require active SME company status and active service relationship.
- Transfers/termination preserve historical payroll audit access according to retention rules.
- Company offboarding must support export before deletion/termination workflow.

---

## 7. Domain Events

Events are past-tense facts. They should be written via an outbox in the same transaction as aggregate changes where implementation requires integration reliability.

### Payroll Workflow Events

- `PayrollRunCreated`
- `PayrollDataImported`
- `PayrollValidationCompleted`
- `PayrollValidationIssuesFound`
- `PayrollRunMarkedReadyForReview`
- `PayrollExceptionReviewStarted`
- `PayrollExceptionReviewCompleted`
- `PayrollSubmittedForSmeApproval`
- `PayrollApprovalRejected`
- `PayrollRunApprovedForPayment`
- `PayrollPaymentExportRecorded`
- `PayrollPaymentProofUploaded`
- `PayrollRunClosed`
- `PayrollRunReopenedForCorrection`
- `PayrollCorrectionApplied`
- `PayrollRunArchived`

### Import and Validation Events

- `ImportBatchUploaded`
- `ImportBatchParsed`
- `ImportBatchRejected`
- `ImportBatchAppliedToPayrollRun`
- `ValidationReportGenerated`
- `BlockingValidationIssueRaised`

### Exception Review Events

- `ExceptionCaseRaised`
- `ExceptionCaseAssigned`
- `ExceptionCaseResolved`
- `ExceptionCaseWaived`
- `BlockingExceptionCleared`

### Evidence and Audit Events

- `EvidenceRequirementCreated`
- `EvidenceItemAttached`
- `EvidenceItemSuperseded`
- `EvidenceChecklistSatisfied`
- `AuditPackGenerated`
- `SensitivePayrollDataViewed`
- `SensitivePayrollDataExported`

### Export and Payment Events

- `PaymentExportGenerated`
- `PaymentExportSuperseded`
- `PayrollJournalPreviewGenerated`
- `StatutorySummaryExported`
- `PaymentProofSubmitted`
- `PaymentProofAccepted`
- `PaymentProofRejected`

### Access and Tenant Events

- `ServiceProviderTenantCreated`
- `SmeCompanyWorkspaceCreated`
- `SmeServiceRelationshipActivated`
- `CompanyAssignmentGranted`
- `CompanyAssignmentRevoked`
- `SensitiveFieldPermissionGranted`
- `SensitiveFieldPermissionRevoked`
- `BreakGlassAccessStarted`
- `BreakGlassAccessEnded`

---

## 8. Cross-Cutting Invariants

### Tenancy and Authorization

- Every command must carry authenticated actor, tenant context, SME company scope, and correlation ID.
- Every payroll/evidence/export record must include `sme_company_id`.
- Every service-provider action must be authorized against the user’s tenant membership and SME assignment.
- Sensitive salary, bank, statutory, and identity data access must be separately permissioned and logged.

### Auditability

- Workflow transitions are append-only facts.
- Corrections do not silently overwrite approved facts; they create new versions/events.
- Generated artifacts record source run version, template version, mapping version, timestamp, actor, and checksum.
- Audit timeline must be reproducible and exportable.

### Payroll Lifecycle

- Payroll cannot move to SME approval with blocking validation findings or unresolved blocking exceptions.
- Payroll cannot be payment-exported before SME approval.
- Payroll cannot close without required evidence or authorized waiver.
- Reopen/correction always requires reason and authority.

### MVP Boundary

- Payroll journal export is an accounting handoff artifact, not a GL posting.
- Statutory summaries are readiness/export artifacts unless a future statutory engine is explicitly implemented.
- Payment proof records external payment evidence; the MVP does not need to execute bank transfers.

---

## 9. Mermaid Domain Model Diagram

```mermaid
classDiagram
  class ServiceProviderTenant {
    +ServiceProviderTenantId id
    +TenantName name
    +grantAssignment(user, company)
    +revokeAssignment(user, company)
  }

  class SmeCompany {
    +SmeCompanyId id
    +CompanyRegistrationNumber registrationNo
    +CompanyStatus status
    +PayrollCycleConfig payrollCycle
  }

  class SmeServiceRelationship {
    +RelationshipStatus status
    +EffectiveDateRange effectiveDates
  }

  class Membership {
    +UserId userId
    +RoleCode role
    +MembershipStatus status
  }

  class PayrollRun {
    +PayrollRunId id
    +PayrollPeriod period
    +PayrollRunStatus status
    +Version version
    +markReadyForReview()
    +submitForSmeApproval()
    +approveForPayment()
    +recordPaymentExported()
    +recordPaymentProofUploaded()
    +close()
    +reopenForCorrection(reason)
  }

  class PayrollRunLine {
    +EmployeeSnapshotRef employee
    +Money grossPay
    +Money deductions
    +Money netPay
  }

  class PayrollComponentLine {
    +PayrollComponentCode component
    +Money amount
    +StatutoryTreatment statutoryTreatment
  }

  class ValidationReport {
    +ValidationReportId id
    +hasBlockingFindings()
  }

  class ValidationFinding {
    +ValidationRuleCode rule
    +ValidationSeverity severity
    +AffectedRecordRef affectedRecord
  }

  class ExceptionCase {
    +ExceptionCaseId id
    +ExceptionType type
    +ExceptionSeverity severity
    +resolve(reason)
    +waive(reason)
  }

  class EvidenceChecklist {
    +EvidenceChecklistId id
    +isSatisfied()
  }

  class EvidenceItem {
    +EvidenceCategory category
    +DocumentReference document
    +FileHash hash
  }

  class PaymentExport {
    +PaymentExportId id
    +ExportFormat format
    +ExportChecksum checksum
  }

  class PaymentProof {
    +PaymentProofId id
    +PaymentProofType type
    +ProofStatus status
  }

  class AuditPack {
    +AuditPackId id
    +AuditPackVersion version
    +generate()
  }

  ServiceProviderTenant "1" --> "many" Membership
  ServiceProviderTenant "1" --> "many" SmeServiceRelationship
  SmeCompany "1" --> "many" SmeServiceRelationship
  SmeCompany "1" --> "many" PayrollRun
  PayrollRun "1" *-- "many" PayrollRunLine
  PayrollRunLine "1" *-- "many" PayrollComponentLine
  PayrollRun "1" --> "many" ValidationReport
  ValidationReport "1" *-- "many" ValidationFinding
  PayrollRun "1" --> "many" ExceptionCase
  PayrollRun "1" --> "1" EvidenceChecklist
  EvidenceChecklist "1" *-- "many" EvidenceItem
  PayrollRun "1" --> "many" PaymentExport
  PayrollRun "1" --> "many" PaymentProof
  PayrollRun "1" --> "many" AuditPack
```

---

## 10. Design Notes and Open Questions

### Accepted Defaults Reflected

- Company-neutral service-provider terminology is used.
- SME company is the data owner boundary.
- MVP emphasizes verification/approval/evidence, not full ERP/accounting/statutory engine.
- Corrections are append-only and audit-visible.
- Payroll state machine is explicit and controlled.

### Open Questions for Later Discovery

- Which pilot roles can waive mandatory evidence?
- Is internal maker-checker mandatory or configurable per service-provider tenant?
- Which payment export formats are required for first pilot?
- Which statutory readiness checks are hard blockers versus warnings?
- What retention period and deletion process should legal counsel approve?
