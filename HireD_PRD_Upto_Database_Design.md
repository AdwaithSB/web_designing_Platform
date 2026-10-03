# HireD Phase 1 MVP — Product Requirements Document

**Version:** 0.1  
**Status:** Draft  
**Scope:** Phase 1 MVP

---

## 1. Product Overview

HireD is a B2B platform intended to digitize the operational workflow from receiving a partner order through trip execution, driver payout, and month-end statement/invoice generation.

The Phase 1 MVP focuses on replacing manual operational processes with a structured workflow while keeping the existing team's business process familiar.

### Core product flow

```text
Order → Admin Review → Accepted Order → Trip → Driver Assignment
→ Trip Execution → Trip Closure → Driver Payout
→ Month-end Statement/Invoice → Two-person Verification → Published to Partner
```

---

## 2. Problem Statement

The current B2B operational process involves manual handling of orders, trip assignment, trip execution, driver expenses, payout calculation, and month-end invoicing.

HireD should provide a centralized workflow for these activities while preserving the existing business process.

---

## 3. Product Goals

The Phase 1 MVP should:

1. Digitize the workflow from order intake to trip completion.
2. Allow B2B partners to create and track orders.
3. Allow Admins to accept or reject orders.
4. Convert accepted orders into operational trips.
5. Allow Admins to assign and reassign drivers.
6. Allow Drivers to manage availability and complete assigned trips.
7. Record trip expenses.
8. Close trips using OTP verification and required trip-close information.
9. Calculate driver/company payout shares automatically.
10. Credit the driver's wallet after successful trip closure.
11. Generate month-end company statements/invoices.
12. Require two-person verification before publishing a statement.
13. Maintain appropriate data-access boundaries.
14. Maintain an audit trail for important operational and financial changes.

---

# 4. Users and Roles

## 4.1 Admin

Admins manage the operational workflow.

Responsibilities:

- View and manage orders
- Create orders on behalf of partners
- Accept/reject orders
- Set or confirm trip amounts
- Assign/reassign drivers
- Manage driver onboarding
- Manage companies
- Configure company payout splits
- Monitor ongoing trips
- Generate statements/invoices
- Perform statement verification
- View reports

Phase 1 does not define multiple Admin permission tiers.

## 4.2 B2B Partner

A partner represents a company using HireD.

Responsibilities:

- Log in
- Create orders
- View their company's orders
- Track trips
- View trip status/ETA
- View trip reports
- View verified/published statements
- Receive relevant notifications

A partner must only access their own company's data.

## 4.3 Driver

Drivers execute assigned trips.

Responsibilities:

- Log in
- Complete onboarding information
- Upload required documents
- Set availability
- View assigned trips
- View trip details
- Start trips
- Record expenses
- Complete trip closure
- View trip history
- View wallet transactions/balance
- Update documents

A driver must only access their own driver-related information and assigned operational data.

---

# 5. Phase 1 Scope

## 5.1 In Scope

### Admin Web

- Admin authentication
- Order inbox
- Admin-created orders
- Order acceptance/rejection
- Trip amount management
- Ongoing trip monitoring
- Driver directory
- Driver assignment/reassignment
- Driver onboarding
- Company management
- Company payout configuration
- Payroll settings
- Month-end statement/invoice generation
- Two-person statement verification
- Reports

### B2B Partner Portal

- Partner authentication
- Order creation
- Order list
- Trip list
- Trip status and ETA
- Trip report
- Verified statement access
- Notifications

### Driver App

- Driver authentication
- Document upload
- Availability management
- Trip list/detail
- Selfie/start workflow
- Expense recording
- OTP-based trip closure
- Trip history
- Document updates
- Wallet

### Automation

- Payout calculation
- Wallet credit after successful trip closure
- Month-end statement generation
- Statement verification workflow

## 5.2 Out of Scope

- Formal automated document verification
- Automated commission/rate determination
- Multiple Admin permission tiers
- Payment gateway integration
- Driver accept/reject functionality
- Real-time location tracking unless separately confirmed
- Automated expense reimbursement unless separately confirmed

---

# 6. Core Business Entities

The product requires these major business concepts:

| Entity | Purpose |
|---|---|
| Company | B2B partner company |
| User | Authentication identity for Admins, Partners, and Drivers |
| Driver | Driver profile and operational information |
| Driver Document | Uploaded driver documents |
| Driver Availability | Driver availability records |
| Order | Incoming request before acceptance |
| Trip | Operational job created from an accepted order |
| Driver Assignment | Assignment/reassignment history |
| Trip Expense | Expense recorded during a trip |
| Trip Closure | Information generated when a trip closes |
| Trip Payroll | Final payout calculation and applied rate snapshot |
| Wallet Transaction | Driver wallet movement |
| Statement/Invoice | Month-end company financial statement |
| Statement Trip | Trips included in a statement |
| Statement Verification | Admin verification records |
| Audit Log | History of important changes |

These concepts form the basis for the later database design.

---

# 7. End-to-End Workflow

## 7.1 Order Intake

Orders can enter HireD through:

- B2B partner portal
- Admin entry for phone/WhatsApp requests

An order contains:

- Partner company
- Pickup location
- Drop location
- Scheduled date/time
- Vehicle/service requirements
- Contact information
- Order source

Initial status:

```text
RECEIVED
```

## 7.2 Order Review

An Admin reviews the order.

```text
RECEIVED → ACCEPTED
RECEIVED → REJECTED
```

A rejected order should record the rejection reason.

Only an accepted order can become a trip.

## 7.3 Trip Creation

An accepted order becomes an operational trip.

A trip contains:

- Source order
- Trip amount
- Estimated duration/ETA
- Status
- Start time
- Close time
- Trip-close OTP information

Relationship:

```text
One Order → Zero or One Trip
```

---

# 8. Driver Management

## 8.1 Driver Onboarding

A driver has:

- Profile information
- Login identity
- Active/inactive status
- Online/offline state

Required Phase 1 documents:

- Driver License
- Aadhar Card
- PAN Card
- Police Clearance Certificate (PCC)

Drivers may upload newer document versions while the system identifies the current version.

## 8.2 Driver Availability

Drivers can manage:

- Online/offline state
- Planned availability/unavailability by date

Conflicting availability records for the same driver/date should not be allowed.

---

# 9. Driver Assignment

Admin assigns a driver to a trip.

The system must support reassignment and preserve:

- Assigned driver
- Admin who made the assignment
- Assignment start time
- Assignment end time

The product should preserve the complete assignment timeline rather than only the current driver.

---

# 10. Trip Lifecycle

```text
UNASSIGNED
     ↓
ASSIGNED
     ↓
ONGOING
     ↓
CLOSED
```

A closed trip should not move backwards.

---

# 11. Trip Execution

The execution workflow is:

1. Driver views trip details.
2. Driver completes the required start/selfie workflow.
3. Trip becomes ongoing.
4. Driver records applicable expenses.
5. Partner-provided OTP is used for closure.
6. Driver submits the OTP.
7. Trip closes.
8. Trip report becomes available.
9. Payout is calculated.
10. Driver wallet is credited.

---

# 12. Trip Expenses

A trip may contain multiple expenses.

Each expense should record:

- Trip
- Driver
- Amount
- Description
- Creation time

Expenses are displayed in the trip report.

For Phase 1, expenses are not included in the driver/company commission split.

Expense reimbursement is not currently defined and should not be implemented as a payout until confirmed.

---

# 13. Trip Closure

A successful closure requires:

1. Required trip execution information is completed.
2. Expenses are recorded where applicable.
3. Driver enters the correct OTP.
4. OTP is validated.
5. Trip becomes `CLOSED`.
6. Closure information is stored.
7. Trip report is generated/updated.
8. Payout is calculated.
9. Driver wallet is credited.

The process must prevent accidental duplicate payouts if the closure request is retried.

---

# 14. OTP Requirements

- OTP is numeric.
- Authorized partner users can access the OTP according to the product workflow.
- Driver submits the OTP through the driver application.
- OTP has an expiration time.
- Plaintext OTP must not be stored persistently.
- The submitted OTP must be securely verified.

---

# 15. Payout Rules

The payout model uses the partner company's configured split.

Example:

```text
Driver share: 30%
Company share: 70%
```

The percentages must total 100%.

There is no per-driver payout rate in Phase 1.

Admin configures the company's current payout split.

---

# 16. Historical Payout Rate

Changing a company's current payout configuration must not change historical trip payouts.

Example:

```text
Current rate: Driver 30% / Company 70%

Trip A closes → 30% / 70%

Rate later changes → Driver 40% / Company 60%

Trip A remains → 30% / 70%
```

Therefore, the rate actually applied when a trip closes must be preserved with that trip's payout information.

---

# 17. Payroll Calculation

When a trip closes, HireD calculates:

- Driver share
- Company share
- Total trip amount

Rules:

```text
Driver Rate + Company Rate = 100%
Driver Share + Company Share = Trip Amount
```

The applied rates and calculated amounts should be retained as part of the trip payroll record.

---

# 18. Driver Wallet

The wallet is transaction-based.

```text
Trip Closed
    ↓
Payroll Calculated
    ↓
Wallet Credit Created
```

A closed trip should create at most one trip-credit transaction.

The transaction history should be traceable, and the ledger should be the authoritative source for the balance.

---

# 19. Month-End Statement / Invoice

At month end, HireD generates a statement/invoice for a partner company.

A statement represents:

- One company
- One calendar month
- Closed trips in the reporting period
- Total trip amount
- Total driver share
- Total company share
- Verification status

There should be no duplicate statement for the same company/month.

---

# 20. Statement Trip Inclusion

A statement contains the relevant closed trips.

Each included trip should retain enough information to reproduce the statement calculation, including:

- Trip amount
- Applied driver rate
- Driver share
- Company share

Published statements should remain historically reliable.

---

# 21. Two-Person Verification

A generated statement must pass two-person verification before publication.

```text
Statement Creator
       +
Different Admin
       ↓
VERIFIED
       ↓
PUBLISHED
```

Rules:

- The creator verifies the statement.
- A different Admin performs the second verification.
- The two users must be different.
- Both verification records must exist before `VERIFIED`.
- A statement cannot be `PUBLISHED` before verification.

---

# 22. Statement Lifecycle

```text
DRAFT
  ↓
PENDING_VERIFICATION
  ↓
VERIFIED
  ↓
PUBLISHED
```

Partners can access statements according to the publication/access rules.

---

# 23. Audit Requirements

The following actions require an audit trail:

- Company payout split changes
- Trip amount changes
- Driver assignment/reassignment
- Statement/invoice verification

An audit record should identify:

- Actor/user
- Entity affected
- Action
- Previous values where applicable
- New values where applicable
- Timestamp

Audit history should not be overwritten.

---

# 24. Security and Data Access

## Authentication

The system authenticates Admins, Partners, and Drivers.

Passwords must never be stored as plaintext.

## Partner Isolation

A partner can access only:

```text
Own Company
   ↓
Own Orders
   ↓
Own Trips
   ↓
Own Reports
   ↓
Own Verified/Published Statements
```

## Driver Isolation

A driver can access only their:

- Profile
- Documents
- Availability
- Assigned trips
- Expenses
- Trip history
- Wallet transactions

## Admin Access

Phase 1 Admins have full operational visibility. There are no Admin permission tiers in Phase 1.

---

# 25. File Storage

Driver documents and trip-close selfies should use secure object/file storage rather than binary storage directly in the relational database.

The application should retain a secure reference to each file.

---

# 26. Notifications

Potential notification channels include:

- Email
- Push
- SMS

The exact notification events and delivery rules are not fully defined yet and should be confirmed before implementing a dedicated notification data model.

---

# 27. Non-Functional Requirements

## Data Integrity

The system should prevent:

- Invalid payout percentages
- Duplicate wallet credits
- Duplicate monthly statements
- Conflicting driver availability
- Multiple successful closures for one trip

## Financial Accuracy

Payout calculations must be deterministic and traceable. Historical trip payouts must not change when future company payout settings change.

## Auditability

Important operational and financial actions must be traceable to the user who performed them.

## Transaction Safety

Trip closure and payout creation should be atomic:

```text
BEGIN
  Validate trip
  Validate OTP
  Close trip
  Create closure record
  Calculate payroll
  Create wallet credit
COMMIT
```

A critical failure should roll back the transaction.

---

# 28. Data Model Requirements

The requirements above imply the following data concepts:

```text
Users
Companies
Drivers
Driver Documents
Driver Availability
Orders
Trips
Driver Assignments
Trip Expenses
Trip Closures
Trip Payroll
Wallet Transactions
Statements
Statement Trips
Statement Verifications
Audit Logs
```

The database design should translate these requirements into relational tables, relationships, constraints, indexes, and historical snapshots where necessary.

---

# 29. Database Design Preparation

Before creating the database schema, the following questions must be answered:

### Identity

- How are Admin, Partner, and Driver identities represented?
- How are partner users associated with companies?
- How is a driver associated with a login identity?

### Orders and Trips

- How is an accepted order connected to a trip?
- How is driver reassignment history preserved?
- How are trip lifecycle states represented?

### Driver Operations

- How are document versions represented?
- How is daily availability represented?
- How are expenses connected to drivers and trips?

### Payout

- Where is the current company payout configuration stored?
- Where is the historical rate applied to a closed trip stored?
- How is duplicate wallet credit prevented?

### Statements

- How is a monthly statement identified?
- Which trips belong to a statement?
- How are the two verification records represented?

### Audit

- How are important changes recorded without overwriting history?

These questions define the requirements that the database design must satisfy.

---

# 30. Database Design Deliverable

The next artifact after this PRD should define:

1. Entities
2. Tables
3. Columns
4. PostgreSQL data types
5. Primary keys
6. Foreign keys
7. Relationships
8. Cardinality
9. Constraints
10. Indexes
11. Normalization decisions
12. Historical snapshot decisions
13. ER diagram
14. Transaction boundaries
15. Database-level integrity rules

The database design should remain traceable to this PRD.

---

# 31. Open Product Questions

These questions should be resolved before treating the requirements as completely finalized.

## 31.1 Trip Pricing

Who determines the trip price before Admin confirms it?

- Partner
- Admin
- Another defined process

Current assumption: Admin ultimately controls the confirmed trip amount.

## 31.2 Expense Reimbursement

Are driver expenses reimbursed?

If yes:

- Who pays?
- When?
- Is reimbursement separate from commission?
- What approval is required?

Current requirement: record and display expenses only.

## 31.3 No-Driver Scenario

What happens if no driver is available for an accepted trip?

The dedicated workflow/status must be confirmed before adding it.

## 31.4 Live Trip Information

What does "live trip" mean for Phase 1?

If it requires real-time location tracking, additional location/event requirements will be needed.

## 31.5 Notifications

Which events require email, push, or SMS?

The notification matrix should be finalized before implementation.

---

# 32. Requirements Definition of Done

The Phase 1 requirements are ready for detailed system/database design when:

- User roles are confirmed.
- Core workflow is confirmed.
- Order and trip lifecycles are confirmed.
- Driver assignment/reassignment behavior is confirmed.
- Trip closure is confirmed.
- Payout rules are confirmed.
- Historical payout behavior is confirmed.
- Wallet behavior is confirmed.
- Month-end statement workflow is confirmed.
- Two-person verification rules are confirmed.
- Data access boundaries are confirmed.
- Audit requirements are confirmed.
- Open questions affecting the data model are resolved or explicitly accepted as assumptions.

---

# 33. Next Artifact

Recommended project documentation:

```text
docs/
├── product-requirements.md
└── database-design.md
```

`product-requirements.md` describes **what the product needs to do**.

`database-design.md` describes **how the required data is structured to support it**.

The database design should be derived from and traceable back to this PRD.
