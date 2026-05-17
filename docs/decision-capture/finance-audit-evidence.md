# SME Payroll Approval SaaS — Finance, Audit Pack & Document Evidence Expert Interview + Decision Capture

**Product:** SME Payroll Approval SaaS ERP + CoSec Console SaaS  
**Perspective embodied:** Malaysian SME accounting/audit practitioner advising on practical MVP scope, audit-readiness, document evidence, payroll-to-finance handoff, and CoSec-facing compliance workflows.  
**Decision posture:** Avoid building a full accounting ERP in MVP. Start with defensible evidence capture, payroll approval/payment proof, audit timeline, and exportable accounting handoff artifacts.  
**Audience:** Nik review / product decisioning  
**Status:** Draft decision capture

---

## 1. Executive Recommendation

For MVP, SME Payroll Approval SaaS should **not** attempt to replace SQL Accounting, AutoCount, Xero, QuickBooks, Bukku, or a full GL/AP/AR system. Malaysian SMEs already rely on accountants, outsourced bookkeepers, or existing accounting packages. The immediate high-value gap is not “another ledger”; it is **evidence discipline**:

- Was payroll calculated from approved HR/attendance inputs?
- Who approved it, when, and what changed?
- Were EPF/SOCSO/EIS/PCB/payment files exported?
- Can management, accountant, auditor, or company secretary retrieve a clean evidence pack?
- Can payroll cost be handed off into accounting without manual reconstruction?

### MVP decision headline
Build **Payroll Evidence + Approval + Payment Export + Audit Timeline + Journal Preview/Export**, not full finance ERP.

### Phase 2 headline
After evidence workflows prove adoption, add optional lightweight finance modules or deep accounting integrations: COA mapping, GL posting API, AP/AR document capture, bank reconciliation, month-end checklist, and audit pack automation.

---

## 2. Decision Principles

1. **Audit evidence before accounting depth**  
   Malaysian SMEs fail audits and due diligence less because they lack a ledger and more because supporting documents, approvals, payment proof, and reconciliations are scattered.

2. **Export-first, integrate-later**  
   MVP should produce Excel/CSV/PDF exports accepted by accountants and auditors before attempting real-time posting to multiple accounting systems.

3. **Immutable timeline over editable finance records**  
   Keep a tamper-evident event trail for approvals, recalculations, exports, uploads, and document changes.

4. **Do not own statutory correctness silently**  
   For payroll statutory values, the system may calculate and display, but every finalization needs approval and traceable evidence. Where official third-party portals are used, store generated files and proof of submission/payment.

5. **Accountant-friendly terminology**  
   Use common SME terms: salary register, payroll summary, journal entry, payment batch, EPF/SOCSO/EIS/PCB files, supporting documents, audit trail, management approval, accountant export.

---

## 3. Expert Interview & Decision Capture

### Q1 — Are we trying to become a full accounting system or an audit/evidence system first?

**Expert question:**  
“As a Malaysian SME accountant, what problem are you asking SME Payroll Approval SaaS to solve first: bookkeeping, payroll compliance, audit evidence, or management approval?”

**Why it matters:**  
Full accounting means chart of accounts, double-entry validation, GST/SST/e-Invoice logic, AP/AR ageing, bank reconciliation, ledgers, financial statements, opening balances, adjustments, year-end closing, and accounting package migration. That is a large product with high liability. SMEs will compare it to mature accounting tools. Evidence and approval workflows are narrower, more defensible, and immediately useful even when the client keeps their existing accountant.

**MVP decision:**  
Position MVP as **Payroll & Compliance Evidence Workspace**, not full accounting ERP. Include finance-facing outputs but avoid owning the general ledger as system of record.

**MVP scope:**
- Payroll run evidence pack
- Salary register and variance summary
- Approval workflow
- Payment export/proof upload
- Statutory contribution summary/export artifacts
- Payroll journal preview/export
- Immutable audit timeline

**Phase 2 deferral:**
- Full GL
- Trial balance
- Management accounts
- Financial statements
- AP/AR subledgers
- Bank reconciliation
- Month-end close engine

**Risks:**
- If marketed as “ERP accounting,” users expect full ledger functionality.
- If marketed as “evidence workspace,” value proposition must be crisp and accountant-friendly.
- Scope creep into accounting can delay MVP and create statutory/accounting liability.

**Decision for Nik:**  
Adopt MVP positioning: **“Payroll evidence, approvals, and accountant-ready exports.”**

---

### Q2 — What minimum accounting artifact must payroll produce?

**Expert question:**  
“When payroll is finalized, what should the accountant receive so they can post it without reworking everything manually?”

**Why it matters:**  
Even if SME Payroll Approval SaaS does not own the GL, payroll must feed accounting. Accountants need payroll expense, employer statutory costs, deductions, net salary payable, statutory payable, and payment references. Without this, payroll becomes operational only and finance still repeats work.

**MVP decision:**  
Provide a **Payroll Journal Preview and Export** rather than posting to a live ledger.

**MVP scope:**
- Configurable mapping table:
  - Salary/wages expense
  - Allowance expense
  - Overtime expense
  - Bonus/commission expense
  - Employer EPF expense
  - Employer SOCSO expense
  - Employer EIS expense
  - Employee deductions payable
  - Net salary payable / bank clearing
  - PCB payable
  - EPF/SOCSO/EIS payable
- Payroll journal preview by payroll run
- Export to CSV/XLSX/PDF
- Include cost centre/department/project if available
- Include rounding and variance line if required

**Phase 2 deferral:**
- Direct posting to accounting system APIs
- Multi-entity consolidation
- Accrual/reversal automation
- Complex cost allocation rules
- Full COA lifecycle management

**Risks:**
- Wrong account mapping creates accounting misstatement.
- Payroll journal may differ by accountant preference.
- SMEs may not know their COA; setup assistance or templates will be needed.

**Decision for Nik:**  
Build **journal preview/export** with customer-configurable COA mapping, but require accountant/admin approval before use.

---

### Q3 — Should SME Payroll Approval SaaS include a Chart of Accounts in MVP?

**Expert question:**  
“Do we need to maintain a full Chart of Accounts, or only enough mapping to export payroll journals?”

**Why it matters:**  
A full COA implies complete accounting ownership. It also requires opening balances, account types, financial statement mapping, account locking, posting controls, and audit treatment. For MVP, that is unnecessary if payroll is the first domain.

**MVP decision:**  
Use a **limited payroll accounting mapping table**, not a full COA module.

**MVP scope:**
- Simple list of account codes/names imported or manually entered
- Payroll component-to-account mapping
- Department/cost-centre optional mapping
- Export format templates
- Mapping effective date/versioning

**Phase 2 deferral:**
- Full COA maintenance
- Account hierarchy
- Financial statement grouping
- Account locking by period
- Opening balances
- Multi-currency ledgers

**Risks:**
- Users may ask, “Where is my Profit & Loss?” if COA is shown like an accounting module.
- Incorrect mapping version can affect historical payroll journals.
- Need to preserve the mapping used at finalization time.

**Decision for Nik:**  
Name the feature **Accounting Export Mapping**, not **Chart of Accounts**, in MVP.

---

### Q4 — What should be inside a payroll audit pack?

**Expert question:**  
“If an auditor or external accountant asks for payroll support for March 2026, what documents should SME Payroll Approval SaaS produce in one pack?”

**Why it matters:**  
Audit pack quality is a major value differentiator. Auditors do not only want final numbers; they need evidence trail: source inputs, approvals, calculations, statutory submissions, payment proof, and change logs.

**MVP decision:**  
Generate a **Payroll Run Audit Pack** per company/month/pay cycle.

**MVP scope:**
- Payroll summary by employee and component
- Salary register
- Variance report vs previous month
- Attendance/leave/OT source summary, where applicable
- Claim/allowance source references, where applicable
- Approval history: preparer, reviewer, approver, timestamps
- Final payslip archive references
- Bank payment file export record
- Payment proof upload/reference
- EPF/SOCSO/EIS/PCB summary/export files or proof references
- Payroll journal preview/export
- Exception notes and management comments
- Document index with file hashes/checksums

**Phase 2 deferral:**
- Auditor portal with request/response workflow
- Sampling workflow
- Materiality tagging
- Automated analytical review
- Direct statutory portal confirmation integrations
- Signed audit confirmation letters

**Risks:**
- Pack may create false sense of audit completeness if underlying docs are missing.
- Confidential payroll data requires strict role-based access.
- Audit pack must be reproducible exactly as at finalization date.

**Decision for Nik:**  
MVP must include **one-click payroll audit pack generation** with document index and immutable timeline.

---

### Q5 — What document evidence is mandatory versus optional?

**Expert question:**  
“Which evidence must be captured before payroll can be finalized, and which can be attached later?”

**Why it matters:**  
If the system makes every document mandatory, SMEs will bypass it. If nothing is mandatory, the audit pack becomes weak. MVP needs practical evidence rules that improve discipline without blocking small businesses unnecessarily.

**MVP decision:**  
Use configurable **evidence checklist rules** with a small mandatory baseline.

**MVP mandatory baseline:**
- Payroll run calculation snapshot
- Approval record
- Salary register
- Bank/payment export record or payment proof placeholder
- Statutory contribution summary
- Change log since draft payroll

**Conditionally required evidence:**
- OT approval if overtime exists
- Leave/attendance source if deductions/OT depend on attendance
- Claim receipt if claims are included
- Bonus/commission approval if included
- New joiner/resignation document reference where payroll changes materially

**Optional evidence:**
- Management notes
- Email correspondence
- Accountant comments
- External spreadsheets
- Board/management approval minutes

**Phase 2 deferral:**
- OCR classification and auto-matching
- Evidence completeness scoring
- Auto-reminders for missing documents
- E-signature workflow
- External auditor evidence request portal

**Risks:**
- Overly strict rules reduce adoption.
- Weak rules reduce audit value.
- Different SMEs and auditors have different documentation standards.

**Decision for Nik:**  
Implement **configurable evidence checklist templates** with sensible Malaysian SME defaults.

---

### Q6 — How should document evidence be stored and protected?

**Expert question:**  
“If payroll records and payment proof are uploaded, how do we prove files were not replaced or manipulated later?”

**Why it matters:**  
Audit evidence must be trustworthy. For SaaS, document integrity, access control, versioning, and retention matter. Payroll data is sensitive personal data, so PDPA-aware handling is essential.

**MVP decision:**  
Store documents as versioned evidence records with hash, uploader, timestamp, linked business object, and access log.

**MVP scope:**
- Evidence object model:
  - document ID
  - tenant/company ID
  - linked object: payroll run, employee, approval, payment batch, statutory file, journal export
  - file name/type/size
  - SHA-256 hash
  - uploaded by/at
  - version number
  - status: draft, submitted, approved, superseded, voided
  - retention category
- File replacement creates new version; old version remains auditable
- Download/view access logging
- Role-based permissions
- Soft delete only for normal users; hard delete restricted/admin policy-driven

**Phase 2 deferral:**
- WORM/object lock storage
- Digital signatures
- Long-term archive tiering
- Legal hold
- External auditor read-only vault
- Tamper-evident ledger/Merkle chain

**Risks:**
- Mishandling payroll documents can create PDPA/privacy exposure.
- Hashing proves integrity only if original hash/event log is protected.
- Document retention requirements vary by document type and customer policy.

**Decision for Nik:**  
MVP evidence records must be **versioned, hashed, permissioned, and event-logged**.

---

### Q7 — Should the audit timeline be editable?

**Expert question:**  
“When someone changes payroll after approval, should they be allowed to overwrite the old record?”

**Why it matters:**  
Auditors and accountants need to know what changed, who changed it, who approved the change, and whether the final numbers tie to payment. Editable histories destroy evidence value.

**MVP decision:**  
Create an **append-only audit timeline** for finance/audit-sensitive events.

**MVP timeline events:**
- Payroll draft created
- Payroll recalculated
- Employee/component changed
- Evidence uploaded/superseded
- Approval requested
- Approval granted/rejected
- Payroll finalized
- Payment export generated
- Statutory file/export generated
- Payment proof uploaded
- Journal export generated
- Audit pack generated
- Finalized run reopened/voided

**Phase 2 deferral:**
- Cryptographic event chain
- External auditor event annotations
- Anomaly detection
- Cross-module audit timeline across payroll/AP/AR/CoSec events

**Risks:**
- Too much event noise makes timeline hard to read.
- Too little detail weakens accountability.
- Reopening finalized payroll must be tightly controlled.

**Decision for Nik:**  
MVP timeline should be **append-only**, with human-readable event summaries and exportable logs.

---

### Q8 — What approval controls are minimally required before payroll payment?

**Expert question:**  
“Before salary payment files go out, who must review and approve payroll?”

**Why it matters:**  
Payroll fraud and mistakes often happen because one person can prepare, approve, and pay. Even small SMEs need basic segregation or at least an explicit management approval trail.

**MVP decision:**  
Support simple but enforceable payroll approval workflow.

**MVP scope:**
- Maker-checker approval option
- Configurable approver by company/payroll group
- Approval snapshot locks key values
- Recalculation after approval invalidates approval or requires re-approval
- Approval comments mandatory on rejection/reopen
- Payment export only after approval, unless override permission is used

**Phase 2 deferral:**
- Multi-level approval matrix by amount/headcount
- Department-level approvals
- Board/management approval packs
- E-signatures
- Integration with banking approval status

**Risks:**
- SMEs with one admin may resist maker-checker.
- Too strict approval gating may delay salary payment.
- Override actions must be visibly logged.

**Decision for Nik:**  
MVP should enforce **approval-before-payment-export** by default, with logged admin override.

---

### Q9 — Should SME Payroll Approval SaaS perform bank reconciliation in MVP?

**Expert question:**  
“Do we need to reconcile bank statements against salary payments now, or is payment proof enough for MVP?”

**Why it matters:**  
Bank reconciliation is useful but quickly expands into full accounting. For payroll MVP, the key evidence is that payment file/export and proof exist and tie back to the approved payroll run.

**MVP decision:**  
Do **not** build full bank reconciliation. Capture payment batch evidence and proof.

**MVP scope:**
- Payment batch generated from approved payroll
- Bank file/export metadata
- Manual payment proof upload
- Payment reference numbers
- Paid date/status
- Exception notes for failed/reversed payments

**Phase 2 deferral:**
- Bank statement import
- Auto-match payments
- Bank feed integration
- Reconciliation reports
- Cash book/GL clearing

**Risks:**
- Payment proof is weaker than bank reconciliation.
- Manual upload may be missed or delayed.
- Failed salary payments need operational follow-up.

**Decision for Nik:**  
MVP uses **payment evidence capture**, not reconciliation.

---

### Q10 — Should SME Payroll Approval SaaS build AP/AR invoice processing in MVP?

**Expert question:**  
“Is AP/AR invoice OCR and accounting automation required to prove the SaaS concept, or can it wait?”

**Why it matters:**  
AP/AR OCR, e-Invoice, supplier/customer ledgers, SST handling, payment matching, ageing, credit notes, debit notes, and approvals are major product areas. If MVP already includes payroll + CoSec console, AP/AR will overload scope.

**MVP decision:**  
Defer AP/AR as finance Phase 2 unless there is a signed customer requirement.

**MVP scope:**
- Allow generic document upload/tagging for non-payroll evidence if needed
- Do not create AP/AR ledgers
- Do not promise invoice automation in MVP finance scope

**Phase 2 deferral:**
- Invoice OCR
- Supplier/customer master
- AP approval workflow
- AR invoice issuance
- LHDN e-Invoice integration
- Ageing reports
- Payment matching

**Risks:**
- Existing project docs may overpromise “AI accounting automation.”
- AP/AR is attractive but competes directly with mature accounting software.
- LHDN e-Invoice compliance requires careful statutory implementation.

**Decision for Nik:**  
Keep AP/AR out of MVP finance scope; mention as **Phase 2 accounting automation**.

---

### Q11 — What month-end closing features belong in MVP?

**Expert question:**  
“Do Malaysian SMEs need SME Payroll Approval SaaS to close the books, or only to confirm payroll month-end evidence is complete?”

**Why it matters:**  
Month-end closing across the full company means accruals, prepayments, depreciation, bank rec, AP/AR cut-off, stock, intercompany, and management accounts. Payroll month-end is narrower and valuable.

**MVP decision:**  
Implement **Payroll Month-End Evidence Checklist**, not full month-end close.

**MVP scope:**
- Payroll finalized
- Approval completed
- Payment exported/proof uploaded
- Statutory summaries generated
- Journal export generated
- Evidence checklist complete/incomplete
- Accountant export downloaded
- Audit pack generated

**Phase 2 deferral:**
- Full close calendar
- GL period locking
- Accrual and adjustment workflow
- Bank/AP/AR reconciliation tasks
- Management accounts pack

**Risks:**
- “Month-end close” wording may imply full accounting close.
- Accountants may still need manual journals outside payroll.
- Evidence checklist must clearly show what is outside SME Payroll Approval SaaS.

**Decision for Nik:**  
Use label **Payroll Month-End Checklist** in MVP.

---

### Q12 — What should CoSec users see versus finance/payroll users?

**Expert question:**  
“What payroll/finance evidence is relevant to a company secretary, and what should remain restricted?”

**Why it matters:**  
SME Payroll Approval SaaS includes CoSec Console. Company secretaries may care about compliance status, resolutions, director/shareholder records, and document evidence. But payroll details are confidential and not normally broad CoSec data.

**MVP decision:**  
Separate CoSec evidence visibility from payroll detail visibility.

**MVP scope:**
- CoSec Console may see high-level compliance/evidence status only if authorized:
  - payroll pack exists / missing
  - statutory payment evidence exists / missing
  - board/management approval documents linked, if relevant
- Payroll amounts and employee-level data hidden unless explicit role permission
- Company-level document vault shared only by category and permission

**Phase 2 deferral:**
- CoSec-managed board approval workflows for payroll/bonus/director fees
- Director fee approval packs
- AGM/audit liaison workflows
- Auditor/secretarial shared evidence portal

**Risks:**
- Over-sharing payroll data creates privacy issues.
- Under-sharing reduces CoSec Console value.
- Need clear RBAC and document category permissions.

**Decision for Nik:**  
Implement **role-scoped evidence visibility**; CoSec should not automatically access employee payroll details.

---

### Q13 — Should statutory submission/payment proof be integrated or uploaded?

**Expert question:**  
“Will SME Payroll Approval SaaS submit EPF/SOCSO/EIS/PCB directly, or only prepare files and store proof?”

**Why it matters:**  
Direct submission integrations introduce operational, legal, authentication, and support burden. For MVP, many SMEs already submit via official portals or bank channels. The key is traceability.

**MVP decision:**  
Export statutory summaries/files where possible and capture proof of submission/payment manually.

**MVP scope:**
- EPF/SOCSO/EIS/PCB contribution summaries
- Export files or reports if format is confirmed
- Manual proof upload fields:
  - submitted by
  - submitted date
  - payment date
  - reference number
  - proof document
- Reminder/status flags

**Phase 2 deferral:**
- Direct portal submission
- API integrations, if available and commercially viable
- Auto-confirmation of payment status
- Statutory calendar automation across modules

**Risks:**
- File formats and portal requirements can change.
- Manual proof upload depends on user discipline.
- Incorrect statutory calculation/submission can carry compliance risk.

**Decision for Nik:**  
MVP: **prepare/export and evidence**, not direct statutory submission.

---

### Q14 — How should correction and backdated adjustment be handled?

**Expert question:**  
“What happens if payroll is finalized, paid, and then HR discovers an error?”

**Why it matters:**  
Real SMEs have late claims, missed unpaid leave, wrong OT, salary changes, and statutory corrections. The system must not silently edit prior finalized payroll without trace.

**MVP decision:**  
Finalized payroll cannot be overwritten. Corrections require reopen/void with permission or adjustment in next run.

**MVP scope:**
- Finalized run is locked
- Reopen requires privileged role, reason, and timeline event
- Adjustment entry can be included in next payroll run
- Re-export journal/payment/statutory pack after recalculation
- Prior generated audit pack marked superseded if affected

**Phase 2 deferral:**
- Formal payroll adjustment module
- Retroactive statutory recalculation assistant
- Automatic next-month recovery/spread rules
- Auditor-facing correction notes

**Risks:**
- Users may want to “just edit” paid payroll.
- Reopening paid payroll can break tie-out to payment proof.
- Backdated statutory corrections may need accountant/payroll specialist review.

**Decision for Nik:**  
MVP must have **locked finalization + controlled correction path**.

---

### Q15 — What reports are essential for accountant/auditor adoption?

**Expert question:**  
“If I am the external accountant, what reports do I need monthly and annually from SME Payroll Approval SaaS?”

**Why it matters:**  
Reports drive adoption. If outputs match accountant/auditor working style, SME Payroll Approval SaaS reduces friction. If reports are beautiful but not useful, users revert to spreadsheets.

**MVP decision:**  
Prioritize practical accountant/auditor reports over dashboards.

**MVP reports:**
- Payroll summary by month
- Employee salary register
- Component breakdown
- Employer statutory cost summary
- Employee deduction/payable summary
- Net pay/payment batch summary
- Variance vs previous month
- Payroll journal export
- Evidence completeness report
- Audit timeline export
- Document index

**Phase 2 deferral:**
- Full P&L/Balance Sheet
- Cash flow statement
- AP/AR ageing
- Bank reconciliation report
- Audit sampling dashboard
- Custom report builder

**Risks:**
- Report definitions vary between accountants.
- Over-customization can delay MVP.
- Exports must be stable and versioned.

**Decision for Nik:**  
MVP reporting should be **export-heavy and accountant-readable**, not dashboard-first.

---

## 4. MVP Finance/Audit Scope — Recommended Cut

### Include in MVP

1. **Payroll run evidence pack**
2. **Approval workflow and approval snapshot**
3. **Payment export and payment proof capture**
4. **Statutory summary/export evidence for EPF/SOCSO/EIS/PCB**
5. **Payroll journal preview/export**
6. **Accounting export mapping, not full COA**
7. **Evidence document vault for payroll-linked records**
8. **Versioned document evidence with hash and access log**
9. **Append-only audit timeline**
10. **Payroll month-end evidence checklist**
11. **Audit pack PDF/ZIP export with document index**
12. **Role-based access separating payroll, finance, accountant, auditor, and CoSec views**

### Explicitly exclude from MVP

1. Full GL
2. Full COA module
3. AP/AR ledgers
4. Invoice OCR and e-Invoice automation
5. Bank reconciliation
6. Trial balance
7. P&L/Balance Sheet/Cash Flow
8. Full month-end close
9. Direct accounting package posting
10. Direct statutory portal submission
11. Auditor request portal
12. CoSec access to employee-level payroll by default

---

## 5. Phase 2 Roadmap Candidates

### Phase 2A — Accounting integrations
- Export templates for SQL Accounting, AutoCount, Xero, QuickBooks, Bukku, etc.
- Accounting package import/export mapping
- API posting where viable
- Multi-format journal templates

### Phase 2B — Finance automation
- AP document capture
- Supplier invoices and approvals
- AR invoice records
- LHDN e-Invoice integration
- Bank statement import and reconciliation

### Phase 2C — Audit collaboration
- Auditor read-only portal
- Evidence request workflow
- Sampling workflow
- Comments and PBC tracker
- Evidence completeness scoring

### Phase 2D — Advanced controls
- WORM storage/object lock
- Digital signatures
- Tamper-evident event chain
- Legal hold and retention policy engine
- Cross-module compliance dashboard

---

## 6. Key Product Risks & Mitigations

### Risk 1 — MVP is perceived as incomplete accounting ERP
**Mitigation:** Market and label MVP carefully: payroll evidence, approvals, exports, audit pack. Avoid navigation labels like “General Ledger” unless built properly.

### Risk 2 — Incorrect accounting mappings cause bad journals
**Mitigation:** Require accountant/admin mapping approval, mapping versioning, preview before export, and clear disclaimer that journal is export/handoff until integrated.

### Risk 3 — Audit pack contains sensitive payroll data
**Mitigation:** Strict RBAC, pack generation permissions, access logs, redaction options in Phase 2.

### Risk 4 — Evidence can be replaced after approval
**Mitigation:** Versioned documents, hashes, immutable timeline, superseded status instead of overwrite.

### Risk 5 — Statutory compliance changes
**Mitigation:** Keep statutory exports modular, version file templates, retain generated file metadata, and avoid over-promising direct submission in MVP.

### Risk 6 — SMEs ignore evidence upload
**Mitigation:** Evidence checklist with status badges, reminders, and “audit pack incomplete” warnings without blocking every workflow.

### Risk 7 — CoSec scope overlaps payroll privacy
**Mitigation:** Permissioned evidence categories; CoSec sees company-level compliance status only unless granted explicit payroll role.

---

## 7. Open Decisions for Nik

1. **Positioning:** Confirm MVP should be named/positioned as payroll evidence + accountant export, not accounting ERP.
2. **Journal export:** Confirm payroll journal preview/export is included in MVP.
3. **Audit pack:** Confirm one-click payroll audit pack is a must-have MVP feature.
4. **Document evidence:** Confirm document vault must support versioning, hash, timeline, and access logs from day one.
5. **Accounting scope:** Confirm GL/AP/AR/bank rec/month-end close are Phase 2, not MVP.
6. **CoSec visibility:** Confirm CoSec users do not get employee-level payroll access by default.
7. **Statutory workflow:** Confirm MVP prepares/export/captures proof, but does not directly submit to statutory portals.

---

## 8. Suggested MVP Feature Names

- Payroll Evidence Pack
- Payroll Audit Pack
- Accounting Export Mapping
- Payroll Journal Preview
- Payment Evidence
- Statutory Evidence
- Audit Timeline
- Evidence Checklist
- Document Index
- Payroll Month-End Checklist

Avoid in MVP unless truly implemented:
- General Ledger
- Full Accounting
- Bank Reconciliation
- Month-End Close
- Financial Statements
- AP Automation
- AR Automation

---

## 9. Final Expert View

As a Malaysian SME accounting/audit expert, I would advise SME Payroll Approval SaaS to win trust by becoming the **source of truth for payroll evidence and approvals**, not by competing immediately with accounting systems. A clean payroll audit pack, defensible document evidence, approval timeline, and accountant-ready journal export solve a real SME pain point while keeping MVP scope achievable.

The strongest MVP promise is:

> “Every payroll run is approved, evidenced, exportable, and audit-ready.”

That promise is valuable, narrow, and credible.
