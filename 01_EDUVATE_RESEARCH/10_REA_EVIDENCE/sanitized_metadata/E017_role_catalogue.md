# ERP user-level (role) catalogue — runtime capture

Evidence: E017. Source: `GET /qbox/academic-communication/meta/` (OBSERVED, 2026-11-23) via a targeted read-only CDP capture; only role/level labels and communication enums were retained (organisational configuration, no personal data). Observed account: `GET /qbox/acms/assessments/my-access/` → `scope: "limited"`, `user_level: 11` (Teacher).

## ERP user levels (80)

| Level | Name |
|---|---|
| 1 | Super Admin |
| 2 | Academic Planners |
| 3 | Tech Support |
| 4 | Owner |
| 5 | School Admin |
| 7 | HR Admin |
| 8 | Principal |
| 9 | Campus Incharge (CIC) |
| 10 | Academic Coordinators |
| 11 | Teacher |
| 12 | Parent |
| 13 | Student |
| 14 | PRMs |
| 15 | Driver |
| 16 | Librarian |
| 17 | Hostel Manager |
| 18 | Warden |
| 19 | Store Manager |
| 20 | MIS |
| 21 | Vendor |
| 22 | Security Manager |
| 23 | Other Staff |
| 24 | Guest |
| 25 | Zonal FinOps Manager |
| 26 | School Manager |
| 27 | Zonal Business Head |
| 28 | Transport Incharge |
| 29 | Central Finance |
| 30 | Logistics Manager |
| 31 | Trustee |
| 32 | Branch Accountant |
| 33 | Store Admin |
| 34 | EA Academics |
| 35 | Customer Success |
| 36 | Admin Customer Support |
| 37 | Special Educator |
| 38 | ORM-Branding team |
| 39 | Central Approver |
| 40 | Talent Acquisition |
| 41 | Manager |
| 42 | Employee |
| 43 | Accounts Payable Team |
| 46 | Nurse |
| 47 | Relation Manager |
| 48 | Reimbursement Head |
| 49 | Reimbursement Manager |
| 50 | Reimbursement Checker |
| 54 | Central Transport Head |
| 55 | Transport Regional Manager |
| 57 | BDM |
| 58 | PRO |
| 59 | VP's |
| 60 | Marketing |
| 64 | CCA |
| 65 | Transport MIS |
| 66 | Branch Admission Counselor |
| 69 | School Partner |
| 70 | Investors |
| 71 | Branch Central FOE |
| 72 | Clicker User |
| 73 | Business Development Executive |
| 74 | Affiliate Marketing |
| 75 | Projects |
| 80 | Regional Finops Manager |
| 81 | Business Expansion VP |
| 82 | Business Expansion Deputy Head |
| 83 | Legal Counsel |
| 84 | CCTV |
| 85 | Expansion Team |
| 86 | L and D Admin |
| 87 | L and D Team |
| 88 | Internal Audit Team |
| 89 | Central Office |
| 90 | Zonal Operation Manager |
| 91 | KPss |
| 92 | yummy plates support |
| 93 | SchoolGate Kiosk |
| 94 | Contract Security |
| 95 | Contract Employee |
| 96 | Warehouse Logistics |

## Communication roles (assignable in Communication Inbox)

| Key | Label | Level | Can assign to levels |
|---|---|---|---|
| 1 | Super Admin | 1 | [2, 8, 9, 10, 11, 14, 26, 27, 28, 32, 90] |
| 2 | Academic Planners | 2 | [8, 10, 11] |
| 7 | HR Admin | 7 | [] |
| 8 | Principal | 8 | [8, 11, 10] |
| 9 | Campus Incharge (CIC) | 9 | [] |
| 10 | Academic Coordinators | 10 | [11, 10] |
| 11 | Teacher | 11 | [] |
| 14 | PRMs | 14 | [] |
| 25 | Zonal FinOps Manager | 25 | [] |
| 26 | School Manager | 26 | [9, 14, 28, 32] |
| 27 | Zonal Business Head | 27 | [8, 9, 10, 11, 14, 26, 28, 32, 90] |
| 28 | Transport Incharge | 28 | [] |
| 32 | Branch Accountant | 32 | [] |
| 57 | BDM | 57 | [] |
| 58 | PRO | 58 | [] |
| 90 | Zonal Operation Manager | 90 | [9, 14, 26, 28, 32] |

## Communication enums

- Priorities: [{'key': 'critical', 'label': 'Critical'}, {'key': 'high', 'label': 'High'}, {'key': 'standard', 'label': 'Standard'}, {'key': 'info', 'label': 'For information'}]
- Required actions: [{'key': 'immediate', 'label': 'Immediate attention'}, {'key': 'by_date', 'label': 'Action required by date'}, {'key': 'none', 'label': 'No action required'}]
- Reminder offsets: ['3_days_before', '1_day_before', 'on_day', 'escalate_1_after', 'escalate_3_after']
- Categories: ['Academics', 'Arts', 'Assessments', 'Audit and Academic Operations', 'Coding', 'Dance', 'DIY', 'Eduvate', 'Events and Competition', 'EYP', 'Facility & Maintainance', 'FInance', 'Horticulture', 'HR Communications', 'IT Hardware and Infrastructure', 'Logistics and Procurement', 'Marketing', 'Music', 'OCAP', 'OCFP Academics', 'Pottery', 'Projects', 'Public Speaking', 'Robotics', 'Security', 'SEN', 'Sports', 'STEM', 'Talent Acquisition', 'Textile', 'Theatre', 'Training', 'Transport']