# M07 — Homework

**Routes:** `/erp/homework` (teacher), `/erp/homework-dashboard` (denied for teacher), `/erp/homework/{centralized-home, centralized-eval-reports, evaluate-homework, homework-evaluation, student-analytics, submit-homework, update-homework}` (STATIC). Screenshot `homework-filters.png`.

## Screen (OBSERVED)
- Tabs: **Homework** | **Upload** | **Worksheet**.
- Filters: Select Grade, Select Section, Start date, End date; subject search box with search button.
- Data loads only after the filters are applied (filter-first). Grade list: `GET /qbox/erp_user/user-mgmt/user-grades/?acad_session&access_level&session_year`.

## STATIC endpoints
Teacher: `/hw-hub/hw_view/`, `/hw-hub/view-users/`, `/hw-hub/upload_hw_files/`, zip upload via a cloud function (`hw_zip_upload_v1`), `/academic/student-homework/`, `/academic/homework-submitted-data/`, `/academic/stu-submited-data/`. Student: `/academic/v2/today-stu-hws/`, `/stu-pending-hws/`, `/stu-submitted-hws/`, `/stu-evaluated-hws/`, `/academic/homework-submission/`. Centralized homework (`centralizedHomework` group, 11 endpoints) and dashboards (`homeworkDashboard`, `homeworkDashboardV2`, `homeworkAnalytics`). Worksheets (`worksheet` group).

## Behaviour (reconstructed)
- Homework is created through the Diary (period entry) or the Homework module; it has due date/time, attachments and online-submission mode.
- **Upload** tab: bulk upload of homework files per grade/section (I).
- **Worksheet** tab: assign worksheets from a library (I).
- Evaluation: teacher views submissions per student and marks/annotates; status per student.
- Centralized homework: central team publishes homework content for all branches (S).

Workflow: `wf04_daily_diary_homework.md`. ACs: HW-01..03.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/homework/evaluate-homework`, `/homework/update-homework` and `/homework/student-analytics` all **rendered** for a teacher. Route-specific endpoints observed:
- `GET ERP /erp_user/user-mgmt/student-subjects` — subject scope (student-analytics)
- `GET ERP /erp_user/profanity-txt` — profanity word-list loaded by the evaluate screen (moderation of free-text feedback)
- `GET REPORTS /api/reports/v2/{id}/view` — analytics report embed (served by `reports.letseduvate.com`)

`/homework/evaluate-homework` and `/update-homework` also returned a **404** on one sub-resource (a teacher-context id the crawl lacked). Grading submit endpoints need a write and were not triggered. Status: route RUNTIME_OBSERVED; marking APIs stay STATIC.
