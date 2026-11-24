# System Requirements (functional + non-functional)

Priority: M = Must (MVP), S = Should (v1), C = Could (v2). Evidence: O/S/I/P (see README). Where a row mixes classes, each part is labelled separately: "O" covers only what the read-only tour actually saw (audit ER-15…ER-30, applied 2026-11-23). Acceptance criteria IDs in brackets.

## 1. Platform & shell
| ID | Requirement | Pri | Ev |
|---|---|---|---|
| FR-SH-01 | Multi-tenant SaaS: each tenant (school group) has branches; tenant branding (name, logo, primary colour) | M | O/S |
| FR-SH-02 | App shell: icon rail of permitted categories; mega-menu with sub-sections and items; breadcrumb; page header card | M | O |
| FR-SH-03 | Global context selectors: Branch and Academic Year; all screens re-scope on change; remember the last selection | M | O (selectors) / I (re-scope) / P (remember) |
| FR-SH-04 | Global search (students, staff; type selector) honouring scope | S | O (type selector seen) / P (student/staff providers) |
| FR-SH-05 | Notification bell with unread counts (total + approvals) and a paginated list; mark read | M | O (counts, list) / P (mark read) [COM-03] |
| FR-SH-06 | Help centre/assistant entry point (FAQ, optional AI later) | C | O |
| FR-SH-07 | Home banners (announcement and promotional) with web/app images | C | O |
| FR-SH-08 | Unauthorized routes show "No access" | M | P [RBAC-05] |

## 2. Identity & access
| FR-IAM-01 | Login for staff, students and guardians with username (ERP ID)/email/phone + password; OTP option | M | S (staff/student login endpoints) / I (guardian login) / P (OTP for ERP users) [AUTH-01] |
| FR-IAM-02 | Forced password change, password reset, lockout, optional 2FA | M | S [AUTH-02..06] |
| FR-IAM-03 | Roles = permission bundles; system roles seeded; tenant-defined roles | M | S/P |
| FR-IAM-04 | Role assignments scoped to tenant/branch/grade/class section/subject and academic year | M | O/P [RBAC-03] |
| FR-IAM-05 | Navigation derived from permissions (single registry) | M | O [RBAC-01] |
| FR-IAM-06 | Individual time-bound grants with reason (replaces hard-coded user allow-lists) | S | P |
| FR-IAM-07 | Impersonation for super admins (audited, banner, read-only default) | C | S |
| FR-IAM-08 | Audit log viewer with filters (actor, entity, date) | M | P |

## 3. Organisation & academic structure
| FR-ORG-01 | Manage zones, cities, branches (code, type) | M | S (management) / O (read lists) |
| FR-ORG-02 | Academic years with one current year; branch participation per year | M | O (fields) / S (management) [ORG-01] |
| FR-ORG-03 | Grades (with sort order and optional global code), sections, subjects (scholastic/co-scholastic) | M | O (fields) / S (management) |
| FR-ORG-04 | Mappings: grade offering per branch-year, class sections, class subjects; soft delete/restore | M | S [ORG-02] |
| FR-ORG-05 | Year rollover: candidates → preview → apply → history | S | S [ORG-03] |
| FR-ORG-06 | Curriculum catalogue: volumes/editions, chapters, topics, key concepts per grade-subject | S | S |
| FR-ORG-07 | System configuration per tenant/branch (week holidays, attendance mode, edit windows, notification toggles) | M | S |

## 4. People
| FR-PPL-01 | Staff directory and profile (designation, department, branch, reporting manager, shift, teacher type, contact) | M | O (own profile only) / S (directory) |
| FR-PPL-02 | Student profile (personal, enrolment, guardians, siblings, status, documents) | M | S |
| FR-PPL-03 | Guardian accounts linked to multiple students; child switcher | M | S [RBAC-04] |
| FR-PPL-04 | Bulk import/export for students, staff, guardians | M | S [PPL-02] |
| FR-PPL-05 | ID card generation (students, staff) | C | S |
| FR-PPL-06 | Sensitive field masking with permissioned reveal and audit | M | O [RBAC-07] |
| FR-PPL-07 | Profile change requests requiring approval for sensitive fields | S | O (UI notice) / S (routes) / I (flow) |

## 5. Calendar & timetable
| FR-CAL-01 | Typed calendar events with scope (branches/grades/subjects), recurrence, colour, notes | M | O [CAL-01, CAL-04] |
| FR-CAL-02 | Draft → generated → published workflow; review comments | M | O (flags, Reviews button) / I (transitions, comments) / P (review step) [CAL-02] |
| FR-CAL-03 | Month/week/day views; filters by branch and grade; fullscreen | M | O (month view, filters, fullscreen) / S (legacy weekly/daily endpoints) / P (week/day views) |
| FR-CAL-04 | Holidays feed attendance and timetable | M | O/I [CAL-03] |
| FR-TT-01 | Period systems (ordered periods, times, breaks) with overlap validation; assign to sections | M | O/S [TT-01] |
| FR-TT-02 | Slot allocation (day × period × section × subject × teacher), common groups, SEN 1:1 slots, draft/publish | M | O (read fields) / S, I (allocation, publish) [TT-02, TT-03] |
| FR-TT-03 | Teacher, section and branch timetable views; teacher workload | M | O (teacher/section data via Diary page; Timetable screen showed an empty state) / S (workload) |
| FR-TT-04 | Teacher absence and substitution with reports | S | S [TT-04] |

## 6. Teaching & learning
| FR-DIA-01 | Diary: week strip; today's periods; classwork + homework per period; attachments; due datetime; online-submission flag | M | O (read view, fields) / S (write endpoints) / I (editor, save) [DIA-01, DIA-02] |
| FR-DIA-02 | Diary for another class; multi-section/subject entries; substitute flag | M | O (button, array fields, flag) / S, I (write) [DIA-03] |
| FR-DIA-03 | Diary completion reports (grade → section → subject → teacher) | S | S |
| FR-HW-01 | Student homework lists: today, pending, submitted, evaluated; submission with files | M | S [HW-01, HW-02] |
| FR-HW-02 | Teacher evaluation (marks/remarks), analytics | M | S [HW-03] |
| FR-LP-01 | Lesson plans per chapter/topic/period with resources; link from the diary period card; completion tracking | S | O/S |
| FR-OC-01 | Online classes (Zoom/Meet providers): create, recurring, cancel, join/start, attendance, recordings | C | O/S |
| FR-EB-01 | E-books / online books by grade and book type | C | O (dropdown) / UNKNOWN (listing endpoint returned 404) |

## 7. Attendance
| FR-ATT-01 | Mark attendance: required Grade + Section(s) filters, roster table, bulk save (upsert) | M | O (filters, columns) / S (bulk endpoint names) / I (method, upsert) [ATT-01, ATT-02] |
| FR-ATT-02 | View attendance by date range per section/student; monthly reports; school strength | M | S [ATT-06] |
| FR-ATT-03 | Absence notifications to guardians (SMS/push/WhatsApp) | M | S [ATT-05] |
| FR-ATT-04 | Rules: no future/holiday marking; edit window; audit | M | P [ATT-03, ATT-04] |
| FR-ATT-05 | Staff attendance (manual/biometric import) and leave (light HR) | C | S |

## 8. Assessment
| FR-ASM-01 | Exam types; grading systems (bands) | S | O/S |
| FR-ASM-02 | Assessments per grade-subject with date/time (or via calendar), total marks, duration, mode, section access, status | S | O [ASM-01] |
| FR-ASM-03 | Papers: practice/sample/final (lock), blueprint; question bank (optional) | C | O/S |
| FR-ASM-04 | Marks entry (grid + Excel/OMR), validation, publish per branch, overdue/urgent dashboard | S | O (dashboard fields; Enter Marks / Template / Upload buttons) / S (upload endpoints) / I (grid, publish) [ASM-02..05] |
| FR-RC-01 | Report-card templates (components, sub-components, weights, co-scholastic, traits), generation runs, review, publish, revert, PDF with principal signature | S | S/O [RC-01..03] |
| FR-RC-02 | Hall tickets (bulk generation) | C | S |

## 9. Communication
| FR-COM-01 | Announcements: categories, audience builder with count, attachments, drafts, publish | M | S [COM-01, COM-02] |
| FR-COM-02 | Multi-channel delivery (in-app, push, email, SMS, WhatsApp) with logs | M/S | O (FCM registration only) / S (other channels) |
| FR-TKT-01 | Parent tickets: categories, routing, thread, statuses, CSAT, dashboard and reports | S | S [TKT-01..03] |
| FR-COM-03 | Feedback forms (event feedback, PTM feedback) | C | O/S |
| FR-COM-04 | Chat (staff-parent/staff-staff) with monitoring | C | S |

## 10. Finance
| FR-FEE-01 | Fee heads, terms, plans (components per term), receipt series, bank/fee accounts | S | S [FEE-01] |
| FR-FEE-02 | Plan assignment (bulk/individual), dues, demand notices | S | S [FEE-02] |
| FR-FEE-03 | Concessions (master, sibling) with maker–checker; fines with rules and waivers | S | S [FEE-03, FEE-04] |
| FR-PAY-01 | Online payments via gateway with webhook verification; offline cash/cheque; receipts (PDF) | S | S [PAY-01, PAY-02] |
| FR-PAY-02 | Cancellations/refunds/wallets with approval; immutable ledger | S | S [PAY-03] |
| FR-PAY-03 | Reconciliation export; finance dashboards (collection, dues, late fine) | S | S [PAY-04] |

## 11. Student lifecycle
| FR-LIFE-01 | Registration → admission form → admission fee → student creation → enrolment | S | S |
| FR-LIFE-02 | Approvals for section shuffle, student type change, temporary/permanent status, TC | S | S [LIFE-01, LIFE-02] |
| FR-LIFE-03 | Promotion (bulk) | S | S [ENR-02] |

## 12. Transport (C)
| FR-TRN-01 | Routes, stops, vehicles, drivers/attendants, trips; student allocation; transport fee plans; GPS live tracking integration | C | S |

## 13. Reporting
| FR-RPT-01 | Dashboards per persona (teacher, principal, accountant, parent, student) | S | S |
| FR-RPT-02 | Exports (CSV/XLSX/PDF) as async jobs with notification | S | P |

## 14. Non-functional
See `../07_NON_FUNCTIONAL_REQUIREMENTS/` — security (SR-*), performance (PR-*), scalability (SC-*), reliability (RL-*), deployment — and NFR-01..05 in acceptance criteria.
