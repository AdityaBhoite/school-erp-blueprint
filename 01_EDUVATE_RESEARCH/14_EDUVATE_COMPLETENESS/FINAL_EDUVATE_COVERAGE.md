# Final Eduvate Coverage

**Date:** 2026-11-24 · Stage 1B completion report.
**Honest headline:** The Eduvate **client** (its frontend contract and UX surface) is now understood comprehensively for the teacher surface and catalogued across all services. The Eduvate **product as a whole** is not fully observed — role-gated screens, the finance SPA frontend, and all server/business rules remain, by access boundary or by nature. **Coverage is not "complete" merely because the JavaScript was downloaded.**

## The six-way distinction (completion criteria)

### 1. Fully observed functionality (screen + runtime data seen, teacher role)
Daily-teaching core as a teacher: academic calendar, diary, attendance (filters/roster), homework (filters/evaluate/update/analytics), timetable (grid), assessment/ACMS lists & marks dashboard, lesson plan, online-class lists, announcements, chat (Mattermost), PTM feedback, transport **view** lists, HRMS self-service (grievance, payroll set-pin), profile. → 42 routes rendered; 171 runtime endpoints; 72+ value-free response schemas.

### 2. Statically analysed functionality (code-derived, not run)
The whole shipped client: **6,577 catalogued endpoints** across ERP (4,019), HRMS (1,527), **CRM (610)**, **FIN (151)**, REPORTS/ACTIVITIES/MGMT/etc.; **~729 routes**; call-site index; response-field names; 65 inline role-level checks; 772 menu items. Methods resolved for 5,427 (1,158 honestly UNKNOWN). New in Stage 1B: CRM module structure and finance service groupings synthesised from this metadata (`CRM_APPLICATION_ANALYSIS.md`, `FINANCE_APPLICATION_ANALYSIS.md` Part A).

### 3. Runtime-verified functionality (static claims confirmed at runtime)
Static-vs-runtime methods: **104/107 direct, 0 contradictions**; 39 STATIC→RUNTIME promotions; the access-matrix's effect proven at runtime (42 render / 359 role-redirect / 7 other for a teacher). Verified for the **teacher role only**.

### 4. Role-restricted functionality (exists; needs an authorised account)
88% of ERP routes role-redirect a teacher. Not yet observed: **admin** org/user screens, **accountant/finance** screens + the finance SPA frontend, **principal** approvals, **parent** portal, **student** portal, **transport administration**, **tickets/dashboards**, and the authenticated **CRM** workspace. Each is specified in `ROLE_ACCESS_REQUIREMENTS.md`; the 18 interrupted teacher routes are in `REMAINING_ROUTE_RESULTS.csv`. **Pending legitimately-provisioned accounts + an authenticated browser.**

### 5. Business rules requiring customer/owner input (not discoverable)
Fee computation, gateway, receipts/tax, concessions, refunds, offline modes, accounting export (G-64…G-69); grading/weighting + report-card layout (G-48/G-49); attendance mode/status/edit-window (Q01–Q03); ticket SLA (Q09); admission forms/TC (G-59/G-61); promotion (G-62); retention/consent (G-12/G-86/G-87). These are decisions, not evidence — see `DOCUMENTATION_GAPS.md` / `OPEN_QUESTIONS.md`.

### 6. Server-side information not recoverable through browser inspection
Write payloads; server-side authorization enforcement (SEC-05); database schema/indexes/constraints; validation/idempotency/error semantics; server-held enum values; access-matrix response contents; background jobs/dispatch internals. **Client-side reverse engineering does not reveal the complete backend** — these are supplied by the replacement's own design, not inferred (`WORKFLOW_EVIDENCE_GAPS.md` §C).

## Coverage scorecard

| Dimension | Teacher | Other roles | Finance SPA | CRM UI | Server/DB |
|---|---|---|---|---|---|
| Routes | ✅ observed + static | ◐ static only | ◐ API only (frontend pending) | ◐ public + API only | — |
| Endpoints / methods | ✅ | ✅ catalogued (path-only for gated) | ✅ service API (151) | ✅ API (610) | ✗ bodies/rules |
| Screens / UX | ✅ (42) | ✗ (need account) | ✗ (need handoff) | ✗ (need account) | — |
| Workflows (reads) | ✅ | ◐ | ◐ | ◐ (inferred) | ✗ writes |
| Rules / enforcement | ✗ | ✗ | ✗ | ✗ | ✗ (by nature) |

Legend: ✅ done · ◐ partial/pending-access · ✗ not available by access boundary or by nature.

## Is the Eduvate investigation "sufficiently complete"?
- **For Stage 3 comparison:** the teacher-verified core + the full static surface (now including CRM and the finance service API) is a **sufficient and faithful baseline** to compare against the existing build. The role-gated screens and the finance SPA frontend would **raise fidelity on those specific surfaces** but are not prerequisites to begin comparing.
- **For a truly exhaustive functional map:** two access-gated captures remain (authenticated 18-route + finance-frontend crawl; and per-role crawls), plus the business-rule and server-side categories that **no amount of browser work can close**.

**Recommendation:** either (a) arrange the authenticated browser + test accounts to run the two remaining read-only captures and lift §4 further, or (b) accept the current baseline as sufficient for Stage 3 and capture role specifics opportunistically. **This is the owner's call — Stage 2 is held until that approval.**
