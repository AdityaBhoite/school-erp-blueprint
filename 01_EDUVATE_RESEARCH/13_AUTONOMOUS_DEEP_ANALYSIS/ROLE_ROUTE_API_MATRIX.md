# Role / Route / API Matrix

The account used throughout the investigation is **Teacher (ERP level 11)**. Other roles were never logged in. §1–§5 gather the **static** authorization signals in the shipped code; **§6 adds runtime role-restriction evidence** from the Phase 3 crawl. None of it proves server-side enforcement (see `AUDIT_REPORT.md` SEC-05).

## 1. How the original gates access (recap, confirmed at call sites)
- **ERP**: there is no per-route permission array in the ERP router (route objects carry only `path` + lazy `Component`). ERP authorization is driven by the server-provided **access matrix** (`/erp_user/access-control/matrix/`), cached client-side, plus inline `user_level` checks inside components. So ERP route guards are **data**, not code — which is why this pass finds **0 ERP routes with a static permission declaration** but **65 inline level checks**.
- **HRMS**: the `/recruitment` route table *does* carry `permission:{is_view,is_add,is_modify,erpIds,...}` arrays (Phase 1/2 E007). That remains the richest static role evidence and is unchanged.

## 2. Inline user-level checks found in ERP code (65 sites)
These are `user_level === N`, `!==`, or `[...].includes(user_level)` comparisons inside shipped components. Levels referenced (name from E017):

| Level | Name (E017) | Check sites |
|---|---|---|
| 1 | Super Admin | 1 | [2, 8, 9, 10, 11, 14, 26, 27, 28, 32, 90] | 25 |
| 7 | HR Admin | 7 | [] | 4 |
| 8 | Principal | 8 | [8, 11, 10] | 6 |
| 9 | Campus Incharge (CIC) | 9 | [] | 3 |
| 10 | Academic Coordinators | 10 | [11, 10] | 4 |
| 11 | Teacher | 11 | [] | 1 |
| 12 | Parent | 2 |
| 13 | Student | 2 |
| 14 | PRMs | 14 | [] | 4 |
| 19 | Store Manager | 3 |
| 25 | Zonal FinOps Manager | 25 | [] | 3 |
| 26 | School Manager | 26 | [9, 14, 28, 32] | 2 |
| 27 | Zonal Business Head | 27 | [8, 9, 10, 11, 14, 26, 28, 32, 90] | 1 |
| 31 | Trustee | 1 |
| 32 | Branch Accountant | 32 | [] | 4 |
| 33 | Store Admin | 1 |
| 34 | EA Academics | 1 |
| 35 | Customer Success | 1 |
| 36 | Admin Customer Support | 2 |
| 40 | Talent Acquisition | 4 |
| 41 | Manager | 7 |
| 42 | Employee | 8 |
| 56 | UNKNOWN | 1 |
| 71 | Branch Central FOE | 1 |
| 80 | Regional Finops Manager | 2 |
| 90 | Zonal Operation Manager | 90 | [9, 14, 26, 28, 32] | 1 |

These confirm that components branch on the same numeric levels catalogued in E017 (e.g. 1 Super Admin, 8 Principal, 11 Teacher, 13 Student, 32 Branch Accountant). They are display/UX branches; the server must still enforce.

## 3. Hard-coded identifier allow-lists (SEC-04, re-counted)
The frontend still embeds per-person allow-lists. This pass found **46 sites** carrying **115 identifier entries in total** (values **not** recorded — counts only):
- `includes(id-like strings)` in `assets/index.Budtsq2r.js` (line 1): 13 entries
- `includes(id-like strings)` in `assets/index.Budtsq2r.js` (line 1): 13 entries
- `includes(id-like strings)` in `assets/index.45ajA7Vp.js` (line 1): 8 entries
- `includes(id-like strings)` in `assets/deliveryReportComponent.D1N5j31U.js` (line 1): 6 entries
- `includes(id-like strings)` in `assets/getCcaAgentAdmissionAccess-DDB5G8nm.js` (line 1): 5 entries
- `erpIds property` in `assets/index-GEUUShrI.js` (line 497): 4 entries
- `erpIds property` in `assets/index-GEUUShrI.js` (line 497): 4 entries
- `erpIds property` in `assets/index-GEUUShrI.js` (line 497): 4 entries
- `erpIds property` in `assets/index-GEUUShrI.js` (line 497): 4 entries
- `erpIds property` in `assets/index-GEUUShrI.js` (line 497): 4 entries
- `includes(id-like strings)` in `assets/index-BquURWmW.js` (line 1): 4 entries
- `includes(id-like strings)` in `assets/index-DL03ZzYw.js` (line 2): 4 entries

(Full machine list: `API_CALLSITE_INDEX.csv` is value-free; the id-list sites are in the role-evidence data, counts only.) This is the concrete evidence behind `SECURITY_REQUIREMENTS.md` SR-ID-1: grants must be server-side data, never shipped code.

## 4. Feature flags that gate navigation/behaviour
Flags referenced in code (occurrence counts): `is_recruitment_enabled` (40), `is_crm_enabled` (39), `impersonating` (34), `is_superuser` (32), `is_virtual_student` (16), `is_hrbp_support` (13), `hideForSwitchUser` (12), `show_bug_tracker` (11), `k12_access_matrix` (10), `requiresKeyperson` (6), `allowAcmsOpsTeam` (6), `allowSeoTeam` (6), `require_reporting_manager` (6), `is_taproot_user` (2), `require_keyperson` (2).

## 5. Route → API → (role) linkage
`ROUTE_COMPONENT_MATRIX.csv` links each of 729 routes to its lazy chunk, the APIs called directly in that chunk, and the service modules it imports. Because ERP carries no per-route permission in code, the role column there is populated only for HRMS/CRM routes that declare one. For ERP, the authoritative route→role mapping is the runtime access matrix, which is data this static pass cannot read.

## 6. Runtime role-restriction evidence (Phase 3 crawl, 2026-11-23) — NEW
The read-only crawl navigated 408 safe routes as a **Teacher (level 11)** and recorded the final URL. This is the first **runtime** measurement of the access matrix's effect, and it is decisive:

| Access outcome for a teacher | Routes | Share of attempted |
|---|---:|---|
| **Rendered** (teacher may use) | 42 | 10% |
| **Redirected to `/erp/user-profile`** (role-gated) | 359 | 88% |
| Redirected elsewhere | 7 | 2% |

- The redirect target is **always** `/erp/user-profile` — the client guard has a single "not allowed" destination. This is **INFERRED** role-gating (a client-side redirect, not an observed server 403). It confirms, at runtime, the §1 claim that ERP route access is decided by the cached access matrix + guards, not per-route code.
- **Caveat (enforcement):** the gate observed is client-side. Whether the *server* also refuses those endpoints to a teacher was **not** tested (no direct API calls were made). `SECURITY_REQUIREMENTS.md` SEC-05 (verify server-side enforcement) therefore stays open.

### Teacher-accessible ERP surface (the 35 rendered ERP routes, grouped)
- **Attendance:** `/attendance` · **Timetable:** `/timetable` · **Calendar:** `/calendar`
- **Assessment / ACMS:** `/acms`, `/acms/dashboard-v2`, `/assessment-reports`, `/assessment/test`, `/assessment/question-paper`, `/assessment/report-card`, `/offline-marks`, `/question-chapter-wise`
- **Homework:** `/homework/evaluate-homework`, `/homework/update-homework`, `/homework/student-analytics`
- **Teaching:** `/lesson-plan`, `/board-prep`, `/board-prep/inbox`, `/online-class`, `/language-app-dashboard`
- **Communication:** `/announcement`, `/chat` (Mattermost; 36 calls), `/ptm-feedback`
- **Transport (view):** `/transport-buses`, `/transport-drivers`, `/transport-routes`, `/transport-stop-points`
- **Other:** `/student-registration`, `/access-form`, `/feature-request/list`, `/finance-partner`, `/referral-success`, `/store/kit-master-form/`, `/pdf`, `/privacy-policy`, `/terms-and-conditions`
- **HRMS self-service (teacher):** `/employee-grievance/action`, `/njwelcomeform-sample`, `/payroll/set-pin`
- **CRM public partner pages:** `/become-partner`, `/partner-welcome`, `/thank-you-page`

### Runtime anomalies worth noting
- `/assessment/report-card` returned a **500** on one call (server error for the teacher context) — a real reliability signal.
- `/homework/*`, `/lesson-plan`, `/transport-drivers` each returned a **404** on a sub-resource (teacher context lacks some id), alongside 200s.
- The crawl's terminating **429 came from `/chat`** — the Mattermost page's polling (36 calls on load) tripped the rate limit. A build note: chat polling is heavy.

Full per-route data (outcome, `access`, `route_attributable`, statuses, static UI labels): `ROUTE_CRAWL_RESULTS.csv`.
