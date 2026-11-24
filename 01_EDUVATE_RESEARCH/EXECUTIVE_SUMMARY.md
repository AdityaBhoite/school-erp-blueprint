# Executive Summary

## What the product is
"Orchids ERP" is one tenant ("brand") of **Eduvate**, a multi-brand K-12 platform for school chains. The same codebase serves at least five brands, each on its own host. It is not a simple school ERP: it is a **group operating system** with **772 navigation items** across three web applications on one origin:

- **ERP** (`/erp`, 688 client routes): academics (calendar, timetable, diary, homework, attendance, lesson plans, assessments, report cards, online classes, e-books), students, finance (fees, wallets, expenses, P&L), transport, operations (store, procurement, infrastructure, expansion), communication, approvals and admin.
- **HRMS** (`/recruitment`): recruitment, onboarding, people, attendance and leave, payroll, training, assets, travel, grievances.
- **CRM** (`/crm`): admissions marketing and leads.

## How it is built (evidence-backed)
- **Frontend:** React 18 + Ant Design 5, Vite builds with heavy lazy-loading. ERP uses Redux + redux-saga; HRMS uses React Context. There is a shared "unified shell" (rail, mega-menu, branch/year selectors, notifications, AI help).
- **Backend:** REST under `/qbox` on several services (core ERP, finance, HRMS, CRM, central management, reports, classes, events, analytics, Mattermost chat) on Google Cloud. Inferred Django REST Framework (trailing slashes, `__` lookups, SimpleJWT-style tokens) plus MongoDB for notifications.
- **Tenancy:** brand (host + `X-DTS-BRAND` header) → zone → city → branch → session year → grade → section → subject mappings.
- **Access control:** 80 named ERP user levels (e.g. 11 Teacher, 13 Student). A server-side access matrix maps navigation items to levels and individual employee IDs; it is cached client-side and redirects unauthorized routes to the profile.

## What was observed
- About 17 REA tool operations (target discovery, 6 page inspections, 3 screenshots, bundle analysis, 2 script exports, evidence export); 84 HRMS + 16 ERP scripts exported and statically analysed; **2,604 static endpoint catalog entries** recovered from client catalogs (about 2,430 distinct path templates; the catalogs record no HTTP methods).
- A read-only automated tour of **70 unique routes** (71 navigations) with the teacher account: **39 accessible, 31 denied** (redirected), **94 unique API endpoints** with value-free response shapes (87 product-service endpoints plus 2 in-house analytics, 4 Mattermost and 1 Microsoft Clarity call).
- The teacher's day: Academic Calendar (landing) → Diary (period cards with classwork/homework and lesson-plan links) → Attendance (grade/section filter-first roster) → Homework → Timetable → Assessments (tests, marks-upload dashboard) → Announcements and Communication Inbox. HR self-service covers leave, payslip, tax declaration, assets and grievances.

## Key findings for the replacement
1. The **core value** is daily academic operations plus communication; finance and admissions are the next tier. Most of the original's breadth (expansion, procurement, store, CRM, payroll, internal tools) should be out of scope initially.
2. Reproduce the **hierarchy and scoping model** (tenant → branch → year → class section → subject), period-based timetable concepts (common, split and SEN periods, substitutions), diary-per-period, filter-first rosters, typed calendar with publishing, and assessment-to-report-card pipeline.
3. **Do better on security:** the original ships secrets in client bundles, stores tokens in localStorage, hard-codes employee-ID allow-lists in frontend code, and runs session recording on PII screens.
4. **Architecture recommendation:** TypeScript modular monolith (NestJS + PostgreSQL with RLS + Redis/BullMQ) and one React SPA, with an RBAC permission catalogue shared by frontend and backend.

## Limits
Only the teacher role was available, so admin, finance, student/parent and transport screens are specified from static evidence. No write operations were performed. See `COVERAGE_MATRIX.md` and `OPEN_QUESTIONS.md` (22 questions with proposed defaults).

## Deliverable
`12_CLAUDE_CODE_HANDOFF/` is a self-contained build brief: master prompt, 74 functional requirements, architecture blueprint, phased implementation sequence (Phase 0–7), development rules and definition of done. It is backed by 19 module specs, 15 workflows, a data model with ER diagram, API contracts, and 74 acceptance criteria with 70 test cases.
