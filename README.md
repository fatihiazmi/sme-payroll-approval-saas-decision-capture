# SME Payroll Approval SaaS — Personal Product Review Repo

This is a public review repository for Nik's personal SaaS product exploration. It is intentionally generalized and not tied to any company-specific branding.

The product direction being reviewed: a SaaS workflow for SME payroll verification, OT exception handling, approval, payment export, and audit evidence across multiple companies/service providers.

---

# SME Payroll Approval SaaS — Master Decision Review Index

**Purpose:** Single review entry point for expert interview + decision capture documents.

**Status:** Draft for Nik review. All decisions are proposed defaults until accepted, changed, or rejected.

---

## Expert Interview Documents

1. **Payroll / OT / CoSec Workflow Expert**
   - File: `SME Payroll Approval SaaS: Expert Interview and Decision Capture - Payroll CoSec Workflow.md`
   - Focus: Malaysian payroll, OT, CoSec/service-provider workflow, statutory readiness, owner approval.

2. **Finance / Audit Evidence Expert**
   - File: `SME Payroll Approval SaaS: Finance Audit Evidence Expert Interview & Decision Capture.md`
   - Focus: finance scope boundary, audit pack, document evidence, payroll-to-accounting handoff.

3. **SaaS Product / Architecture Expert**
   - File: `SME Payroll Approval SaaS: SaaS Expert Interview and Decision Capture.md`
   - Focus: tenancy, RBAC, security, PDPA, pricing, onboarding, SaaS metrics, support model.

---

## Review Priority

### Review First — Product Direction

1. **Primary MVP wedge**
   - Proposed: Multi-company payroll verification and approval console for CoSec/service-provider managed SMEs.
   - Decision needed: Accept / change / reject.

2. **Product positioning**
   - Proposed: Do not sell/build MVP as full ERP first. Position as payroll verification, approval, evidence, and audit-readiness workflow.
   - Decision needed: Accept / change / reject.

3. **Buyer and tenant model**
   - Proposed: CoSec/service-provider is primary SaaS tenant; SME is client company workspace and data owner.
   - Decision needed: Accept / change / reject.

---

### Review Second — MVP Scope

4. **Payroll calculation depth**
   - Proposed: MVP supports payroll import/manual-entry plus verification workflow. Full statutory payroll engine can be phased.
   - Decision needed: Confirm whether MVP must calculate EPF/SOCSO/EIS/PCB or only validate/report them.

5. **Finance scope**
   - Proposed: Do not build full GL/AP/AR/bank reconciliation in MVP. Include payroll journal preview/export and evidence pack only.
   - Decision needed: Accept / change / reject.

6. **Audit pack scope**
   - Proposed: MVP audit pack includes payroll summary, OT verification, owner approval, payment export, source files, reviewer notes, and audit timeline.
   - Decision needed: Confirm minimum pack content.

---

### Review Third — Trust, Security, Operations

7. **Sensitive data access**
   - Proposed: Salary and bank fields require explicit permission and audit logging.
   - Decision needed: Accept / change / reject.

8. **Payroll lock state machine**
   - Proposed: Draft → Submitted → In Review → Returned/Verified → Pending Owner Approval → Approved → Exported → Completed; reopening requires reason and audit trail.
   - Decision needed: Validate states and who can transition them.

9. **Data ownership and offboarding**
   - Proposed: SME owns company data; CoSec has assigned access; export/offboarding must be supported.
   - Decision needed: Accept / change / reject.

10. **Commercial packaging**
   - Proposed: Price/package by CoSec tenant + number of SME companies and/or employees, with modules phased.
   - Decision needed: Validate pricing model with business owner.

---

## Suggested Review Method

For each decision, mark one of:

- **Accepted** — proceed with this assumption.
- **Changed** — replace with corrected decision.
- **Rejected** — remove from scope or choose a different approach.
- **Needs Client Confirmation** — ask partner/client before locking.

Recommended review sequence:

1. Confirm buyer and MVP wedge.
2. Confirm what is explicitly out of MVP.
3. Confirm payroll/statutory responsibility.
4. Confirm finance/audit pack boundary.
5. Confirm tenancy, data ownership, and access rules.
6. Convert accepted decisions into PRD + agile backlog.

---

## Immediate Proposed Baseline

If no client answer exists yet, use this as the working baseline:

> SME Payroll Approval SaaS MVP is a CoSec/service-provider console for managing payroll verification, OT exception review, SME owner approval, payment export, evidence retention, and audit trail across multiple SME companies. It is not a full accounting ERP or full payroll statutory engine in MVP.

---

## Next Artifact to Produce After Review

Once decisions are reviewed, produce:

1. MVP PRD
2. MVP scope boundary
3. Final agile backlog
4. Payroll workflow state machine
5. SaaS tenancy/RBAC model
6. Partner demo script
