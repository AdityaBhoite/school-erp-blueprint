# WF-05 Student attendance — mark, view, notify

| Field | Detail |
|---|---|
| Actors | Class teacher / subject teacher (mark), Coordinator/Principal (reports), Parents (notified), Students (view own) |
| Trigger | Start of the school day (daily attendance) or a period (period-wise, UNKNOWN which mode the original uses by default) |
| Preconditions | Student enrolled in a section for the session; teacher allocated to the grade/section; the day is not a holiday |
| Evidence | O: `/erp/attendance` with tabs **Mark Attendance** / **View Attendance**; filters **Grade\*** and **Sections\*** (required) + **Search**; table columns `Sl No., ERP Id, Name, Grade, Section, Attendance`; grade list from `/erp_user/user-mgmt/user-grades/?acad_session&session_year`. S: `/academic/get_user_details/` (student list), `/academic/bulk_create_attendance/`, `/academic/create_or_update_attendance/`, `/academic/show_attendance/`, `/academic/student_attendance_between_date_range/`, `/academic/multiple_student_attendance_between_dates/`, `/academic/single_student_attendance_between_days/`, `/erp_user/sms-notifying-students/`, `/erp_user/user-mgmt/student-attendance/`, `/monthly-attendance-report/`, `/erp_user/present_absent` |

## Steps
1. Teacher opens Attendance → Mark Attendance. Grades are limited to the teacher's grades for the session. (O)
2. Selects Grade, then Section(s), then Search. (O: filters and Search button; I: multi-select, inferred from the placeholder "Select Sections")
3. The student list loads with a per-row attendance control (Present/Absent and possibly Late/Leave/Half-day, UNKNOWN). (O columns; statuses UNKNOWN)
4. Teacher marks all (default Present, PROPOSED) and submits once (**bulk create**). (S)
5. Edits the same day update the records (**create_or_update**). (S)
6. Optionally notify absentees' parents by SMS (`sms-notifying-students`). (S)
7. View Attendance tab: date-range view per section or student; monthly reports. (S)

## Business rules
- BR-ATT-1: One record per (student, date[, period]) — upsert semantics. (S)
- BR-ATT-2: Cannot mark future dates or holidays (PROPOSED; UNKNOWN in original).
- BR-ATT-3: Edit window (e.g. same day, or N days for coordinators) — UNKNOWN; PROPOSED configurable.
- BR-ATT-4: Attendance % = present days ÷ working days (excluding holidays) for the period. (I)
- BR-ATT-5: Students with access blocked / temporary status are excluded or flagged (I from "access-blocker" features).

```mermaid
flowchart LR
  A[Open Mark Attendance] --> B[Select Grade*]
  B --> C[Select Section(s)*]
  C --> D[Search → load roster]
  D --> E{Holiday / future date?}
  E -- yes --> X[Block with message]
  E -- no --> F[Mark P/A per student]
  F --> G[Submit bulk]
  G --> H[(Attendance records upsert)]
  H --> I{Notify absentees?}
  I -- yes --> J[Queue SMS/push to parents]
  I -- no --> K[Done]
```

Entities: Attendance, Student, SectionMapping, SessionYear, Holiday. Permissions: `attendance.mark` (scoped to allocated sections), `attendance.view`, `attendance.edit_past`, `attendance.notify`.
