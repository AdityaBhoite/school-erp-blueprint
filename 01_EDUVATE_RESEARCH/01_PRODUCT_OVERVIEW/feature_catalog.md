# Feature Catalog

Class: O = observed at runtime (screen and/or API), S = static (route/menu/endpoint), D = denied for the observed teacher (exists statically). Priority for the replacement: M/S/C/W (Must/Should/Could/Won't now). Traceability: Feature → Spec → Workflow → API → Entity → Test.

| ID | Feature | Class | Pri | Spec | Workflow | Key APIs (see api_contracts / catalogs) | Entities | Tests |
|---|---|---|---|---|---|---|---|---|
| F-001 | Permission-filtered navigation rail + mega-menu | O/S | M | m01 | — | access-control matrix | Role, Permission | RBAC-01 |
| F-002 | Branch & academic-year context switch | O (selectors) / I (switch behaviour) | M | m01 | — | `/user-mgmt/user-branches/`, `/user-session-year/` | Branch, AcademicYear | — |
| F-003 | Notifications (counts, list) and push registration | O | M | m01, m12 | WF-11 | `/mongoapp/notifications/*`, `/fcm-register/` | Notification | COM-03 |
| F-004 | Home banners (announcement/promotional) | O | C | m12 | WF-11 | `/home-banner/*/active/` | Banner | — |
| F-005 | Login (staff/student/parent), refresh, OTP, 2FA flag, forced update | S | M | m02 | — | staff-login, student-login, access-token, user-otp | User | AUTH-* |
| F-006 | User profile (view, change password, address) | O (view, change-password button) / UNVERIFIABLE (address) | M | m02 | — | `/user-mgmt/user-info/` | User | — |
| F-007 | Staff/student/guardian management, siblings, bulk import | S/D | M | m02, m14 | WF-07 | user-mgmt student/staff, parent endpoints | Student, Guardian, Staff | PPL-* |
| F-008 | Org masters (branch, year, grade, section, subject, mappings) with soft delete/restore | S | M | m03 | WF-01 | masterManagement group | Org entities | ORG-* |
| F-009 | Year rollover (section carry-forward) | S | S | m03 | WF-01 | `/acms/section-carry-forward/*` | ClassSection | ORG-03 |
| F-010 | Academic calendar (typed events, scope, recurrence, publish, review) | O (view, event fields) / I (publish, review, recurrence behaviour) | M | m04 | WF-02 | `/acms/calendar-events/*` | CalendarEvent | CAL-* |
| F-011 | Paid/registrable school events (imprest events) | O | C | m04 | — | `/academic/imprest-event/` | Event | — |
| F-012 | Timetable (period systems, allocation, draft/publish, common/split/SEN periods) | O (read data via Diary) / S / I (allocation, draft/publish, split) | M | m05 | WF-03 | `/timetable/*` | PeriodSystem, TimetableSlot | TT-01..03 |
| F-013 | Substitution management | S | S | m05 | WF-03 | absent-teacher, substitute-teacher | Substitution | TT-04 |
| F-014 | Daily diary (classwork/homework per period, media, multi-section) | O | M | m06 | WF-04 | `/school-feed/my-diary-given/`, create-dairy | DiaryEntry | DIA-* |
| F-015 | Homework (assign, upload, worksheets, submissions, evaluation) | O/S | M | m07 | WF-04 | hw-hub, student homework endpoints | HomeworkSubmission | HW-* |
| F-016 | Student attendance (mark, view, reports, absence SMS) | O/S | M | m08 | WF-05 | academic attendance endpoints | StudentAttendance | ATT-* |
| F-017 | Lesson plans (period view, annual view, resources, completion) | O/S | S | m09 | — | `/academic/lp-*` | LessonPlanPeriod | — |
| F-018 | Curriculum authoring (ACMS: YCP, chapters, key concepts, LP upload, version mapping) | S/D | C | m09 | — | acms endpoints | Volume, Chapter, Topic | — |
| F-019 | Assessments (create test, online/offline, schedule via calendar, papers, blueprint) | O/S | S | m10 | WF-06 | `/acms/assessments/`, `/assessment/test/` | Assessment, QuestionPaper | ASM-01 |
| F-020 | Marks upload dashboard (overdue/urgent, publish per branch) | O (dashboard) / I (publish) | S | m10 | WF-06 | `/acms/assessments/marks_upload_dashboard/` | MarksEntry | ASM-02..05 |
| F-021 | Report cards (templates, pipeline, principal signature, publish) | S (pipeline) / O (signature list only) | S | m10 | WF-06 | report-card endpoints, principle-signature | ReportCard* | RC-* |
| F-022 | Question bank (central + school) | S/D | C | m10 | — | questionBank group | Question | — |
| F-023 | Online classes (Zoom/Meet, recurring, attendance, recordings) | O (list, provider fields) / S (create, recurring, attendance, recordings) | C | m11 | WF-15 | `/erp_user/students-online-class/`, classes service | OnlineClass | — |
| F-024 | E-books / online books | O | C | m11 | — | `/academic/ebook-dropdown/` | EBook | — |
| F-025 | Classroom clickers | O | C | m11 | — | `/school-clicker/*` | Clicker | — |
| F-026 | Announcements (categories, inbox/drafts/sent, flash events) | O | M | m12 | WF-11 | `/announcement/v2/announcement/` | Announcement | COM-01/02 |
| F-027 | Communication inbox (central→school tasks with priority, required action, reminders, chat) | O | S | m12 | — | `/academic-communication/*` | Communication | — |
| F-028 | Chat (Mattermost) | O | C | m12 | — | chat service | ChatThread | — |
| F-029 | School wall (posts with moderation) | O | C | m12 | — | `/school-feed/feed-draft/` | Post | — |
| F-030 | Parent tickets with CSAT | D/S | S | m13 | WF-12 | ticket routes | Ticket | TKT-* |
| F-031 | Feedback (event, PTM, parent feedback analysis) | O/S | C | m13 | — | event-feedback, ptm-feedback | Feedback | — |
| F-032 | Admissions (registration → admission → payment) | S/D | S | m14 | WF-07 | registration/admission routes | Student | — |
| F-033 | Student lifecycle approvals (shuffle, type, status, TC, promotion) | S/D | S | m14 | WF-08 | approval-request routes | ApprovalRequest | LIFE-* |
| F-034 | Fee configuration (terms, plans, groups, misc fees, fines, settings) | S/D | S | m15 | WF-09 | finance service | FeePlan, FeeTerm | FEE-* |
| F-035 | Fee collection (online, cheque, kiosk, cash), receipts | S/D | S | m15 | WF-09 | finance service | Payment, Receipt | PAY-* |
| F-036 | Concessions (incl. sibling), refunds, wallets, cancellations | S/D | S | m15 | WF-10 | finance service | Concession, Wallet | FEE-03 |
| F-037 | Fee follow-up and reminders (calling) | S/D | C | m15 | — | fee-reminder routes | — | — |
| F-038 | Expense, payables, rent, P&L | S/D | W | m15 | — | finance | — | — |
| F-039 | Transport (routes, stops, buses, GPS, fees, optimisation) | S/D | C | m16 | — | transport routes | Route, Stop, Vehicle | — |
| F-040 | Dashboards (teacher, student, admin, finance) | S/D | S | m17 | — | reports service | — | NFR-01 |
| F-041 | Board-exam preparation (cohorts, buckets, goals) | O | C | m19 | — | `/board-prep/*` | Cohort | — |
| F-042 | Goal tracker | O | W | m19 | — | goal-tracker | — | — |
| F-043 | Staff test results (staff assessments) | O | C | m10 | — | `/test-module/my-results/` | — | — |
| F-044 | HRMS self-service (profile, leave, holidays, payslip, IT declaration, assets, travel, grievances, documents, courses, transfers, resignation) | O | W (except leave/holidays: C) | m18 | WF-13, WF-14 | HRMS endpoints | Employee… | — |
| F-045 | Recruitment, payroll, CRM, store, procurement, expansion, internal tools | S | W | m19 | — | catalogs | — | — |
| F-046 | AI help assistant | O | C | m01 | — | — | — | — |
| F-047 | Usage analytics and session recording | O | C (privacy-reviewed) | m01 | — | analytics service, Clarity | — | SEC-07 |
