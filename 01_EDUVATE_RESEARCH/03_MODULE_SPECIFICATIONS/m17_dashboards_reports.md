# M17 — Dashboards and Reports

**Access:** `/erp/dashboard` redirects the teacher to the profile (no dashboard permission, OBSERVED). Dashboards are served largely by a separate reports service (`reports.letseduvate.com`) (STATIC).

## Dashboards in the menu (STATIC)
ERP: Main, Academic, Operations, Branch Analysis, Finance V1/V2, Fee Collection, Collection, Late Fine, Books & Uniform, Net Paying Strength, Store, Ekart, Fee Follow Up, Reimbursement, Reconciliation, Expense, Parent Feedback Analysis, Accountant, Finance Partner, Sibling, PTM Feedback, Language App. HRMS: HRMS, Attrition, Teacher Recruitment, KP, Recruiter Performance. CRM: CRM, School Partner Payment.

## Teacher dashboard widgets (STATIC `teacherDashboard` endpoints)
Today's attendance, class-wise attendance, assessment report, curriculum completion (grade-subject-section), classwork stats, homework stats, curriculum widget, today's diary stats, today's classes (periods), student attendance, activities, curriculum report.

## Student dashboard widgets (STATIC `studentDashboard`)
Today's classes (online), pending homework, pending classwork, assessment report, annual/monthly attendance, upcoming holidays, assessment performance, upcoming exams, homework performance, diary stats, today's timetable.

## Admin dashboard (STATIC `adminDashboard`, 63 endpoints)
Branch strength (school/grade-wise, Excel downloads), attendance, fees, staff, etc.

## Reports (STATIC)
Students: contact, temporary status, TPND, total forms, SEN, user status, admissions. Diary reports (grade → teacher). Online class reports. Assessment reports (individual student, comparison, report-card pipeline). Finance reports (see M15). Payroll reports gallery (HRMS).

Replacement: role dashboards built on SQL views; export jobs. ACs: NFR-01, PR-6.
