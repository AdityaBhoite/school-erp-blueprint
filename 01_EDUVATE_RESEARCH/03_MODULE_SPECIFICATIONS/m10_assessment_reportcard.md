# M10 — Assessments, Marks and Report Cards

**Routes (OBSERVED accessible for the teacher):** `/erp/assessment/test`, `/erp/assessment-reports`, `/erp/assessment/report-card`, `/erp/acms/assessments`, `/erp/test-result`, `/erp/upload-marks`. **Denied:** `/erp/assessment`, `/erp/assessment/clicker`, `/erp/acms/questions`, `/erp/acms/blueprints`, `/erp/acms/performance-analytics`, `/erp/acms/dashboard`, `/erp/test-module`.

## Screens (OBSERVED)
**Assessment → Test** (`/erp/assessment/test`): Status radios (Upcoming / Completed); filters Grade, Subject, Date Range, "Show more filters"; mode chips (All / Online / Offline); search by test name; **Create Test** button.
APIs: `GET /qbox/assessment/exam-type-list/` → `[{id, exam_name, is_delete, created_at, updated_at, created_by, updated_by}]`; `GET /qbox/assessment/get-start-quiz-user-levels-access/` → `[int]` (user levels allowed to start quizzes); `GET /qbox/assessment/report-card-question-category-mapping/` → `[{central_category_id, central_category_name, erp_category_id, erp_category_name, is_draft, is_review, is_subject_dependent, is_delete, created_at}]`.

**ACMS Assessments** (`/erp/acms/assessments`): list + marks-upload dashboard.
- `GET /qbox/acms/assessments/?branch_id&session_year&date_filter&page&page_size&sort_dir` → paginated assessments (fields in entity dictionary: code, name, test_type, section_access, grade, subject, test_date/time, scheduled_via_calendar, calendar_event_id, effective_test_date, total_marks, duration_minutes, status, practice/sample paper counts, has_final_paper, final_paper_locked, blueprint flags…).
- `GET /qbox/acms/assessments/marks_upload_dashboard/?tab&branch_id&session_year&page&page_size&paginate_by` → rows with is_overdue, is_urgent, days_remaining, marks_published, assessment_mode, teacher_pdf_url, branch_assignment_id, branch_marks_published, online_pending_sync, online_pending_grading, section_mapping_id.
- `GET /qbox/acms/assessments/my-access/` → `{scope, user_level, grades[], subjects[]}`.
- `GET /qbox/acms/curriculum/grades/`.

**Test Result** (`/erp/test-result`): tabs My Results / My Tutor Tests; Refresh; per result: total, percentage, View Details. `GET /qbox/test-module/my-results/?session_year` → `[{paper, test_type, test_type_id, is_tutor, sections:[{name, obtained, max, count, cut_off, passed}], total_obtained, total_possible, percentage, result, submitted_at, rank}]`. This is the **staff test module** (teachers take tests, e.g. SJT/training assessments).

**Assessment reports** (`/erp/assessment-reports`): branches from `/user-branches/`; access check `GET mgmt /central-admin/b2b-report-config/?user_level` → `{access}`.

**Report card** (`/erp/assessment/report-card`): `GET /qbox/erp_user/principle-signature/?branch_id&session_year&user_level` → paginated signatures (signature URL, branch, author erp_id/name, role, user level); `GET /qbox/assessment/get-student-grades/` returned **500** without parameters (OBSERVED error envelope `{status:int, data:string}`).

## STATIC capabilities
Question bank (central + school; create, bulk create, publish), question papers (create, edit, publish, auto-generate, render/download PDF), test creation, groups, OMR result upload (`/assessment/upload-omr-result-excel/`), marks upload, grading systems, report-card components/sub-components/config summary/cache clear, report pipeline (status list, publish status, revert), personality traits, hall tickets (config, generation, bulk, requests), assessment dashboards, NeoBoard games, clicker assessments, computational thinking and common question banks.

Workflow: `wf06_assessment_report_card.md`. ACs: ASM-01..05, RC-01..03.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
Multiple assessment screens **rendered** for a teacher: `/acms`, `/acms/dashboard-v2`, `/assessment/test`, `/assessment/report-card`, `/assessment-reports`, `/offline-marks`, `/question-chapter-wise`. Route-specific endpoints observed:
- **ACMS dashboard** (`/acms/dashboard-v2`): `GET ERP /acms/assessments/dashboard_home` (rich shape: `upcoming_exams, upload_deadlines, top_bottom_branches, year_timeline, computed_at`), `GET /acms/assessments/{id}` (paginated list: `tests, total, offset, limit, facets, computed_at`), `GET /acms/branch-zones`, `/acms/curriculum/grades`, `/acms/curriculum/session-years`, `/acms/curriculum/subjects`
- **Report card** (`/assessment/report-card`): `GET ERP /assessment/get-student-grades`, `GET ERP /erp_user/principle-signature`
- **Test** (`/assessment/test`): `GET ERP /assessment/exam-type-list`, `GET ERP /assessment/{id}`, `GET ERP /erp_user/user-mgmt/user-grades`

A dedicated **`/acms/` API namespace** with its own curriculum sub-resources is runtime-confirmed (distinct from the older `/assessment/` paths). `/assessment/report-card` returned a **500** on one call — a reliability signal to note. Richer value-free shapes in E019. Status: RUNTIME_OBSERVED.
