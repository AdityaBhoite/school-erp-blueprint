# Role Access Requirements Matrix (Priority 5)

**Date:** 2026-11-24 · Part of Stage 1B (`14_EDUVATE_COMPLETENESS/`).
**Purpose:** Specify precisely what each higher-privilege role could reveal that the **Teacher (ERP level 11)** account could not, so legitimately-provisioned test accounts can be requested with a clear objective per role.

> **Evidence base (no new analysis of the original):** E017 role catalogue (80 ERP user levels, OBSERVED), `ROLE_ROUTE_API_MATRIX.md` (65 inline level checks; 42 routes rendered vs 359 role-redirected for a teacher), `COVERAGE_MATRIX.md` (per-module teacher reachability), and the module specs. **Rules:** legitimate login only; **no privilege escalation, impersonation, or ID enumeration**; read-only, value-free capture; no writes. Passwords are never requested in chat — the owner authenticates manually in the browser.

## How this was derived
The teacher crawl showed **88%** of ERP routes redirect a teacher to `/erp/user-profile`. Those 359 role-gated screens, plus the modules marked "denied / not visible" in the coverage matrix (admin org screens, finance, students/admissions, transport, tickets, dashboards, parent & student portals), are the surface below. Each row names the E017 level(s) whose access matrix would render them.

---

## A. The six requested roles

### 1. School Administrator
- **Required account type:** E017 **L5 School Admin** (superset views also at L1 Super Admin, L4 Owner; branch-ops at L26 School Manager).
- **Features inaccessible to Teacher:** organisation & academic structure management (branches, academic years, grades, sections, subjects, mappings — M03), user/account lifecycle (create/invite/activate/deactivate staff, guardians, students — M02), system configuration keys (G-30b), school-wide dashboards (M17).
- **Screens to observe:** branch/year/grade/section/subject CRUD screens; staff & student directory and account-creation forms; config/settings pages. *(None were ever rendered — G-29.)*
- **API functionality to verify:** the ERP admin endpoints that are STATIC_PATH_ONLY for a teacher (org CRUD, user management); confirm which fire and their value-free response shapes.
- **Workflows:** WF-01 (academic-year setup/rollover), user lifecycle (G-25), org-structure setup.
- **Evidence unobtainable without this role:** all admin screen layouts and their runtime data shapes; the access-matrix contents for an admin (G-20); which org/user APIs are real vs path-only.

### 2. Principal
- **Required account type:** E017 **L8 Principal** (6 inline level-8 checks found in code).
- **Features inaccessible to Teacher:** branch-level oversight dashboards, approval authority (student lifecycle approvals WF-08, leave approvals WF-13), cross-class/section visibility, result-publishing authority (M10 approval chain).
- **Screens to observe:** principal dashboard; approval queues; branch-wide attendance/assessment/communication overviews.
- **API functionality to verify:** approval-action endpoints (read-only: observe the queues, **not** the approve action); branch-scoped read endpoints.
- **Workflows:** WF-08 approvals, result publish authority (G-50), announcement approval (G-54).
- **Evidence unobtainable without this role:** the approval UI/state surfaces; branch-wide read scopes; what a level-8 access matrix exposes.

### 3. Accountant
- **Required account type:** E017 **L32 Branch Accountant** (4 inline checks); related finance levels L29 Central Finance, L25 Zonal FinOps, L80 Regional Finops, L43 Accounts Payable.
- **Features inaccessible to Teacher:** the **entire finance surface** — fee setup, dues, collection, receipts, concessions (sibling/staff/scholarship), refunds, wallets, accounting export. Coverage marks M15 Finance as **not visible** (teacher saw only one shell call). This is the single biggest role gap.
- **Screens to observe:** the **Finance SPA** (`orchids.finance.letseduvate.com`) screens reached via the ERP finance handoff (see `FINANCE_APPLICATION_ANALYSIS.md`); fee-structure, collection, receipt, concession and refund screens.
- **API functionality to verify:** the 151 catalogued FIN service endpoints (which fire for an accountant, methods, value-free response shapes); the finance SPA's own API services.
- **Workflows:** WF-09 (fees collection), WF-10 (concession/refund/wallet).
- **Evidence unobtainable without this role:** finance screens, the finance SPA frontend behaviour, and runtime finance contracts. **Fee/receipt/tax *rules* remain CUSTOMER decisions (G-64…G-69) — do not infer them from screens.**

### 4. Parent
- **Required account type:** E017 **L12 Parent**.
- **Features inaccessible to Teacher:** the **entire parent portal** — child dashboard, diary/homework/attendance views for the child, fee payment, PTM booking, communication/announcements as received, leave applications for the child, report-card viewing.
- **Screens to observe:** parent home; per-child academic views; fee-payment flow (observe only); PTM/meeting booking; inbox.
- **API functionality to verify:** parent-scoped read endpoints (child data, fees due, announcements); confirm which exist vs are path-only.
- **Workflows:** parent side of WF-04 (diary/homework), WF-05 (attendance notifications), WF-09 (fee payment), WF-12 (parent ticket), WF-14 (profile-change approval).
- **Evidence unobtainable without this role:** the whole parent UX and its data contracts. **Risk R-6: the parent experience is currently *entirely designed*, with no observation.**

### 5. Student
- **Required account type:** E017 **L13 Student** (also L15 is "Virtual student" flag `is_virtual_student`).
- **Features inaccessible to Teacher:** the **entire student portal** — timetable/diary/homework as a student, assessments/results view, e-books/online-class join, school-wall/clicker participation.
- **Screens to observe:** student home; homework submission view (observe only); results; online-class entry.
- **API functionality to verify:** student-scoped read endpoints; confirm existence vs path-only.
- **Workflows:** student side of WF-04, WF-06 (report card view), WF-15 (online class).
- **Evidence unobtainable without this role:** the whole student UX and data contracts; same "entirely designed" caveat as parent.

### 6. Transport Administrator
- **Required account type:** E017 **L28 Transport Incharge** (also L54 Central Transport Head, L55 Transport Regional Manager, L65 Transport MIS, L15 Driver for the driver app).
- **Features inaccessible to Teacher:** transport **administration** — route/vehicle/driver management, student→stop assignment, transport fee slabs, bus attendance, GPS. Teacher could only **view** `/transport-buses`, `/transport-drivers`, `/transport-routes`, `/transport-stop-points` (read-only lists, one 404 on a sub-resource).
- **Screens to observe:** route/stop/vehicle/driver management screens; assignment screens; bus-attendance.
- **API functionality to verify:** the FIN-service `/api/v2/transport/*` endpoints (several runtime-observed in the teacher crawl) under a transport-admin scope, plus the management (write-capable) endpoints — **observe only**.
- **Workflows:** transport setup and assignment (M16 — only menu/route names today, G-71).
- **Evidence unobtainable without this role:** transport management screens and their contracts; assignment/fee logic (rules are CUSTOMER/OWNER, G-71).

---

## B. Other administrative roles discovered (E017) worth a scoped look

Prioritised; each would unlock the modules the teacher could not reach. Request only those relevant to the commercial product's scope.

| E017 level | Role | What it would reveal (not seen as teacher) | Priority |
|---|---|---|---|
| L1 Super Admin | Super Admin | Full platform configuration, access-matrix authoring, all modules (25 inline checks reference L1) | Med (broad, but superset) |
| L9 Campus Incharge | CIC | Campus-level operations between principal and coordinator | Med |
| L10 Academic Coordinators | Coordinator | Timetable/substitution assignment, academic oversight (inline checks) | High (owns substitution — G-37) |
| L26 School Manager | School Manager | Branch operations, can assign to L9/14/28/32 (E017) | Med |
| L7 HR Admin | HR Admin | Full HRMS beyond teacher self-service (M18) | Med (if HR in product scope) |
| L16 Librarian | Librarian | e-books/library surface (M11) | Low (Q18-deferred) |
| L17/L18 Hostel Manager / Warden | Hostel | Hostel module (not in current module set) | Low |
| L19/L33 Store Manager / Store Admin | Store | Inventory/store module | Low (Q18) |
| L46 Nurse | Nurse | Health module | Low |
| L37 Special Educator | SEN | SEN surface | Low |
| L66 Branch Admission Counselor | Admissions | Admission/registration screens (M14, G-59) | High (admissions gap) |
| L31 Trustee / L4 Owner | Governance | Executive dashboards | Low |

---

## C. Collection protocol (for whoever drives the authenticated browser)
1. Owner logs in **as that role** in their own browser (manual authentication). No credentials are shared with the assistant.
2. The assistant (with the Claude browser extension connected) runs a **read-only, navigate-only** pass of that role's reachable screens, capturing **value-free** structure (route render-vs-redirect, response field *names*, status codes) exactly as the Phase 3 teacher crawl did.
3. **Respect rate limits** (stop on 429, cooldown, no aggressive retry). **No writes, no form submissions, no payments, no result publishing.**
4. Output per role: a `ROLE_<name>_CRAWL.csv` + value-free shape JSON, mirroring the Phase 3 artifacts.

See `COMPLETENESS_ASSESSMENT.md` for status and `WORKFLOW_EVIDENCE_GAPS.md` for what even a full role crawl still cannot reveal.
