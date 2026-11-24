# Implementation Priorities (MoSCoW)

| Priority | Feature | Evidence basis | Why |
|---|---|---|---|
| **Must** | Multi-tenant org structure (tenant → branch → year → grade → section → subject) | OBSERVED | Everything is scoped by it |
| Must | Auth, RBAC with scopes, permission-driven navigation | OBSERVED/STATIC | Security and UX shell |
| Must | Staff, student, guardian records; enrolment | STATIC | Core data |
| Must | Academic calendar with typed events, publish workflow | OBSERVED | Teacher landing page in the original |
| Must | Timetable (period system, allocation, teacher/section views, substitution) | OBSERVED | Drives diary and attendance |
| Must | Diary (classwork/homework per period), homework submission/evaluation | OBSERVED | Daily teacher workflow |
| Must | Student attendance (filter-first, bulk mark, view, monthly report, absence notify) | OBSERVED/STATIC | Daily workflow |
| Must | Notifications (in-app + push + email), announcements | OBSERVED | Communication backbone |
| Must | Audit log | PROPOSED | Compliance |
| **Should** | Assessments, marks entry, grading, report cards | OBSERVED/STATIC | Core academic outcome |
| Should | Fees: plans, dues, online/offline payments, receipts, concessions, fines | STATIC | Core revenue function |
| Should | Parent tickets with CSAT | STATIC | Parent engagement |
| Should | Admissions and student lifecycle approvals, promotion, rollover | STATIC | Year-round admin |
| Should | Teacher and parent dashboards | STATIC | Engagement |
| **Could** | Transport, online classes, e-books, lesson-plan library and completion, observations, house system, event feedback, PTM feedback, visitor gate | STATIC | Extended value |
| Could | Staff attendance/leave (light HR) | STATIC | Needed for substitution |
| **Won't (now)** | Recruitment, payroll, CRM marketing, procurement, store/e-commerce, expansion/real estate, internal dev tools, AI interview/proctoring | STATIC | Separate products |
