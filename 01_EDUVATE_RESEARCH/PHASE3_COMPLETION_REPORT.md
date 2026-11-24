# Phase 3 Completion Report — Autonomous Deep Reverse Engineering

**Date:** 2026-11-23 · **Scope:** static deep analysis of all three Eduvate SPAs + an executed read-only route crawl (one **teacher** account, ERP level 11) · **Rule obeyed throughout:** static discovery is never equated with runtime verification; nothing was implemented; no write, probe, privilege escalation or ID enumeration was performed.

> **Headline:** the full static corpus was analysed and the live teacher-role crawl was executed. Runtime-observed endpoints rose **94 → 171**, 72 new value-free response schemas were captured, and we have the first **runtime** evidence of ERP role-gating. The crawl stopped cleanly on the server's **429 rate limit** after 408 of 426 routes — so this is **not** full runtime coverage, and this report says so wherever it matters.

---

## 1–13 · The numbers you asked for

| # | Metric | Value |
|---|---|---|
| 1 | **Routes attempted** | **408** of 426 queued (18 unreached — the crawler hit the server's 429 and stopped by design) |
| 2 | **Routes successfully rendered** | **42** (ERP 35, HRMS 3, CRM 4) |
| 3 | **Routes redirected** | **366** — 359 to `/erp/user-profile`, 7 elsewhere; **0 redirected to login** (session stayed authenticated) |
| 4 | **Routes blocked by role permissions** | **359** (redirect-to-profile; **INFERRED** — a client-side guard redirect, not an observed server 403) |
| 5 | **Routes skipped for safety** | **287 excluded from the queue by design** (PARAM 149 + STATE_CHANGING 98 + SENSITIVE 40) + **18 not reached** after the 429 stop |
| 6 | **New runtime-observed APIs** | **77** (method+path) vs the Phase 1/2 baseline — 65 on rendered routes |
| 7 | **Total unique API candidates** | **6,577** catalog rows (6,539 discovered statically + 38 runtime-only) |
| 8 | **Methods resolved statically** | **5,427** of 6,577 (82.5%), read from actual code call sites — never guessed from path shape |
| 9 | **Methods confirmed at runtime** | 171 endpoints runtime-observed; of **107** with both a static call-site method and a runtime method, **104 agree directly**, 3 are the benign multi-method REST pattern, **0 true contradictions** |
| 10 | **Newly recovered response schemas** | **72** value-free field/type shapes (`10_REA_EVIDENCE/sanitized_metadata/E019_phase3_crawl_api_shapes.json`) |
| 11 | **Documentation gaps closed** | see §3 |
| 12 | **Remaining unknowns** | see §4 |
| 13 | **Updated handoff readiness** | see §5 |

---

## 2 · Validation that the results hold up

- **Every attempted route has a final status classification** (408/408 rows in `ROUTE_CRAWL_RESULTS.csv`: 42 ACCESSIBLE, 359 ROLE_RESTRICTED, 7 REDIRECT). The 18 unreached routes are listed explicitly in `REMAINING_UNKNOWNS.md`.
- **Redirects and role restrictions are recorded accurately** — final URL per route, with an `access` class and a `route_attributable` flag. Role restriction is labelled INFERRED because it is a client redirect, not a server denial.
- **Newly observed endpoints are deduplicated** — keyed by `(service, method, normalized-path)`; IDs masked to `{id}`; `/qbox` prefix stripped; 114 distinct observed, 77 new.
- **Static and runtime counts remain separate** — static candidates (6,577 catalog rows) and runtime-observed (171) are reported in different columns and never summed into a "working APIs" figure.
- **HTTP methods match the evidence** — runtime methods come from the observed request; static methods from the call site; agreement is 104/107 with 0 contradictions.
- **Response schemas are field names + inferred types only** — validated: all 23 distinct leaf values are type/format descriptors (`string`, `number`, `string(date)`, `string(email)` as a *format* tag, never a value).
- **Privacy: clean, package-wide.** No personal data, bank/account numbers, secrets, tokens, cookies, or the browser WebSocket identifier appear in any deliverable or in the raw capture. Multiple sweeps confirmed (emails: none non-framework; digit runs: none; WS-id: none; secret values: none).

---

## 3 · Documentation gaps closed (and how)

| Gap | Status after Phase 3 |
|---|---|
| "Catalogs record no HTTP methods" | **Closed** — methods resolved for 5,427 endpoints from code call sites |
| Finance-service API "not recovered" | **Closed (surface)** — 141 finance endpoints with methods/fields; runtime-confirmed `/apiV1/*` and `/api/v2/transport/*` |
| No runtime reachability data per role | **Closed for the teacher role** — 42 render / 359 role-redirect measured at runtime (`ROLE_ROUTE_API_MATRIX.md` §6) |
| Response shapes only for 88 endpoints | **Extended** — +72 runtime shapes (E019); total ~160 runtime shapes |
| Service boundaries uncertain | **Resolved at runtime** — transport data → **finance** service; lesson-plan content → **MGMT** service; chat → **Mattermost** (`/api/v4/*`); analytics → first-party `analytics.letseduvate.com` |
| G-19 (finance) | Narrowed — service API mapped; finance **SPA** screens + fee **rules** still open |
| G-13 (errata not applied) | Closed earlier this phase — 48 errata applied in place |

## 4 · Remaining unknowns (unchanged by more crawling)

- **18 queued routes never visited** (429 stop) — several teacher-facing (`/dashboard`, `/homework`, `/feedback`, `/teacher-observation`, `/test-result`…). A later, rate-limit-respecting re-run could reach them.
- **Other roles** — admin, accountant, principal, parent, student runtime behaviour. Only a teacher account exists; impersonation is out of bounds. 359 role-gated screens' own data needs a higher-privilege login.
- **Server-side enforcement (SEC-05)** — the gate observed is **client-side**. Whether the server also refuses those endpoints to a teacher was not tested (no direct API calls).
- **Write/interaction flows** — request bodies, state transitions, validation and error behaviour require performing actions; the crawl is navigate-only. Static body-key names (1,720 endpoints) are the ceiling of passive analysis.
- **Finance SPA internals, server framework/DB, source-level names** — not in the shipped client (0 source maps); Django/DRF stays INFERRED.

Full detail: `13_AUTONOMOUS_DEEP_ANALYSIS/REMAINING_UNKNOWNS.md`.

---

## 5 · Which features can now be specified more accurately

The crawl rendered the **teacher daily-teaching core**, so these specs now carry runtime-observed, method-bearing, schema-backed evidence (module notes added in place):

| Area | Module | What runtime evidence added |
|---|---|---|
| **Timetable** | m05 | `/timetable/student-timetable`, `/timetable/unfilled-section-slots` runtime-confirmed with shapes; richer admin timetable surface stays STATIC (needs a coordinator role) |
| **Attendance** | m08 | Screen reachable; loads `/erp_user/user-mgmt/user-grades` first — the marking API is gated behind a grade/section selection (honest boundary of navigate-only) |
| **Academic calendar** | m04 | Event-driven, branch-scoped: `/academic/imprest-event`, `/academic/{id}`, `/erp_user/user-mgmt/user-branches` |
| **Student information** | m05/m14 | `/erp_user/user-mgmt/student-information`, `/student-subjects` envelopes runtime-confirmed |
| **Assessment / report card** | m10 | Dedicated **`/acms/` namespace** with curriculum sub-resources; rich dashboard shape (`upcoming_exams, upload_deadlines, top_bottom_branches, year_timeline`); report-card returned a **500** once (reliability signal) |
| **Lesson plan** | m09 | Curriculum content served by the **MGMT service** (`/lesson_plan/*`) — a service boundary for the build to preserve or merge |
| **Homework** | m07 | Evaluate/update/analytics screens reachable; profanity-list + REPORTS embed; grading submit needs a write |
| **Finance-related** | m14/m16 | Student registration pulls **finance receipt config**; **transport data is served by the finance service** (`/api/v2/transport/*`) — resolves earlier ambiguity |
| **HRMS / recruitment** | m18 | Teacher self-service only (`/employee-grievance/action`, `/payroll/set-pin`); confirms per-route permission arrays gate the rest |
| **CRM** | — | Only public partner pages render for a teacher (`/become-partner`, `/partner-welcome`, `/thank-you-page`) |
| **Teacher ↔ class relationship** | m05/m11 | Teacher's timetable, online-class roster (`/erp_user/students-online-class`) and subject scope runtime-observed — the teacher-to-section linkage is evidenced, not just inferred |
| **Role-based route restrictions** | m02 | **Runtime-proven**: 88% of ERP routes redirect a teacher to the profile page — the access matrix's effect, first measured live |
| **App shell** | m01 | The per-navigation bootstrap fan-out (notifications, feedback nudge, FCM register, ticket badge, analytics) documented — a consolidation opportunity |

### Updated handoff readiness
Verdict is **unchanged: conditionally ready**. Phase 3 **raises evidence quality** for Phases 1–4 (the daily-teaching core is now runtime-backed with real contracts), lowering endpoint/method risk. It does **not** change the gated phases: Phase 5 (Fees) still needs customer fee rules + the gateway decision; authorization design stays PROPOSED because server-side enforcement was not tested; other roles remain unexercised. See `HANDOFF_READINESS.md` (updated).

---

## 6 · Artifacts produced / updated this phase

**New:** `ROUTE_CRAWL_RESULTS.csv`, `E019_phase3_crawl_api_shapes.json`, this report.
**Updated (runtime):** `RUNTIME_VERIFICATION_REPORT.md`, `STATIC_VS_RUNTIME_COMPARISON.md`, `COVERAGE_REPORT.md`, `REMAINING_UNKNOWNS.md`, `RESPONSE_SCHEMA_CATALOG.md`, `ROLE_ROUTE_API_MATRIX.md` (§6), `API_ENDPOINT_CATALOG.csv` (39 promoted + 38 added → 6,577 rows), folder-13 `README.md`.
**Handoff pointers:** root `README.md`, `CLAUDE_CODE_MASTER_PROMPT.md`, `AUDIT_REPORT.md` §H.2, `DOCUMENTATION_GAPS.md`, `HANDOFF_READINESS.md`, `04_BACKEND_ANALYSIS/api_inventory.md`, `02_FRONTEND_ANALYSIS/route_inventory.md`.
**Module specs with endpoint-cited runtime notes:** m01, m04, m05, m07, m08, m09, m10, m11, m12, m14, m16, m18.

**Note on `API_CALLSITE_INDEX.csv`:** unchanged by the crawl, intentionally. It indexes **static call sites** (file:line, client, wrapper). A runtime observation is not a call site; the 38 runtime-only endpoints (Mattermost + bootstrap calls built outside the ERP bundles) therefore appear in the endpoint catalog as RUNTIME_OBSERVED but have no call-site row — correctly. **Erratum (2026-11-24):** this file holds **8,530** call-site records across **5,364** endpoints (Σ catalog `callsite_count` = **10,006**); the "11,146 resolved call sites" quoted elsewhere was a non-retained static-pass counter and is not reproducible — see `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md`.

**Reproducibility (value-free tooling, scratchpad):** `process_crawl.py` (crawl → deliverables), `compare_methods.py` (static vs runtime method agreement), `crawler.mjs` (read-only CDP navigator). Raw capture and the JS corpus stay only in the temporary session directory, per the package's data-handling statement.
