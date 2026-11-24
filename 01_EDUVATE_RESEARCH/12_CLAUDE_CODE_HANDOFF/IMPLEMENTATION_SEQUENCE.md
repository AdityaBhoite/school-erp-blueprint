# Implementation Sequence

Each step lists deliverables and the acceptance criteria that close it. Do them in order.

## Phase 0 — Foundations
| Step | Deliverables | Closes |
|---|---|---|
| 0.1 | Monorepo scaffold, CI pipeline, docker-compose (Postgres, Redis, Mailpit, MinIO), lint/type/test baseline | — |
| 0.2 | `db`: Tenant, User, Role, Permission, RolePermission, RoleAssignment(scope), Session/RefreshToken, AuditLog; RLS policies; seed tenant + super admin | TEN-02 |
| 0.3 | Auth API: login, logout, refresh (rotation + reuse detection), forgot/reset password, change password, OTP/TOTP 2FA, lockout | AUTH-01..07 |
| 0.4 | Permission catalogue + nav registry package; PermissionGuard; ScopeService; generated authz-matrix test harness | RBAC-02 |
| 0.5 | Web shell: login, layout (rail + mega-menu from nav registry), branch/year context selectors, notifications bell (stub), profile menu, No-access page, theming tokens | RBAC-01, RBAC-05 |
| 0.6 | Audit interceptor + audit viewer (admin); file upload service (pre-signed, scan job); notification infrastructure (in-app + email, outbox, worker) | NFR-05 |

## Phase 1 — Org & People
| Step | Deliverables | Closes |
|---|---|---|
| 1.1 | Branch, AcademicYear (single current), BranchAcademicYear, Grade, GradeOffering, Section, ClassSection, Subject, ClassSubject; CRUD + soft delete/restore; admin screens | ORG-01, ORG-02 |
| 1.2 | Staff (profile, designation, department, teacher type), role assignment UI with scopes | RBAC-03 (partial) |
| 1.3 | Student, Guardian, StudentGuardian, siblings; StudentEnrolment; student/guardian screens; PII masking + reveal | PPL-01, PPL-03, ENR-01, RBAC-07 |
| 1.4 | Bulk import (students, staff, guardians) with validation report | PPL-02 |
| 1.5 | Parent and student login experience (child switcher) | RBAC-04 |

## Phase 2 — Daily teaching core
| Step | Deliverables | Closes |
|---|---|---|
| 2.1 | Calendar: event types, events (scope, recurrence), publication workflow, month/week views, holidays | CAL-01..04 |
| 2.2 | Timetable: period systems (overlap validation), slot allocation (clash rules), draft/publish, teacher/section views | TT-01..03 |
| 2.3 | Substitution: teacher absence, substitute assignment, reports | TT-04 |
| 2.4 | Diary: today's periods, classwork/homework entry, attachments, multi-section, "for another class" | DIA-01..03 |
| 2.5 | Homework: student lists (today/pending/submitted/evaluated), submission, evaluation, notifications | HW-01..03 |
| 2.6 | Attendance: mark (filter-first), bulk upsert, view by date range, monthly report, absence notifications, edit window | ATT-01..06 |
| 2.7 | Teacher dashboard (today's classes, attendance pending, diary pending, homework to evaluate) and student/parent dashboard | — |

## Phase 3 — Communication
| 3.1 | Announcements (category, audience builder with count, drafts, attachments, publish), banners | COM-01, COM-02 |
| 3.2 | Notifications centre (list, unread counts, mark read, push via FCM) | COM-03 |
| 3.3 | Tickets: categories/routing config, parent raise, staff thread, states, CSAT, dashboard | TKT-01..03 |
| 3.4 | SMS/WhatsApp provider adapters + message log | — |

## Phase 4 — Assessment & report cards
| 4.1 | Exam types, grading systems | — |
| 4.2 | Assessments (schedule, link to calendar), branch assignment, papers (upload PDF; question bank optional) | ASM-01 |
| 4.3 | Marks entry grid + Excel upload with validation; publish/unpublish; overdue dashboard | ASM-02..05 |
| 4.4 | Report-card templates (components, weights), generation run, review, publish, revert, PDF with signature | RC-01..03 |

## Phase 5 — Fees
| 5.1 | Fee heads, terms, plans (components per term), fee accounts, receipt series | FEE-01 |
| 5.2 | Plan assignment, dues computation, concessions (maker–checker), fines | FEE-02..04 |
| 5.3 | Payments: online gateway (order, webhook), offline cash/cheque, receipts (PDF), immutable ledger, cancellations/refunds with approval | PAY-01..03 |
| 5.4 | Parent fee page (dues, pay, history, receipts); wallet (basic); reconciliation export; finance dashboard | PAY-04 |

## Phase 6 — Lifecycle & admissions
| 6.1 | Registration → admission form → admission fee → create student → enrol | — |
| 6.2 | Approvals framework (generic maker–checker) for shuffle, student type, temporary/permanent status, TC | LIFE-01, LIFE-02 |
| 6.3 | Promotion and year rollover with preview/apply/history | ORG-03, ENR-02 |

## Phase 7 — Extended (choose per customer)
Transport; online classes; lesson-plan library and completion tracking; staff attendance and leave; reports gallery and exports; mobile app.
