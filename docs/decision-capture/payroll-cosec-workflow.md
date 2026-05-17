# SME Payroll Approval SaaS — Expert Interview & Decision Capture

**Focus:** Malaysian payroll/OT + CoSec/service-provider managed SME workflow  
**Product framing:** SME Payroll Approval SaaS ERP + CoSec Console SaaS  
**Recommended wedge:** Multi-company payroll verification and approval console for CoSec/service-provider managed SMEs  
**Decision status:** All recommendations are **Proposed — Pending Nik/Client Review** unless explicitly confirmed later.

---

## 1. MVP Positioning Decision

**Expert view:** For Malaysian SMEs managed by CoSec/accounting/payroll service providers, the highest-value MVP is not a full ERP suite first. The strongest wedge is a **multi-company payroll verification, exception handling, statutory readiness, and approval console** that lets one service-provider team supervise payroll across many SMEs with lower compliance risk.

**Proposed SaaS MVP decision — Pending Nik/Client Review:**
- Build around the monthly payroll cycle: collect inputs → calculate draft payroll → flag exceptions → SME approval → service-provider finalization → statutory/payment export.
- Include EPF/KWSP, SOCSO/PERKESO, EIS, PCB/MTD readiness checks, OT verification, payroll approval audit trail, and multi-company portfolio status.
- Defer broad ERP modules unless needed to support payroll evidence, approvals, or compliance workflows.

---

## 2. Client Discovery Questions + Decision Capture

### A. Target Customer, Operating Model & Scope

#### Q1. Who is the primary paying customer for MVP: CoSec firm, outsourced payroll/accounting service provider, SME employer, or a hybrid?

**Why it matters:** Pricing, permissions, dashboard design, support model, and onboarding all change depending on whether SME Payroll Approval SaaS sells to service providers managing many SMEs or directly to individual SMEs.

**Recommended SaaS MVP decision:** Treat the **service provider / CoSec firm as the primary customer and workspace owner**, with SMEs as managed client companies who approve and supply payroll inputs.

**Client confirmation required:** Yes — confirm buyer, budget holder, and contractual owner.

#### Q2. Are CoSec firms actually responsible for payroll processing, or is payroll handled by a related accounting/payroll service team?

**Why it matters:** In Malaysia, company secretarial work and payroll outsourcing may be bundled commercially but operationally handled by different teams. The product language must match the real service-provider role.

**Recommended SaaS MVP decision:** Use neutral terminology in product: **Service Provider Console** with optional CoSec branding, rather than assuming every CoSec performs payroll directly.

**Client confirmation required:** Yes — confirm target firms’ real operating structure.

#### Q3. How many SMEs does a target service provider manage, and how many employees per SME are typical?

**Why it matters:** This determines portfolio dashboard design, batch processing, pricing tiers, database scale, review workload, and whether bulk actions are essential.

**Recommended SaaS MVP decision:** Optimize MVP for **many small companies**, e.g. 10–200 SMEs per provider, each with small-to-mid employee counts, rather than one large enterprise payroll.

**Client confirmation required:** Yes — collect actual pilot ranges.

#### Q4. Is SME Payroll Approval SaaS expected to calculate payroll end-to-end or verify payroll prepared elsewhere?

**Why it matters:** A calculation engine has higher compliance risk and implementation depth; a verification and approval console can launch faster while still solving service-provider pain.

**Recommended SaaS MVP decision:** MVP should support **calculated draft payroll + verification**, but position the first release as **payroll verification and approval workflow**, not a guaranteed statutory filing authority.

**Client confirmation required:** Yes — confirm liability appetite and whether clients expect final payroll calculation.

---

### B. Malaysian Statutory Payroll Compliance

#### Q5. Which statutory items must MVP handle on day one: EPF/KWSP, SOCSO/PERKESO, EIS, PCB/MTD, HRDF/levy, zakat, or others?

**Why it matters:** Malaysian payroll trust depends on statutory correctness. EPF, SOCSO, EIS, and PCB are table/rule-driven and must be auditable.

**Recommended SaaS MVP decision:** Include **EPF/KWSP, SOCSO/PERKESO, EIS, and PCB/MTD readiness checks** in MVP. Defer HRDF, zakat, and special levies unless pilot clients require them.

**Client confirmation required:** Yes — confirm statutory scope by pilot customer.

#### Q6. Should SME Payroll Approval SaaS generate statutory submission/payment files, or only provide calculated amounts and reports?

**Why it matters:** File generation requires exact format support, version tracking, and operational liability. Reports are easier but may not reduce enough admin effort.

**Recommended SaaS MVP decision:** Provide **export-ready statutory summaries and payment schedules** first; add official bank/portal file formats as a controlled Phase 2 after pilot validation.

**Client confirmation required:** Yes — identify required banks, portals, and formats.

#### Q7. How should statutory rate and table updates be governed?

**Why it matters:** EPF, SOCSO, EIS, PCB tables and tax treatment can change. Hardcoded rules create maintenance and compliance risk.

**Recommended SaaS MVP decision:** Implement a **versioned statutory rules table** with effective dates, change audit, and admin-only updates. Payroll runs must store the rule version used.

**Client confirmation required:** Yes — confirm who signs off rule updates operationally.

#### Q8. Should payroll support different employee categories: Malaysian citizen, permanent resident, foreign worker, intern, part-time, director, contractor?

**Why it matters:** Contribution obligations and tax handling differ by worker category. Misclassification is a common payroll compliance failure.

**Recommended SaaS MVP decision:** MVP must capture employee category and flag unsupported categories. Start with **local employees and standard foreign workers** only if pilot requires foreign staff; defer complex director/contractor handling unless urgent.

**Client confirmation required:** Yes — confirm pilot employee population.

#### Q9. Is PCB/MTD expected to be calculated natively or integrated/verified against LHDN/e-PCB outputs?

**Why it matters:** PCB is sensitive and depends on tax profile, reliefs, benefits, deductions, prior remuneration, and marital/dependent data.

**Recommended SaaS MVP decision:** For MVP, provide **PCB field capture, variance checks, and export/report support**; native PCB calculation can be introduced only after detailed LHDN schedule validation.

**Client confirmation required:** Yes — confirm expected PCB depth and acceptable fallback.

---

### C. Overtime, Attendance & Employment Act Rules

#### Q10. Which employees are eligible for statutory overtime under the Employment Act rules in the target client base?

**Why it matters:** OT rules commonly apply to employees earning up to the statutory threshold and to specific covered employee categories. Incorrect eligibility causes overpayment or non-compliance.

**Recommended SaaS MVP decision:** Capture **OT eligibility per employee** and default using monthly wage threshold logic, with manual override and audit reason.

**Client confirmation required:** Yes — confirm legal interpretation and client policy.

#### Q11. What OT scenarios must MVP support: normal day, rest day, public holiday, off day, shift work, standby, call-back, meal allowance?

**Why it matters:** Malaysian OT multipliers differ by day type and work pattern. SMEs often have custom allowances layered on top.

**Recommended SaaS MVP decision:** MVP supports **normal day OT, rest day OT, and public holiday OT** with configurable multipliers. Defer standby/call-back/shift allowance unless pilot requires.

**Client confirmation required:** Yes — confirm required OT scenarios.

#### Q12. What source of attendance/OT evidence will be used: manual timesheet, biometric device, Excel, WhatsApp approval, HR system, or manager entry?

**Why it matters:** The workflow must match reality. Service providers often receive messy monthly input files rather than clean attendance integrations.

**Recommended SaaS MVP decision:** MVP supports **Excel/CSV upload + manual adjustment + evidence attachment**. Integrations with biometric/attendance systems should be Phase 2.

**Client confirmation required:** Yes — collect sample input files.

#### Q13. Who approves OT before payroll: SME owner, line manager, service-provider payroll officer, or all of them?

**Why it matters:** Payroll approval auditability is the product’s defensibility. Without clear responsibility, disputes become service-provider risk.

**Recommended SaaS MVP decision:** Require **SME-side approval for OT inputs** before final payroll, with service-provider review and exception notes.

**Client confirmation required:** Yes — confirm approval authority.

#### Q14. How should SME Payroll Approval SaaS handle OT exceptions: missing clock-out, excessive OT, public holiday mismatch, employee not OT-eligible, unusual multiplier?

**Why it matters:** Exception handling is where multi-company payroll consoles create real value. Reviewers need to focus on risky payroll items first.

**Recommended SaaS MVP decision:** Build an **OT exception queue** with severity, reason, affected employee, payroll impact, and required reviewer action.

**Client confirmation required:** Yes — confirm exception thresholds and severity rules.

---

### D. Payroll Inputs, Adjustments & Monthly Closing

#### Q15. What recurring and ad hoc payroll components must MVP support: basic salary, unpaid leave, allowance, commission, bonus, claims, advance, deduction, loan, arrears?

**Why it matters:** Payroll is not just salary. Components affect statutory contributions differently and must be classified correctly.

**Recommended SaaS MVP decision:** MVP supports configurable payroll components with statutory treatment flags: **EPF-subject, SOCSO-subject, EIS-subject, PCB-subject, taxable/non-taxable, recurring/ad hoc**.

**Client confirmation required:** Yes — collect component list from pilot clients.

#### Q16. Should claims and reimbursements be inside payroll MVP or only imported as approved amounts?

**Why it matters:** Claims workflow can become a full module. Including it too early may dilute the payroll wedge.

**Recommended SaaS MVP decision:** MVP imports **approved claim/reimbursement totals** as payroll components; full claims approval workflow is Phase 2.

**Client confirmation required:** Yes — confirm whether claims are a must-have for pilot.

#### Q17. How are unpaid leave, late arrival, absence, and salary proration currently calculated?

**Why it matters:** SMEs vary by policy. Formula disputes are common during monthly payroll review.

**Recommended SaaS MVP decision:** Provide configurable proration formulas but default to transparent Malaysian SME-friendly settings; show calculation breakdown in payroll preview.

**Client confirmation required:** Yes — confirm default formula and rounding policy.

#### Q18. Is payroll processed on a fixed monthly cycle, multiple cycles, or ad hoc cycles?

**Why it matters:** Cycle design impacts cut-off dates, approval reminders, statutory deadlines, and locking rules.

**Recommended SaaS MVP decision:** MVP supports **one regular monthly payroll cycle per SME** with configurable cut-off date; multiple cycles are Phase 2.

**Client confirmation required:** Yes — confirm common payroll calendars.

#### Q19. What should happen after payroll is approved: lock period, generate payslips, bank payment file, statutory reports, accounting journal?

**Why it matters:** The finalization step determines integration points and user-perceived completeness.

**Recommended SaaS MVP decision:** On approval, MVP should **lock payroll**, generate employee payslips, produce payroll summary, statutory contribution summary, and accounting journal export. Bank file generation can be Phase 2 unless pilot requires.

**Client confirmation required:** Yes — confirm must-have post-approval outputs.

---

### E. CoSec / Service-Provider Workflow

#### Q20. What is the current monthly service-provider workflow from data request to client approval?

**Why it matters:** The product must reduce coordinator work: chasing documents, checking data, sending drafts, getting approvals, and storing evidence.

**Recommended SaaS MVP decision:** Model the workflow as statuses: **Not Started → Awaiting SME Inputs → Draft Payroll Ready → Exceptions Found → Awaiting SME Approval → Approved → Finalized → Exported**.

**Client confirmation required:** Yes — validate real workflow states.

#### Q21. How does the service provider manage client communications today: email, WhatsApp, shared drive, Excel, payroll software portal?

**Why it matters:** Adoption depends on replacing or complementing existing communication habits. WhatsApp/email evidence often needs structured capture.

**Recommended SaaS MVP decision:** MVP includes **in-app requests, comments, evidence attachments, and approval log**. External messaging integrations are Phase 2.

**Client confirmation required:** Yes — confirm communication channels and evidence needs.

#### Q22. What portfolio-level view does a payroll manager need across all SMEs?

**Why it matters:** The CoSec/service-provider wedge is the ability to see which clients are blocked, risky, overdue, or ready for approval.

**Recommended SaaS MVP decision:** Build a **multi-company payroll control tower** showing each SME’s payroll month, status, blockers, exception count, pending approver, and statutory readiness.

**Client confirmation required:** Yes — confirm dashboard columns and priority filters.

#### Q23. What internal service-provider roles are needed: partner/admin, payroll manager, payroll executive, reviewer, client relationship manager?

**Why it matters:** Role-based permissions and maker-checker controls are essential for payroll confidentiality and quality assurance.

**Recommended SaaS MVP decision:** MVP roles: **Provider Admin, Payroll Manager/Reviewer, Payroll Processor, SME Approver, SME Viewer**.

**Client confirmation required:** Yes — confirm authority matrix.

#### Q24. Is maker-checker review required before payroll is sent to SME for approval?

**Why it matters:** Service providers often need internal QA to prevent payroll errors reaching clients.

**Recommended SaaS MVP decision:** Include optional **internal review gate** before SME approval. Allow small providers to disable it per workspace.

**Client confirmation required:** Yes — confirm workflow control preference.

#### Q25. What audit evidence must be retained for each payroll run?

**Why it matters:** Payroll disputes require proof of inputs, changes, approvals, calculations, and final outputs.

**Recommended SaaS MVP decision:** Store immutable audit trail: input files, employee/component changes, calculation version, exception resolutions, approval timestamps, final reports, and export history.

**Client confirmation required:** Yes — confirm retention period and audit requirements.

---

### F. Data Migration, Integrations & Imports

#### Q26. Where does employee master data come from initially: Excel, existing payroll software, SQL system, HR platform, or manual entry?

**Why it matters:** Onboarding friction can kill adoption. Service providers need fast company setup across many SMEs.

**Recommended SaaS MVP decision:** MVP supports **Excel employee import with validation and error report**. Direct integrations are Phase 2.

**Client confirmation required:** Yes — collect sample employee master files.

#### Q27. Must SME Payroll Approval SaaS integrate with accounting systems such as SQL Accounting, AutoCount, Xero, QuickBooks, or Bukku?

**Why it matters:** Payroll journals often need to flow into accounting, but full two-way integration may be unnecessary for MVP.

**Recommended SaaS MVP decision:** MVP provides **CSV journal export by account code and cost center**. Native accounting integrations are Phase 2 based on pilot demand.

**Client confirmation required:** Yes — confirm top accounting systems used.

#### Q28. Are bank payment files required for salary disbursement in MVP?

**Why it matters:** Bank formats vary, and payment responsibility may remain with SME employers.

**Recommended SaaS MVP decision:** MVP provides salary payment listing and approval report; bank file generation is **optional pilot-specific add-on**.

**Client confirmation required:** Yes — identify banks and payment process owner.

---

### G. Security, Privacy, Compliance & Liability

#### Q29. Who is allowed to view salary data within the SME and service-provider organizations?

**Why it matters:** Payroll data is highly sensitive. Poor access control will block adoption and may create PDPA risk.

**Recommended SaaS MVP decision:** Enforce strict role-based access, company-level isolation, salary-data permissions, and audit logs for every view/export.

**Client confirmation required:** Yes — confirm salary confidentiality policy.

#### Q30. What is the service provider’s liability position if SME Payroll Approval SaaS calculates payroll incorrectly?

**Why it matters:** This determines legal disclaimers, approval language, verification UX, and whether calculations need certified review.

**Recommended SaaS MVP decision:** MVP should present calculations as **draft payroll requiring authorized human review and approval**. Keep final responsibility explicitly assigned in workflow.

**Client confirmation required:** Yes — legal/commercial review required.

#### Q31. What payroll data retention period is required?

**Why it matters:** Retention affects storage, deletion workflows, audit exports, and PDPA compliance.

**Recommended SaaS MVP decision:** Default to configurable retention with export-before-delete capability; do not hard-delete approved payroll runs without admin/legal workflow.

**Client confirmation required:** Yes — confirm statutory/legal retention expectations.

---

### H. Pricing, Packaging & MVP Success

#### Q32. Should pricing be per SME, per employee, per payroll run, or service-provider tier?

**Why it matters:** Pricing must align with service-provider economics and avoid penalizing growth unfairly.

**Recommended SaaS MVP decision:** Use **service-provider tier + per active employee or per active SME band**. Keep pricing predictable for multi-company operators.

**Client confirmation required:** Yes — validate willingness to pay.

#### Q33. What is the strongest measurable MVP success metric?

**Why it matters:** Without a sharp success metric, SME Payroll Approval SaaS risks becoming a broad ERP backlog instead of a validated wedge.

**Recommended SaaS MVP decision:** Track: **payroll cycle time reduced**, **number of SMEs managed per payroll executive**, **exception resolution time**, **approval turnaround time**, and **payroll error/rework rate**.

**Client confirmation required:** Yes — agree baseline and target numbers.

#### Q34. What must be true after a 60–90 day pilot for the client to continue paying?

**Why it matters:** This identifies the real adoption gate and prevents overbuilding non-critical modules.

**Recommended SaaS MVP decision:** Pilot success condition: service provider can process monthly payroll for selected SMEs with visible status, fewer manual follow-ups, documented approvals, and exportable statutory/payroll summaries.

**Client confirmation required:** Yes — define pilot acceptance criteria.

---

## 3. Recommended MVP Feature Boundary

### Include in MVP

- Service-provider workspace managing multiple SME company payrolls.
- SME company profile and employee master data import.
- Monthly payroll cycle status workflow.
- Payroll component setup with statutory treatment flags.
- Basic salary, allowances, deductions, unpaid leave, OT, claims-import components.
- OT calculation support for normal day, rest day, and public holiday scenarios.
- EPF/KWSP, SOCSO/PERKESO, EIS, PCB/MTD readiness checks and summaries.
- Exception queue for payroll and OT anomalies.
- Maker-checker internal review option.
- SME approval workflow with audit trail.
- Payslip generation, payroll summary, statutory summary, and accounting journal CSV export.
- Role-based access and immutable payroll run audit log.

### Defer / Phase 2

- Full claims module.
- Native biometric/attendance integrations.
- Direct statutory portal submission.
- Bank-specific payment file generation unless required by pilot.
- Full native PCB engine until validated against detailed LHDN requirements.
- HRDF/zakat/special levy support unless pilot-specific.
- Multi-cycle payroll, advanced shift scheduling, complex director/contractor tax treatment.
- Full accounting system integrations.
- WhatsApp/email automation integrations.

---

## 4. High-Risk Unknowns Requiring Immediate Client Review

1. Whether the real buyer is CoSec, payroll bureau, accounting firm, or SME employer.
2. Whether MVP is expected to calculate payroll fully or verify and approve payroll inputs/results.
3. Required statutory depth for EPF, SOCSO, EIS, and PCB.
4. Actual OT policies and covered employee categories.
5. Required payroll input formats and sample Excel files.
6. Approval authority between service provider and SME.
7. Liability model for payroll mistakes.
8. Required output formats: payslips, statutory summaries, bank files, accounting journals.
9. Salary data access rules and retention requirements.
10. Pilot success metrics and willingness to pay.

---

## 5. Proposed Decision Register

### DR-001 — MVP wedge

**Decision:** Multi-company payroll verification and approval console for service-provider managed SMEs.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Stronger, narrower wedge than broad ERP; directly supports CoSec/service-provider portfolio operations.

### DR-002 — Primary workspace owner

**Decision:** Service provider / CoSec firm owns the SaaS workspace; SMEs participate as managed approval users.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Matches B2B2B operating model and portfolio dashboard value.

### DR-003 — Payroll engine stance

**Decision:** MVP provides draft calculation, verification, exception handling, and approval; final payroll remains human-approved.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Reduces compliance liability while enabling practical automation.

### DR-004 — Statutory scope

**Decision:** MVP covers EPF/KWSP, SOCSO/PERKESO, EIS, and PCB/MTD readiness/reporting; defer other statutory items unless pilot requires.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** These are table-stakes for Malaysian payroll trust.

### DR-005 — OT scope

**Decision:** MVP supports normal day, rest day, and public holiday OT with configurable eligibility and multipliers.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Covers core Malaysian OT scenarios without overbuilding shift/standby complexity.

### DR-006 — Input strategy

**Decision:** Start with Excel/CSV imports and manual adjustments with attachments; integrations later.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Fastest path to match existing SME/service-provider workflows.

### DR-007 — Approval model

**Decision:** Payroll must pass service-provider review and SME authorized approval before finalization.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Protects service provider, creates audit trail, and clarifies accountability.

### DR-008 — Outputs

**Decision:** MVP outputs payslips, payroll summary, statutory contribution summary, and accounting journal CSV.  
**Status:** Proposed — Pending Nik/Client Review.  
**Rationale:** Provides enough operational closure without committing to every bank/statutory file format.

---

## 6. Suggested Next Client Interview Agenda

1. Confirm target buyer and operational role: CoSec vs payroll/accounting service provider.
2. Walk through one real monthly payroll cycle from data request to final payment.
3. Collect sample files: employee master, attendance/OT, payroll adjustment sheet, payslip, statutory report.
4. Validate statutory and OT scope for pilot employees.
5. Confirm approval authority and audit requirements.
6. Agree MVP outputs and what can be deferred.
7. Define 60–90 day pilot success metrics.

---

## 7. Expert Recommendation Summary

SME Payroll Approval SaaS should avoid starting as a generic SME ERP. The sharper Malaysian SaaS opportunity is a **service-provider control tower for payroll compliance and approvals across many SMEs**. Payroll/OT is painful, recurring, regulated, and operationally repetitive — ideal for SaaS workflow automation. The MVP should reduce manual follow-ups, expose exceptions, preserve approval evidence, and produce clean payroll/statutory outputs while keeping final compliance decisions human-reviewed until the rule engine is proven.
