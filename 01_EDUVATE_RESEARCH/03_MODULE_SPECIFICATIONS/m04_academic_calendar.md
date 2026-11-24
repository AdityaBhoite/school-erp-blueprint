# M04 — Academic Calendar

**Route:** `/erp/acms/calendar` (Academics default for teachers). Breadcrumb `Home · Acms · Academic Calendar`. Also `/erp/calendar` (events/imprest events list) and `/erp/calendar-hub` (denied for teacher).
**Evidence:** E013–E016 (OBSERVED screen + APIs). Screenshot `academic-calendar-month-view.png`.

## Screen
- Header card: calendar icon + "Academic Calendar", chips: academic year (`2026-27`), branch (green outline), "All Grades"; right action **Reviews**.
- Toolbar: **Today**, ◀ month ▶ (read-only month input "November 2026"), branch indicator, **grade select** (All Grades), fullscreen toggle.
- Month grid Sun–Sat; each day is a button whose accessible name lists its events ("November 4, 2026 Weekly Test 1 Assessment +2 more"). Event chip = title + category caption, colour by category, and a two-colour progress underline for assessments (I: status of paper/marks). "+N more" opens the day's list.
- Sundays and holidays in red; today highlighted.

## APIs (OBSERVED)
| Method | Path | Query | Purpose |
|---|---|---|---|
| GET | `/qbox/acms/cities/` | `session_year?` | city filter options `{id, city_name, description, is_active, branch_count, created_at}` |
| GET | `/qbox/erp_user/grade/` | — | grades with branch `{id, grade_id, grade_name, branch_id, branch_name}` (envelope uses `data`, not `result`) |
| GET | `/qbox/acms/team-roles/my_roles/` | — | user's ACMS team roles (empty for a teacher) |
| GET | `/qbox/acms/calendar-events/holiday_range/` | `session_year` | `{has_holidays, start_date, end_date, count, generated_at}` |
| GET | `/qbox/acms/calendar-events/calendar_status/` | `session_year` | `{session_year_id, is_published, is_generated, draft_events_count, total_events_count}` |
| GET | `/qbox/acms/calendar-events/` | `branch, start_date, end_date, session_year` | event list (see entity CalendarEvent) |
| GET | `/qbox/academic/imprest-event/` | `acad_session, start_date, end_date, page` | paid/registrable events (`event_price`, `refundable`, `is_subscription_need`, registration window, participation counts, `approval_status`, `scores_published`) |
| GET | `/qbox/academic/event-coscholastic-subjects/` | — | co-scholastic subject list |
| GET | `/qbox/erp_user/user-mgmt/user-branches/` | `session_year` | branches in the user's scope |

## Event types observed
Holiday, Assessment, Other Event, Competition, Parent-Teacher Meeting (labels from screen). `test_type` subtypes for assessments (e.g. Weekly Test, Pre-Board Examination).

## Permissions
- View: all staff in branch scope (O). Create/edit/generate/publish/review: ACMS ops roles (S: `allowAcmsOpsTeam`). UNKNOWN exact role IDs.

## Workflow
See `06_WORKFLOWS/wf02_academic_calendar.md`.

## Replacement notes
- Provide month/week/agenda views; colour legend; filters (branch, grade, type); ICS export (P).
- Event editing drawer with scope builder (cities → branches with exclusions, grades, subjects), recurrence, assessment link.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/calendar` **rendered** for a teacher. Route-specific endpoints observed on the wire (beyond the shell set in m01):
- `GET ERP /academic/imprest-event` — calendar event feed
- `GET ERP /academic/{id}` — event detail (envelope `{status_code, message, result}`)
- `GET ERP /erp_user/user-mgmt/user-branches` — branch scope for the calendar

Confirms the calendar is academic-event driven and branch-scoped. No write/publish endpoint was triggered (navigate-only). Status: RUNTIME_OBSERVED. See `ROLE_ROUTE_API_MATRIX.md` §6.
