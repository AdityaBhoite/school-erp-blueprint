# Acceptance Criteria (measurable)

Format: **ID — Given / When / Then**. "Scope" means the user's role-assignment scope (tenant/branch/year/grade/section/subject).

## Authentication (AUTH)
- AUTH-01 — Given an active user with a correct password, when they log in, then they receive a session (httpOnly cookie) and land on their role's default page within 2 s (p75).
- AUTH-02 — Given 5 failed attempts within 15 min, when a 6th attempt is made, then the account is temporarily locked for 15 min and the response does not reveal whether the user exists.
- AUTH-03 — Given `must_change_password=true`, when the user logs in, then every route except "change password" and "logout" redirects to change password.
- AUTH-04 — Given an expired access token and a valid refresh token, when an API call is made, then exactly one refresh occurs (concurrent calls queue) and the original calls succeed.
- AUTH-05 — Given a reused (rotated) refresh token, when presented, then all sessions of that user are revoked.
- AUTH-06 — Given 2FA enabled, when the password is correct, then an OTP/TOTP is required before a session is issued.
- AUTH-07 — Logout invalidates the refresh token server-side; the back button shows no cached protected data.

## Tenant isolation (TEN)
- TEN-01 — Given users from tenant A and tenant B, when user A requests any resource ID belonging to B (all endpoints), then the response is 404 and no data leaks (automated matrix).
- TEN-02 — Direct SQL without the tenant context set returns 0 rows for tenant tables (RLS enforced).
- TEN-03 — File download URLs are signed, expire in ≤ 10 min, and are tenant-scoped.

## Authorization (RBAC)
- RBAC-01 — The navigation shows exactly the items whose `requiredPermission` the user holds (snapshot test per seeded role).
- RBAC-02 — Every API endpoint returns 403 for a user lacking the permission (generated matrix, 100% endpoint coverage).
- RBAC-03 — A subject teacher can read/write attendance and diary only for class sections in scope; other sections → 403.
- RBAC-04 — A parent can access only linked children; switching child changes all data views.
- RBAC-05 — Directly opening an unauthorized route shows a "No access" page (not a silent redirect) and logs nothing sensitive.
- RBAC-06 — Role and permission changes take effect within 60 s without re-login.
- RBAC-07 — Reveal of a masked sensitive field requires `pii.reveal` and creates an audit record.

## Org & academic structure (ORG)
- ORG-01 — An admin can create an academic year with non-overlapping dates; exactly one year is current per tenant.
- ORG-02 — Grades, sections and subjects can be mapped per branch/year; deleting a referenced mapping is blocked (or soft-deleted with restore), and restore brings back identical relationships.
- ORG-03 — Rollover preview lists all sections to be carried forward; apply is idempotent (running twice creates no duplicates).

## People & enrolment (PPL, ENR)
- PPL-01 — A student record requires first name, DOB, gender and guardian; the ERP ID is unique per tenant and auto-generated.
- PPL-02 — Bulk import of 500 students completes in ≤ 60 s; invalid rows are reported with row number and reason; valid rows import atomically per row.
- PPL-03 — Guardians are de-duplicated by phone; two students sharing a guardian appear as siblings.
- ENR-01 — A student has at most one active enrolment per year; moving section (shuffle) creates history.
- ENR-02 — Promotion moves selected students to the next grade's section for the target year, preserving history.

## Calendar (CAL)
- CAL-01 — Events have a type (holiday, assessment, event, competition, PTM, custom), date range and scope (branches/grades/subjects); month view loads ≤ 1 s for 200 events.
- CAL-02 — Draft events are invisible to non-editors; publishing makes them visible to all scoped users and sends a notification (configurable).
- CAL-03 — Holidays mark the day as non-working for attendance and timetable.
- CAL-04 — Recurring events expand correctly (weekly/monthly) with per-occurrence exceptions.

## Timetable (TT)
- TT-01 — Period times cannot overlap within a period system (validation error shows the conflicting periods).
- TT-02 — Allocating a teacher to two sections in the same day/period is rejected unless they share a common group.
- TT-03 — Teacher weekly view and section weekly view match allocations; draft changes are invisible until published.
- TT-04 — Marking a teacher absent lists their periods for the date; assigning a substitute makes the period appear in the substitute's diary flagged as substitution.

## Diary & homework (DIA, HW)
- DIA-01 — A teacher sees today's periods from the published timetable (including substitutions) in time order.
- DIA-02 — Saving a diary entry with classwork (required) and optional homework (with due datetime ≥ now) succeeds; attachments ≤ 20 MB each, allowed types only.
- DIA-03 — An entry can target multiple sections/subjects in scope; out-of-scope targets → 403.
- HW-01 — Students in target sections see the homework in "Today/Pending" within 1 min; guardians receive a notification.
- HW-02 — Student submission before the due time → "Submitted"; after → "Late" (if allowed by config) or blocked.
- HW-03 — Teacher evaluation (marks/remarks) changes status to "Evaluated" and notifies the student.

## Attendance (ATT)
- ATT-01 — Grade and Section are required before roster load; the roster shows Sl No, ERP ID, Name, Grade, Section, Status.
- ATT-02 — Bulk save of 45 students completes ≤ 1 s and is idempotent (re-submitting updates, not duplicates).
- ATT-03 — Marking attendance for a future date or holiday is rejected with a clear message.
- ATT-04 — Edits after the configured window (default same day) require `attendance.edit_past` and are audited.
- ATT-05 — Absence notifications are sent once per student per day per channel.
- ATT-06 — The monthly report attendance % equals present ÷ working days (unit-tested with holidays and half-days).

## Communication (COM, TKT)
- COM-01 — An announcement audience preview shows the recipient count before publishing.
- COM-02 — Publishing to 10,000 recipients enqueues delivery and completes in-app notifications within 5 min.
- COM-03 — The notification bell shows the unread count; marking read updates it in real time (≤ 5 s).
- TKT-01 — A parent can raise a ticket with category and attachments; it is routed to the configured assignee for that branch/category.
- TKT-02 — Status transitions follow the state machine; CSAT (1–5) is collectable only after Resolved.
- TKT-03 — The dashboard filters by branch, category, date range and CSAT.

## Assessment & report cards (ASM, RC)
- ASM-01 — An assessment requires grade, subject, exam type, date and total marks (> 0).
- ASM-02 — Marks entry rejects values > total marks or < 0; accepts AB/EX codes.
- ASM-03 — Excel marks upload validates ERP IDs and reports mismatches; nothing partial is committed for a section unless confirmed.
- ASM-04 — Publishing marks makes them visible to students/parents; unpublishing is audited.
- ASM-05 — The overdue indicator is shown when today > result date and marks are unpublished.
- RC-01 — Report-card generation computes weighted totals and grades per the grading system (unit tests with fixtures).
- RC-02 — Generated PDFs include the principal signature (if configured) and are downloadable by guardians after publish only.
- RC-03 — Reverting a published run hides it and records an audit entry.

## Fees (FEE, PAY)
- FEE-01 — A fee plan defines components per term; the total equals the sum of components (validation).
- FEE-02 — Assigning a plan to a student creates dues per term; dues = plan − approved concessions + fines.
- FEE-03 — Concessions require maker ≠ checker; amounts above the threshold need a second approver.
- FEE-04 — Late fines are computed per the rule after the last date and are capped.
- PAY-01 — Online payment is marked successful only after a verified gateway webhook; duplicate webhooks do not double-post.
- PAY-02 — Each payment produces exactly one receipt with a unique sequential number per series; the PDF downloads in ≤ 3 s.
- PAY-03 — Payments cannot be edited; cancellation/refund requires approval and creates reversing entries.
- PAY-04 — The daily reconciliation export lists gateway settlements vs receipts with mismatches highlighted.

## Lifecycle (LIFE)
- LIFE-01 — Status changes (temporary block, left/TC) require approval; a blocked student cannot log in or join online classes.
- LIFE-02 — A section shuffle request needs approval; on approval, attendance history remains attached to the old section for past dates.

## Non-functional (NFR)
- NFR-01 — Lighthouse performance ≥ 80 on dashboard pages (desktop).
- NFR-02 — Axe: 0 serious/critical violations on all pages.
- NFR-03 — Responsive: all core pages usable at 390 px width without horizontal scroll (except data tables, which scroll inside a container).
- NFR-04 — No secrets in the frontend bundle (CI check).
- NFR-05 — Every write to student, attendance, marks, fees or permissions produces an audit log entry with actor, timestamp, before/after.
