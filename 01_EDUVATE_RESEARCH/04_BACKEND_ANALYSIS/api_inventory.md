# API Inventory

Two sources, kept separate:

1. **Runtime-observed** endpoints (E016): called by the application while a read-only tour made 71 navigations over 70 unique routes with a teacher account. Methods, paths, query-parameter **names**, HTTP statuses and screens are OBSERVED. Path parameters are normalised (`{id}`).
2. **Static catalogs** (E006 HRMS, 1,430 entries; E011 ERP, 1,174 entries) in `10_REA_EVIDENCE/bundle_analysis/`. These are **candidates**: referenced by the frontend, but not observed being called.

Service codes: ERP = `<brand-host>/qbox`, FIN = finance service, HRMS = recruitment service, MGMT = central management service, ANALYTICS = usage analytics, CHAT = Mattermost, CLARITY(3p) = Microsoft Clarity.

## Runtime-observed endpoints (94)

Composition: 87 product-service endpoints (ERP 53, HRMS 30, FIN 2, MGMT 2) + 2 in-house analytics + 4 Mattermost (3 API, 1 static CSS file) + 1 Microsoft Clarity beacon. 89 GET, 5 POST; no domain write was observed (audit ER-03).

| Module | Method | Svc | Path | Query params | Status | Screens (accessible) | Confidence |
|---|---|---|---|---|---|---|---|
| Academics | GET | ERP | `/qbox/academic/ebook-dropdown/` | acad_session, book_type | 200 | erp/online-books | HIGH |
| Academics | GET | ERP | `/qbox/academic/event-coscholastic-subjects/` | — | 200 | erp/calendar | HIGH |
| Academics | GET | ERP | `/qbox/academic/imprest-event/` | acad_session, end_date, page, start_date | 200 | erp/calendar | HIGH |
| Academics | GET | ERP | `/qbox/academic/lp-grade-subject-list/` | acad_session | 200 | erp/lesson-plan | HIGH |
| Academics | GET | ERP | `/qbox/academic/v1/ebook_school_wise_filter/` | acad_session_id, academic_year, book_type, branch, domain_name, page_number, page_size, session_year | 404 | erp/online-books | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/assessments/` | branch_id, date_filter, page, page_size, session_year, sort_dir | 200 | erp/acms/assessments | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/assessments/marks_upload_dashboard/` | branch_id, page, page_size, paginate_by, session_year, tab | 200 | erp/acms/assessments | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/assessments/my-access/` | — | 200 | erp/acms/assessments | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/calendar-events/` | branch, end_date, session_year, start_date | 200 | erp/acms/calendar | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/calendar-events/calendar_status/` | session_year | 200 | erp/acms/calendar | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/calendar-events/holiday_range/` | session_year | 200 | erp/acms/calendar | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/cities/` | session_year | 200 | erp/acms/calendar | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/curriculum/grades/` | — | 200 | erp/acms/assessments | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/curriculum/volumes/` | — | 200 | erp/school-clicker | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/meetings/` | page, page_size | 404 | erp/meeting-scheduler | HIGH |
| Academics/ACMS | GET | ERP | `/qbox/acms/team-roles/my_roles/` | — | 200 | erp/acms/assessments, erp/acms/calendar | HIGH |
| Announcements | GET | ERP | `/qbox/announcement/announcement-category/` | — | 200 | erp/announcement | HIGH |
| Announcements | GET | ERP | `/qbox/announcement/v2/announcement/` | acad_session, page, page_size | 200 | erp/announcement | HIGH |
| Assessment | GET | ERP | `/qbox/assessment/exam-type-list/` | — | 200 | erp/assessment/test | HIGH |
| Assessment | GET | ERP | `/qbox/assessment/get-start-quiz-user-levels-access/` | — | 200 | erp/assessment/test | HIGH |
| Assessment | GET | ERP | `/qbox/assessment/get-student-grades/` | — | 500 | erp/assessment/report-card | HIGH |
| Assessment | GET | ERP | `/qbox/assessment/report-card-question-category-mapping/` | — | 200 | erp/assessment/test | HIGH |
| Board prep | GET | ERP | `/qbox/board-prep/central/rollup/` | branch_id, grade, include_no_marks | 200 | erp/board-prep | HIGH |
| Board prep | GET | ERP | `/qbox/board-prep/cohorts/` | — | 200 | erp/board-prep | HIGH |
| Central admin | GET | MGMT | `/qbox/central-admin/b2b-report-config/` | user_level | 200 | erp/assessment-reports | HIGH |
| Chat (Mattermost) | GET | CHAT | `/api/v4/config/client` | format | 200 | erp/chat | HIGH |
| Chat (Mattermost) | GET | CHAT | `/api/v4/custom_profile_attributes/fields` | — | 401 | erp/chat | HIGH |
| Chat (Mattermost) | GET | CHAT | `/api/v4/license/client` | format | 200 | erp/chat | HIGH |
| Chat (Mattermost) | GET | CHAT | `/static/files/7654b55b2f3442e91404.css` | — | 200 | erp/chat | HIGH |
| Clicker | GET | ERP | `/qbox/school-clicker/clickers/` | acad_session, page, page_size | 200 | erp/school-clicker | HIGH |
| Clicker | GET | ERP | `/qbox/school-clicker/config/check/` | acad_session | 200 | erp/school-clicker | HIGH |
| Comms inbox | GET | ERP | `/qbox/academic-communication/communications/chat/unread-count/` | — | 200 | erp/communication-inbox | HIGH |
| Comms inbox | GET | ERP | `/qbox/academic-communication/communications/inbox/` | bucket, page, page_size, session_year | 200 | erp/communication-inbox | HIGH |
| Comms inbox | GET | ERP | `/qbox/academic-communication/meta/` | — | 200 | erp/communication-inbox | HIGH |
| Curriculum (central) | GET | MGMT | `/qbox/lesson_plan/list-volume/` | — | 200 | erp/online-books | HIGH |
| Diary/Feed | GET | ERP | `/qbox/school-feed/comments-draft/` | comments_status, page, page_size | 200 | erp/school-wall | HIGH |
| Diary/Feed | GET | ERP | `/qbox/school-feed/feed-draft/` | acad_session, page, page_size, post_status | 200 | erp/school-wall | HIGH |
| Diary/Feed | GET | ERP | `/qbox/school-feed/my-diary-given/` | acad_session, end_date, page_num, page_size, start_date | 200 | erp/diary | HIGH |
| Finance | GET | FIN | `/qbox/apiV1/v2/reimbursement-form/` | erp_id, include_withdrawn, is_draft, is_local_conveyance, page, page_size, request_kind | 200 | hr/reimbursement/claim-history | HIGH |
| Goal tracker | GET | ERP | `/qbox/goal-tracker/goals/` | page_size, period_id | 200 | erp/goal-tracker | HIGH |
| Goal tracker | GET | ERP | `/qbox/goal-tracker/hierarchy/` | — | 200 | erp/goal-tracker | HIGH |
| Goal tracker | GET | ERP | `/qbox/goal-tracker/periods/` | period_type | 200 | erp/goal-tracker | HIGH |
| HRMS (lead-mgmt app) | GET | HRMS | `/qbox/lead-mgmt/employee-refferal-history/` | page, page_size | 200 | hr/my-referral | HIGH |
| HRMS (lead-mgmt app) | GET | HRMS | `/qbox/lead-mgmt/internal-transfer-request/` | page, page_size, view_type | 200 | hr/transfers/apply-transfer | HIGH |
| HRMS LMS | GET | HRMS | `/qbox/lms/my-courses/` | course_type, is_active, page, page_size | 200 | hr/course/my-courses | HIGH |
| HRMS LMS | GET | HRMS | `/qbox/lms/teacher-cpd-hours/` | — | 200 | hr/course/my-courses | HIGH |
| HRMS assets | GET | HRMS | `/qbox/asset/asset-items/` | employee_id | 200 | hr/asset/your-assets | HIGH |
| HRMS assets | GET | HRMS | `/qbox/asset/asset-requests/` | employee_id | 200 | hr/asset/your-assets | HIGH |
| HRMS assets | GET | HRMS | `/qbox/asset/asset-types/` | — | 200 | hr/asset/your-assets | HIGH |
| HRMS assets | GET | HRMS | `/qbox/asset/subscription-assignments/` | employee_id, status | 200 | hr/asset/your-assets | HIGH |
| HRMS central DB | GET | HRMS | `/qbox/central-db/branch-view/` | is_active | 200 | hr/transfers/apply-transfer, hr/travel/my-travel-request | HIGH |
| HRMS documents | GET | HRMS | `/qbox/document-center/summary/` | — | 200 | hr/document-center | HIGH |
| HRMS exit | GET | HRMS | `/qbox/exit-mgmt/resignation-request/` | view_type | 200 | hr/resignation | HIGH |
| HRMS grievances | GET | HRMS | `/qbox/grievance-module/employee-detail/` | erp_id | 200 | hr/employee-grievance/my-tickets | HIGH |
| HRMS grievances | GET | HRMS | `/qbox/grievance-module/employee-team-member-check/` | — | 200 | hr/employee-grievance/my-tickets | HIGH |
| HRMS grievances | GET | HRMS | `/qbox/grievance-module/employee-ticket-category-group/` | page_size | 200 | hr/employee-grievance/my-tickets | HIGH |
| HRMS grievances | GET | HRMS | `/qbox/grievance-module/employee-ticket/` | page, page_size, scope | 200 | hr/employee-grievance/my-tickets | HIGH |
| HRMS growth plan | GET | HRMS | `/qbox/pip/my-plan/` | — | 200 | hr/growth-action-plan/my-plan | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/employee-attendance-details/` | current_date, end_date, erp_id, start_date | 200 | hr/attendance | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/employee-payslip/` | employee_id, month-year | 200 | hr/payroll/my-payslip | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/leave-applications/` | application_type, page, page_size, status | 200 | hr/leave-apply | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/leave-types/` | attendance, is_active | 200 | hr/attendance | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/payroll-cycle-lock/` | payroll_month, payroll_year | 200 | hr/attendance | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/reportees-dropdown/` | include_reportees, page_size | 200 | hr/attendance | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/tax-declaration-summary/` | financial_year | 200 | hr/payroll/it-declaration | HIGH |
| HRMS payroll/attendance | GET | HRMS | `/qbox/payroll/user-holiday/` | session_year | 200 | hr/holiday-calendar | HIGH |
| HRMS travel | GET | HRMS | `/qbox/travel/travel-request/` | page, page_size, view_type | 200 | hr/travel/my-travel-request | HIGH |
| HRMS users | GET | HRMS | `/qbox/user-mgmt/department-list/` | access, is_active | 200 | hr/transfers/apply-transfer | HIGH |
| HRMS users | GET | HRMS | `/qbox/user-mgmt/designation-list/` | access, is_active | 200 | hr/transfers/apply-transfer | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/access-control/matrix/` | — | 200 | (shell / denied pages) | HIGH |
| Identity/Users | POST | ERP | `/qbox/erp_user/access-token/` | — | 200 | (shell / denied pages) | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/grade/` | — | 200 | erp/acms/calendar | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/principle-signature/` | branch_id, session_year, user_level | 200 | erp/assessment/report-card | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/profanity-txt/` | — | 404 | erp/school-wall | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/students-online-class/` | end_date, page, page_size, start_date, status | 200 | erp/online-class | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/user-mgmt/user-branches/` | all_branches, session_year | 200 | erp/assessment-reports, erp/calendar, erp/meeting-scheduler | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/user-mgmt/user-grades/` | acad_session, access_level, session_year | 200 | erp/assessment/test, erp/attendance, erp/homework, erp/online-class … | HIGH |
| Identity/Users | GET | ERP | `/qbox/erp_user/user-mgmt/user-info/` | session_year | 200 | erp/user-profile | HIGH |
| Shell | POST | CLARITY(3p) | `/collect` | — | 200, 204, None | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | POST | ANALYTICS | `/qbox/api/v1/track/batch` | — | 200 | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | POST | ANALYTICS | `/qbox/api/v1/track/session` | — | 201, None | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | GET | FIN | `/qbox/apiV1/expense-delegation-context/` | erp_id | 200, None | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | POST | ERP | `/qbox/erp_user/fcm-register/` | — | 200 | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | GET | ERP | `/qbox/event-feedback/staff/my/pending/` | — | 200 | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | GET | HRMS | `/qbox/grievance-module/employee-ticket-summary/` | — | 200 | erp/user-profile | HIGH |
| Shell | GET | ERP | `/qbox/home-banner/announcement/active/` | — | 200 | erp/user-profile | HIGH |
| Shell | GET | ERP | `/qbox/home-banner/promotional/active/` | — | 200 | erp/user-profile | HIGH |
| Shell | GET | HRMS | `/qbox/lms/training-reminders/` | — | 200, None | hr/asset/your-assets, hr/attendance, hr/course/my-courses, hr/document-center … | HIGH |
| Shell | GET | ERP | `/qbox/mongoapp/notifications/` | page, page_size | 200, 403, None | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | GET | ERP | `/qbox/mongoapp/notifications/counts/` | — | 200, 403, None | erp/acms/assessments, erp/acms/calendar, erp/announcement, erp/assessment-reports … | HIGH |
| Shell | GET | HRMS | `/qbox/payroll/kp/access/` | — | 200, None | hr/asset/your-assets, hr/attendance, hr/course/my-courses, hr/document-center … | HIGH |
| Staff tests | GET | ERP | `/qbox/test-module/my-results/` | session_year | 200 | erp/test-result | HIGH |
| Timetable | GET | ERP | `/qbox/timetable/branch-staff-timetable/` | section_mapping | 200 | erp/diary | HIGH |
| Timetable | GET | ERP | `/qbox/timetable/teacher-timetable/` | acad_session, teacher_id | 200 | erp/diary | HIGH |

## Static catalog summary

| Catalog | Groups | Entries | File |
|---|---|---|---|
| ERP `endpoints` object | 124 | 1,174 | `10_REA_EVIDENCE/bundle_analysis/E011_erp_endpoint_catalog.md` |
| HRMS endpoint catalog | ~85 | 1,430 | `10_REA_EVIDENCE/bundle_analysis/E006_hrms_endpoint_catalog.md` |

Statuses: 403 on first load came from token bootstrap timing (requests retried with 200). 404s (`/acms/meetings/`, `/academic/v1/ebook_school_wise_filter/`, `/erp_user/profanity-txt/`) and a 500 (`/assessment/get-student-grades/` without params) were OBSERVED and are recorded as-is.
---

## Phase 3 deep static analysis (2026-11-23) — supersedes the static-catalog summary above

A full-corpus AST pass (1,954 chunks of all three apps, 0 parse errors) resolved endpoint **call sites and HTTP methods**, which the shipped catalogs do not record. See `13_AUTONOMOUS_DEEP_ANALYSIS/`.

| Metric | Value |
|---|---|
| Distinct endpoint candidates | **6,539** |
| Methods resolved from code | 5,381 (82%) · STATIC_CALLSITE_CONFIRMED 5,287, RUNTIME_OBSERVED 94 |
| Method unknown (path only) | 1,041 STATIC_PATH_ONLY + 117 dynamic templates |
| Resolved static call-site records | 8,530 in `API_CALLSITE_INDEX.csv` (8,527 with a method, 8,513 to a known client) across 5,364 endpoints; Σ catalog `callsite_count` = 10,006. Earlier "11,146" was a non-retained static-pass counter — see `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` |
| Endpoints with static response-field names | 2,877 · with request-body key names | 1,720 |
| Finance-service endpoints recovered | 141 (methods + field names; Phase 1/2 had "not recovered") |
| Validation | 89/89 runtime GET/POST methods confirmed by static call sites, 0 disagreements |

Machine-readable: `13_AUTONOMOUS_DEEP_ANALYSIS/API_ENDPOINT_CATALOG.csv` (one row per endpoint) and `API_CALLSITE_INDEX.csv` (one row per exported call-site record — 8,530; see `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` — with file:line, method basis, client, catalog key, field names). HTTP methods for the Phase 1/2 static catalogs (E006/E011) are now resolved here; treat those two files as path lists and this as the method-bearing catalog.

## Phase 3 runtime route crawl (2026-11-23) — observed-endpoint update

A controlled, read-only, navigate-only crawl ran the authenticated **teacher** session over 408 of 426 safe unique-screen routes (stopped cleanly on the server's 429 rate limit; 18 routes unreached). Full report: `13_AUTONOMOUS_DEEP_ANALYSIS/RUNTIME_VERIFICATION_REPORT.md`.

| Metric | Value |
|---|---|
| Runtime-observed endpoints (E016 + crawl) | **171** (was 94) |
| New endpoints observed by the crawl | **77** (method+path), 65 on rendered routes |
| New value-free response schemas | **72** → `10_REA_EVIDENCE/sanitized_metadata/E019_phase3_crawl_api_shapes.json` |
| Catalog rows after runtime-only additions | 6,577 (6,539 static + 38 runtime-only) |
| Method agreement, static call site vs runtime | 104/107 direct, 3 benign multi-method REST, **0 contradictions** |
| Routes rendered (teacher) / role-redirected | 42 / 359 — see `ROLE_ROUTE_API_MATRIX.md` §6 |

Runtime-confirmed this pass: the standard ERP response envelope `{status_code, message, result}`; pagination `{total, offset, limit}` and `{count, next, previous, results}`; the **chat service is Mattermost** (`/api/v4/...`). The 38 runtime-only endpoints have **no static call site** (third-party Mattermost + profile/bootstrap calls built outside the ERP bundles), so they appear in `API_ENDPOINT_CATALOG.csv` as RUNTIME_OBSERVED but are **not** in `API_CALLSITE_INDEX.csv` (which indexes static call sites only — a runtime observation is not a call site).
