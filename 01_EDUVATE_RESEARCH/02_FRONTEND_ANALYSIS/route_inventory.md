# Route Inventory

| Source | Count | Class | File |
|---|---|---|---|
| ERP client routes (`/erp/*`) | 688 path→component pairs | STATIC | `10_REA_EVIDENCE/bundle_analysis/E012_erp_route_table.md` |
| HRMS client routes (`/recruitment/*`) with permissions | 82 top-level (about 250 incl. children) | STATIC | `10_REA_EVIDENCE/bundle_analysis/E007_hrms_route_permissions.md` |
| Unified navigation items (all apps) | 772 | STATIC | `10_REA_EVIDENCE/bundle_analysis/E005_unified_navigation_megamenu.md` |
| Runtime-visited routes (teacher) | 70 unique routes, 71 navigations (39 accessible, 31 denied) | OBSERVED | `02_FRONTEND_ANALYSIS/screen_inventory.md` |

## ERP route groups (largest prefixes, STATIC)
| Prefix | Routes | Domain |
|---|---|---|
| `/student-fee/*` | 42 + receipts and payment forms | Fees, payments, wallets, receipts |
| `/transport-*` | about 55 | Transport |
| `/expansion/*` | 18 | Real-estate expansion |
| `/store/*` | 17 | Store/e-commerce |
| `/master-management/*` | 9+ | Masters, settings |
| `/user-management/*` | 15 | Users, students, staff |
| `/assessment/*` | 15+ | Assessments, report cards |
| `/procurement/*` | 7 | Procurement |
| `/approval-request/*` | 10 | Approvals |
| `/homework/*` | 8 | Homework |
| `/acms/*` | 25+ | Academic content management, calendar, assessments |
| `/bytelearn/*` | 25+ | L&D |
| `/work-tracker/*`, `/bug-tracker/*`, `/feature-request/*` | 20+ | Internal tools |

## Route patterns worth reproducing (OBSERVED/STATIC)
- Parameterised receipts: `/student-fee/{fee|installment|due|misc|curriculum|delivery|transport|sen}-receipt/:erpId/:id` → the replacement uses `/finance/receipts/:receiptId` with a type field.
- Payment return: `/student-fee/student-fee-payment/:orderIdValue`, `/transport-payment-success/:Id` → `/finance/payments/:orderId/result`.
- Drill-down dashboard URLs encode filters in the path (`/ticket/:label/:branchIds/:startDate/:endDate/:csatScore`) → the replacement uses query params.
- Iframe-embeddable tracking pages (`/iframe/track/:busNo`) → public, signed, time-limited share links.

## Proposed replacement route map (top level)
```
/login, /forgot-password, /reset-password/:token
/                       → role default (teacher: /teaching/diary or /calendar)
/calendar               /calendar/events/:id
/timetable              /timetable/sections/:id  /timetable/teachers/:id  /timetable/substitutions
/teaching/diary         /teaching/homework  /teaching/homework/:id  /teaching/lesson-plans
/attendance/mark        /attendance/view    /attendance/reports
/assessments            /assessments/:id    /assessments/:id/marks   /report-cards
/comms/announcements    /comms/notifications /comms/tickets /comms/tickets/:id
/people/students        /people/students/:id /people/staff /people/guardians
/admissions             /approvals
/finance/fee-plans      /finance/dues  /finance/payments  /finance/receipts/:id  /finance/concessions
/transport/...          /reports/...   /admin/(org|roles|settings|audit|imports)
/me (profile, password, sessions)
```

---

## Phase 3 route→component→API graph (2026-11-23)

The static router of all three apps was AST-parsed into `13_AUTONOMOUS_DEEP_ANALYSIS/ROUTE_COMPONENT_MATRIX.csv`: **729 distinct routes** (ERP 687, HRMS 25, CRM 17), each linked to its lazy chunk, the APIs it calls directly, the service modules it imports, and (for HRMS/CRM) its permission array. 692 routes resolve to a concrete chunk; 511 call ≥1 API in their own chunk. Safety classification for automated crawling is in `ROUTE_DISCOVERY_SAFETY.csv` (442 safe read-only, 149 need an ID, 98 state-changing, 40 sensitive).

### Runtime render/redirect outcomes (Phase 3 crawl, 2026-11-23)
A read-only crawl navigated 408 of 426 safe routes as a **teacher** (stopped on the server's 429; 18 unreached). Outcomes: **42 rendered**, **359 redirected to `/erp/user-profile`** (role-gated for this level, inferred from a client redirect), 7 redirected elsewhere, 0 login-redirects, 0 nav errors. So for a teacher, only ~10% of the safe ERP route surface actually mounts; the rest is role-guarded. Per-route data: `ROUTE_CRAWL_RESULTS.csv`; the accessible teacher surface is grouped in `ROLE_ROUTE_API_MATRIX.md` §6. This is **one role's** reachability, not a statement about the routes themselves.
