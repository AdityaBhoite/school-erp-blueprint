# Module Index

Coverage legend: **R** = runtime-observed screens/APIs (teacher account), **S** = static only (routes/endpoints/menu), **D** = access denied for the observed account (redirected to profile), so static only.

| # | Module | Spec file | App | Coverage | Replacement priority |
|---|---|---|---|---|---|
| M01 | App shell, navigation and context | `m01_shell_navigation.md` | all | R | Must |
| M02 | Identity, access control and user management | `m02_identity_access_user_management.md` | ERP/HRMS | R (profile) + S | Must |
| M03 | Organisation and academic structure (masters) | `m03_org_academic_structure.md` | ERP | S + R (fields) | Must |
| M04 | Academic calendar | `m04_academic_calendar.md` | ERP (ACMS) | R | Must |
| M05 | Timetable and substitution | `m05_timetable.md` | ERP | R (data) + S | Must |
| M06 | Daily diary | `m06_diary.md` | ERP | R | Must |
| M07 | Homework | `m07_homework.md` | ERP | R (filter screen) + S | Must |
| M08 | Student attendance | `m08_attendance.md` | ERP | R (screen) + S | Must |
| M09 | Lesson plans and curriculum (ACMS) | `m09_lesson_plan_curriculum.md` | ERP | R + S | Could (per `implementation_priorities.md`; audit ER-46) |
| M10 | Assessments, marks and report cards | `m10_assessment_reportcard.md` | ERP | R (partial) + S/D | Should |
| M11 | Online classes and e-books | `m11_online_class_ebooks.md` | ERP | R | Could |
| M12 | Communication: announcements, notifications, comms inbox, chat, school wall | `m12_communication.md` | ERP | R | Must |
| M13 | Parent helpdesk tickets and feedback | `m13_tickets_feedback.md` | ERP | D + S | Should |
| M14 | Students: admission, lifecycle, approvals | `m14_students_lifecycle.md` | ERP | D + S | Should |
| M15 | Finance: fees, payments, wallets, expenses | `m15_finance.md` | ERP/Finance | D + S | Should |
| M16 | Transport | `m16_transport.md` | ERP | D + S | Could |
| M17 | Dashboards and reports | `m17_dashboards_reports.md` | ERP | D (dashboard) + S | Should |
| M18 | HRMS self-service (profile, attendance/leave, payslip, documents, grievances) | `m18_hrms_self_service.md` | HRMS | R | Optional |
| M19 | Operations, CRM and other bounded contexts (summary) | `m19_other_contexts.md` | ERP/CRM/HRMS | S | Won't (now) |
