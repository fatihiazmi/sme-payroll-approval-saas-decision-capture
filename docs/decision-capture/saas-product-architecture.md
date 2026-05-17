# SME Payroll Approval SaaS: SaaS Expert Interview and Decision Capture

**Version:** 1.0  
**Date:** 2026-05-17  
**Status:** Draft for stakeholder review  
**Document Classification:** Internal - Product / Architecture Decision Capture  
**Scope:** SME Payroll Approval SaaS ERP + CoSec Console as B2B2B SaaS for CoSec firms managing SME companies in Malaysia

---

## 1. Purpose

This document captures the critical SaaS product and architecture decisions that SME Payroll Approval SaaS must validate before MVP implementation and commercial launch. It is written as an expert interview guide plus a reviewable decision register.

Each item includes:

- **Question:** What stakeholders must answer or confirm.
- **Why it matters:** Product, SaaS, compliance, architecture, or commercial impact.
- **Proposed default decision:** A recommended default based on SaaS best practice and current SME Payroll Approval SaaS context.
- **Review status:** All default recommendations were accepted by Nik on 2026-05-17T16:35:38+08:00.

---

## 2. Decision Status Legend

- **Accepted:** Proposed default exists but needs business, legal, security, or technical validation.
- **Accepted:** Decision approved for implementation.
- **Changed:** Default was modified after review.
- **Rejected:** Default was rejected and replaced by a different decision.

Current document status: **All decisions pending review.**

---

## 3. Executive SaaS Assumptions to Validate

- SME Payroll Approval SaaS is a **multi-tenant B2B2B SaaS** platform.
- The primary commercial buyer/channel is the **Company Secretary firm**.
- The operational end customer is the **SME company**.
- A CoSec tenant may manage many SME companies.
- SME companies own their business, payroll, banking, statutory, and document data.
- CoSec users require controlled access to assigned SME companies only.
- SME Payroll Approval SaaS must protect sensitive financial, payroll, bank, identity, and compliance data.
- Malaysia PDPA and auditability requirements are first-class product constraints.
- MVP must balance enterprise-grade security with fast onboarding and scalable unit economics.

---

## 4. SaaS Tenancy and Account Model

### DEC-SaaS-001: What is the primary tenant boundary?

**Question:** Should the primary tenant be the CoSec firm, the SME company, or a hybrid hierarchy?

**Why it matters:** Tenant boundary drives data isolation, billing, RBAC, audit logs, onboarding, support, exports, deletion, and future enterprise architecture. A wrong tenant model is expensive to change later.

**Proposed default decision:** Use a **hybrid hierarchy**:

- **Platform account:** SME Payroll Approval SaaS system operator.
- **CoSec tenant:** Primary SaaS workspace, commercial partner, and staff management boundary.
- **SME company workspace:** Data ownership and operational boundary under one or more CoSec relationships.
- Every sensitive business record must include `sme_company_id`; every CoSec-scoped operation must include `cosec_firm_id` and assignment checks.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-002: Can one SME company be linked to multiple CoSec firms?

**Question:** Should the same SME company record support multiple CoSec firms over time or concurrently?

**Why it matters:** Real-world SME-CoSec relationships can change. This affects ownership, transfer workflows, historical audit trails, duplicate company prevention, and billing responsibility.

**Proposed default decision:** Support **one active managing CoSec per SME company at MVP**, with a future-proof relationship table that supports transfer history:

- `sme_company`
- `cosec_firm`
- `cosec_sme_relationship`
  - `status`: invited, active, suspended, transferred, terminated
  - `effective_from`, `effective_to`
  - `role`: managing_cosec, viewer, auditor, former_cosec

Do not support concurrent multi-CoSec management in MVP unless a legal/commercial use case requires it.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-003: Who owns the SME data: CoSec, SME, or SME Payroll Approval SaaS?

**Question:** For documents, compliance records, bank data, payroll records, user actions, and derived AI outputs, who is the legal and operational data owner?

**Why it matters:** Data ownership determines consent, export rights, deletion rights, transfer rights, dispute handling, contractual terms, and PDPA obligations.

**Proposed default decision:** Treat the **SME company as the data owner/controller for its company data**, the **CoSec as an authorized processor/service provider for assigned SMEs**, and **SME Payroll Approval SaaS as SaaS processor/sub-processor** depending on contract structure. SME Payroll Approval SaaS owns platform telemetry, aggregated anonymized metrics, and system audit metadata, subject to privacy policy.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-004: Should tenant isolation be logical, physical, or hybrid?

**Question:** Should all tenants share one database with row-level isolation, have separate schemas/databases, or use a hybrid tiered model?

**Why it matters:** Isolation affects security, cost, scalability, backup/restore, support tooling, analytics, and enterprise sales readiness.

**Proposed default decision:** Use **logical isolation for MVP** with strong defense-in-depth:

- PostgreSQL shared database.
- Mandatory tenant columns.
- PostgreSQL Row-Level Security for critical tables.
- Application-level tenant middleware.
- Automated cross-tenant access tests.
- Audit logging of all privileged access.

Reserve **dedicated database/schema** for future enterprise/regulatory tiers.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-005: What is the canonical tenant context for every request?

**Question:** Should tenant context come from JWT claims, selected workspace, request path, database lookup, or a combination?

**Why it matters:** Tenant confusion is a top cause of multi-tenant data leaks. Context must be explicit, validated, and unspoofable.

**Proposed default decision:** Resolve tenant context server-side using authenticated user identity and membership records. JWT may include immutable identifiers for performance, but every privileged operation must validate:

- user identity
- active membership
- role
- assigned SME/company scope
- request resource ownership

Never trust client-supplied tenant IDs without authorization checks.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-006: How are internal SME Payroll Approval SaaS administrators isolated from customer tenants?

**Question:** Can platform admins impersonate tenants or directly access customer data?

**Why it matters:** Admin access is high risk for payroll, banking, identity, and company documents. It must be controlled for trust, PDPA, and auditability.

**Proposed default decision:** Implement **break-glass support access** only:

- No default unrestricted customer data browsing.
- Explicit support session with reason code, ticket reference, expiry, and customer/authorized CoSec approval where practical.
- Read-only by default.
- Full audit log visible to internal compliance and optionally customer admins.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 5. RBAC, Assignment, and Authorization

### DEC-SaaS-007: What role model should SME Payroll Approval SaaS use at MVP?

**Question:** Which user roles are required on day one, and which permissions do they carry?

**Why it matters:** RBAC must support CoSec operations, SME self-service, least privilege, and future enterprise customers without becoming too complex for MVP.

**Proposed default decision:** Use a small, explicit role set:

- **Platform Admin:** SME Payroll Approval SaaS operations; no routine customer data access.
- **CoSec Owner/Admin:** manages CoSec tenant, staff, billing, full portfolio access.
- **CoSec Manager:** manages assigned teams/SMEs.
- **CoSec Staff:** works only on assigned SME companies and permitted modules.
- **SME Owner/Admin:** manages SME users, company profile, data export, consent.
- **SME Staff:** upload/view permitted documents and tasks.
- **External Auditor/Viewer:** time-limited read-only access.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-008: Should CoSec staff access all SMEs or only assigned SMEs?

**Question:** Are CoSec staff granted portfolio-wide access by default, or must they be explicitly assigned to SME companies?

**Why it matters:** CoSec firms may have junior staff, outsourced workers, temporary staff, or role segregation needs. Payroll and banking data require least privilege.

**Proposed default decision:** Require **explicit assignment** for all non-admin CoSec staff. CoSec Admins can view the full portfolio, but operational staff can only access assigned SME companies and assigned modules.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-009: Should permissions be role-only or role-plus-policy?

**Question:** Should SME Payroll Approval SaaS use static roles only, or combine roles with contextual policies such as assignment, company status, module subscription, and approval state?

**Why it matters:** Static roles are simple but insufficient for multi-company portfolio access. Contextual authorization prevents accidental access escalation.

**Proposed default decision:** Use **RBAC plus policy checks**:

- Role grants general capability.
- Assignment grants SME scope.
- Subscription grants module availability.
- Data sensitivity grants extra restrictions.
- Company status controls write access.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-010: Which actions require maker-checker approval?

**Question:** Which sensitive actions require dual control or explicit approval?

**Why it matters:** Maker-checker workflows reduce fraud, accidental changes, and compliance risk for bank, payroll, statutory, and ownership data.

**Proposed default decision:** Require maker-checker for:

- Bank account changes.
- Payroll export/payment file generation.
- Beneficial ownership/director/shareholder changes.
- Compliance filing submission/finalization.
- Bulk imports that overwrite existing records.
- Tenant transfer or data deletion requests.

Allow lower-risk document uploads and routine task updates without maker-checker.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-011: Should SME users be able to restrict CoSec visibility?

**Question:** Can SME Admins hide specific categories like payroll, bank accounts, or director identity data from CoSec staff?

**Why it matters:** CoSec firms need enough access to perform services, but SMEs may expect granular consent and confidentiality controls.

**Proposed default decision:** Use **service-scope consent** rather than arbitrary hiding. SME Admins approve the service relationship and data categories needed. CoSec users then access only categories required for subscribed services and assignment.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 6. Security, Privacy, and Compliance

### DEC-SaaS-012: Which data classes are considered sensitive?

**Question:** What SME Payroll Approval SaaS data must receive enhanced protection, masking, access logging, and encryption controls?

**Why it matters:** Not all data needs the same controls. Data classification lets engineering focus security where risk is highest.

**Proposed default decision:** Classify as **Restricted/Sensitive**:

- Payroll data, salaries, EPF/SOCSO/tax identifiers.
- Bank account numbers and bank statements.
- IC/passport numbers and identity documents.
- Director/shareholder/beneficial owner data.
- Financial transactions and invoices.
- Statutory compliance filings and supporting documents.
- Authentication credentials, secrets, tokens.

Apply masking by default in UI and logs.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-013: What encryption model should be used for sensitive fields and documents?

**Question:** Should SME Payroll Approval SaaS use platform-level encryption only, field-level encryption, tenant-specific keys, or customer-managed keys?

**Why it matters:** Payroll, bank, and identity data may require stronger controls than normal SaaS data. Key strategy affects breach impact and enterprise readiness.

**Proposed default decision:** Use layered encryption:

- TLS 1.3 in transit.
- Managed encryption at rest for database and object storage.
- Field-level encryption for bank account numbers, identity numbers, and payroll-sensitive fields.
- KMS-managed keys with per-environment separation at MVP.
- Evaluate per-tenant keys for enterprise tier.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-014: How long should audit logs be retained?

**Question:** What audit events must be retained, for how long, and who can view them?

**Why it matters:** Audit logs support compliance, dispute resolution, incident investigation, and trust. Retention also has cost and privacy implications.

**Proposed default decision:** Retain security and compliance audit logs for **7 years**, aligned with business record expectations, unless legal counsel specifies otherwise. Log:

- authentication and MFA events
- role and permission changes
- SME-CoSec relationship changes
- sensitive data access
- document view/download/export
- bank/payroll/statutory data changes
- admin/support access
- data import/export/deletion

Make customer-visible audit trails available to CoSec Admins and SME Admins with appropriate scoping.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-015: What is SME Payroll Approval SaaS's PDPA operating posture?

**Question:** What PDPA obligations apply to SME Payroll Approval SaaS, CoSec firms, and SMEs, including consent, purpose limitation, access requests, correction, retention, breach response, and sub-processors?

**Why it matters:** SME Payroll Approval SaaS operates on personal and business-sensitive data in Malaysia. PDPA posture must be embedded in contracts, UX, data model, and operations.

**Proposed default decision:** Treat PDPA as a core product requirement:

- Capture consent and service-scope authorization during onboarding.
- Publish privacy notice and data processing terms.
- Maintain processor/sub-processor register.
- Support data access, correction, export, and deletion workflows.
- Define breach notification playbook.
- Keep purpose limitation metadata for sensitive data categories.

Legal review required before launch.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-016: Should MFA be mandatory?

**Question:** Which users must use MFA at MVP?

**Why it matters:** CoSec admins and SME admins can access or change highly sensitive data. MFA materially reduces account takeover risk.

**Proposed default decision:** Make MFA mandatory for:

- Platform Admins.
- CoSec Owner/Admins.
- Users with payroll, bank, or statutory filing privileges.

Offer optional MFA for all other users in MVP, then move toward mandatory MFA for all admin users.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-017: What session and token strategy should be used?

**Question:** Should sessions use localStorage JWTs, HTTP-only cookies, server-side sessions, or hybrid tokens?

**Why it matters:** Token storage affects XSS/CSRF exposure, session revocation, supportability, and security posture.

**Proposed default decision:** Use **secure HTTP-only cookies** for browser sessions, short-lived access tokens, refresh rotation, server-side session registry, device/session management, and immediate revocation on password reset or role change. Avoid storing tokens in localStorage.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-018: What data residency and backup requirements apply?

**Question:** Must all production data remain in Malaysia, ASEAN, or a specified cloud region? What are RPO/RTO targets?

**Why it matters:** Data residency can affect vendor choice, cloud region, backup storage, disaster recovery, and enterprise customer acceptance.

**Proposed default decision:** Host production in a region acceptable for Malaysian customers, document exact residency, and keep backups in controlled regions. Default targets:

- RPO: 15 minutes for core database.
- RTO: 4 hours for MVP production.
- Daily encrypted backups with restore tests.
- Separate retention policy for documents and audit logs.

Confirm with legal/commercial stakeholders.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 7. Pricing, Packaging, and Commercial Model

### DEC-SaaS-019: Who is the primary paying customer?

**Question:** Is the subscription paid by SME companies, CoSec firms, or both?

**Why it matters:** The payer determines pricing psychology, invoicing, collections, churn ownership, partner incentives, and onboarding flow.

**Proposed default decision:** Use **CoSec-led channel billing with SME-level pricing**:

- Price is calculated per active SME company.
- CoSec firm can either resell, pass through, bundle, or let SME Payroll Approval SaaS bill SMEs directly depending on partner agreement.
- MVP should support direct SME billing first if simpler, but commercial reporting must show CoSec commission/partner attribution.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-020: Should pricing remain RM199/month per SME?

**Question:** Is RM199/month per SME the right base package price for MVP and market entry?

**Why it matters:** Pricing must support gross margin, CoSec incentives, SME willingness to pay, and value capture versus manual compliance/admin costs.

**Proposed default decision:** Keep **RM199/month per SME** as the published MVP base price, with annual discount and partner commission. Validate through CoSec and SME discovery interviews before public launch.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-021: What should be packaged in the base plan?

**Question:** Which capabilities are included in base subscription versus paid add-ons?

**Why it matters:** Packaging controls adoption friction, margin, upsell path, and perceived fairness. Too much in base hurts revenue; too little slows activation.

**Proposed default decision:** Base plan includes:

- SME company workspace.
- CoSec assignment and portfolio visibility.
- Document upload and storage allowance.
- Basic OCR/extraction quota.
- Compliance deadline tracking.
- Basic financial summaries.
- Audit trail.
- Standard support.

Paid add-ons/tier upgrades:

- Advanced AI Copilot.
- Higher document/OCR volume.
- Payroll/bank integrations.
- Advanced analytics/export packs.
- White-label CoSec portal.
- Dedicated data isolation / enterprise security.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-022: Should CoSec firms receive commission, discount, or wholesale pricing?

**Question:** What partner incentive model best aligns CoSec firms to onboard and retain SMEs?

**Why it matters:** CoSec firms are the distribution engine. Misaligned incentives can cause low adoption or channel conflict.

**Proposed default decision:** Start with **25% recurring commission** on active paid SME subscriptions attributed to the CoSec partner, with tiered increases based on active SME count and retention quality. Revisit wholesale/bulk plans after pilot data.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-023: What defines an active billable SME?

**Question:** When does an SME become billable, and when does billing stop?

**Why it matters:** Billing ambiguity causes disputes with CoSec partners and SMEs. It also affects MRR accuracy.

**Proposed default decision:** An SME is billable when:

- onboarding is accepted, and
- trial period has ended, and
- company status is active, and
- subscription is not suspended/cancelled.

Billing pauses only for formal cancellation, suspension, or failed payment grace expiry. Archived/historical companies are non-billable but read-only with retention limits.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-024: Should there be a free trial or pilot model?

**Question:** What trial model should SME Payroll Approval SaaS offer to CoSec firms and SMEs?

**Why it matters:** Trials reduce adoption friction but can increase support load and non-paying usage. Pilot design must measure activation and value.

**Proposed default decision:** Offer a **30-day trial per SME** and a **structured CoSec pilot** for selected firms:

- 5-20 SME companies per pilot CoSec.
- Defined success criteria before pilot starts.
- Conversion checkpoint at day 21.
- Data export and deletion options if not converting.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-025: What usage limits protect margin?

**Question:** What limits should apply to document storage, OCR, AI Copilot, users, API calls, and support?

**Why it matters:** AI, OCR, storage, and support costs can erode margins. SaaS packaging needs fair-use controls.

**Proposed default decision:** Define base-plan fair-use limits:

- Included document pages/OCR per SME per month.
- Included storage per SME.
- Included users per SME and CoSec staff seats per partner tier.
- AI Copilot query quota if enabled.
- Paid overage or tier upgrade for sustained excess use.

Do not hard-enforce during early pilot; measure usage first, then calibrate limits.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 8. Onboarding, Imports, and Customer Success

### DEC-SaaS-026: What is the default onboarding path?

**Question:** Should onboarding be CoSec-led, SME self-service, assisted migration, or hybrid?

**Why it matters:** Onboarding determines activation rate, support effort, time-to-value, data quality, and sales scalability.

**Proposed default decision:** Use **CoSec-led assisted onboarding** for MVP:

1. CoSec firm setup.
2. CoSec admin invites staff.
3. CoSec imports or creates SME companies.
4. SME admin invited to verify company profile and consent.
5. Documents/imports uploaded.
6. First compliance tasks and dashboards activated.

Add SME self-service onboarding after workflows stabilize.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-027: What minimum data is required to activate an SME workspace?

**Question:** What is the smallest dataset needed before an SME can become active?

**Why it matters:** Too much required data slows activation; too little makes the product useless or risky.

**Proposed default decision:** Minimum activation data:

- Legal company name.
- Registration number.
- Company type/entity details.
- Primary contact and SME Admin.
- Managing CoSec relationship.
- Financial year-end / compliance calendar inputs.
- Consent/service authorization.

Optional after activation: historical documents, bank data, payroll data, transaction imports.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-028: What import methods are supported at MVP?

**Question:** Which data import channels should SME Payroll Approval SaaS support first?

**Why it matters:** Import quality drives activation and trust. Complex integrations can delay MVP.

**Proposed default decision:** MVP supports:

- CSV/XLSX imports for company profile, contacts, transactions, and compliance dates.
- Bulk document upload.
- Template-based import validation with preview and rollback.
- Manual correction queue for failed rows.

Defer deep accounting/payroll/bank integrations until after pilot usage validates need.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-029: How are imported records validated and approved?

**Question:** Should imported data immediately become production data, or require review before activation?

**Why it matters:** Bad imports can corrupt compliance deadlines, bank details, payroll, and financial reports.

**Proposed default decision:** Use a **staging import workflow**:

- Upload file.
- Validate structure and required fields.
- Detect duplicates and conflicts.
- Show preview and error report.
- Require confirmation before commit.
- For sensitive overwrites, require maker-checker approval.
- Keep import audit log and rollback metadata.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-030: How should SME consent be captured during onboarding?

**Question:** Does the SME need to explicitly authorize the CoSec firm and SME Payroll Approval SaaS to process specific data categories?

**Why it matters:** Consent and authority are central to privacy, data ownership, and dispute handling.

**Proposed default decision:** Capture explicit SME Admin acceptance of:

- SME Payroll Approval SaaS terms and privacy notice.
- CoSec service relationship.
- Data categories processed.
- Optional payroll/bank data processing consent.
- Data retention and export terms.

Store consent version, timestamp, actor, IP, and accepted document versions.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-031: What is the target time-to-value?

**Question:** Within what timeframe should a new CoSec firm and its first SME see useful value?

**Why it matters:** Time-to-value predicts activation, retention, referrals, and sales velocity.

**Proposed default decision:** MVP targets:

- CoSec tenant created: under 15 minutes.
- First SME workspace active: under 30 minutes with minimum data.
- First document processed: under 10 minutes after upload.
- First compliance dashboard visible: same day.
- First meaningful weekly portfolio report: within 7 days.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 9. Product Metrics and Operating Dashboard

### DEC-SaaS-032: What is the North Star metric?

**Question:** What single metric best represents recurring customer value for SME Payroll Approval SaaS?

**Why it matters:** A North Star metric aligns product, engineering, sales, customer success, and partner operations.

**Proposed default decision:** Use **Active Compliant SME Companies** as the North Star metric.

Definition: number of active paid SME companies with at least one current compliance workflow/document activity in the last 30 days and no overdue critical onboarding blocker.

Supporting metric: **Managed SMEs per active CoSec staff member** to measure CoSec productivity improvement.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-033: What activation metrics should be tracked?

**Question:** What events prove that a CoSec firm or SME has activated?

**Why it matters:** Activation is the bridge from sign-up to retention. Without clear activation metrics, onboarding cannot be optimized.

**Proposed default decision:** Track activation milestones:

**CoSec activation:**

- CoSec tenant created.
- Admin invited at least one staff member.
- First SME company created/imported.
- At least five SMEs added or one pilot batch completed.
- First portfolio dashboard viewed.

**SME activation:**

- SME Admin accepted invite/consent.
- Company profile completed.
- First document uploaded.
- First OCR/extraction completed.
- Compliance calendar generated.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-034: What retention and health metrics matter most?

**Question:** Which metrics indicate that CoSec firms and SMEs are getting recurring value?

**Why it matters:** Retention is the best early SaaS PMF signal. SME Payroll Approval SaaS should optimize retention before scaling acquisition.

**Proposed default decision:** Track:

- Logo retention by CoSec firm.
- SME company retention.
- Net revenue retention.
- Weekly active CoSec users.
- Monthly active SME companies.
- Documents processed per active SME.
- Compliance tasks completed on time.
- Portfolio overdue rate.
- Support tickets per 100 SMEs.
- Churn reason by CoSec and SME segment.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-035: What revenue metrics should be canonical?

**Question:** Which billing and revenue metrics should leadership trust?

**Why it matters:** SaaS companies need consistent definitions for MRR, ARR, churn, expansion, commission, and gross margin.

**Proposed default decision:** Establish a canonical SaaS revenue dashboard:

- Gross MRR.
- Net MRR after discounts/credits.
- ARR.
- Active billable SMEs.
- Average revenue per SME.
- CoSec commission payable.
- Gross margin by plan/cohort.
- Trial-to-paid conversion.
- Payment failure and recovery rate.
- Revenue churn and net revenue retention.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-036: What operational metrics protect service quality?

**Question:** What reliability, performance, and security metrics should be tracked from MVP?

**Why it matters:** SME Payroll Approval SaaS handles business-critical and sensitive data. Trust depends on measurable service quality.

**Proposed default decision:** Track MVP service metrics:

- Uptime and error rate by interface/API.
- P95 page and API latency.
- OCR processing latency and failure rate.
- Import success/failure rate.
- Cross-tenant access test results.
- Security events and failed login rate.
- Backup success and restore-test status.
- Audit log ingestion completeness.
- Incident count and time to resolution.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-037: What product analytics events are mandatory?

**Question:** Which event names and properties must be emitted from day one?

**Why it matters:** Retrofitting analytics later causes blind spots. Multi-tenant SaaS needs clean event taxonomy with privacy protection.

**Proposed default decision:** Instrument privacy-safe events for:

- tenant_created
- user_invited
- user_activated
- sme_created
- sme_import_started/completed/failed
- consent_accepted
- document_uploaded
- document_processed
- compliance_task_created/completed/overdue
- dashboard_viewed
- report_exported
- subscription_started/changed/cancelled
- payment_failed/recovered

Event properties must include non-sensitive IDs, tenant type, plan, role, and timestamps. Do not send PII, payroll, bank, or document contents to analytics tools.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 10. AI, Automation, and Data Use

### DEC-SaaS-038: Can customer data be used to train AI models?

**Question:** May SME Payroll Approval SaaS use SME documents, payroll, bank data, or compliance records to train models or improve extraction?

**Why it matters:** AI data use is legally and trust sensitive. Customers may reject the product if their confidential data is reused without clear consent.

**Proposed default decision:** Default to **no training on identifiable customer data**. Use customer data only to provide contracted services unless explicit opt-in is obtained. Allow aggregated, anonymized operational metrics for service improvement. Any model improvement dataset must be de-identified, access-controlled, and legally reviewed.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-039: What human review is required for AI outputs?

**Question:** Which AI/OCR/reconciliation outputs can be auto-applied, and which require review?

**Why it matters:** AI errors can create financial, compliance, and trust failures. Confidence thresholds and review queues are mandatory.

**Proposed default decision:** Use confidence-based automation:

- High-confidence low-risk extractions can be auto-suggested and marked as system-generated.
- Financial, payroll, bank, and statutory changes require human confirmation.
- Low-confidence outputs go to review queue.
- All AI-derived fields preserve source, confidence, reviewer, timestamp, and correction history.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 11. Support, Data Lifecycle, and Exit

### DEC-SaaS-040: What happens when an SME leaves a CoSec firm?

**Question:** How is access removed, data retained, transferred, or exported when the SME-CoSec relationship ends?

**Why it matters:** CoSec changes are common. SME Payroll Approval SaaS must avoid data hostage concerns and unauthorized former-provider access.

**Proposed default decision:** On termination/transfer:

- Immediately remove CoSec operational access unless retention/legal hold applies.
- Preserve historical audit trail.
- SME Admin can export company data.
- New CoSec relationship can be invited/accepted.
- Former CoSec may retain limited historical records only if legally/contractually required and explicitly scoped.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-041: What is the customer data export promise?

**Question:** What data can customers export, in what format, and how quickly?

**Why it matters:** Exportability builds trust, reduces procurement friction, and supports PDPA/data ownership obligations.

**Proposed default decision:** Provide customer export for:

- company profile
- users and assignments
- documents and metadata
- transactions and financial summaries
- compliance tasks/history
- audit trail relevant to customer scope

Formats: CSV/XLSX for structured data and original file format/PDF bundle for documents. Target export generation within 24 hours for normal accounts.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

### DEC-SaaS-042: What support model is included in each package?

**Question:** What support response times and channels are included for CoSec firms and SMEs?

**Why it matters:** Support cost directly affects gross margin and partner satisfaction. CoSec firms may expect higher-touch onboarding.

**Proposed default decision:** Base support:

- Email/in-app support for SMEs.
- Priority partner support for CoSec Admins.
- Assisted onboarding for pilot CoSec firms.
- Published severity definitions.
- Security/privacy incident channel.

Premium tiers may include dedicated customer success, faster SLAs, and migration assistance.

**Review status:** Accepted — default recommendation approved by Nik on 2026-05-17T16:35:38+08:00

---

## 12. Priority Review Agenda

Recommended order for stakeholder review:

1. **Tenant and data ownership:** DEC-SaaS-001 to DEC-SaaS-006.
2. **RBAC and sensitive access:** DEC-SaaS-007 to DEC-SaaS-011.
3. **Security and PDPA posture:** DEC-SaaS-012 to DEC-SaaS-018.
4. **Pricing and package model:** DEC-SaaS-019 to DEC-SaaS-025.
5. **Onboarding/import workflow:** DEC-SaaS-026 to DEC-SaaS-031.
6. **Metrics and analytics:** DEC-SaaS-032 to DEC-SaaS-037.
7. **AI and lifecycle decisions:** DEC-SaaS-038 to DEC-SaaS-042.

---

## 13. Open Review Checklist

Before accepting these decisions, confirm:

- Legal counsel has reviewed PDPA, data ownership, consent, and retention assumptions.
- CoSec pilot partners agree with tenancy, assignment, and commission defaults.
- SME representatives understand data export, consent, and access control model.
- Engineering confirms feasibility of RLS, tenant middleware, audit logging, and import staging in MVP scope.
- Finance confirms RM199 pricing, commission economics, gross margin assumptions, and fair-use limits.
- Customer success confirms onboarding time-to-value targets are realistic.
- Security confirms MFA, encryption, audit log, and admin access posture.

---

## 14. Decision Register Summary

All decisions are currently **Accepted**:

- DEC-SaaS-001: Hybrid tenant hierarchy.
- DEC-SaaS-002: One active managing CoSec per SME at MVP; relationship history supported.
- DEC-SaaS-003: SME owns company data; CoSec authorized processor/service provider; SME Payroll Approval SaaS processor/sub-processor.
- DEC-SaaS-004: Shared database with logical isolation/RLS for MVP; dedicated isolation as future enterprise option.
- DEC-SaaS-005: Server-side tenant context resolution and authorization checks.
- DEC-SaaS-006: Break-glass-only internal admin access.
- DEC-SaaS-007: Small explicit MVP role model.
- DEC-SaaS-008: Explicit SME assignment for non-admin CoSec staff.
- DEC-SaaS-009: RBAC plus contextual policy checks.
- DEC-SaaS-010: Maker-checker for sensitive changes.
- DEC-SaaS-011: Service-scope consent for CoSec visibility.
- DEC-SaaS-012: Payroll, bank, identity, financial, statutory, and credentials classified as sensitive.
- DEC-SaaS-013: Layered encryption with field-level encryption for highest-risk fields.
- DEC-SaaS-014: Seven-year security/compliance audit log retention, subject to legal review.
- DEC-SaaS-015: PDPA operating posture built into product, contracts, and operations.
- DEC-SaaS-016: Mandatory MFA for admins and sensitive privilege users.
- DEC-SaaS-017: HTTP-only secure cookie sessions; avoid localStorage tokens.
- DEC-SaaS-018: Documented data residency, RPO 15 minutes, RTO 4 hours.
- DEC-SaaS-019: CoSec-led channel billing with SME-level pricing.
- DEC-SaaS-020: RM199/month per SME retained as MVP base price pending validation.
- DEC-SaaS-021: Base plan plus AI/volume/security/analytics add-ons.
- DEC-SaaS-022: 25% recurring CoSec commission with future tiers.
- DEC-SaaS-023: Active billable SME definition.
- DEC-SaaS-024: 30-day SME trial and structured CoSec pilot.
- DEC-SaaS-025: Fair-use limits measured during pilot before hard enforcement.
- DEC-SaaS-026: CoSec-led assisted onboarding for MVP.
- DEC-SaaS-027: Minimum SME activation dataset defined.
- DEC-SaaS-028: CSV/XLSX and document bulk upload imports for MVP.
- DEC-SaaS-029: Staging import workflow with validation, preview, commit, audit, and rollback metadata.
- DEC-SaaS-030: Explicit SME consent capture with versioned records.
- DEC-SaaS-031: Time-to-value targets for CoSec and SME activation.
- DEC-SaaS-032: North Star metric: Active Compliant SME Companies.
- DEC-SaaS-033: CoSec and SME activation milestone tracking.
- DEC-SaaS-034: Retention and customer health metrics.
- DEC-SaaS-035: Canonical SaaS revenue dashboard.
- DEC-SaaS-036: Reliability, security, import, OCR, backup, and audit operational metrics.
- DEC-SaaS-037: Privacy-safe product analytics taxonomy.
- DEC-SaaS-038: No training on identifiable customer data by default.
- DEC-SaaS-039: Human review and confidence thresholds for AI outputs.
- DEC-SaaS-040: SME-CoSec termination/transfer lifecycle.
- DEC-SaaS-041: Customer data export promise.
- DEC-SaaS-042: Base and premium support model.
