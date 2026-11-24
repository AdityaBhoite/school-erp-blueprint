# Development Roadmap (PROPOSED)

Sizing assumes a team of 3–5 engineers. Each phase ends with a demo against the acceptance criteria in `08_TESTING/acceptance_criteria.md`.

| Phase | Weeks | Scope | Exit criteria |
|---|---|---|---|
| **0. Foundations** | 1–3 | Monorepo, CI/CD, environments, auth (login, refresh, logout, password reset, OTP), tenant resolution + RLS, RBAC engine + permission catalogue, audit log, file storage, notification infrastructure (in-app + email), app shell (rail, mega-menu, context selectors, notifications bell), design tokens | AUTH-*, TEN-*, RBAC-* tests pass; shell navigates by permissions |
| **1. Org & People** | 4–6 | Tenant/branch/academic year, grades, sections, subjects and mappings (with soft delete/restore), staff directory and profiles, students, guardians, sibling links, enrolment, bulk import (CSV/XLSX) with validation report | ORG-*, PPL-*, ENR-* pass; seed school importable |
| **2. Daily teaching core** | 7–11 | Academic calendar (draft → publish), period systems, timetable allocation and publish, substitutions, diary (classwork/homework), homework submission and evaluation, student attendance (mark/view/reports, absence notifications), teacher dashboard, parent/student dashboard (read) | CAL-*, TT-*, DIA-*, HW-*, ATT-* pass; pilot-ready for teachers |
| **3. Communication** | 12–13 | Announcements (targeted, drafts, attachments), push/SMS/WhatsApp providers, parent tickets with CSAT, banners | COM-*, TKT-* pass |
| **4. Assessment** | 14–18 | Exam types, grading systems, assessments, blueprint (basic), question bank (basic), marks entry (grid + Excel), publish, report-card templates and generation (PDF), principal signature | ASM-*, RC-* pass |
| **5. Fees** | 19–24 | Fee heads, terms, plans, assignment, dues, online payments (gateway), offline (cash/cheque) receipts, concessions with approval, fines, refunds/wallet (basic), reconciliation export, fee dashboards | FEE-*, PAY-* pass; finance UAT signed |
| **6. Lifecycle & admin** | 25–27 | Admissions (registration → admission → enrolment), promotion, section shuffle, status changes with approvals, TC, ID cards, year rollover with preview | LIFE-* pass |
| **7. Extended** | 28+ | Transport, online classes (Zoom/Meet), lesson-plan library, staff attendance and leave, reports gallery, mobile app | per module |

Optional later contexts: HRMS/payroll, CRM/admissions marketing, store/e-commerce, procurement.
