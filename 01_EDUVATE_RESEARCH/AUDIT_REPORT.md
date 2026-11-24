# Audit Report: Orchids ERP Research Handoff

| | |
|---|---|
| Audit date | 2026-11-23 |
| Audited package | `D:\REA Claude\orchids-erp-research\` (98 files, about 850 KB of text, 10 screenshots) |
| Auditor | Claude Code (Opus 5.5), documentation audit only |
| Constraints honoured | The original system was not contacted again and the investigation was not repeated. No ERP code was written. No existing specification was edited except the two pointer changes listed in §H. |
| Companion deliverables | `DOCUMENTATION_GAPS.md`, `HANDOFF_READINESS.md`, `CLAUDE_CODE_MASTER_PROMPT.md`, plus `SECURITY_REQUIREMENTS.md` (task F) and `05_DATA_MODEL/er_diagram_core_v2.mmd` (task D) |

## 0. Verdict

The package is **usable as a build handoff for the core school-operations scope**, once the corrections in this report are applied. The runtime evidence is honest and reproducible. Every one of the 94 observed endpoint records in the docs matches the sanitized capture exactly. The privacy handling is clean.

The main weaknesses are:

1. **Evidence labels drift.** In about 40 places the docs label a feature OBSERVED when only a field name, a button or an empty screen was seen. In those places the behaviour itself is static or inferred.
2. **Finance, transport, administration and the parent/student experience are specified from menus and route names.** These areas were never observed, and almost no static API evidence exists for them.
3. **The proposed architecture leaves several choices open.** It also contains one escape hatch (a Django option) that conflicts with the agreed TypeScript stack.
4. **The ER diagram has no keys or scoping columns.** It also asserts one hierarchy (Zone → City) that the evidence does not support.
5. **The existing master prompt is not self-contained.** It requires reading about 60 files, and it tells the coding agent to treat all STATIC findings as requirements, which invites scope creep.

| Task | Result | High | Medium | Low |
|---|---|---|---|---|
| A. Evidence classification | Pass with corrections (errata ER-01…ER-48) | 3 | 6 | 4 |
| B. Architecture consistency | Pass with corrections (decisions recorded in §B.3) | 0 | 4 | 3 |
| C. API documentation | Observed: **pass** (verified by script). Static: pass with corrections | 1 | 3 | 2 |
| D. Domain model | Partial: keys, scoping and several entities missing. Corrected core diagram supplied | 2 | 4 | 1 |
| E. Functional completeness | Partial: 9 modules buildable now (4 outright, 5 with documented defaults), 5 need decisions or more specification, 5 blocked, deferred or out of scope | 2 | 3 | 0 |
| F. Security requirements | Requirements written: `SECURITY_REQUIREMENTS.md` (75 requirements, mapped to 13 observations, plus 10 proposed acceptance criteria) | — | — | — |

Severity scale:
- **High**: would cause a wrong implementation or misrepresent evidence.
- **Medium**: an inconsistency that needs a decision.
- **Low**: a count, wording or cosmetic issue.

## 1. Method

| Check | How | Result |
|---|---|---|
| Read every document | All 98 files read. The large evidence files (E005/E006/E007/E011/E012/E016) were parsed by script. | — |
| Observed APIs vs raw evidence | Script compared every row of `api_inventory.md` and every block of `api_contracts.md` with `10_REA_EVIDENCE/sanitized_metadata/E016_runtime_api_shapes.json` (method, path, query keys, statuses, request shape, response shape) | 94/94 match. 3 response shapes are truncated for display only (Mattermost client config, `grievance-module/employee-ticket/`, `payroll/employee-payslip/`). |
| Screen tour arithmetic | Counted `screens` in E016 | 71 navigations, 70 unique routes, 39 unique accessible, 31 denied |
| Static catalogs | Parsed E011 (1,174 rows) and E006 (1,430 rows), normalised the paths, and looked up each observed endpoint in them | No HTTP methods anywhere in the catalogs. About 2,430 distinct path templates. 36 of the 94 observed endpoints are **absent** from the catalogs. |
| Traceability | Matched FR → AC → TC IDs | 74 FR, 74 AC, 70 TC. 4 ACs have no test case (AUTH-07, TEN-03, RBAC-06, NFR-01). M04 and M06 do not cite their ACs. M09, M11, M16 and M18 have no ACs. |
| Privacy | Scanned for 10+ digit numbers, ERP-ID patterns, emails, JWT/API-key patterns, long hex strings and DevTools endpoints | None found. 11 `REDACTED` markers in E007 as designed. |

---

## A. Evidence classification

### A.1 What is sound
- The five labels (OBSERVED, STATIC, INFERRED, PROPOSED, UNKNOWN) are defined once in `README.md` and are used in almost every document.
- Static evidence files state that runtime reachability is not proven. Module specs M13–M17 openly say they are static-only.
- Value-free capture was respected. The only values retained are configuration labels (E017).
- Unknowns are collected in `OPEN_QUESTIONS.md`, each with a proposed default.

### A.2 Systemic findings

| ID | Sev | Finding | Correction (binding via errata) |
|---|---|---|---|
| A-01 | High | **HTTP methods attached to static endpoints are inferred, not static.** E006 and E011 hold path templates only; the strings `GET`, `POST`, `PUT`, `PATCH`, `DELETE` and `method` occur 0 times. Yet the specs present methods as STATIC, for example m08 "POST `/academic/bulk_create_attendance/` … STATIC" and m06 "STATIC write endpoints: POST `/academic/create-dairy/`". | Any method on a non-observed endpoint is **INFERRED** from its name (ER-07). Only the 94 endpoints in E016 have OBSERVED methods. |
| A-02 | High | **Observing a field is not observing a behaviour.** Many features are labelled O because a field exists in a GET response. The behaviour was never exercised, because the tour was read-only and navigate-only, with no clicks and no submissions. Examples: calendar Draft → Generated → Published; timetable draft/publish; marks publish per branch; SEN slots; attendance upsert. | Split labels: **O** for the field or screen element, **I** for the behaviour (ER-15…ER-34). |
| A-03 | High | **Several "observed" screens only showed filters or an empty state:** Timetable ("Period system has not been created"), Attendance (empty roster), Homework (filters only), Lesson Plan ("Please apply filter"), Report Card (title and Go Back only), Clickers, School Wall and Comms Inbox (empty lists). These prove the layout and the required filters. They do not prove data presentation or interaction. | Coverage cells corrected (ER-14). The module completeness matrix in §E reflects this. |
| A-04 | Medium | Mixed labels such as "O/S" do not say which part is observed (for example `feature_catalog.md` F-012, F-021, F-023). | Split per errata ER-31. |
| A-05 | Medium | **Some OBSERVED claims cannot be checked against the retained evidence.** The accessibility outlines were not retained (E016 keeps only requested/final/denied per screen). Affected claims: the M01 profile-menu items, M02 "Update Address" and "Employee Care card", performance.md "10–25 API calls per page, several duplicated", M01 "called up to 3×", and "ERP user levels up to 113". | Kept as written but downgraded to LOW confidence (ER-09, ER-11, ER-12, ER-13). The profile-menu items are probably mislabelled, because a navigate-only tour cannot open a menu. |
| A-06 | Medium | M01 says the shell calls are "observed on every page". In fact the home banners and the grievance summary were seen only on `/erp/user-profile` (1 screen). FCM register, event feedback, Clarity and the finance delegation call were seen on 24 ERP screens and on no HRMS screen. Notifications were seen on 38 screens. Likewise, M18 says every HRMS page calls `user-mgmt/current-user/`, but that endpoint is absent from E016; it exists only in the static catalog. | ER-08, ER-47 |
| A-07 | Medium | `COVERAGE_MATRIX.md` overstates coverage. It marks ● for Timetable (empty grid), Online class, Communication (no compose), Security and access control (server-side enforcement is UNKNOWN per SEC-05), Integrations (mostly static) and Product functionality (teacher role only). | ◐ for each (ER-14) |
| A-08 | Medium | Static entries are counted as endpoints. "2,604 endpoints" is really 2,604 catalog **entries**. That figure includes 9 base-URL variable rows and duplicate paths under different keys, which leaves about 2,430 distinct templates. | ER-45 |
| A-09 | Medium | **Absence from the static catalog proves nothing.** 36 of the 94 observed endpoints do not appear in E006/E011. They include the whole ACMS calendar and assessments family, the comms inbox, the diary list and board prep. The catalogs reflect older `endpoints` objects; newer modules use inline paths in lazy chunks. | Recorded in §C.2. The coding agent must not treat the catalogs as complete. |
| A-10 | Low | Arithmetic: "70 routes: 40 accessible, 31 denied" sums to 71. | 70 unique routes = 39 accessible + 31 denied, over 71 navigations (ER-01, ER-02) |
| A-11 | Low | `api_contracts.md` says "Request bodies: none were observed", but 4 request shapes were captured. | Infrastructure-only request shapes (ER-04) |
| A-12 | Low | "94 API endpoints" mixes product and non-product calls. | 94 = 87 product-service endpoints (ERP 53, HRMS 30, FIN 2, MGMT 2) + 2 in-house analytics + 4 Mattermost (3 API + 1 static CSS) + 1 Microsoft Clarity beacon (ER-03) |
| A-13 | Low | WF-14 is rated HIGH confidence on the strength of a single UI notice. | MEDIUM (ER-35) |

### A.3 Focus modules: what is actually known

| Module | OBSERVED | STATIC | INFERRED | PROPOSED | UNKNOWN |
|---|---|---|---|---|---|
| **Administration** (user management, org masters, access control, approvals) | Own profile (`/erp/user-profile`, `user-info`). Own scope lists (`user-branches`, `user-grades`). 80 ERP user-level names and communication roles (E017). Zone/branch/city/branch-type lists inside `/academic-communication/meta/`. `GET /erp_user/access-control/matrix/` returned 200 (body not captured). Teacher denied on 31 routes. | Admin, Students and Approvals menus (E005). `/user-management/*`, `/master-management/*`, `/access-control` routes (E012). `masterManagement` (81) and `userManagementRevamp` (62) endpoint names (E011). Access-matrix structure and 1-hour client cache (bundle code). HRMS route permission arrays (E007). | CRUD and soft-delete/restore semantics (from `delete_*`/`restore_*` pairs). Zone → City nesting. All HTTP methods. | 11 system roles, `module.resource.action` permissions, scoped role assignments, UserGrant, audit viewer | Admin screen layouts and fields. Validation rules. Whether the server enforces authorization. Matrix response schema. Permissions of every level except Teacher. |
| **Finance** | `GET FIN /apiV1/expense-delegation-context/` (shell call). `GET FIN /apiV1/v2/reimbursement-form/` (an HRMS screen). `GET /academic/imprest-event/` (paid events: price, refundable, approval counts). No fee screen. Finance absent from the Teacher's rail. | Finance menu (11 sub-sections, about 110 items). 79 `/student-fee*` routes. Fee approval menus. About 20 finance-service endpoint names (sibling concession, wallet lists, imprest top-up, dues-till-date, multi-fee payment link, finance dashboards, defaulters banner). **The core fee plan, payment and receipt APIs were not recovered.** | Fee term/plan/group structure (from route names). Instalments and partial payments. Receipt types. Wallet types. Maker–checker on cancellations. | BR-FEE-1…5, ledger model, webhook verification, FEE/PAY ACs, every entity field | Fee computation, instalment rules, rounding, fine formula, how partial payments are allocated, payment gateway, receipt legal content, taxes, refund destination |
| **Parent / student** | Nothing from a parent or student session. Indirectly, teacher-side data exposes student names and IDs (SEN timetable slots, board-prep rollup). | Student/parent navigation categories with default routes (E005). `student-login`. Parent fetch/search/link endpoints. Student homework, timetable and attendance endpoints. `studentDashboard` group (15 endpoints). Level names 12 Parent and 13 Student (E017, OBSERVED). | Guardian login (no guardian-login endpoint was found). Sibling switcher (`siblingsData`). Students never see HRMS or CRM. | Account model (Q12, Q13). Parent OTP login. Child switcher. Dashboards. Permission rows. | Every parent and student screen. Visibility rules. Notification preferences. Consent. |
| **Transport** | Nothing. | Transport menu (17 items). 53 `/transport-*` and `/iframe/track/*` routes. About 15 finance-service bus report, cleanliness and document endpoints. **No route, stop, vehicle, allocation or GPS API was recovered.** | Entities and fields (stops with lat/lng, vehicle document expiry, driver licence), all from route names | Replacement scope (Could), GPS-provider integration, signed tracking links | Every behaviour, fee slabs, GPS provider, bus attendance |

Code-derived functionality in these four modules is consistently presented as such in the module specs (M13–M16 say "STATIC evidence only"). The leaks into OBSERVED happen in `SYSTEM_REQUIREMENTS.md`, `feature_catalog.md`, `entity_dictionary.md` and `COVERAGE_MATRIX.md`, and are corrected in §G.

---

## B. Architecture consistency

### B.1 Where each architecture is documented
- **Original system, as observed or inferred:**
  - `02_FRONTEND_ANALYSIS/technology_stack.md`
  - `04_BACKEND_ANALYSIS/backend_architecture.md`
  - `04_BACKEND_ANALYSIS/authentication.md`
  - `04_BACKEND_ANALYSIS/integrations.md`
  - `07_NON_FUNCTIONAL_REQUIREMENTS/deployment.md`, "Observed" section
- **Replacement, as proposed:**
  - `11_IMPLEMENTATION_PLAN/recommended_architecture.md` (rationale)
  - `12_CLAUDE_CODE_HANDOFF/ARCHITECTURE_BLUEPRINT.md` (prescriptive)
  - `12_CLAUDE_CODE_HANDOFF/DEVELOPMENT_RULES.md`
  - `07_NON_FUNCTIONAL_REQUIREMENTS/deployment.md`, "Proposed" section

The two are already in separate files and are not conflated. The issues below are about consistency inside the proposed side, and about the missing side-by-side reasoning.

### B.2 Findings

| ID | Sev | Finding | Resolution |
|---|---|---|---|
| B-01 | Medium | `recommended_architecture.md` §2 allows "an equivalent Python stack (Django + DRF + Celery + React)". This conflicts with the agreed replacement stack (React + TypeScript + PostgreSQL) and with the NestJS blueprint. | **Withdrawn.** TypeScript end-to-end is binding. Reviving the option needs an ADR approved by the owner (ER-40). |
| B-02 | Medium | Proposed docs leave choices open: Prisma or Drizzle, BullMQ or pg-boss, Ant Design or MUI, Zustand or Context, NestJS or "Fastify + modular". | Resolved in §B.3 (PROPOSED defaults; an ADR may override them) |
| B-03 | Medium | The tenancy hierarchy is named three different ways. `authentication.md` says "tenant → school → branch". `recommended_architecture.md` says "Tenant (school group) → School/Branch". The ER diagram has Tenant → Zone → City → Branch. SR-AZ-2 lists both "school" and "branch" as scopes. | **Tenant** = school group (the original's "brand"). **Branch** = one school campus; wherever requirements say "school", they mean Branch. **Zone** and **City** are optional tenant-level groupings on Branch (two independent nullable FKs, with no Zone → City FK). See §D. (ER-41) |
| B-04 | Medium | The existing master prompt says "Treat OBSERVED/STATIC as product requirements to match functionally". Static evidence spans 2,604 catalog entries and 688 routes, most of them out of scope. | The new master prompt fixes this: scope comes only from the tier tables. STATIC proves existence in code, not a requirement (ER-44). |
| B-05 | Low | The observed backend framework (Django REST Framework), MongoDB for notifications and GCP hosting are correctly labelled INFERRED everywhere. | No change. The brief's statement "Django-based backend services" remains an inference. |
| B-06 | Low | JSON casing is "camelCase or snake_case; pick one" in `recommended_architecture.md` §6, but camelCase in DEVELOPMENT_RULES 18. | camelCase |
| B-07 | Low | Responsive breakpoints are 360/768/1024/1440 in `design_system.md` §6, but 390/768/1440 in DoD and NFR-03. | Test at 390, 768 and 1440. A 1024 token is allowed for layout (ER-43). |

### B.3 Side-by-side: original vs replacement

| Concern | Original (class) | Replacement (PROPOSED) | Reason for the change |
|---|---|---|---|
| Application topology | 3 separately built SPAs on one origin (`/erp`, `/crm`, `/recruitment`); moving between apps reloads the page (OBSERVED) | One React SPA with lazy feature modules and one shell | One router, one auth state and one design system; no reload between modules |
| Frontend language | JavaScript; minified HRMS bundle, readable ERP bundle (STATIC) | **TypeScript strict** | Shared types and contracts with the API |
| UI library | Ant Design 5.29 (HRMS), Ant Design in ERP (STATIC) | **Ant Design 5** with an original theme | Gives the same interaction patterns under an MIT licence. The original's brand colour and assets must not be reused. |
| Client state | Redux + redux-saga (ERP); React Context (HRMS) (STATIC) | **TanStack Query** for server state; React Context for session and branch/year context; no Redux | Query caching removes the duplicate calls seen in the original; less boilerplate |
| Routing | React Router; 688 ERP routes (STATIC) | **TanStack Router**, file-based per feature; `beforeLoad` permission check (UX only) | Type-safe routes and search params (branch and year live in the URL) |
| Backend | Django REST Framework (INFERRED, HIGH); about 10 HTTP services: core ERP, finance, HRMS, CRM, mgmt, reports, classes, events, analytics, chat (OBSERVED/STATIC) | **NestJS modular monolith** plus a worker process from the same codebase | One authorization and tenancy layer, ACID transactions across modules, small-team operations; modules can be extracted later |
| Database | Relational DB behind Django (INFERRED); MongoDB for notifications (INFERRED, MEDIUM); database per brand UNKNOWN | **PostgreSQL 16+**, one database, row-level security on `tenant_id`; notifications in Postgres (partitioned) | One store; RLS gives tenant isolation by construction |
| ORM / migrations | — | **Prisma** (as the blueprint lists first). Every tenant-scoped query runs inside a transaction that issues `SET LOCAL app.tenant_id`, using a DB role without `BYPASSRLS`. | Type safety and migration tooling. The transaction rule keeps RLS sound with pooled connections. Changing to Drizzle needs an ADR. |
| Jobs | Cloud Functions and separate services (STATIC) | **BullMQ on Redis**, fed by an outbox table | Redis is needed anyway for rate limiting; retries and dead-letter queues |
| API style | `/qbox/<app>/<resource>/`, ad-hoc versions (`v1`, `v2`, `apiV1`), mixed casing, 7 envelope variants, 5 pagination shapes (OBSERVED) | `/api/v1`, plural kebab-case resources, camelCase JSON, `{data}` / `{data, page:{number,size,total}}`, errors as `{error:{code,message,details,requestId}}` | Predictable clients; one generated OpenAPI document |
| Tenant selection | Brand chosen by host plus an `X-DTS-BRAND` request header on shared services (STATIC) | Tenant derived **server-side** from host and session; never from a client header | A client-chosen tenant header is a cross-tenant risk |
| Authorization | Numeric user levels (80 named); server-provided access matrix **cached in localStorage for 1 h**; route permissions and **employee-ID allow-lists hard-coded in the bundle** (STATIC); server enforcement UNKNOWN | Permission strings granted to roles, role assignments carrying scope and academic year, time-bound UserGrant records with a reason, server-side guards on every endpoint, navigation derived from the same catalogue | Single source of truth, auditable grants, revocation within 60 s (RBAC-06) |
| Tokens | Access and refresh JWTs in **localStorage**; refresh queue in an interceptor (STATIC) | httpOnly, Secure, SameSite cookies; access token ≤ 15 min; rotating refresh with reuse detection; CSRF protection | Script injection cannot steal the session |
| Secrets | OnlyOffice JWT secret, API keys and analytics key in client bundles (STATIC) | Secrets live only on the server; the build fails if any appear in the bundle | SEC-01, SEC-02 |
| Impersonation | `X-Is-Switch` header plus a localStorage flag (STATIC) | Super-admin only, time-boxed, read-only by default, visible banner, audited | SEC-08 |
| Analytics | Microsoft Clarity session replay on screens with personal data; in-house tracker with an embedded key (OBSERVED) | No session replay on screens with personal data; first-party, PII-free product analytics only | SEC-07, DPDP (children's data) |
| Files | GCS buckets and public CDNs (STATIC) | S3-compatible storage, tenant-prefixed keys, signed URLs ≤ 10 min, virus scan | TEN-03 |
| Hosting | GCP asia-south1 (INFERRED, HIGH) | Managed containers in an India region by default (Q20) | Data residency (DPDP) |
| Mobile | Mobile app likely (FCM, `app_image_url`, Play Store reviews page) (INFERRED, MEDIUM) | PWA first; native apps later on the same API | Cost; one codebase |

---

## C. API documentation

### C.1 The 94 observed endpoints (E016)

| Property | Verified state | Notes |
|---|---|---|
| HTTP method | 89 GET, 5 POST. All match E016. | The 5 POSTs are token refresh, FCM registration, analytics session, analytics batch and the Clarity beacon. **No domain write was observed.** |
| Paths | 94/94 match. Path parameters are not used in any observed path. | Services: ERP 53, HRMS 30, CHAT 4, ANALYTICS 2, FIN 2, MGMT 2, Clarity 1 |
| Query keys | 94/94 match. **Names only**: values and allowed values were not recorded. | Parameters whose meaning is UNKNOWN: `acad_session` vs `session_year` (Q22), `access_level`, `date_filter`, `tab`, `bucket`, `view_type`, `scope`, `paginate_by` |
| Statuses | 94/94 match. Non-200 responses: 404 × 3 (`/acms/meetings/`, `/academic/v1/ebook_school_wise_filter/`, `/erp_user/profanity-txt/`), 500 × 1 (`/assessment/get-student-grades/` without parameters), 401 × 1 (Mattermost), 403 during token bootstrap (notifications ×2) | `None` statuses are requests that were still in flight when the tour moved on |
| Request shapes | 4 JSON shapes (refresh `{refresh}`, `{fcm_token}`, analytics ×2) | No domain request body exists in the evidence. **Write contracts must not be reconstructed from E016.** |
| Response shapes | 88 captured. 6 not captured: Clarity, CSS, the three 404s, and `access-control/matrix` (200 with a non-JSON or empty body). | The access-matrix structure in `authentication.md` comes from bundle code (STATIC), not from a response. |
| Envelopes | 7 variants: `{status_code,message,result}` ×58; bare object ×13; raw DRF pagination ×7; bare array ×5; `{status_code,status,message,data}` ×2; `{status,message,data,force_update}` / `{status,data}` ×2; `{success,message,data}` ×1 | The `api_contracts.md` table misses the access-token variant (ER-05) |
| Pagination | 20 paginated endpoints in **5 shapes**: DRF `count/next/previous/results` (4); the same plus `limit/current_page/total_pages` (10); `total/page/page_size/data` (3); `total/page/page_size/results` (1, notifications); `count/page/page_size/results` (2) | **4 parameter spellings:** `page`+`page_size`, `page_num` (diary), `page_number` (e-book; returned 404), `paginate_by` (marks dashboard). `backend_architecture.md` describes only one style (ER-06). |
| Relationships | Inferred from `*_id` fields and Django `__` lookup keys (for example `section_mapping__grade_id`, `mapping_subject_id`) | INFERRED. No response contains embedded relationship metadata. |
| Authentication | Bearer JWT with refresh is **STATIC** (bundle). Header values were never read. Bootstrap 403s followed by 200 are OBSERVED. | Server-side authorization was not tested (no probing) and stays **UNKNOWN** |
| Confidence | Every row is HIGH in `api_inventory.md` | Appropriate for existence and shape. Field **meaning** and enum **values** are INFERRED/UNKNOWN because of the value-free policy. |

Field-level gaps that matter for the build: assessment `status`, online-class `status`, imprest `approval_status` (an integer), `event_type` (codes known only from screen labels), and `test_type`. None of their allowed values was captured. **They must not be invented as copies of the original.** The replacement defines its own enums (PROPOSED), as the module specs already do.

### C.2 The 2,604 static catalog entries (E006, E011)

| Property | Finding |
|---|---|
| Counts | E011: 1,174 entries in 124 groups, about 1,066 distinct normalised paths (22 entries are function expressions). E006: 1,430 entries in 82 groups, 9 of which are base-URL variables; about 1,386 distinct paths. **Combined: about 2,430 distinct templates.** |
| HTTP methods | **None recorded** (A-01) |
| Request / response shapes | None |
| Pagination | None |
| Authentication / authorization | None. HRMS route permissions are a separate source (E007). |
| Completeness | **Incomplete.** 36 of the 94 observed endpoints are missing. The core finance (fee plan, payment, receipt) and transport (route, stop, vehicle, allocation) APIs are missing. |
| Confidence | HIGH that the strings exist in the shipped bundle; **no** confidence that they are reachable, current or used |
| Use for the build | A feature-existence signal and a vocabulary source, nothing more. The replacement API is designed fresh from the module specs and the blueprint conventions. |

### C.3 Findings

| ID | Sev | Finding |
|---|---|---|
| C-01 | High | **No replacement API surface exists per module.** There is no endpoint list or OpenAPI draft, only conventions (blueprint §4) and examples. The coding agent must design these and record them as contracts. This is expected, but it is not stated anywhere, so an agent may try to mirror `/qbox` paths. The master prompt now forbids that. |
| C-02 | Medium | The error body schema of the original is UNKNOWN beyond one 500 response (`{status,data}`). This is not needed, because the replacement defines its own errors (blueprint §4). |
| C-03 | Medium | `access-control/matrix` returned no captured shape. The documented matrix structure (`userLevel`, `erpIds`, `is_view/add/modify/delete`) is STATIC from code. |
| C-04 | Medium | Three contract shapes are truncated in the markdown (C.1). The online-class shape is truncated to "rec…" in m11/WF-15, which hides `recording_password`, `recording_play_url` and `zoom_start_url` in a list response. This matters for security (SEC-10, ER-38). |
| C-05 | Low | `api_contracts.md` "A2" envelope row lists `/test-module/my-results/` incorrectly (it uses `result`) (ER-05). |
| C-06 | Low | `api_inventory.md` intro says "71 screens". It is 71 navigations over 70 unique routes (ER-02). |

---

## D. Domain model (`05_DATA_MODEL/`)

| Required element | Present? | Where | Issue | Resolution |
|---|---|---|---|---|
| Entity relationships | Yes | `er_diagram.mmd` (about 95 edges), `relationships.md` (24 rows with evidence) | Good provenance in `relationships.md` | — |
| Primary-key assumptions | **No** in the diagram; inconsistent elsewhere | Dictionary says "`id (uuid or bigint)`"; blueprint §5 says "`id uuid` (UUIDv7)" | The diagram has no attributes at all | **UUIDv7 primary keys** (the blueprint is prescriptive) (ER-42). The original's integer IDs (OBSERVED) are irrelevant except as a potential `external_ref` for imports. |
| Foreign keys | Implied only | Edges and `*_id` fields | No FK columns, no delete behaviour | FKs declared in `er_diagram_core_v2.mmd`. Default `ON DELETE RESTRICT` plus soft delete (`deleted_at`). |
| Multi-school (tenant) scoping | Convention only | Dictionary: "all tenant-scoped tables carry `tenant_id` (P)" | Not shown in the diagram. It is not said which tables also carry `branch_id`/`academic_year_id` for scoping and indexing. | v2 diagram shows `tenant_id` on every tenant table and denormalised `branch_id`/`academic_year_id` where scope checks need them. RLS enforces tenant only; branch, year and section scope is enforced in services (ScopeService). |
| Brand → branch hierarchy | Yes, with an unsupported edge | TENANT → ZONE → CITY → BRANCH, plus TENANT → BRANCH | **Zone → City is not evidenced.** In `/central-db/branch-view/` each branch has its own `zone{}` and `city_fk{}`, and `/acms/cities/` has no zone. The dictionary marks `City.zone_id` as O, which is wrong. | Branch has nullable `zone_id` and `city_id` that are independent of each other (ER-36). Zone → City becomes an ASSUMPTION, to be confirmed with the owner. |
| Academic-year relationships | Yes | TENANT → ACADEMIC_YEAR; BRANCH_ACADEMIC_YEAR join; PERIOD_SYSTEM → BRANCH_ACADEMIC_YEAR; FEE_TERM → ACADEMIC_YEAR | STUDENT_ENROLMENT links to both ACADEMIC_YEAR and CLASS_SECTION (a redundant path) with no consistency rule. Assessment and FeePlan have no year edge in the diagram (the dictionary has one). | Rule: `enrolment.academic_year_id` must equal the class section's year, enforced by the composite FK `(class_section_id, academic_year_id)` → `class_section(id, academic_year_id)` (v2). Year edges for Assessment and FeePlan get added when Phases 4–5 are modelled (DOCUMENTATION_GAPS G-79). |
| Student / class / section | Yes | STUDENT → STUDENT_ENROLMENT ← CLASS_SECTION ← GRADE_OFFERING; SECTION names it | No class-teacher edge (the dictionary has `class_teacher_id`, I). No roll-number uniqueness rule. | v2 adds the class teacher (via TeachingAssignment with role `class_teacher`) and a unique roll number per class section (ASSUMPTION). |
| Role / permission | Partial | USER → ROLE_ASSIGNMENT ← ROLE → ROLE_PERMISSION ← PERMISSION | **Scope is missing from ROLE_ASSIGNMENT in the diagram** (the dictionary has it). No UserGrant. **No teaching assignment**, yet teacher data scope depends on it (`role_permission_matrix.md` says "timetable allocation and/or explicit assignment", which is ambiguous). | v2 adds `scope_type/scope_id/academic_year_id`, USER_GRANT and **TEACHING_ASSIGNMENT** (staff × class subject × year) as the single source of teacher scope. Timetable slots must match an assignment. |
| Assumptions labelled | Partly | Two edges marked "(inferred)" | Most assumptions are unlabelled | Every assumption in v2 carries an `ASSUMPTION` comment |

Entities required by the requirements but **missing from the diagram**: AuditLog, Session/RefreshToken, UserGrant, TeachingAssignment, StaffAbsence/Leave (needed for substitution), Attachment/File, AnnouncementCategory/Audience, TicketCategory/routing rule, GradingBand, FineRule/Fine, Refund/ApprovalRequest, FeeAccount/ReceiptSeries, Communication (the central-to-school inbox, F-027), Outbox, ImportJob.

`05_DATA_MODEL/er_diagram_core_v2.mmd` adds the Phase 0–2 core with keys and labelled assumptions. The original `er_diagram.mmd` is unchanged and remains the broad map; v2 is authoritative for the core where they differ.

| ID | Sev | Finding |
|---|---|---|
| D-01 | High | No primary keys, foreign keys or tenant columns in the ER diagram (fixed in v2) |
| D-02 | High | No TeachingAssignment entity, so teacher scope (RBAC-03, SR-AZ-3) has no data source (fixed in v2) |
| D-03 | Medium | Zone → City hierarchy asserted without evidence |
| D-04 | Medium | Role-assignment scope missing from the diagram |
| D-05 | Medium | Enrolment/year redundancy has no consistency rule |
| D-06 | Medium | Missing entities (list above). The finance ones are deferred to Phase 5 design. |
| D-07 | Low | Finance and transport field provenance overstated as S (ER-37) |

---

## E. Functional completeness (19 module specs)

Legend: ● present and sufficient to build · ◐ present but partial · ○ missing · — not applicable. "APIs (orig.)" means the original's observed or static API references.

| Module | Screens | Roles | Permissions | Workflows | APIs (orig.) | Entities | Validation | Errors | ACs | Build-ready? |
|---|---|---|---|---|---|---|---|---|---|---|
| M01 Shell | ● | ◐ | ◐ (no nav→permission registry) | — | ● | ◐ | — | ◐ | ● | **Yes** |
| M02 Identity & users | ◐ (profile only) | ● | ◐ (catalogue not enumerated) | ○ (login, user creation, role assignment) | ◐ | ● | ◐ | ◐ | ● | Yes, with defaults |
| M03 Org structure | ○ (no screens, endpoint list only) | ◐ | ◐ | ● WF-01 | ◐ | ● | ◐ | ◐ | ● | Yes, with defaults |
| M04 Calendar | ● | ◐ (editor roles UNKNOWN) | ◐ | ● WF-02 | ● (reads) | ● | ◐ | ◐ | ◐ (exist, not cited) | **Yes** |
| M05 Timetable | ◐ (empty grid) | ◐ | ● | ● WF-03 | ● (reads) | ● | ● | ◐ | ● | **Yes** (split period semantics UNKNOWN) |
| M06 Diary | ● (editor not seen) | ● | ● | ● WF-04 | ● (reads) | ● | ● (P) | ◐ | ◐ (exist, not cited) | **Yes** |
| M07 Homework | ◐ (filters only; Upload and Worksheet tabs unknown) | ◐ | ◐ | ● WF-04 | ◐ | ◐ (no Homework/Worksheet entity) | ◐ (Q04) | ○ | ● | Yes, with defaults |
| M08 Attendance | ◐ | ● | ● | ● WF-05 | ◐ | ● | ● | ◐ | ● | Yes, with defaults (Q01–Q03) |
| M09 Lesson plans | ◐ | ◐ | ◐ | ○ | ◐ | ◐ | ○ | ○ | ○ | No (Could) |
| M10 Assessment & report cards | ◐ (marks grid not seen) | ● | ● | ● WF-06 | ● (reads) | ● | ● | ◐ | ● | With decisions (Q05, Q06) |
| M11 Online class & e-books | ● (list) | ◐ | ○ | ● WF-15 | ◐ | ● | ○ | ○ | ○ | No (Could) |
| M12 Communication | ● (lists; compose not seen) | ◐ (Q11) | ◐ | ● WF-11 | ● (reads) | ◐ (no Communication entity; audience undefined) | ◐ | ○ | ● | Yes, with defaults |
| M13 Tickets & feedback | ○ (denied) | ◐ | ◐ | ● WF-12 (P) | ○ | ◐ (no category/routing entity) | ○ | ○ | ● | With decision (Q09) |
| M14 Students lifecycle | ○ (menu and routes only) | ◐ | ◐ | ● WF-07/08 | ○ | ◐ (no Registration/Application/TC) | ◐ | ○ | ◐ (none for admissions) | Partial |
| M15 Finance | ○ (menu only) | ◐ | ◐ | ● WF-09/10 (rules P) | ○ (finance API not recovered) | ◐ | ◐ (P) | ◐ | ● | **No: blocked on Q07, Q08 and customer fee rules** |
| M16 Transport | ○ | ◐ | ◐ | ○ | ○ | ◐ (P) | ○ | ○ | ○ | No (Could) |
| M17 Dashboards | ○ (denied) | ◐ | ○ | — | ◐ (widget names) | — | — | ○ | ◐ (NFR-01 only) | Partial (metric definitions missing) |
| M18 HRMS self-service | ● (read) | ◐ | ◐ | ◐ WF-13/14 | ● (reads) | ◐ (no Leave/Absence entity) | ○ | ○ | ○ | Light HR only, needs a spec |
| M19 Other contexts | Summary by design | — | — | — | — | — | — | — | — | Out of scope |

| ID | Sev | Finding |
|---|---|---|
| E-01 | High | **Finance (M15) cannot be implemented faithfully from this package.** No screen, field, rule or API was observed or recovered. Everything after the menu is PROPOSED. Fee rules must come from the customer (Q07) before Phase 5. |
| E-02 | High | **Staff absence/leave is a hidden MVP dependency.** Substitution (TT-04, Must/Should) needs teacher absence, but light HR (FR-ATT-05) is Could, and M18 has no entity, AC or spec for it. Resolution: a minimal `StaffAbsence` entity in Phase 2.3, specified as PROPOSED. |
| E-03 | Medium | Error scenarios are the weakest criterion across all modules. Most specs list none, and they appear only inside ACs. |
| E-04 | Medium | Specs M04 and M06 do not cite their own ACs (CAL-01…04, DIA-01…03 exist). M09, M11, M16 and M18 have no ACs. Four ACs have no test case (AUTH-07, TEN-03, RBAC-06, NFR-01). |
| E-05 | Medium | Priority conflicts between documents. Tickets are Core (MVP) in `product_scope.md` but Should in `implementation_priorities.md`/`module_index.md`. Dashboards: Core vs Should. Lesson plans: Core+ vs Could vs Should. **Resolution:** `implementation_priorities.md` and the roadmap phases are authoritative. |

Itemised missing details and how to close each one: `DOCUMENTATION_GAPS.md`.

---

## F. Security requirements

`SECURITY_REQUIREMENTS.md` (new) turns the observations into testable replacement requirements. It covers: secrets on the server only; authentication and session; server-side authorization including object-level checks; tenant isolation; no hard-coded identifiers; audit logging; protection of financial data; protection of student data; third parties and impersonation.

Besides SEC-01…09 in `07_NON_FUNCTIONAL_REQUIREMENTS/security.md`, the audit found four further observations in the retained evidence. All are passive, from response shapes or bundle configuration; nothing was probed.

| ID | Observation | Class |
|---|---|---|
| SEC-10 | The online-class list response (teacher account) contains `zoom_start_url` (a host-privilege link), `recording_password`, `recording_play_url` and `class_link`. Whether students receive the same fields is UNKNOWN. | OBSERVED (shape) |
| SEC-11 | Self-service endpoints take the person's identifier as a query parameter (`erp_id`, `employee_id`: payslip, attendance, assets, reimbursements, delegation). Whether the server checks ownership could not be verified. | OBSERVED (pattern); risk INFERRED; enforcement UNKNOWN |
| SEC-12 | Shared services choose the tenant from a client-sent `X-DTS-BRAND` header. | STATIC |
| SEC-13 | Teacher-scoped responses return per-student names with ERP IDs (board-prep rollup, SEN timetable slots) and staff names with ERP IDs (`mandatory_cc.people`). | OBSERVED (shape) |

---

## G. Errata register (binding corrections)

These corrections **override** the cited text wherever the two conflict. The source files were deliberately left unedited, so the original statements stay available for comparison. `CLAUDE_CODE_MASTER_PROMPT.md` instructs the coding agent to apply this register.

| ID | File / location | Current | Correction | Basis |
|---|---|---|---|---|
| ER-01 | `EXECUTIVE_SUMMARY.md` "What was observed"; `INVESTIGATION_LOG.md` Stage 6; `02_FRONTEND_ANALYSIS/route_inventory.md` | "70 routes … 40 accessible, 31 denied" | 70 unique routes (71 navigations; `/erp/acms/calendar` visited twice): **39 accessible, 31 denied** | E016 `screens` |
| ER-02 | `04_BACKEND_ANALYSIS/api_inventory.md` intro | "visited 71 screens" | 71 navigations over 70 unique routes | E016 |
| ER-03 | `README.md`, `EXECUTIVE_SUMMARY.md`, `api_inventory.md` | "94 unique API endpoints" | 94 = 87 product-service endpoints (ERP 53, HRMS 30, FIN 2, MGMT 2) + 2 analytics + 4 Mattermost (3 API, 1 CSS) + 1 Clarity | E016 |
| ER-04 | `api_contracts.md` header | "Request bodies: none were observed" | 4 request shapes observed, all infrastructure. No domain write bodies. | E016 `req_shape` |
| ER-05 | `api_contracts.md` envelope table | A2 row cites `/test-module/my-results/`; the access-token variant is missing | Add `{status, message, data, force_update}` (refresh, 200). `/test-module/my-results/` uses `{status_code,status,message,result}`. | E016 |
| ER-06 | `backend_architecture.md` §3 "Pagination" | One DRF style | 5 response shapes and 4 parameter spellings (§C.1) | E016 |
| ER-07 | m02, m03, m05, m06, m07, m08, m10, m12; `wf04`, `wf05` | Methods on static endpoints implied or labelled STATIC (for example "POST `/academic/create-dairy/`") | Method is **INFERRED**. Only E016 endpoints have OBSERVED methods. | E006/E011 contain no methods |
| ER-08 | m01 "Shell bootstrap calls observed on every page" | every page | Notifications: 38 screens. Analytics: 39. FCM, event feedback, Clarity, finance delegation: 24 ERP screens. Banners and grievance summary: `/erp/user-profile` only. HRMS shell (`kp/access`, `training-reminders`): 15 HRMS screens. `current-user` is not in E016 (see ER-47). | E016 `screens` per endpoint |
| ER-09 | m01 top-bar table, avatar row | "Profile, change password, logout — O" | INFERRED (a navigate-only tour cannot open a menu). LOW confidence. | INVESTIGATION_LOG Stage 6 |
| ER-10 | m01 bell row | "Opens the notification list — O" | Count and list OBSERVED; opening behaviour INFERRED | E016 |
| ER-11 | m02 profile | "Update Address", "Employee Care card", "carousels" | Cannot be checked against retained evidence. `screen_inventory.md` records only Previous/Next, Change Password and Reset Email Password. LOW. | E016, screen_inventory |
| ER-12 | `performance.md` observations; m01 "(called up to 3×)" | Per-page call counts and duplicates | Cannot be checked: E016 keeps no per-page counts. LOW. | E016 structure |
| ER-13 | `product_scope.md` §2; `authentication.md` §3; `scalability.md` | "ERP user levels up to 113" | Source not retained. OBSERVED: 80 named levels, highest 96 (E017). | E017 |
| ER-14 | `COVERAGE_MATRIX.md` | ● for Timetable, Online class, Communication, dimensions 1, 13 and 16 | ◐ for each | §A A-07 |
| ER-15 | `SYSTEM_REQUIREMENTS.md` FR-SH-03 | O | O (selectors present) / I (re-scope on change) / P (remember selection) | No clicks performed |
| ER-16 | FR-SH-05 | O | O (count, list) / P (mark read) | No mark-read call observed |
| ER-17 | FR-IAM-01 | S | S (staff and student login endpoints) / I (guardian login) / P (OTP for ERP users; OTP endpoints exist only in the HRMS catalog) | E011/E006 |
| ER-18 | FR-ORG-01 | O | S (management) / O (read lists of zones, cities, branches) | Admin screens denied |
| ER-19 | FR-ORG-02, FR-ORG-03 | O | O (fields) / S (management) | — |
| ER-20 | FR-PPL-01 | O | O (own profile fields only) / S (directory) | — |
| ER-21 | FR-PPL-07 | O | O (UI notice text) / S (route names) / I (flow) | E002 |
| ER-22 | FR-CAL-02; `wf02` step 4 and state machine | O | O (`is_generated`, `is_published`, draft count; Reviews button) / I (transition order, review comments) / P (InReview state) | E016 |
| ER-23 | FR-CAL-03 | O | O (month view) / S (legacy weekly/daily endpoints) / P (week/day views) | — |
| ER-24 | FR-TT-02 | O | O (fields in read responses) / S, I (allocation and publish actions) | — |
| ER-25 | FR-TT-03 | O/S | O (teacher and section data via the Diary page; the Timetable screen showed an empty state) / S (workload) | screen_inventory row 49 |
| ER-26 | FR-DIA-01, FR-DIA-02 | O | O (read view, fields, buttons) / S (write endpoints) / I (editor UI, save) | — |
| ER-27 | FR-ATT-01 | O | O (filters, columns, required markers) / S (bulk endpoint names) / I (POST, upsert) | — |
| ER-28 | FR-ASM-04 | O | O (dashboard fields; Enter Marks / Template / Upload buttons) / S (upload endpoints) / I (grid, publish) | — |
| ER-29 | FR-COM-02 | O/S | O (FCM token registration only) / S (SMS, WhatsApp, email) | — |
| ER-30 | FR-EB-01 | O | O (dropdown) / UNKNOWN (listing endpoint returned 404) | E016 |
| ER-31 | `feature_catalog.md` F-002, F-010, F-012, F-020, F-021, F-023, F-006 | O or O/S | F-002 O/I; F-010 O (view)/I (publish, review, recurrence); F-012 O (data)/S/I (allocation, draft/publish, split); F-020 O (dashboard)/I (publish); F-021 S (pipeline)/O (signature list only); F-023 O (list)/S (create, recurring, attendance, recordings); F-006 O (change-password button)/UNVERIFIABLE (address) | — |
| ER-32 | `wf02` business rule BR-CAL-4 and state diagram | — | InReview state and review comments are PROPOSED | — |
| ER-33 | `wf05` step 2 | "multi-select allowed per placeholder (O)" | INFERRED from the placeholder text | — |
| ER-34 | `wf06` step 4 | "teacher PDF of the paper becomes available (O)" | O (`teacher_pdf_url` field) / I (meaning) | — |
| ER-35 | `workflow_index.md` WF-14 | Confidence HIGH | MEDIUM (single UI notice; approver sequence UNKNOWN) | — |
| ER-36 | `entity_dictionary.md` Zone/City; `domain_model.md` §2; `er_diagram.mmd` `ZONE ||--o{ CITY` | `City.zone_id` O; "Zone 1—* City 1—* Branch" | INFERRED / ASSUMPTION. Evidence shows Branch with independent zone and city references. | `/central-db/branch-view/`, `/acms/cities/` shapes |
| ER-37 | `entity_dictionary.md` Finance and Transport tables | Src S | I/P for fields not named by any route or endpoint (`gateway_merchant_id`, `allocations[]`, `is_refundable`, concession amount-or-%, approval thresholds, stop lat/lng and times, vehicle document expiries, driver licence) | E011/E012 |
| ER-38 | m11 and `wf15` online-class shape | Truncated "rec…" | Full field list includes `zoom_start_url`, `zoom_join_url`, `zoom_meeting_id`, `recording_status`, `recording_play_url`, `recording_password`, `recording_bucket_status`, `recording_bucket_url` | E016 |
| ER-39 | `authentication.md` §1 | "OTP endpoints exist" | In the HRMS catalog only. The ERP catalog has none. | E006/E011 |
| ER-40 | `recommended_architecture.md` §2 last paragraph | Python/Django stack acceptable | Withdrawn. TypeScript end-to-end is binding. | §B B-01 |
| ER-41 | `authentication.md` §4; `recommended_architecture.md` §4; `security.md` SR-AZ-2 | "school" as a separate level | Tenant (school group) → Branch (= school campus) → academic year → grade offering → class section. Zone and City are optional groupings. | §B B-03 |
| ER-42 | `entity_dictionary.md` header | `id (uuid or bigint)` | UUIDv7 | Blueprint §5 |
| ER-43 | `design_system.md` §6 | 360/768/1024/1440 | Test at 390/768/1440; 1024 allowed as a layout token | DoD, NFR-03 |
| ER-44 | `12_CLAUDE_CODE_HANDOFF/CLAUDE_CODE_MASTER_PROMPT.md` §0 | "Treat OBSERVED/STATIC as product requirements" | Superseded by the root `CLAUDE_CODE_MASTER_PROMPT.md`. Scope comes from the tier tables only. | §B B-04 |
| ER-45 | `README.md`, `EXECUTIVE_SUMMARY.md`, `backend_architecture.md` §5, `api_inventory.md` | "2,604 endpoints" | 2,604 catalog entries (9 are base-URL variables), about 2,430 distinct path templates, with no methods | §C.2 |
| ER-46 | `product_scope.md` §3 vs `implementation_priorities.md` vs `module_index.md` | Conflicting tiers for tickets, dashboards and lesson plans | `implementation_priorities.md` (MoSCoW) and `development_roadmap.md` phases are authoritative | §E E-05 |
| ER-47 | m18 intro | "Every HRMS page also calls `GET /qbox/user-mgmt/current-user/`" (presented as observed) | Not in E016. `current-user` exists only as the static catalog key `login.tokenLoginApi` (E006). OBSERVED HRMS shell calls: `payroll/kp/access/` and `lms/training-reminders/` (15 screens each). | E016, E006 |
| ER-48 | `08_TESTING/test_strategy.md` seed | "2 branches … grades 1–10 with sections A–B, 40 students per section … 60 guardians" | That is about 1,600 students with 60 guardians, which is inconsistent. Seed about 1 guardian per student, with about 15% siblings sharing guardians (PROPOSED). | Arithmetic |

---

## H. Change log (files touched by this audit)

| File | Change | Reason |
|---|---|---|
| `AUDIT_REPORT.md` | New | Task deliverable |
| `DOCUMENTATION_GAPS.md` | New | Task deliverable |
| `HANDOFF_READINESS.md` | New | Task deliverable |
| `CLAUDE_CODE_MASTER_PROMPT.md` (root) | New | Task deliverable. Supersedes the prompt in `12_CLAUDE_CODE_HANDOFF/`. |
| `SECURITY_REQUIREMENTS.md` | New | Task F |
| `05_DATA_MODEL/er_diagram_core_v2.mmd` | New | Task D: corrected core diagram with keys, scoping and labelled assumptions. The original diagram is not modified. |
| `README.md` | "Start here" list now points to the root deliverables; one line notes the audit and errata. No other text changed. | Stops a fresh session from following the superseded prompt |
| `12_CLAUDE_CODE_HANDOFF/CLAUDE_CODE_MASTER_PROMPT.md` | A 2-line "superseded" banner added at the top; the body is unchanged | Same reason |

No other file was modified or deleted by the audit.

## H.2 Phase 3 change log (2026-11-23, deep analysis follow-up)

The audit's 48 errata were **applied in place** to the canonical documents in a later Phase 3 pass (the audit itself had left them as a register only; gap G-13).

| Action | Detail |
|---|---|
| Errata ER-01…ER-48 applied in place | 97 exact-match edits across 35 files, each asserted to match once; machine change log at `scratchpad/out/errata_changelog.json`. The errata register in §G and this change log are preserved. |
| `13_AUTONOMOUS_DEEP_ANALYSIS/` added | 15 new files (deep static analysis + crawler + reports). Additive; no canonical spec rewritten. |
| Pointers appended | `04_BACKEND_ANALYSIS/api_inventory.md`, `02_FRONTEND_ANALYSIS/route_inventory.md`, `DOCUMENTATION_GAPS.md` (G-19 partly closed), `CLAUDE_CODE_MASTER_PROMPT.md` and `README.md` folder maps. |
| Privacy cleanup | Two residual PII tool-result files from the Phase 1/2 session directory were verified and deleted; a working catalog file had 4 secret *values* redacted (key names kept). No secret, PII, or browser identifier appears in any deliverable. |
| Read-only route crawl **executed** (2026-11-23) | 408 of 426 safe routes navigated as a teacher (stopped cleanly on the server's 429; 18 unreached). Runtime-observed endpoints 94 → **171**; 72 new value-free response schemas (E019); first runtime evidence of ERP role-gating (42 rendered / 359 role-redirected). Static-vs-runtime method check: 104/107 direct, **0 contradictions**. Reports updated: `RUNTIME_VERIFICATION_REPORT.md`, `STATIC_VS_RUNTIME_COMPARISON.md`, `COVERAGE_REPORT.md`, `REMAINING_UNKNOWNS.md`, `RESPONSE_SCHEMA_CATALOG.md`, `ROLE_ROUTE_API_MATRIX.md` §6; new `ROUTE_CRAWL_RESULTS.csv`. Full privacy sweep of all crawl outputs: clean. |
