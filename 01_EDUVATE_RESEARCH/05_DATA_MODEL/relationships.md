# Relationships and Cardinality

| From | To | Cardinality | Evidence | Class |
|---|---|---|---|---|
| Tenant (Brand) | Branch | 1 : N | `branch__brand_id`, `branch__brand__code` in `/user-branches/` | OBSERVED |
| City | Branch | 1 : N | `branch_count` on city; `city_fk` on branch | OBSERVED |
| Zone | Branch | 1 : N (independent of City) | `zone{}` on branch | OBSERVED |
| Staff | ClassSection / ClassSubject (TeachingAssignment) | M : N per year | source of teacher data scope | PROPOSED (original: user-grades/user-subjects scopes OBSERVED, timetable allocation OBSERVED) |
| Branch × AcademicYear | BranchAcademicYear | M : N via join | `session_year_id` + `branch_id` rows in `/user-branches/`; `acad_session` params | OBSERVED |
| BranchAcademicYear | GradeOffering | 1 : N | grademapping endpoints | STATIC |
| GradeOffering | ClassSection | 1 : N | `section_mapping__grade_id`, `section_mapping__section_id` | OBSERVED |
| ClassSection | ClassSubject | 1 : N | `mapping_subject_id`, `mapping_subject__subject__subject_name` | OBSERVED |
| Student | StudentEnrolment | 1 : N (one per year) | `student_session_data[]` with current-session flag | STATIC |
| Guardian | Student | M : N | sibling data, add-child-to-parent | STATIC |
| Staff | Branch/Grade/Section/Subject scopes | M : N | user-branches / user-grades / user-sections / user-subjects | OBSERVED (branches, grades) |
| PeriodSystem | Period | 1 : N ordered | `period_system[]` with `position` | OBSERVED |
| TimetableSlot | (Period, day, ClassSection, ClassSubject, Staff) | composite | `teacher_periods[]` | OBSERVED |
| TimetableSlot | Substitution | 1 : N (per date) | `substitute_*` fields | OBSERVED |
| DiaryEntry | ClassSection, Subject | M : N | `sections[]`, `subjects[]` | OBSERVED |
| DiaryEntry | HomeworkSubmission | 1 : N | student homework endpoints | STATIC |
| CalendarEvent | Branch/City/Grade/Subject | M : N | arrays of ids in event | OBSERVED |
| CalendarEvent | Assessment | 0..1 : 0..1 | `assessment`, `calendar_event_id`, `scheduled_via_calendar` | OBSERVED |
| Assessment | AssessmentBranchAssignment | 1 : N | `branch_assignment_id`, `branch_marks_published` | OBSERVED |
| Assessment | QuestionPaper | 1 : N (practice, sample, final) | `practice_paper_count`, `sample_paper_count`, `has_final_paper` | OBSERVED |
| Assessment | MarksEntry | 1 : N | upload-marks endpoints | STATIC |
| ReportCardTemplate | Assessment | M : N (weighted) | category-assessment mapping endpoints | STATIC (INFERRED weights) |
| User | Notification | 1 : N | notifications list per user | OBSERVED |
| Student | Payment / Receipt | 1 : N | fee receipt routes `/:erpId/:id` | STATIC |
| Student | Wallet | 1 : N (by wallet type) | wallet routes | STATIC |

## Integrity rules (PROPOSED)
- A student has at most one **active** enrolment per academic year.
- A ClassSection belongs to exactly one GradeOffering (and hence one branch and year).
- TimetableSlot uniqueness: (period_id, day, class_section_id, version) and (period_id, day, staff_id, version) unless `common_group_id` is set.
- Attendance uniqueness: (student_id, date, period_id NULLS NOT DISTINCT).
- MarksEntry uniqueness: (assessment_id, student_id).
- Receipt numbers unique per (tenant, branch, academic_year, series).
