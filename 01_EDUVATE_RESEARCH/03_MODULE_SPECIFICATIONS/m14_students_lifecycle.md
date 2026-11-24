# M14 — Students: Admission, Lifecycle and Approvals

**Access:** the Students category is not visible to the observed teacher. STATIC evidence only.

## Menu (Students mega-menu)
- **Admission**: Admission (`/admission`), Referral Report.
- **Student Management**: Student Shuffle, Section Shuffle, Student Type, Student Promotion, Temporary Status, Permanent Status, Student ID Card, Shuffle Report.
- **Attendance**: Student Attendance.
- **Reports**: Student Contact, Temporary Status, TPND, Total Form, SEN, User Status, Single Day Admission, Application Registration.
- **User Management**: View Users, User Level, Access Blocker, Virtual School, TC Request, OCAP Students.

## Approvals menu (Student approvals)
Temporary Status Request, Permanent Status Request, Permanent Status (Principal), Student Shuffle Request.

## Registration/admission routes
`/student-registration`, `/student-registration-form(/:id)`, `/student-registration-edit/:regid/:appId`, `/admission-form/:regId/:appId`, `/admission-form-edit/:regId`, `/admission-payment/:id`, `/application-input-form/*`.

## Data
Student session (enrolment) per year with section mapping; student type; status (temporary/permanent); siblings; parents; SEN; virtual (online-only) students.

Workflows: `wf07_admission_enrolment.md`, `wf08_student_lifecycle_approvals.md`. ACs: PPL-*, ENR-*, LIFE-*.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/student-registration` **rendered** for a teacher. Route-specific endpoints observed:
- `GET FIN /apiV1/receipt-config-list` and `GET FIN /apiV1/receipt-range-list` — **registration pulls fee-receipt configuration from the finance service** on load

Also runtime-observed elsewhere: `GET ERP /erp_user/user-mgmt/student-information` (envelope `{status_code, message, result}`) and `/erp_user/user-mgmt/student-subjects`. Architecture note: student registration is coupled to **finance receipt config** at the UI layer — relevant to the Phase 5 (Fees) boundary. Status: RUNTIME_OBSERVED.
