# Workflow Index

Each workflow is reconstructed from observed screens (O), static routes/endpoints (S), and inference (I). Steps that could not be verified at runtime are labelled. Nothing was submitted in the original system, so **all write paths are unverified** at runtime.

| # | Workflow | File | Evidence basis | Confidence |
|---|---|---|---|---|
| WF-01 | Academic year setup and rollover (carry-forward) | `wf01_academic_year_setup_rollover.md` | S (master-management + section-carry-forward endpoints) | MEDIUM |
| WF-02 | Academic calendar generation, review and publishing | `wf02_academic_calendar.md` | O (calendar screen, status API) + S | HIGH |
| WF-03 | Timetable: period system → teacher allocation → publish → substitution | `wf03_timetable_substitution.md` | O (teacher timetable data) + S | MEDIUM-HIGH |
| WF-04 | Daily diary (classwork and homework) per period | `wf04_daily_diary_homework.md` | O (diary screen and data) + S | HIGH |
| WF-05 | Student attendance marking and absence notification | `wf05_student_attendance.md` | O (attendance screen) + S | MEDIUM-HIGH |
| WF-06 | Assessment lifecycle: schedule → papers → conduct → marks → publish → report card | `wf06_assessment_report_card.md` | O (ACMS assessments, marks dashboard) + S | MEDIUM-HIGH |
| WF-07 | Student admission and enrolment | `wf07_admission_enrolment.md` | S | MEDIUM |
| WF-08 | Student lifecycle changes with approval (shuffle, type, status, TC, promotion) | `wf08_student_lifecycle_approvals.md` | S | MEDIUM |
| WF-09 | Fee configuration, assignment, collection and receipts | `wf09_fees_collection.md` | S | MEDIUM |
| WF-10 | Concessions, refunds and wallet requests (maker–checker) | `wf10_concession_refund_wallet.md` | S | MEDIUM |
| WF-11 | Announcements (targeted, draft → publish) | `wf11_announcements.md` | S + O (banner/notifications APIs) | MEDIUM |
| WF-12 | Parent ticket (helpdesk) with CSAT | `wf12_parent_ticket.md` | S | MEDIUM |
| WF-13 | Staff leave application and approval | `wf13_staff_leave.md` | S + O (HRMS self-service) | MEDIUM |
| WF-14 | Sensitive profile change with approval (Aadhaar/PAN/Bank) | `wf14_profile_change_approval.md` | O (profile info banner) + S | MEDIUM (single UI notice; approver sequence UNKNOWN) |
| WF-15 | Online class scheduling and attendance | `wf15_online_class.md` | O (online class list shape) + S | MEDIUM |
