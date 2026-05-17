
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
