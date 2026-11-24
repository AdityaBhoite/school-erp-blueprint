# M08 — Student Attendance

**Route:** `/erp/attendance` (Academics → Teaching & Classroom → Attendance; also Students → Attendance). **Evidence:** E016 screen + API (OBSERVED); endpoints (STATIC). Screenshot `attendance-mark-empty.png`.

## Screen
- Breadcrumb `Home · Attendance`.
- Card tabs: **Mark Attendance** (active) | **View Attendance**.
- Filters: **Grade** (select, placeholder "Select Grade\*"), **Sections** (select, "Select Sections \*"), **Search** button (primary, full width of its cell).
- Table columns: **Sl No., ERP Id, Name, Grade, Section, Attendance**; empty state "No data"; horizontal scrollbar.
- Date selection: not visible in the empty state (I: defaults to today; View tab has a date range).

## APIs
Only the `user-grades` row is OBSERVED. For STATIC rows the path comes from the shipped endpoint catalog, which records **no HTTP method**; the methods shown are INFERRED from the names (audit ER-07). See `13_AUTONOMOUS_DEEP_ANALYSIS/` for call-site method resolution.

| Method | Path | Query/body | Class |
|---|---|---|---|
| GET | `/qbox/erp_user/user-mgmt/user-grades/` | `acad_session, session_year` → `[{grade_id, grade__grade_name, grade__eduvate_grade_id}]` | OBSERVED |
| GET (INFERRED) | `/qbox/erp_user/user-mgmt/user-sections/` | (grade) | STATIC |
| GET (INFERRED) | `/qbox/academic/get_user_details/` or `/academic/get_student_list/` | grade, section(s), date | STATIC |
| POST (INFERRED) | `/qbox/academic/bulk_create_attendance/` | list of {student, status} for date | STATIC |
| POST (INFERRED) | `/qbox/academic/create_or_update_attendance/` | single upsert | STATIC |
| GET (INFERRED) | `/qbox/academic/show_attendance/`, `/student_attendance_between_date_range/`, `/multiple_student_attendance_between_dates/`, `/single_student_attendance_between_days/` | view/report | STATIC |
| POST (INFERRED) | `/qbox/erp_user/sms-notifying-students/` | notify absentees | STATIC |
| GET (INFERRED) | `/qbox/erp_user/user-mgmt/student-attendance/`, `/monthly-attendance-report/`, `/erp_user/student-annual-attendance/`, `/student-monthly-attendance/` | reports, student views | STATIC |
| GET (INFERRED) | reports service `/api/acad_performance/v2/erpuser-grade-sections(-students)/` | grade/section-wise attendance analytics | STATIC |

## Validation & rules
See `wf05_student_attendance.md` (BR-ATT-1..5) and AC ATT-01..06. Status values are UNKNOWN in the original. Replacement default: Present, Absent, Late, Half-day, Leave (configurable), with Present pre-selected.

## Permissions
Mark: teachers for their grades/sections (grade list is already scope-filtered, O). View/report: coordinators/principal. Students/parents: own attendance (S).

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/attendance` **rendered** for a teacher, but only the screen's **bootstrap** fired on navigation:
- `GET ERP /erp_user/user-mgmt/user-grades` — the grade/section selector the attendance screen loads first

The actual attendance-marking/fetch endpoints require selecting a grade+section+date (a user interaction the read-only crawl does not perform), so they stay STATIC_CALLSITE_CONFIRMED, not runtime-observed. This is an honest boundary of navigate-only crawling: the route is reachable for a teacher; the data call is gated behind a selection. See `REMAINING_UNKNOWNS.md` §3 (needs a write/interaction).
