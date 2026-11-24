# Domain Model (inferred)

> We did **not** see the private database schema. Entities and fields are inferred from API response shapes observed at runtime (E016, value-free), endpoint names (E006/E011), route names (E012) and screens. Field names in `code` are as observed in APIs; the proposed replacement schema is in `entity_dictionary.md` and `er_diagram.mmd`.

## 1. Bounded contexts
| Context | Core aggregates | Evidence strength |
|---|---|---|
| **Tenancy & Org** | Brand(Tenant), Zone, City, Branch, SessionYear, BranchSession | OBSERVED fields + STATIC |
| **Academic structure** | Grade, Section, Subject, GradeMapping, SectionMapping, SubjectMapping, Board/Curriculum, Volume, Chapter, Topic, KeyConcept | OBSERVED + STATIC |
| **Identity & Access** | User (ERP ID), UserLevel, Role, Module, Permission, AccessMatrix rule, UserBranch/UserGrade/UserSubject scopes | OBSERVED + STATIC |
| **People** | Staff (employee profile), Student, Parent/Guardian, Sibling link, StudentSession (enrolment) | OBSERVED (staff) + STATIC |
| **Scheduling** | PeriodSystem, Period, TeacherPeriod (allocation), Substitution, TeacherAbsence, CalendarEvent, Holiday, EventType | OBSERVED |
| **Teaching & Learning** | LessonPlan (periods, resources, completion), YearlyCurriculumPlan, DiaryEntry, Homework, HomeworkSubmission, OnlineClass, EBook, Clicker | OBSERVED + STATIC |
| **Attendance** | StudentAttendance, StaffAttendance | STATIC (+ OBSERVED screen) |
| **Assessment** | ExamType, GradingSystem, Assessment, AssessmentBranchAssignment, Blueprint, QuestionPaper, Question, MarksEntry, ReportCardConfig/Component/SubComponent, ReportCardPipeline, PrincipalSignature, HallTicket | OBSERVED + STATIC |
| **Communication** | Announcement (+category, audience, attachments), Notification, Banner, Ticket (+category, messages, CSAT), Feedback, ChatThread, MessageLog (SMS/email/WhatsApp) | OBSERVED + STATIC |
| **Finance** | FeeTerm, FeePlan, FeeGroup/Component, StudentFeeAssignment, Due, Payment, Receipt, Concession, Fine, Wallet(+txn), Refund, BankAccount | STATIC |
| **Transport** | Route, Stop, Vehicle(Bus), Driver/Staff, TripSchedule, StudentTransportAllocation, TransportFeePlan | STATIC |
| **HR (optional)** | Employee profile, Department, Designation, Cadre, Shift, Leave, Holiday, Asset, Transfer, Separation, Payroll… | OBSERVED (profile) + STATIC |

## 2. Key relationships
- Brand 1—* Branch. Each Branch references a Zone and a City **independently** (`zone{}` and `city_fk{}` on `/central-db/branch-view/`, OBSERVED). A Zone → City nesting is not evidenced (audit ER-36). Cities expose `branch_count`.
- Branch *—* SessionYear via **BranchSession** (`acad_session`); almost every academic query is keyed by `acad_session` or `session_year` + `branch`.
- BranchSession 1—* GradeMapping (grade enabled in branch for the year) 1—* **SectionMapping** (`section_mapping_id`: the "class", e.g. Grade 10 Sec A).
- SectionMapping 1—* **SubjectMapping** (`mapping_subject_id`).
- Grade has a global identity across brands (`eduvate_grade_id`), i.e. a central catalogue + local copy.
- Student 1—* **StudentSession** (per session year, with section mapping) — "student_session_data … session_year__is_current_session".
- Staff (User) *—* Branch, Grade, Section, Subject through scope tables (`user-branches`, `user-grades`, `user-sections`, `user-subjects`).
- **TeacherPeriod** = (PeriodSystem.Period × day × SectionMapping × Teacher × SubjectMapping) with draft flag, SEN student, common group, substitution.
- **DiaryEntry** → Teacher, date, sections[], subjects[], with homework deadline; HomeworkSubmission → Student × DiaryEntry/Homework.
- **CalendarEvent** → SessionYear; scoped to cities[], branches[] (with exclusions), grades[], subjects[]; optional link to Assessment/test type.
- **Assessment** → Grade, Subject, ExamType/test type, SessionYear; 1—* BranchAssignment (marks publish status per branch); 1—* QuestionPaper (practice/sample/final); 0..1 Blueprint; optional CalendarEvent.
- MarksEntry → Assessment × Student (× section mapping). ReportCard config maps Assessments → Components/SubComponents.
- Parent *—* Student (siblings share parents); Ticket → Parent/Student, category, branch; Notification → User.

## 3. Identifier conventions (OBSERVED)
- Numeric surrogate IDs for all relational entities (`id: integer`).
- Human/business IDs: `erp_id` (users, string), `branch_code` (numeric string), assessment `code`, receipt numbers.
- Notifications use string IDs (MongoDB ObjectId-like) — separate store.
- Soft-delete flag `is_delete` on many entities (sections, papers, online classes, exam types, timetable allocations).
- Audit-ish fields `created_at`, `updated_at`, `created_by`, `updated_by` on some entities.
- Draft flags: `is_draft` (timetable allocation, report-card category), `is_published` (calendar events).
