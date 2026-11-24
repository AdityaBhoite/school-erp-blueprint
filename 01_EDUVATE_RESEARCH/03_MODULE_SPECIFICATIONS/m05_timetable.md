# M05 — Timetable and Substitution

**Route:** `/erp/timetable` (also the timetable icon in the top bar). **Evidence:** E016 (OBSERVED screen + diary-page timetable APIs), STATIC endpoints. Screenshot `timetable-class-tab.png`.

## Screen
- Tabs: **Class Timetable** | **Teacher Timetable** | **Teacher Audit**.
- Legend ("Index"): **Substitute Class** (highlight), **COMM** Common Period, **SPLIT** Split Period, **SEN** Special Needs.
- Filters: Select Grade, Select Section; **How to use** help; side tab **Module Demo** (video/tour).
- Grid: rows = periods (from the period system), columns = weekdays. Empty state "Period system has not been created" when the section has no period system.

## Data (OBSERVED shapes)
`GET /qbox/timetable/branch-staff-timetable/?section_mapping=` →
```json
{"status_code":0,"message":"","result":{
 "period_system":[{"id":0,"period_name":"P1","position":1,"start_time":"08:20","end_time":"08:30","is_break":false}],
 "week_days":{"0":"Monday","...":"..."}, "week_holidays":{"0":null},
 "teacher_periods":[{"id":0,"period_id":0,"period__period_name":"","period__position":0,"period__start_time":"","period__end_time":"",
   "day":0,"teacher_id":0,"teacher__name":"","teacher__erp_id":"","teacher__teacher_type":"",
   "mapping_subject_id":0,"mapping_subject__subject__subject_name":"","is_draft":false,"is_delete":false,
   "dummy_teacher_id":null,"is_sen":false,"student_id":null,"common_group_id":null,
   "substitute_teacher_id":null,"substitute_teacher_erp_id":null,"substitute_teacher_name":null,"substitute_teacher_type":null,
   "sub_mapping_subject_id":null}]}}
```
(Values shown are type placeholders, not real data.) `GET /qbox/timetable/teacher-timetable/?acad_session&teacher_id=` returns the same structure for one teacher, plus `section_mapping__*` fields and `substitute_period_id`.

## STATIC endpoints
Period systems (`/timetable/period-system/`, overlap check, section options, `/timetable/period/`), allocation (`/timetable/teacher-period/`, `-days/`, `-common/`), lists (`/timetable/tt-subjects/`, `/tt-teachers/`, `/teacher-grades/`, `/teacher-sections/`, `/teacher-common-sections/`), student timetable (`/timetable/student-timetable/`, `/student-tt-dash/`), substitution (`/timetable/absent-teacher/`, `/substitute-teacher/`, `/substitute-report/`, `/teacher-tt-report/`, `/substitute-detail-report/`), legacy `/period/timetable/`, `/period/teacher-retrieve-week-periods/`, `/academic/delete_time_table/`. "Teacher Audit" relates to `/school-gate/teacher-audit/` and `teacher-analysis` (classroom audio-based teacher analysis, S).

## Concepts to implement
- **Dummy teacher** placeholders (`dummy_teacher_id`) for unfilled vacancies.
- **Common period** (`common_group_id`): one slot shared by multiple sections (e.g. 2nd-language groups).
- **Split period**: one section split across subjects/teachers in the same slot.
- **SEN** 1:1 periods for a specific student.
- **Draft vs published** allocations.
- **Substitution** per date with substitute teacher and optionally a different subject.

Workflow: `06_WORKFLOWS/wf03_timetable_substitution.md`. ACs: TT-01..04.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/timetable` **rendered** for a teacher. Route-specific endpoints observed:
- `GET ERP /timetable/student-timetable` — the teacher's timetable view (envelope `{status_code, message, result}`)
- `GET ERP /timetable/unfilled-section-slots` — gaps/unfilled slots (a scheduling-completeness signal)
- `GET ERP /erp_user/user-mgmt/student-information` — roster context for the timetable

Both timetable endpoints were **new** vs the Phase 1/2 baseline (now RUNTIME_OBSERVED with value-free shapes in E019). The static catalog's richer `/timetable/*` surface (draft-teachers-data, branch-staff-timetable, etc.) stays STATIC — those need an admin/coordinator role. See `STATIC_VS_RUNTIME_COMPARISON.md`.
