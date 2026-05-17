# Prototype / Demo Flow Script — SME Payroll Approval SaaS

**Purpose:** Define the first clickable prototype flow. The demo should prove the complete business loop, not isolated screens.

## Demo Story

An SME owner needs to approve monthly payroll confidently. A payroll operator imports payroll rows, the system flags validation and OT exceptions, the owner approves, finance exports payment, and the system keeps an audit evidence pack.

## Primary Personas in Demo

- **Payroll Operator:** prepares and submits payroll.
- **SME Owner / Approver:** reviews totals, exceptions, and approves payment.
- **Finance User:** exports payment file and uploads proof.
- **Auditor:** views closed evidence pack.

## End-to-End Demo Path

### 1. Company Workspace

Show:

- Company switcher
- Company dashboard
- Current payroll period status

Proof point:

- User understands payroll is isolated per company.

### 2. Create Payroll Run

Show:

- Create payroll run for month/year/pay date
- Draft status
- Empty checklist

Proof point:

- Payroll work is controlled by pay period.

### 3. Import Payroll Rows

Show:

- Download template
- Upload spreadsheet
- Imported rows with totals
- Validation summary

Proof point:

- Operator can prepare payroll without full payroll engine replacement.

### 4. Resolve Validation Issues

Show:

- Missing/invalid bank account
- Negative net pay guard
- Duplicate employee guard
- Row-level issue list

Proof point:

- The product prevents obvious payroll mistakes before approval.

### 5. OT / Exception Review

Show:

- OT amount flagged
- Exception reason required
- Approve/mark reviewed action

Proof point:

- Risky changes are highlighted instead of hidden inside spreadsheets.

### 6. Submit to SME Owner

Show:

- Payroll summary
- Employee count, gross/net totals, OT total
- Submit for approval

Proof point:

- Operator cannot bypass owner approval.

### 7. Owner Approval Workbench

Show:

- Pending payroll run
- Summary cards
- Exception list
- Evidence checklist
- Approve or reject action

Proof point:

- Owner can approve based on evidence, not blind trust.

### 8. Payment Export and Proof

Show:

- Approved run enables payment export
- Payment file generated
- Upload payment proof
- Status advances to proof uploaded

Proof point:

- The product connects approval to payment execution evidence.

### 9. Audit Evidence Pack

Show:

- Timeline of import, validation, review, approval, export, proof upload
- Evidence documents
- Journal preview/export

Proof point:

- The system creates an audit trail useful to owner/accountant/auditor.

## Suggested Prototype Screens

1. Login / company switcher
2. Company dashboard
3. Payroll run list
4. Payroll run detail / import
5. Validation issue panel
6. OT exception review
7. Owner approval workbench
8. Payment export/proof upload
9. Audit evidence pack
10. User/role settings

## Demo Success Criteria

- A viewer can explain the product in one sentence after the demo.
- The demo completes the flow from payroll import to closed audit evidence.
- The demo makes MVP boundaries obvious: workflow/evidence layer, not full ERP.
- Sensitive payroll data access is visibly controlled.

