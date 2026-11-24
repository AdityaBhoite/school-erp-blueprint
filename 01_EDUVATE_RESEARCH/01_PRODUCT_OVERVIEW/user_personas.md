# User Personas

Personas come from navigation targets, route permissions and the observed teacher account. Only the **Teacher** persona was observed directly; the others are STATIC or INFERRED.

| Persona | Evidence | Class | Primary jobs | Key modules |
|---|---|---|---|---|
| **Subject / Class Teacher** (observed: TGT-Secondary Teacher, Social Studies, Grades 9–10) | logged-in account; rail shows Academics, Comms, Dashboard + HRMS self-service | OBSERVED, HIGH | See today's periods; write the daily diary (classwork and homework); mark and view attendance; follow lesson plans (LP badge per period); create and evaluate homework; view the academic calendar and timetable; assessments and marks entry; answer parent tickets; apply for leave; view payslips | Diary, Lesson Plan, Homework, Attendance, Timetable, Calendar, Assessment, Ticket, HR self-service |
| **Student** | ERP user_level 13; student nav categories (Dashboard, Academics, Fees & Wallet, Communication, Activities) | STATIC, HIGH | View timetable, homework (today/pending/submitted/evaluated), submit homework, online classes, assessments and results, attendance, events | Student dashboard, homework submission, online class, assessment attempt |
| **Parent / Guardian** | sibling data in session, parent search/link endpoints, fee payment routes, tickets, feedback | STATIC, MEDIUM | Pay fees (online/wallet), download receipts, raise tickets, read announcements, track bus, give feedback; switch between siblings | Fees & Wallet, Communication, Transport tracking |
| **Principal / School Head** | "Permanent Status (Principal)" approvals, principal signature, principal appreciation overview, reporting-manager role | STATIC, MEDIUM | Approve student status changes, monitor academics and attendance, observations, sign report cards | Approvals, dashboards, observations, report cards |
| **Academic Coordinator / ACMS Ops team** | `allowAcmsOpsTeam`, ACMS (Academic Content Management System) menus | STATIC, HIGH | Curriculum planning (YCP), lesson-plan configuration and assignment, calendar generation and publishing, assessments and blueprints, teacher allocation | ACMS, Assessments |
| **School Admin / Front office** | Student management, user management, bulk uploads | STATIC, HIGH | Create/edit students and staff, sections, ID cards, TC requests, sibling mapping | User Management, Student Management |
| **Accountant / Finance team** | Accountant dashboard, fee management, kiosk, reconciliation | STATIC, HIGH | Configure fee plans, collect fees, approve concessions/refunds, reconcile, reports | Finance |
| **Transport manager** | Transport hub | STATIC, HIGH | Routes, stops, vehicles, drivers, transport fees, GPS tracking | Transport |
| **Store / Operations** | Store, procurement, infra | STATIC, HIGH | Kits, orders, deliveries, purchase requisitions | Operations |
| **HR (HRBP, recruiter, payroll)** | HRMS user levels 1–36, `is_hrbp_support` | STATIC, HIGH | Hiring, onboarding, payroll, leave approval, transfers, exits | HRMS (optional context) |
| **Marketing / Admissions counsellor (PRM, CCA)** | CRM app | STATIC, HIGH | Lead follow-up, enquiries, admissions | CRM (optional context) |
| **Central management / Super admin** | `is_superuser`, access-control admin, central APIs | STATIC, HIGH | Configure brands, branches, access matrix, masters | Admin |

## Persona priorities for the replacement
1. Teacher, 2. School admin, 3. Parent, 4. Student, 5. Principal, 6. Accountant, 7. Academic coordinator, 8. Super admin, then the optional HR, transport and marketing personas.
