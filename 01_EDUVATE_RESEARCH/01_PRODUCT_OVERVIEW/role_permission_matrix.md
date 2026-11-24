# Role–Permission Matrix

## 1. How the original models roles (STATIC + OBSERVED)
- **ERP user levels** — 80 named levels (E017, OBSERVED), including: 1 Super Admin, 2 Academic Planners, 3 Tech Support, 4 Owner, 5 School Admin, 7 HR Admin, 8 Principal, 9 Campus Incharge (CIC), 10 Academic Coordinators, **11 Teacher**, 12 Parent, 13 Student, 14 PRMs, 15 Driver, 16 Librarian, 17 Hostel Manager, 18 Warden, 19 Store Manager, 20 MIS, 21 Vendor, 22 Security Manager, 23 Other Staff, 24 Guest, 25 Zonal FinOps Manager, 26 School Manager, 27 Zonal Business Head, 28 Transport Incharge, 29 Central Finance, 31 Trustee, 32 Branch Accountant, 37 Special Educator, 41 Manager, 42 Employee, 46 Nurse, 90 Zonal Operation Manager, and others (full list: `10_REA_EVIDENCE/sanitized_metadata/E017_role_catalogue.md`).
- **HRMS user levels** — a separate numeric namespace (1–36) used in HRMS route permissions (E007). Names are UNKNOWN; clusters are inferred in §4.
- **CRM user levels** — separate namespace (1–27); level 10 is treated specially (hides ERP; INFERRED: partner/external user).
- Permissions per navigation item: `is_view / is_add / is_modify / is_delete` arrays of levels, plus per-person `erpIds` overrides, plus feature flags (`is_recruitment_enabled`, `is_crm_enabled`, `is_hrbp_support`, key-person, reporting-manager). Served by `/erp_user/access-control/matrix/` and cached client-side for 1 h.
- **Scope**: branch(es) and academic year from user-branch mappings; grades/subjects from user-grade/subject mappings. The observed teacher has assessment scope `limited` with 5 grades.

## 2. Observed: Teacher (ERP level 11) — E016 runtime access test (read-only navigation)
| Area | Accessible (OBSERVED) | Denied → redirected (OBSERVED) |
|---|---|---|
| Dashboard | — | `/erp/dashboard` |
| Calendar | `/erp/acms/calendar`, `/erp/calendar` | `/erp/calendar-hub` |
| Teaching | `/erp/lesson-plan`, `/erp/diary`, `/erp/homework`, `/erp/attendance`, `/erp/timetable`, `/erp/online-class`, `/erp/online-books`, `/erp/school-clicker` | `/erp/homework-dashboard`, `/erp/acms/my-lesson-plans` |
| Assessment | `/erp/assessment/test`, `/erp/acms/assessments`, `/erp/upload-marks`, `/erp/assessment-reports`, `/erp/assessment/report-card`, `/erp/test-result` | `/erp/assessment`, `/erp/assessment/clicker`, `/erp/acms/questions`, `/erp/acms/blueprints`, `/erp/acms/performance-analytics`, `/erp/acms/dashboard`, `/erp/test-module` |
| Quality | `/erp/board-prep` | `/erp/observation`, `/erp/teacher-observation`, `/erp/video-monitoring/my-observations`, `/erp/audio-analysis/my-insights`, `/erp/academic-improvement/daily-log`, `/erp/house-system/*`, `/erp/event-manager` |
| Communication | `/erp/announcement`, `/erp/communication-inbox`, `/erp/chat`, `/erp/school-wall`, `/erp/meeting-scheduler` | `/erp/ticket`, `/erp/ticket-dashboard`, `/erp/feedback`, `/erp/academic-communication`, `/erp/myschoolgate` |
| Tools | `/erp/goal-tracker` | `/erp/bug-tracker/dashboard`, `/erp/feature-request/dashboard`, `/erp/work-tracker/dashboard`, `/erp/bytelearn*` |
| HRMS self-service | profile, attendance, holiday calendar, leave apply, document centre, my courses, my care, your assets, claim history, travel, apply transfer, growth plan, my referral, my payslip, IT declaration, resignation | performance-management/my-performance, salary-advance/request |
| Not in the teacher's rail | Students, Finance, Transport, Operations, Approvals, Admin, Marketing/CRM | — |

Write permissions (create/update/delete) were **not exercised** and remain UNVERIFIED. The presence of "Create Test", "Create Class", "Homework for another class" buttons suggests the teacher holds create rights for those (INFERRED, MEDIUM).

## 3. Proposed replacement roles × permissions (PROPOSED)
Legend: R = read, C = create, U = update, D = delete (soft), A = approve/publish, X = export, own = own records/scope only, scoped = within assigned branch/grade/section/subject.

| Module \ Role | Super Admin | School Admin | Principal | Academic Coordinator | Class/Subject Teacher | Accountant | Transport Mgr | Front Office | Parent | Student |
|---|---|---|---|---|---|---|---|---|---|---|
| Tenant & branches | RCUD | R | R | R | — | R | R | R | — | — |
| Academic years, grades, sections, subjects, mappings | RCUD | RCUD | R | R | R (scoped) | R | R | R | — | — |
| Roles & permissions | RCUD | RCU (school roles) | R | — | — | — | — | — | — | — |
| Staff records | RCUD | RCUD | R | R | R (directory) | R | R (drivers) | R | — | — |
| Student records | RCUD X | RCUD X | R X | R | R (scoped) | R | R (transport fields) | RCU | R (own children) | R (self) |
| Guardians & siblings | RCUD | RCUD | R | R | R (scoped, masked contact) | R | — | RCU | RU (own profile) | — |
| Enrolment / promotion / shuffle | RCUD A | RCU | A | RC (request) | — | — | — | RC | — | — |
| Calendar | RCUD A | RCUD A | RA | RCU | R | R | R | R | R | R |
| Timetable & substitution | RCUD A | RCUD A | RA | RCU A | R (own + section) | — | — | — | R (child) | R (own) |
| Diary & homework | R | R | R X | R X | RCU (own periods, scoped) | — | — | — | R | R + submit |
| Homework evaluation | — | — | R | R | CU (scoped) | — | — | — | R | R |
| Student attendance | R X | RCU X | R X | RCU X | RCU (scoped, same-day) | R | R (bus attendance) | R | R (child) | R (self) |
| Lesson plans | RCUD | R | R | RCUD | R + mark completion | — | — | — | — | R (published) |
| Assessments & papers | RCUD | R | RA | RCUD A | RC (scoped tests) | — | — | — | — | attempt |
| Marks entry | R | R | R A (publish) | RCU A | CU (scoped) | — | — | — | R (published) | R (published) |
| Report cards | RCUD A | R | A (sign/publish) | RCU | R (scoped) | — | — | — | R (published) | R (published) |
| Announcements | RCUD A | RCUD A | RCU A | RCU | R (C for own classes) | R | R | R | R | R |
| Tickets | R X | RU X | RU X | RU | RU (assigned) | RU (finance category) | RU (transport) | RCU | RC + CSAT | — |
| Fee setup (heads, terms, plans) | RCUD | R | R | — | — | RCUD | R (transport fees) | — | — | — |
| Fee collection & receipts | R X | R X | R X | — | — | RC X | — | RC (counter) | R + pay (own) | — |
| Concessions / refunds / waivers | A | RC | A | — | — | RC (maker) | — | — | — | — |
| Transport | RCUD | R | R | — | — | R | RCUD | R | R (child's route, tracking) | R |
| Reports & dashboards | R X | R X | R X | R X (academics) | R (own classes) | R X (finance) | R X (transport) | R | R (child) | R (self) |
| Audit log | R | R (school) | R (school) | — | — | — | — | — | — | — |

Special rules:
- Teacher scope comes from timetable allocation and/or explicit subject/section assignment; class teachers additionally get section-wide attendance and parent communication.
- Parents see only linked children; students only themselves.
- Maker ≠ checker for all approvals.
- Sensitive PII (government IDs, health/SEN, guardian phone/email) masked for teachers unless `pii.reveal`.

## 4. HRMS levels (inferred clusters, LOW confidence names)
From E007 route permissions: level 1 (all 237 routes) ≈ HR super admin; 2 and 5 (163–182) ≈ HR heads/admins; 6, 8, 9, 33 (MRF recruiter pages) ≈ recruiters/TA; 15 (salary fitment, budget approvals) ≈ compensation/finance approver; 16, 26, 28 (PMS config, transfers, exits, designations) ≈ HRBP/HR operations; 12, 18, 19 (clearance, onboarding) ≈ IT/admin/finance clearance owners; 4, 24, 25 (presence management only) ≈ attendance administrators; 32 (BGV, profile completion) ≈ BGV vendor/coordinator; 34 (audits only) ≈ hiring auditors; 36 (partner hiring master) ≈ hiring partner. All others ≈ regular employees with self-service.
