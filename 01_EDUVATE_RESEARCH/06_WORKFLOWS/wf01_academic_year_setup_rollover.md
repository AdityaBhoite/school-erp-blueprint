# WF-01 Academic structure setup and year rollover

Evidence: STATIC (`masterManagement` endpoint group: academic years, branches, branch-acad maps, grades, grade mappings, sections, section mappings, subjects, subject mappings, delete/restore pairs, `erp_system_config`, `default-current-session-year`, city/zone; `acms/section-carry-forward/{candidates, preview, apply, history}`) and OBSERVED response fields (`session_year_id`, `acad_session`, `branch__branch_code`, `branch__branch_type`, `grade__eduvate_grade_id`, `section_mapping_id`, `mapping_subject_id`). Confidence MEDIUM-HIGH.

## Hierarchy (as the API exposes it)
```
Brand ─┬─ Branch (branch_code, branch_type) ── references Zone and City independently (no Zone → City nesting evidenced)
       └─ SessionYear (academic year, is_current)
Branch × SessionYear  = BranchAcadSession ("acad_session")
BranchAcadSession × Grade = GradeMapping
GradeMapping × Section = SectionMapping ("section_mapping_id")  ← students and timetables attach here
SectionMapping × Subject = SubjectMapping ("mapping_subject_id") ← teachers attach here
```

## Setup steps
1. Create the session year (e.g. 2026-27); mark it current (default current session). (S)
2. Map branches to the session (branch-acad map). (S)
3. Map grades to each branch-session; create sections and map them (grade-section mapping). (S)
4. Map subjects to grades/sections (school subjects mapping; central grade-subject catalogue on the management service). (S)
5. Assign staff to subjects/sections (teacher allocation / timetable). (S)

## Rollover (carry-forward)
1. Select the source session and target session. (S)
2. `candidates` → list sections eligible to carry forward. (S)
3. `preview` → dry-run showing what will be created. (S)
4. `apply` → create target mappings; `history` records runs. (S)
5. Promote students (WF-08) and promote fee structures (WF-09).

Rules: soft delete with restore for all masters; no hard deletes once referenced; the current-session flag is unique per brand/branch (I).
