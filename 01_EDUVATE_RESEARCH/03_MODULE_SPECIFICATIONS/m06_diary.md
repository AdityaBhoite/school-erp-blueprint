# M06 — Daily Diary

**Route:** `/erp/diary`. **Evidence:** E016 (OBSERVED). Screenshot `diary-teacher-week.png` (name redacted).

## Screen
- Header "Diary" + "Welcome, <teacher name>".
- Actions: **+ Homework for another class**, **Annual Lesson Plan**, **Refresh**.
- Week strip MON–SAT with dates; the selected day is solid brand colour.
- Section "ONGOING & UPCOMING": two-column grid of **period cards** — period code (P1…P10), start time (bold) and end time, class chip ("Grade 10 Sec A"), subject title, **LP** button (lesson plan for that period). Some cards have no subject (e.g. homeroom/assembly).
- (I) Past periods and completed entries appear in another section below (not captured).

## APIs (OBSERVED)
| Method | Path | Query | Response shape (abridged) |
|---|---|---|---|
| GET | `/qbox/school-feed/my-diary-given/` | `acad_session, start_date, end_date, page_num, page_size` | paginated `{count, total_pages, current_page, next, previous, results:[{id, title, classwork, homework, has_homework, media_files[], is_online, is_substitute, last_submission_dt, created_at, sections[], subjects[]}]}` (no envelope) |
| GET | `/qbox/timetable/teacher-timetable/` | `acad_session, teacher_id` | `{week_days{0..6}, week_holidays{0..6}, teacher_periods[...]}` |
| GET | `/qbox/timetable/branch-staff-timetable/` | `section_mapping` | `{period_system[], week_days, week_holidays, teacher_periods[]}` |
STATIC write endpoints (paths from the static catalog; HTTP methods are INFERRED from names, because the catalog records none; audit ER-07): `/academic/create-dairy/`, `/academic/dairy-upload/`, `/academic/delete-file/`, `/academic/assign-homework-dairy/`, `/lesson_plan/v2/upcoming-period-data/` (central), `/academic/new/dialy-diary-messages/`, general diary `/academic/v2/general-dairy-messages/`.

## Controls & behaviour
| Control | Purpose | Validation (proposed) |
|---|---|---|
| Day tile | switch date | only current week ± navigation |
| Period card | open entry editor for that period | — |
| Entry editor (I) | classwork (required), homework (optional), attachments, due date/time, online submission toggle | classwork ≤ 5,000 chars; due ≥ now; files ≤ 20 MB |
| LP | open lesson-plan resources for the period's chapter/topic | — |
| Homework for another class | choose grade/section/subject outside the timetable | scope check |
| Annual Lesson Plan | open the yearly plan (YCP) | — |
| Refresh | reload periods and entries | — |

## Permissions
Teachers (own periods + substitutions); coordinators (reports). Students/parents see diary messages for their section (S: general diary endpoints).

## Workflow
`06_WORKFLOWS/wf04_daily_diary_homework.md`.
