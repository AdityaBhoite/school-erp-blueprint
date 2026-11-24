# CLAUDE CODE MASTER PROMPT: Build an Independent Multi-Tenant School ERP

> Version 2, 2026-11-23, written after the documentation audit. It **supersedes** `12_CLAUDE_CODE_HANDOFF/CLAUDE_CODE_MASTER_PROMPT.md`.
> This prompt is self-contained: you can plan and start Phase 0 from it alone. The files it cites hold the detail for later steps.

---

## 0. Mission

You are building a **new, independently implemented, production-grade school ERP** for school groups that run several campuses. Its functional design comes from a documented, read-only study of an existing commercial K-12 school-chain platform ("the original").

**You build your own product.** Do not reproduce the original's code, branding, wording or look.

Work in phases, beginning with Foundations (Phase 0) and continuing through the daily-teaching core (Phase 2). Phase 2 covers calendar, timetable, diary, homework and attendance, the features every teacher, parent and student uses daily. Later phases are gated by business decisions listed in §11.

---

## 1. Non-negotiable constraints

1. **No access to the original.** Never contact its hosts, APIs or apps; never search for or use its source, data or assets. Everything you need is in this folder.
2. **Intellectual property.**
   - Do not copy or paraphrase the original's code, CSS, copy text, icons, images, logos, names or brand colour.
   - Do not mirror its API paths (`/qbox/...`), its internal field names (`section_mapping`, `acad_session`, `mapping_subject`), its user-ID format, or its menu labels.
   - You may re-implement generic functional behaviour and common UX patterns: tables, filter bars, calendars, drawers, week strips.
3. **Names.** Product name: placeholder **"School ERP"** until the owner chooses one (Q19). Never use the names Orchids, Eduvate or letseduvate in code, UI, seeds or docs you produce.
4. **Synthetic data only.** Use seeded generators with a fixed seed. Never use real names, phone numbers, ID numbers or schools.
5. **Security is part of every feature.** §8 and `SECURITY_REQUIREMENTS.md` are mandatory acceptance conditions, not later hardening.
6. **Scope discipline.** Build only what §4 puts in scope for the current phase. Seeing something in the original's menus, routes or endpoint catalogs is **not** a reason to build it.
7. **Do not invent facts about the original.** Where something is UNKNOWN, design your own solution (PROPOSED), record an ADR, and move on.

---

## 2. Background: what was studied, and how much to trust it

### 2.1 The original (for context only)
- A multi-brand K-12 platform serving school chains in India. Each brand (a school group) has many campuses ("branches") grouped by zone and city.
- Three separately built React + Ant Design single-page apps on one origin: ERP (academics, students, finance, transport, operations), HRMS (HR, payroll) and CRM (admissions marketing). They share a navigation shell with 772 menu items.
- The backend is about 10 REST services, **inferred** to be Django REST Framework, plus MongoDB for notifications, hosted on GCP (inferred).
- It was studied on 2026-11-22/23 with **one teacher account**, read-only. The tour only navigated: no clicks, no form submissions. 70 unique routes were visited (39 accessible, 31 denied) and 94 endpoints were captured as value-free JSON shapes. About 2,430 endpoint path templates were recovered from shipped JavaScript.

### 2.2 Evidence labels you will see in the package

| Label | Meaning | How you use it |
|---|---|---|
| **OBSERVED** | Seen at runtime with the teacher account: a screen, a field name in a response, a status code | Strong evidence that the concept exists and matters. Match the *function*; design your own implementation. |
| **STATIC** | Found in shipped frontend code: routes, menus, endpoint names | The feature exists in code; it may be unreachable or legacy. **It is not a requirement by itself**; §4 decides scope. HTTP methods for static endpoints are **unknown** (they are INFERRED from names). |
| **INFERRED** | Deduced from evidence | A reasonable default you may refine |
| **PROPOSED** | Design recommendation for this product | The target design |
| **UNKNOWN** | Not established | Use the documented default (§11), record an ADR, continue |

### 2.3 Strength of evidence by area
- **Strong** (OBSERVED screens and data): app shell; academic calendar; diary; teacher timetable data; attendance and homework filter screens; assessment lists and marks dashboard; announcements; notifications; HR self-service reads.
- **Weak** (STATIC menus and routes only): administration screens; **finance** (no screen, rule or core API was ever seen); transport; tickets; dashboards; the **whole parent and student experience** (no such account existed).
- **Never observed:** any write operation. Every create, update, approve or publish flow in the package is reconstructed or proposed.

---

## 3. Sources of truth and precedence

When documents disagree, the higher item wins:
1. **This prompt.**
2. `SECURITY_REQUIREMENTS.md`: 75 MUST/SHOULD requirements.
3. `AUDIT_REPORT.md` §G **errata register** (48 binding corrections to labels, counts and decisions) and §B.3 (architecture decisions).
4. `05_DATA_MODEL/er_diagram_core_v2.mmd`: the core data model with keys and scoping. It wins over `er_diagram.mmd` for the entities it covers.
5. `12_CLAUDE_CODE_HANDOFF/`: `ARCHITECTURE_BLUEPRINT.md`, `DEVELOPMENT_RULES.md`, `DEFINITION_OF_DONE.md`, `IMPLEMENTATION_SEQUENCE.md`, `SYSTEM_REQUIREMENTS.md`.
6. `08_TESTING/acceptance_criteria.md`, `03_MODULE_SPECIFICATIONS/`, `06_WORKFLOWS/`, `05_DATA_MODEL/entity_dictionary.md`.
7. `OPEN_QUESTIONS.md` and `DOCUMENTATION_GAPS.md` (defaults and open decisions).
8. Evidence (`02_`, `04_`, `09_`, `10_` folders): use it to understand behaviour. **Never copy from it.**

If a conflict is not resolved by this order, choose the more secure and more conservative option, record an ADR, and report it in your next summary.

---

## 4. Product scope

### 4.1 Phases (build in this order)

| Phase | Scope | Gate (§11) |
|---|---|---|
| **0 Foundations** | Monorepo, CI/CD, docker-compose, auth (login, refresh, logout, reset, MFA), tenant resolution and RLS, RBAC engine and permission catalogue, audit log, file service, notification infrastructure (in-app + email via outbox/worker), app shell | none |
| **1 Org & People** | Branches, academic years, branch participation, grades, grade offerings, sections, class sections, subjects, class subjects (soft delete/restore); staff; students; guardians and sibling links; enrolment; bulk import; guardian/student login with child switcher | none (defaults) |
| **2 Daily teaching core** | Academic calendar (draft → publish); period systems; timetable versions and slots; substitution, with minimal staff absence; diary; homework (assign, submit, evaluate); student attendance (mark, view, monthly report, absence notification); teacher dashboard; guardian/student dashboard (read) | build now; confirm Q01–Q04, Q10 and substitution rules before the pilot |
| 3 Communication | Announcements (audience builder, drafts, attachments), notification centre and push, parent tickets with CSAT, SMS/WhatsApp adapters | Q09, Q11, channel decision |
| 4 Assessment | Exam types, grading bands, assessments, marks entry (grid + Excel), publish, report cards (templates, runs, PDF) | 4.4 needs Q05, Q06 and sample report cards |
| 5 Fees | Fee heads, terms, plans, dues, payments, receipts, concessions, fines, refunds | **Blocked:** customer fee rules, gateway, legal receipt content |
| 6 Lifecycle | Generic approvals (maker–checker), shuffle, status changes, promotion and rollover; admissions and TC | admissions blocked on form/TC specs |
| 7 Extended | Transport, online classes, lesson-plan library, light HR, reports gallery, mobile | Q18 |

### 4.2 Out of scope (do not build)
Recruitment, payroll, CRM/marketing, store and e-commerce, procurement, expansion/real estate, contracts, internal developer tools, L&D platforms, AI interview/proctoring, video or audio teaching analysis, session-replay analytics. Phase 3 includes a Communication Inbox (central-team tasks) only if the owner confirms it (G-55).

---

## 5. Architecture (binding)

### 5.1 Original vs replacement: the essentials

| Concern | Original | **This product** | Why |
|---|---|---|---|
| Topology | 3 SPAs; about 10 services | **One React SPA + one NestJS modular monolith + one worker** (same codebase) | One authz and tenancy layer; ACID across modules; small-team operations |
| Language | JavaScript; Python (inferred) | **TypeScript strict, end to end** | Shared contracts and types |
| Database | Relational DB + MongoDB (inferred) | **PostgreSQL 16+, one database, forced RLS on `tenant_id`** | Isolation by construction |
| Tenant selection | Host + client header `X-DTS-BRAND` | Resolved **server-side** from host and session | A client-chosen tenant is a cross-tenant risk |
| Authorization | Numeric user levels; matrix cached client-side for 1 h; employee-ID allow-lists in bundle | Permission strings → roles → scoped assignments; time-bound grants as data; server guards on every route | Single source of truth, auditable, revocation ≤ 60 s |
| Tokens | JWTs in localStorage | httpOnly Secure SameSite cookies; 15-min access; rotating refresh | Script injection cannot steal the session |
| Secrets | Some in the client bundle | Server only; CI fails on detection | — |
| API | `/qbox/...`, 7 envelopes, 5 pagination styles | `/api/v1`, one convention (§5.5) | Predictable clients, generated OpenAPI |

Full comparison with reasons: `AUDIT_REPORT.md` §B.3.

### 5.2 Stack (decided; change only by an ADR the owner approves)

| Layer | Choice |
|---|---|
| Monorepo | pnpm + Turborepo |
| Backend | **NestJS** (TypeScript strict), modular monolith |
| Database | **PostgreSQL 16+** with RLS |
| ORM / migrations | **Prisma**. Every tenant-scoped database access runs inside a transaction that first executes `SET LOCAL app.tenant_id = '<uuid>'`. The app connects with a role that is not owner or superuser and lacks `BYPASSRLS`; migrations use a separate role. RLS policies and indexes live in SQL migrations. |
| Validation and contracts | **Zod** schemas in `packages/contracts` → generated OpenAPI 3.1 + typed client |
| Jobs | **BullMQ on Redis**, fed by a transactional **outbox** table |
| Cache / rate limit | Redis |
| Files | S3-compatible object storage (MinIO locally), pre-signed URLs, virus-scan job |
| Frontend | **React 18 + Vite + TypeScript**, **TanStack Router** (file routes) and **TanStack Query**, **Ant Design 5** with an **original** theme, React Hook Form + Zod, React Context for session and branch/year context only (no Redux) |
| PDF | Server-side HTML → PDF (Playwright/Chromium) in the worker |
| Email (dev) | Mailpit |
| Testing | Vitest (unit), Supertest + Testcontainers (integration), Playwright (e2e), axe-core (a11y); k6 later |
| Observability | OpenTelemetry, pino structured logs with request IDs, Sentry-compatible error tracking |
| CI | GitHub Actions: lint → typecheck → unit → integration → build → bundle secret scan → e2e → OpenAPI diff → secret scan (gitleaks) → docker build |

### 5.3 Repository layout
```
school-erp/
  apps/api/src/modules/{iam,org,people,calendar,timetable,diary,attendance,comms,assessment,finance,reporting}/
       <module>/{api,application,domain,infra,events}/   # controllers → use-cases → pure domain; infra = repositories/adapters
  apps/worker/          # BullMQ processors (outbox relay, notifications, PDFs, imports/exports)
  apps/web/src/{shell,features/<module>,ui}/
  packages/contracts/   # Zod schemas, generated OpenAPI + client
  packages/permissions/ # permission catalogue + navigation registry (shared FE/BE)
  packages/db/          # Prisma schema, SQL migrations (RLS), seeders
  packages/config/      # eslint, tsconfig
  docs/{plan.md,progress.md,decisions/NNNN-title.md}
```
A module may call another module only through the public interface exported from its `index.ts`, never through another module's tables. Enforce this with `eslint-plugin-boundaries` or an architecture test.

### 5.4 Request pipeline
1. `TenantMiddleware`: host → tenant. Reject unknown hosts. Ignore any client tenant header.
2. `AuthGuard`: session cookie → user, plus a check of the user's membership in that tenant.
3. `PermissionGuard`: `@RequirePermission('attendance.mark')`. A route without a declaration fails the architecture test.
4. Use-case: `ScopeService.assertInScope(user, { branchId, academicYearId, classSectionId, classSubjectId, studentId })`, plus an object-level check on every referenced ID.
5. `ZodValidationPipe` for params, query and body.
6. A transaction with `SET LOCAL app.tenant_id`; domain logic; outbox events; an audit entry (with before/after) for mutations.
7. Error filter → `{ error: { code, message, details[], requestId } }`.

### 5.5 API conventions
- Base `/api/v1`. Plural, kebab-case resources; nest at most one level (`/class-sections/{id}/students`). **camelCase** JSON.
- List: `GET /things?page=1&pageSize=50&sort=-createdAt&<explicit filters>` → `{ data: [...], page: { number, size, total } }`; `pageSize` at most 200.
- Get → `{ data }`. Create → `201 { data }`. Update → `PATCH`. Delete → soft (`DELETE`); restore via `POST /things/{id}/restore`.
- Commands as sub-resources: `POST /calendar-publications/{yearId}/publish`, `POST /attendance/bulk`.
- Header `Idempotency-Key` on payments and bulk writes (attendance, marks).
- Context is explicit: `branchId` and `academicYearId` travel as query or path parameters and are validated against scope. There are no context headers.
- Dates use ISO 8601 (`YYYY-MM-DD` for dates). Times are interpreted in the tenant's time zone.
- Status codes: 400 malformed · 401 unauthenticated · 403 forbidden in scope · **404 not found or out of scope or cross-tenant** · 409 conflict or state violation · 422 validation (field errors) · 429 rate limit.
- Keep a stable error-code catalogue in `packages/contracts`, for example `VALIDATION_FAILED`, `OUT_OF_SCOPE`, `STATE_CONFLICT`, `IDEMPOTENCY_REPLAY`.
- `GET /api/v1/me/bootstrap` returns user, memberships, roles, effective permissions, navigation, permitted branches and years, the current context and unread counts. The client calls it **before** any data request.

### 5.6 Data conventions
- `id uuid` (**UUIDv7**). `tenant_id uuid NOT NULL` with a **forced** RLS policy on every tenant table.
- `created_at, updated_at, created_by, updated_by, deleted_at` on mutable tables; `version int` for optimistic locking on editable aggregates.
- Money as `numeric(12,2)` plus the tenant currency, never floats. Enums as Postgres enums or check constraints; tenant-configurable lists as lookup tables.
- Append-only tables (audit, payments, wallet transactions, attendance history) are never updated in place.
- `ON DELETE RESTRICT`; soft delete with restore for master data.
- Unique business keys include `tenant_id`.

### 5.7 Frontend conventions
- The shell has: an icon rail with permitted categories; a mega-menu (category → section → item) built from `packages/permissions` filtered by the user's permissions; a breadcrumb; a page header card; branch and academic-year selectors in the top bar, stored in the URL search params; a notification bell with unread count; a profile menu.
- Pages without permission show a **"No access" page**, never a silent redirect.
- Shared components: `PageHeader`, `FilterBar` (required filters marked `*`; filter-first loading), `DataTable` (server pagination, empty state, horizontal scroll), `EmptyState`, `EntityDrawer`, `FormSection`, `StatusTag`, `MonthCalendar`, `WeekStrip`, `PeriodCard`, `TimetableGrid`, `ApprovalTimeline`, `MaskedField`, `ConfirmDialog`.
- The theme uses CSS variables from tenant branding (name, logo, primary colour). The default palette is **original**; do not use a crimson-based brand.
- Accessibility WCAG 2.1 AA (axe: 0 serious or critical). Test at **390 / 768 / 1440 px**. All strings go through i18n (English default).
- Budgets: authenticated shell JS ≤ 350 KB gzip; route chunks ≤ 150 KB; LCP ≤ 2.5 s p75.

---

## 6. Tenancy and core data model

### 6.1 Hierarchy (resolved)
```
Tenant (school group)                       ← RLS boundary
 ├─ Zone, City                              ← optional groupings; each Branch may reference one of each, independently
 ├─ Branch (one school campus; "school" in requirements means Branch)
 ├─ AcademicYear (e.g. 2026-27; exactly one is_current per tenant; dates must not overlap)
 └─ BranchAcademicYear (a branch participating in a year; holds weekly holidays)
     └─ GradeOffering (grade offered in that branch-year)
         └─ ClassSection (grade offering × section, e.g. "Grade 10 – A")   ← students, timetables and attendance attach here
             └─ ClassSubject (class section × subject)                      ← teachers attach here
```

### 6.2 Phase 0–2 entities
Full keys and comments: `05_DATA_MODEL/er_diagram_core_v2.mmd`.

| Group | Entities | Key rules |
|---|---|---|
| Tenancy | Tenant, Zone, City, Branch, AcademicYear, BranchAcademicYear | `(tenant_id, branch_code)` unique; one current year per tenant (partial unique index) |
| Structure | Grade, GradeOffering, Section, ClassSection, Subject, ClassSubject | `(branch_academic_year_id, grade_id)`, `(grade_offering_id, section_id)` and `(class_section_id, subject_id)` unique. ClassSection carries denormalised `branch_id` and `academic_year_id`. |
| Identity | User, Session, Role, Permission (global catalogue in code), RolePermission, RoleAssignment (`scope_type`, `scope_id`, optional `academic_year_id`, validity), UserGrant (mandatory reason and expiry), AuditLog | `(tenant_id, login_id)` unique. The login ID is system-generated; **do not reuse the original's format**. |
| People | Staff, **TeachingAssignment** (staff × class section × optional class subject × year; role `subject_teacher` or `class_teacher`), Student, Guardian, StudentGuardian, StudentEnrolment | One active enrolment per student per year; the enrolment year must equal its class section's year; guardians are de-duplicated by phone (PPL-03); siblings are students sharing a guardian |
| Scheduling | PeriodSystem, Period, TimetableVersion (draft/published/archived), TimetableSlot (`common_group_id`, `split_group_id`, `sen_student_id`), StaffAbsence, Substitution, CalendarEvent, CalendarEventScope (city/branch/grade/subject, `is_exclusion`) | Periods in a system must not overlap; slot clash rules in §9.6 |
| Teaching | DiaryEntry (+ class sections, subjects, attachments), Homework, HomeworkSubmission | See §9.7 |
| Attendance | StudentAttendance | Unique `(student_enrolment_id, attendance_date, period_id NULLS NOT DISTINCT)` |
| Cross-cutting | Attachment, Notification, Outbox, ImportJob | — |

---

## 7. Authorization model

- **Permission** strings follow `module.resource.action`. The catalogue lives in `packages/permissions` and is shared by the API guards, the navigation registry and the tests.
- **Roles** are tenant-scoped bundles of permissions. System roles seeded per tenant: Super Admin, School Admin, Principal, Academic Coordinator, Class Teacher, Subject Teacher, Accountant, Transport Manager, Front Office, Parent, Student. Tenants may define more.
- **RoleAssignment** carries a scope (tenant, branch, grade offering, class section or class subject) and optionally an academic year.
- **Data scope** is checked on the server, in addition to the permission check:
  - Teachers: class sections and subjects from **TeachingAssignment**, plus **published substitutions** for the given date.
  - Guardians: linked children via StudentGuardian. Students: themselves.
  - Admins and principals: by branch assignment.
- **UserGrant** gives per-person, time-bound exceptions with a reason. **Never hard-code people in code or config.**
- Module-level intent per role: `01_PRODUCT_OVERVIEW/role_permission_matrix.md` §3.

Seed catalogue. Names marked † already appear in the package; the rest are PROPOSED. Extend it as you go.
```
iam.user.manage   iam.role.manage   iam.grant.manage   iam.impersonate   audit.view   pii.reveal†
org.structure.manage   org.year.manage   org.settings.manage
people.staff.view   people.staff.manage   people.student.view   people.student.manage
people.guardian.manage   people.import   enrolment.manage
calendar.view   calendar.event.manage†   calendar.publish†
timetable.view.own†   timetable.view.section†   timetable.configure†   timetable.allocate†   timetable.publish†   timetable.substitute†
diary.write†   diary.write.any_class†   diary.view.reports†
homework.assign   homework.evaluate   homework.submit   homework.view
attendance.mark†   attendance.view†   attendance.edit_past†   attendance.notify†
notification.view   dashboard.teacher.view   dashboard.guardian.view
assessment.create†   assessment.paper.manage†   assessment.marks.upload†   assessment.marks.publish†   reportcard.configure†   reportcard.publish†
fees.collect†   (finance permissions are defined in Phase 5)
```

---

## 8. Security MUSTs (condensed from `SECURITY_REQUIREMENTS.md`, which is authoritative)

1. **Secrets stay on the server.** None in the bundle, HTML, source maps or public config. Only `VITE_PUBLIC_*` variables reach the frontend. CI scans the built output and the repository history. Third-party tokens (document editor, video, maps) are minted on the server, short-lived and scoped.
2. **Sessions.** httpOnly, Secure, SameSite cookies only; nothing in localStorage or sessionStorage. Access ≤ 15 min; rotating refresh with reuse detection; CSRF protection; Argon2id; lockout without user enumeration; MFA enforced for admin, finance, PII-reveal, impersonation and permission-management roles; logout revokes server-side; bootstrap before any data request.
3. **Server-side authorization.** Deny by default; every route declares a permission. **Object-level checks on every referenced ID.** Self-service endpoints use `/me`, never `?userId=` parameters. Permission changes take effect within 60 s. Cross-tenant or out-of-scope access → 404. Maker ≠ checker.
4. **Tenant isolation.** Tenant comes from host plus membership, never from a client header. Forced RLS on every tenant table. App DB role without `BYPASSRLS`. `SET LOCAL` per transaction (including jobs). Tenant-prefixed cache, queue and storage keys. A cross-tenant test matrix runs in CI.
5. **No hard-coded personal identifiers** in code, config, navigation, flags or permissions. CI lint rejects ID-, email- and phone-like literals in those packages.
6. **Audit log.** Append-only (the app role has INSERT and SELECT only); actor, impersonator, action, entity, masked before/after, IP, user agent, request ID. Covers authentication events, permission and grant changes, impersonation, PII reveal, exports, and every write to students, guardians, enrolment, attendance, marks, fees and settings.
7. **Financial data** (Phase 5). Hosted gateway checkout only; payment success only from a verified webhook or server check with the amount matched; amounts computed on the server; immutable ledger with reversals; server-allocated receipt numbers; maker–checker with thresholds; bank numbers encrypted and masked.
8. **Student data.**
   - Minimal fields per role; guardian contact, DOB, address, health/SEN and government IDs masked by default; reveal needs `pii.reveal` and is audited.
   - Special-category fields are encrypted at field level. **Do not collect government ID numbers** unless the owner decides otherwise (G-84).
   - No third-party session replay or behavioural tracking.
   - Online-class host links and meeting or recording passwords go only to hosts.
   - Files via signed URLs ≤ 10 min. No personal data in URLs or logs.
   - Children's data under DPDP rules (parental consent).
9. **Platform.** CSP without `unsafe-inline`; script allow-list with SRI; rate limits on auth, OTP, search, export and upload; upload allow-lists and virus scan; error responses without stack traces; dependency scanning and SAST gates; impersonation only as time-boxed, read-only by default, banner shown, audited.

---

## 9. Module briefs for Phases 0–2

Each brief gives the behaviour to build, its rules, and its acceptance criteria (in `08_TESTING/acceptance_criteria.md`). Read the cited spec and workflow files when you reach the step. They hold more detail; where they conflict with these briefs, these briefs win.

### 9.1 Shell (step 0.5) · spec `m01_shell_navigation.md`
- Layout per §5.7.
- The branch and year selectors list only the user's permitted branches and years. Changing either re-scopes every query.
- The notification bell shows `{ totalUnread, approvalUnread }` and a paginated list with mark-read.
- Home banners and the help assistant are Could; skip them now.
- ACs: **RBAC-01, RBAC-05, COM-03** (bell part).

### 9.2 Identity and access (steps 0.2–0.4, 1.2, 1.5) · spec `m02_identity_access_user_management.md`, `04_BACKEND_ANALYSIS/authentication.md` §4
- Login with login ID or email plus password. Guardians may also log in by phone or email; OTP is an optional factor.
- Forced password change; reset by single-use link; lockout; TOTP or OTP MFA; session list and revoke.
- Roles, assignments, grants and an audit viewer for admins.
- Guardian accounts link to several children, with a child switcher.
- ACs: **AUTH-01…07, TEN-01…03, RBAC-01…07**; also SEC-AC-01…07 from `SECURITY_REQUIREMENTS.md` §3.

### 9.3 Organisation and academic structure (step 1.1) · spec `m03_org_academic_structure.md`, `wf01`
- CRUD with soft delete and restore for branches, years, branch participation, grades, offerings, sections, class sections, subjects and class subjects. Deleting a referenced mapping is blocked.
- Tenant/branch settings: weekly holidays, attendance mode, attendance edit window, notification toggles.
- No admin screen was ever observed, so design clean list + drawer screens.
- Year rollover (preview → apply → history) comes in Phase 6.3.
- ACs: **ORG-01, ORG-02** (ORG-03 in Phase 6).

### 9.4 People and enrolment (steps 1.2–1.5)
- **Staff**: profile (designation, department, primary branch, teacher type, contact) and TeachingAssignments.
- **Students**: required first name, DOB, gender and at least one guardian; generated admission number, unique per tenant.
- **Guardians**: de-duplicated by phone. **Enrolment**: one active per year; section moves keep history.
- **Bulk import** (CSV/XLSX) for students, guardians and staff. A per-row validation report; valid rows import atomically per row. 500 rows in ≤ 60 s.
- PII masking with audited reveal.
- ACs: **PPL-01…03, ENR-01, RBAC-04, RBAC-07** (ENR-02 promotion in Phase 6).

### 9.5 Academic calendar (step 2.1) · spec `m04_academic_calendar.md`, `wf02_academic_calendar.md`
- Typed events: holiday, assessment, event, competition, PTM, custom (tenant-configurable list and colours).
- Each event has a date range, optional time, and scope: cities, branches with exclusions, grades and subjects, resolved to effective branches. Recurrence (weekly/monthly) with per-occurrence exceptions.
- Month view (observed pattern: day cells with typed chips, "+N more", today highlight, branch and grade filters, fullscreen); week and agenda views are PROPOSED.
- Publication per academic year: draft events are visible only to editors. Publishing makes them visible to everyone in scope and optionally notifies. A review step is PROPOSED, optional.
- Holidays make the day non-working for timetable and attendance.
- Editors: Academic Coordinator and School Admin; publish: Principal or School Admin (Q10 default).
- ACs: **CAL-01…04.**

### 9.6 Timetable and substitution (steps 2.2–2.3) · spec `m05_timetable.md`, `wf03_timetable_substitution.md`
- **Period systems** per branch-year: ordered periods with start and end times and break flags; overlap validation; assigned to class sections.
- **TimetableVersion**: edits happen in a draft version; publishing atomically replaces the active version (with an effective date). Draft changes are invisible until published.
- **TimetableSlot**: (period, day, class section, class subject, staff). The staff member must hold a TeachingAssignment for that class subject.
  - Clash rules: one staff member cannot be in two slots at the same day and period unless the slots share `common_group_id` (combined classes across sections). A class section cannot have two slots at the same day and period unless they share `split_group_id` (split period; PROPOSED semantics, G-36).
  - SEN 1:1 slots reference `sen_student_id`.
- Views: teacher week, section week, branch overview; teacher workload counts.
- **Substitution**: a coordinator records a StaffAbsence (minimal: staff, date, reason, source), sees the affected slots for that date, and assigns a substitute (optionally with a different class subject). The period then appears in the substitute's diary flagged as a substitution, and the substitute gains scope for that class on that date only. Eligibility rules are a customer decision (G-37); by default assignment is manual and shows the free teachers.
- ACs: **TT-01…04.**

### 9.7 Diary and homework (steps 2.4–2.5) · spec `m06_diary.md`, `m07_homework.md`, `wf04_daily_diary_homework.md`
- **Teacher diary page**: a week strip (Mon–Sat; holidays disabled) and today's periods from the published timetable, including substitutions, in time order. Each period card shows period, times, class chip, subject and a lesson-plan link (lesson plans arrive in Phase 7; hide the link until then).
  - Actions: "Homework for another class" (any class in scope) and Refresh.
- **DiaryEntry**: teacher, date, optional period, one or more class sections and subjects (in scope only), classwork (required, ≤ 5,000 chars), attachments (allow-listed types, ≤ 20 MB each), and a substitute flag.
- **Homework** is its own aggregate (PROPOSED, ADR; G-41). It may be created from a diary entry or on its own and carries instructions, attachments, a due date-time (≥ now), an online-submission flag, and target class sections and subjects. Students in those sections see it within 1 minute; guardians are notified.
- **HomeworkSubmission** states: `assigned → submitted | late → evaluated`, plus a computed `overdue`. Late submission is allowed and flagged by default (Q04; tenant setting). Teacher evaluation (marks or grade and remarks) notifies the student.
- Coordinator report: diary completion = periods with an entry ÷ periods taught, by grade → section → subject → teacher.
- ACs: **DIA-01…03, HW-01…03.**

### 9.8 Student attendance (step 2.6) · spec `m08_attendance.md`, `wf05_student_attendance.md`
- **Mark tab**: Grade and Section(s) are required (filter-first), then Search loads the roster (Sl No, Student ID, Name, Grade, Section, Status) with Present preselected. One bulk save (`POST /attendance/bulk` with an `Idempotency-Key`) upserts the records.
- **View tab**: date range per section or student; monthly report; attendance % = present ÷ working days, with holidays excluded and half-days counted per configuration.
- **Rules**:
  - No marking on future dates or holidays.
  - The edit window defaults to the same day for teachers. Later edits need `attendance.edit_past` (default: coordinators up to 7 days) and are audited.
  - Daily mode by default; period-wise is a tenant setting (Q01).
  - Statuses: Present, Absent, Late, Half-day, Leave (configurable, Q02).
- **Absence notification**: one per student per day per channel; in-app and email now, SMS/WhatsApp in Phase 3.
- Bulk save of 45 students in ≤ 1 s.
- ACs: **ATT-01…06.**

### 9.9 Dashboards (step 2.7) · spec `m17_dashboards_reports.md`
- **Teacher**: today's classes, attendance still to mark, diary entries still to write, homework to evaluate.
- **Guardian/student**: today's timetable, pending homework, recent attendance, upcoming calendar events.
- Define every metric in code with a unit test (G-72). No other metrics are specified.

---

## 10. Later phases (summary)

Read the listed specs when you get there.
- **Phase 3:** m12, m13, wf11, wf12.
- **Phase 4:** m10, wf06.
- **Phase 5:** m15, wf09, wf10.
- **Phase 6:** m14, wf07, wf08, wf01.
- **Phase 7:** m09, m11, m16, m18.

Apply the same pipeline, security and testing rules. Do **not** start Phase 5, or the admissions part of Phase 6, until the owner has supplied the blocking inputs in §11. Do not fill them with invented rules.

---

## 11. Open decisions and defaults

Use the default, record an ADR in `docs/decisions/`, and continue, unless the row says **STOP**.

| Ref | Decision | Default |
|---|---|---|
| Q01 | Attendance granularity | Daily; period-wise as a tenant setting |
| Q02 | Attendance statuses | Present, Absent, Late, Half-day, Leave (configurable) |
| Q03 | Attendance edit window | Same day for teachers; 7 days for coordinators; everything audited |
| Q04 | Late homework | Allowed and flagged Late (tenant setting) |
| Q05 / Q06 | Grading and term weighting | Configurable grading bands and template weights. **STOP before report-card layout (Phase 4.4)** until samples arrive. |
| Q07 / Q08 | Fee rules and payment gateway | **STOP: Phase 5 is blocked** |
| Q09 | Ticket states and SLA | Open → In Progress → Awaiting Parent → Resolved → Closed; SLA per category |
| Q10 | Calendar roles | Coordinator and School Admin edit; Principal or School Admin publish |
| Q11 | Teachers create announcements? | Yes, for their own class sections |
| Q12 | Parent account model | One user per guardian; children linked; child switcher |
| Q13 | Student login | Own credentials from a configurable grade threshold |
| Q14 | Retention | Configurable per data class. **No real data before a decision.** |
| Q15 | Multiple boards per branch | Board per grade offering |
| Q16 | Languages | English first; i18n from day one |
| Q17 | Mobile | Responsive PWA |
| Q18 | Optional modules | Deferred (Phase 7) |
| Q19 | Product name | "School ERP" placeholder |
| Q20 | Hosting | India region |
| G-26 | Login identifier | Generated opaque login ID; email for staff and guardians; guardian phone/OTP optional |
| G-36 | Split period | `split_group_id` on slots |
| G-37 | Substitution eligibility | Manual assignment; free teachers listed |
| G-41 | Homework aggregate | Standalone, optionally created from a diary entry |
| G-55 | Communication Inbox (central tasks) | Out of scope unless confirmed |
| G-84 | Government ID numbers | Do not collect |
| Tenant defaults | Time zone, locale, currency, week start | `Asia/Kolkata`, `en-IN`, INR, Monday |

The first ADRs to write (step 0.1):
- 0001 stack confirmation (§5.2)
- 0002 tenancy hierarchy (§6.1)
- 0003 TeachingAssignment as the source of teacher scope
- 0004 Homework aggregate
- 0005 Phase 2 defaults (Q01–Q04, Q10, G-36, G-37)
- 0006 error-code catalogue

---

## 12. How to work

1. **Plan first.** Write `docs/plan.md`: phases, the steps of `12_CLAUDE_CODE_HANDOFF/IMPLEMENTATION_SEQUENCE.md`, and the first two weeks of tasks. Keep `docs/progress.md` as a checklist mirroring the sequence, with links to tests.
2. **Per step:** data model and migration (with RLS) → Zod contracts → use-cases with scope checks → controllers → UI → tests named after AC IDs → docs and seed. Finish the step's Definition of Done (`12_CLAUDE_CODE_HANDOFF/DEFINITION_OF_DONE.md`) before starting the next.
3. **Test first** for calculators and state machines: attendance %, clash detection, recurrence expansion, homework states, diary completion; later dues, fines and grading.
4. **Authorization tests are generated:** permission catalogue × seeded role users × endpoints → expected 403/404. The cross-tenant matrix covers every endpoint (TEN-01).
5. **Gates:** domain-module coverage ≥ 85%; lint, typecheck and format clean; OpenAPI regenerated; axe clean; budgets met.
6. **Seed data:** 1 tenant ("Demo School Group"), 2 branches, academic year 2026-27, grades 1–10 with sections A–B, 40 students per section, about 30 staff per branch, about one guardian per student with about 15% siblings sharing guardians. All synthetic, fixed seed.
7. **Small commits** with conventional messages. Do not commit secrets or `.env` files. Commit and push only when the user asks.
8. **Report** at the end of each step: what was built, which ACs pass (test names), which ADRs were recorded, and anything blocked.

---

## 13. First actions

1. Read, in order:
   - this prompt
   - `SECURITY_REQUIREMENTS.md`
   - `AUDIT_REPORT.md` §B.3 and §G
   - `05_DATA_MODEL/er_diagram_core_v2.mmd`
   - from `12_CLAUDE_CODE_HANDOFF/`: `ARCHITECTURE_BLUEPRINT.md`, `DEVELOPMENT_RULES.md`, `DEFINITION_OF_DONE.md`, `IMPLEMENTATION_SEQUENCE.md`
   - `08_TESTING/acceptance_criteria.md` and `test_strategy.md`
2. Reply with a short plan: modules, phases, the first two weeks, and the ADR list. Then create `docs/plan.md` and ADRs 0001–0006.
3. **Step 0.1:** scaffold the monorepo (§5.3). Add docker-compose (PostgreSQL 16, Redis, Mailpit, MinIO) and the CI pipeline, including the bundle secret scan and gitleaks.
4. **Step 0.2:** schema and migrations for Tenant, User, Session, Role, Permission, RolePermission, RoleAssignment, UserGrant and AuditLog, with forced RLS, the restricted app role, and `SET LOCAL` transaction middleware. **Write the RLS tests first:** TEN-02, the pool-interleaving test SEC-AC-05, and the role check SR-TEN-3.
5. Steps 0.3–0.6, then Phase 1 and Phase 2 per §9.

---

## 14. Stop and ask the user when

- A task would require contacting or inspecting the original system.
- You need a Phase 5 fee rule, a payment gateway, receipt legal content, report-card layouts, admission forms or TC formats (no defaults exist).
- Someone asks you to collect government ID numbers, or to add third-party analytics or session replay.
- A security requirement cannot be met as written.
- Real personal data appears anywhere.
- You want to change a §5.2 stack decision or the §6.1 hierarchy.
- Two sources conflict and the §3 precedence does not resolve it.

---

## 15. Package map

| Path | Contents |
|---|---|
| `README.md`, `EXECUTIVE_SUMMARY.md` | Package overview (apply errata ER-01, ER-03, ER-45 to the counts) |
| `AUDIT_REPORT.md` | Audit results, architecture comparison (§B.3), errata (§G) |
| `DOCUMENTATION_GAPS.md` | Known gaps and how each closes |
| `HANDOFF_READINESS.md` | Phase and module readiness, decision deadlines, risks |
| `SECURITY_REQUIREMENTS.md` | Binding security requirements and proposed security ACs |
| `OPEN_QUESTIONS.md` | Q01–Q22 with defaults |
| `01_PRODUCT_OVERVIEW/` | Scope tiers, feature catalog, personas, role–permission intent |
| `02_FRONTEND_ANALYSIS/` | Original UX patterns, design observations, screen inventory (patterns only) |
| `03_MODULE_SPECIFICATIONS/` | M01–M19 specs |
| `04_BACKEND_ANALYSIS/` | Original service map, observed API shapes, auth mechanics (understanding only; do not mirror) |
| `05_DATA_MODEL/` | Domain model, entity dictionary, relationships, ER diagrams (v2 is authoritative for the core) |
| `06_WORKFLOWS/` | WF-01…WF-15 with state machines |
| `07_NON_FUNCTIONAL_REQUIREMENTS/` | Security findings and SR-*, performance PR-*, scalability SC-*, reliability RL-*, deployment |
| `08_TESTING/` | Test strategy, 74 acceptance criteria, 70 test cases |
| `09_VISUAL_EVIDENCE/` | 10 redacted screenshots (look at layout patterns only) |
| `10_REA_EVIDENCE/` | Raw sanitized evidence (static catalogs, runtime shapes, role names) |
| `13_AUTONOMOUS_DEEP_ANALYSIS/` | Phase 3: full-corpus static analysis (6,577-row method-bearing endpoint catalog, route→API graph, call-site index, role/ID-list evidence) **plus an executed read-only route crawl** — 171 runtime-observed endpoints, 42 teacher routes rendered / 359 role-redirected, 72 value-free response schemas, and runtime-confirmed service boundaries (transport→finance service, lesson-plan→MGMT service, chat→Mattermost). Understanding and cross-reference only — do **not** mirror the original's paths. |
| `11_IMPLEMENTATION_PLAN/` | Architecture rationale, roadmap, dependencies, MoSCoW priorities (authoritative for tiers) |
| `12_CLAUDE_CODE_HANDOFF/` | Blueprint, rules, DoD, implementation sequence, system requirements (old master prompt superseded) |
