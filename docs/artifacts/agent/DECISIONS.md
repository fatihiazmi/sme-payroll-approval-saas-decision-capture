
### DEC-2026-05-17-1806 — Feature flag for MVP salary/bank masking rule
**Date**: 2026-05-17T18:06:57+08:00 | **Project**: sme-payroll-approval-saas-decision-capture | **Vote**: Security Auditor opposes customer/tenant feature flag
**Decision**: Do not make salary/bank masking optional via a tenant/customer feature flag; ship it as a mandatory authorization invariant, with only tightly controlled break-glass/admin migration toggles if absolutely needed.
**Key argument**: Payroll salary and bank data are high-sensitivity PII; a runtime feature flag creates avoidable misconfiguration and least-privilege failure risk for a control that must be always-on.
**Dissent**: Product/implementation may want pilot flexibility, but security rejects customer-configurable disablement.
**Actions**:
- Security: Define masking as default-deny RBAC/policy tests and require audit events for every reveal/export.
- Backend: Implement server-side field-level authorization independent of UI flags.
- Product: Treat role exceptions as explicit permissions/roles, not feature-flag overrides.
---

### DEC-2026-05-17-1807 — Tech Lead council on feature flag for MVP salary/bank masking rule
**Date**: 2026-05-17T18:07:17+08:00 | **Project**: sme-payroll-approval-saas-decision-capture | **Vote**: 4 support / 0 oppose / 1 conditional abstain
**Decision**: Do not control the MVP salary/bank masking rule with a tenant/customer-facing feature flag; implement it as a mandatory domain authorization policy and allow only operationally controlled, audited emergency or migration toggles that cannot broaden user access in production by default.
**Key argument**: Salary visibility and bank/payment visibility are part of the payroll security domain model, not optional product behavior; making them flag-driven would duplicate authorization logic across domains and create avoidable misconfiguration risk.
**Dissent**: Product Delivery conditionally abstains because pilot demos may need flexibility, but accepts that flexibility must come through explicit role/permission configuration and seeded demo data, not a masking-off flag.
**Actions**:
- Tech Lead: Encode the invariant in the authorization/domain policy: Owner + Payroll Operator may see salary fields; Payment/Journal User may see approved payment/bank fields; all reveal/export actions audited.
- Backend: Centralize masking in server-side policy/presenter/export services and add tests proving UI and API cannot bypass it.
- Security: Require default-deny behavior, audit events for reveal/export, and no customer-configurable disablement.
- Product Delivery: Document any pilot exception as a role/permission variant or demo fixture, not a feature flag.
---


### DEC-2026-05-17-1808-backend — Backend position on feature flag for MVP salary/bank masking rule
**Date**: 2026-05-17T18:08:20.079463+08:00 | **Project**: sme-payroll-approval-saas-decision-capture | **Vote**: Backend supports no customer-facing flag
**Decision**: Backend supports not using a customer- or tenant-facing feature flag for the MVP salary/bank masking rule; implement it as mandatory server-side RBAC/ABAC policy with audited reveal/export paths.
**Key argument**: A flag would create a second authorization dimension that every API, presenter, export, and audit path must test; mandatory policy is simpler, safer, and easier to prove correct for MVP.
**Dissent**: A short-lived internal kill switch may be acceptable only if it fails closed, cannot broaden production access, and exists solely for migration/incident handling.
**Actions**:
- Backend: Build a central sensitive-field policy service returning field-level grants and masked DTOs.
- Backend: Add matrix tests for Owner, Payroll Operator, Payment/Journal User, Auditor, and Platform Admin across salary, bank/payment, export, and audit pack endpoints.
- Backend: Ensure raw salary/bank fields are never serialized or logged unless the policy grants the field and an audit event is written.
---

### DEC-2026-05-17-1826-evidence-pack — MVP evidence pack format and retention
**Date**: 2026-05-17T18:26:18+08:00 | **Project**: sme-payroll-approval-saas-decision-capture | **Vote**: Accepted by Nik
**Decision**: Use a ZIP evidence pack for MVP containing a PDF summary plus structured CSV/JSON attachments and payment proof/reference files where available. Retain evidence packs and audit timeline records for 7 years by default.
**Key argument**: ZIP gives both human-readable audit review and structured data for accountant/auditor/system use without overbuilding a full retention engine.
**Dissent**: None; auto-purge, legal hold, and per-company retention configuration are deferred.
**Actions**:
- Product: Specify ZIP evidence pack contents in PRD/backlog.
- Backend: Store evidence pack record with file hash, sensitivity markers, generator, timestamp, and retention-until date.
- Security: Apply sensitive-field policy to generated pack and audit generation/download events.
---

### DEC-2026-05-17-1837-workspace-creation — MVP company workspace creator
**Date**: 2026-05-17T18:37:16+08:00 | **Project**: sme-payroll-approval-saas-decision-capture | **Vote**: Accepted by Nik
**Decision**: For MVP, Service Provider Admin creates SME company workspaces; SME owner self-service company registration is deferred to Phase 2.
**Key argument**: The first wedge targets service-provider/CoSec-style multi-company operations, so admin-created workspaces reduce onboarding scope while preserving tenant isolation.
**Dissent**: None.
**Actions**:
- Product: Update PAY-001 acceptance criteria to Service Provider Admin creation.
- Backend: Scope created companies under service-provider tenant and enforce company membership boundaries.
- Product: Defer SME self-service registration to Phase 2.
---

### DEC-2026-05-17-1842-user-onboarding — MVP user invitation and manual creation model
**Date**: 2026-05-17T18:42:59+08:00 | **Project**: sme-payroll-approval-saas-decision-capture | **Vote**: Accepted by Nik: Option 3
**Decision**: MVP supports both email invitations and Service Provider Admin manual user creation; manual users remain pending activation until account/password setup, and all role assignments are company-scoped and audited.
**Key argument**: Supporting both paths gives service providers practical onboarding flexibility while preserving security through pending activation, scoped roles, and audit logging.
**Dissent**: Security guardrail: manual creation must not create active accounts without user activation/password setup.
**Actions**:
- Product: Update PAY-002 acceptance criteria for invite plus manual creation.
- Backend: Model pending_activation users and multi-role company memberships.
- Security: Audit invite, manual creation, activation, and role assignment events.
---
