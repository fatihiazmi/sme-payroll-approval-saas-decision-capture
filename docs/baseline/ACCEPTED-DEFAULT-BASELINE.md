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
- Every request must carry explicit tenant and company context.
- Use logical tenant isolation for MVP with strict authorization and audit controls.
- Restrict service-provider staff to assigned SMEs only.
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

## 6. Next Artifacts to Produce

1. MVP PRD
2. Domain model and ubiquitous language
3. Payroll workflow state machine
4. SaaS tenancy and RBAC model
5. Initial Agile backlog using INVEST stories
6. Prototype/demo flow script

---

## 7. Source Decision Documents

- `docs/decision-capture/payroll-cosec-workflow.md`
- `docs/decision-capture/finance-audit-evidence.md`
- `docs/decision-capture/saas-product-architecture.md`

## 8. Accepted Payment Export Decision

**Decision:** For MVP, export a configurable generic CSV payment file after payroll approval, then require manual payment proof upload.

**Scope:**

- Include employee name, bank name, bank account number, net pay amount, and payment reference.
- Block export until the payroll run is approved.
- Record exporter, timestamp, row count, and exported total in the audit timeline.
- Do not build direct bank integration or bank-specific file formats in MVP.
- Expand to specific Malaysian bank formats after pilot validation.
