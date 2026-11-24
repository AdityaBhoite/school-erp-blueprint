# Backend Architecture (reconstructed)

> **Scope: the ORIGINAL system** (observed, static or inferred). The replacement's PROPOSED architecture is in `11_IMPLEMENTATION_PLAN/recommended_architecture.md` and `12_CLAUDE_CODE_HANDOFF/ARCHITECTURE_BLUEPRINT.md`; the side-by-side comparison is in `AUDIT_REPORT.md` §B.3. Only §6 below is a proposal.

Evidence: E008 (HRMS runtime config), E011 (ERP `endpoints` catalog and `config.apiGateway`), E006 (HRMS endpoint catalog), E016 (runtime API tour). We have **no direct access to the backend**. Everything here is derived from what the frontend calls and from observed responses. Classifications are given inline.

## 1. Service map

The frontend talks to several HTTP services. All application APIs follow the same conventions: a `/qbox` prefix, REST-style paths with trailing slashes, and JSON.

| Logical service | Base URL (production, Orchids brand) | Used by | Evidence | Class |
|---|---|---|---|---|
| **Core ERP API** (academics, users, attendance, assessment, communication, notifications) | `https://<brand-host>/qbox` (for Orchids `orchids.letseduvate.com/qbox`) | ERP, shell | config `VITE_API_BASE_URL*`, runtime calls | OBSERVED, HIGH |
| **Finance API** (fees, wallets, expenses, delegation) | `https://orchids.finance.letseduvate.com/qbox` (paths `/apiV1/...`) | ERP finance and shell | config `baseFinanceURL`; runtime `GET /qbox/apiV1/expense-delegation-context/` | OBSERVED, HIGH |
| **HRMS / Recruitment API** (people, hiring, payroll, leave, grievances) | `https://recruitment.letseduvate.com/qbox` | HRMS app, also ERP (designations, departments, user activity) | config `baseRecruitment`; runtime `/qbox/grievance-module/employee-ticket-summary/` | OBSERVED, HIGH |
| **CRM / Marketing API** (leads, admissions funnel) | `https://marketingv2.letseduvate.com/qbox` | CRM app, shell search | config `crm` | STATIC, HIGH |
| **Central management API** (cross-school masters, lesson-plan content, central admin, user levels) | `https://mgmt.letseduvate.com/qbox` | ERP | config `baseURLCentral` | STATIC, HIGH |
| Reports service | `https://reports.letseduvate.com` (`/api/acad_performance/v2/...`) | ERP analytics | config `msReportsUrl` | STATIC, MEDIUM |
| Online classes service | `https://api-classes.letseduvate.com` | ERP online class | config `msOriginUrl` | STATIC, MEDIUM |
| Events service | `https://events.letseduvate.com` | ERP events | config `baseEvent` | STATIC, MEDIUM |
| Activities / blog | `https://activities.letseduvate.com` | ERP school feed and blog | config `newBlogURL` | STATIC, MEDIUM |
| Quiz service | `https://mpquiz.letseduvate.com` | ERP assessments | config `baseURLMPQ` | STATIC, LOW |
| Chat | `https://chat.letseduvate.com` (Mattermost reference) | ERP chat | config `chat` | STATIC, MEDIUM |
| Analytics (Module Adoption Tracker) | `https://analytics.letseduvate.com/qbox/api/v1` | all apps | runtime `POST /track/session`, `/track/batch` | OBSERVED, HIGH |
| Notifications (document store) | `<brand-host>/qbox/mongoapp/notifications/...` | shell | runtime | OBSERVED, HIGH |
| WhatsApp bot | `https://whatsappbot.letseduvate.com/whatsapp` | HRMS | config | STATIC, MEDIUM |
| Serverless functions | `https://asia-south1-prj-prod-service-k12.cloudfunctions.net` | ERP | config `gcloud` | STATIC, HIGH |
| Page time tracker | `https://pagetimetracker.letseduvate.com/api/tracker/` | ERP | config | STATIC, MEDIUM |
| Document server | OnlyOffice at `office.letseduvate.com` with callback `…/qbox/acms/onlyoffice/callback/` | ERP (ACMS lesson plans) | config | STATIC, HIGH |
| Object storage / CDN | GCS buckets (`storage.googleapis.com/erp-revamp`, `…/letseduvate/prod/media`, `…/revamp-recruitment/media`), CDNs `acad-cdn.letseduvate.com`, `mgmt-cdn.letseduvate.com`, a CloudFront distribution for e-books | all | config | STATIC, HIGH |

**Hosting inference:** Google Cloud Platform (Cloud Functions region `asia-south1` (Mumbai), project naming `prj-prod-service-k12`, GCS buckets). INFERRED, HIGH.

## 2. Multi-tenancy model ("brands")

- The platform (Eduvate, internally "K12") serves several school **brands** from one codebase: `orchids`, `orchidsworld`, `taproot`, `tilia`, `mcollege` (codes `OWS`, `MC`, `TIL`). STATIC, HIGH.
- Each brand has its **own host and API base** (`<brand>.letseduvate.com/qbox`). The frontend picks the base from `window.location.host`. STATIC, HIGH.
- Brand metadata held client-side: `{code, name, short_name, logo, branding_photo, url, primary_color, terms_conditions, privacy_policy}` (Orchids primary colour `#BB2139`). STATIC, HIGH.
- Calls to shared services (finance, analytics) carry an **`X-DTS-BRAND: <brand code>`** header to select the tenant. STATIC, HIGH.
- `POST /qbox/erp_user/brand/switch/` lets a user who belongs to more than one brand switch context. STATIC, HIGH.
- **Within a brand**, data is partitioned by **Branch** (campus) and **Academic session year**. Every academic API takes `branch_id`/`session_year`/`acad_session` parameters (OBSERVED in query keys). Zones and cities group branches. OBSERVED/STATIC, HIGH.
- Database-per-brand versus a shared database with tenant column: **UNKNOWN**. Separate API hosts per brand suggest separate deployments or databases (INFERRED, LOW).

## 3. API conventions (OBSERVED unless noted)

| Convention | Detail |
|---|---|
| Path style | `/qbox/<django-app>/<resource>/` with a trailing slash; snake_case and kebab-case mixed (`/erp_user/user-mgmt/user-info/`, `/academic/lp-grade-subject-list/`) |
| Versioning | ad hoc: `/v1/`, `/v2/`, `-v2`, `apiV1` segments (for example `/erp_user/v2/sectionmapping/`) |
| Response envelope (core ERP) | `{"status_code": int, "message": string, "result": <payload>}` |
| Envelope variants | Some endpoints return `{"message", "result", "status_code"}` in a different order (same shape); finance returns bare objects (`{"is_delegate": bool, "delegators": []}`); HRMS returns bare objects |
| Pagination | **Five response shapes across 20 paginated endpoints** (audit ER-06): DRF `count/next/previous/results` (4); the same plus `limit/current_page/total_pages` (10); `total/page/page_size/data` (3); `total/page/page_size/results` (1, notifications); `count/page/page_size/results` (2). Parameter spellings: `page`+`page_size`, `page_num` (diary), `page_number` (e-book), `paginate_by` (marks dashboard). DRF PageNumberPagination is INFERRED for the first two shapes. |
| Filtering | query params such as `branch_id`, `session_year`, `acad_session`, `grade_id`, `section_id`, `subject_id`, `start_date`, `end_date` |
| Lookups | DRF/Django ORM double-underscore field names leak into responses: `grade__grade_name`, `session_year__is_current_session`, `erp_user__is_recruitment_enabled` → Django REST Framework (INFERRED, HIGH) |
| Errors | HTTP 403 observed during token bootstrap (requests sent before the token was ready were retried after refresh and returned 200). Error body schema: UNKNOWN |
| Soft delete | `delete_*` / `restore_*` endpoint pairs for branches, grades, sections, subjects, mappings, roles, modules (STATIC) → `is_delete` flags rather than hard delete (INFERRED, HIGH) |

## 4. Backend framework inference
- **Django + Django REST Framework** (INFERRED, HIGH): trailing slashes, `__` lookup names, `access`/`refresh` JWT pair (SimpleJWT conventions), DRF pagination shape, app-namespaced URL prefixes (`erp_user`, `academic`, `assessment`, `period`, `timetable`, `communication`).
- **MongoDB** for notifications/activity (`/mongoapp/` prefix). INFERRED, MEDIUM.
- Separate Django projects per service (ERP, finance, recruitment, marketing, mgmt) sharing user identity by `erp_id` (format `<11 digits>_<BRANDCODE>`, e.g. `…_OIS`). INFERRED, MEDIUM.

## 5. Endpoint volume
| Source | Count | File |
|---|---|---|
| ERP `endpoints` catalog (static) | 1,174 entries (about 1,066 distinct paths), 124 groups; no HTTP methods recorded | `10_REA_EVIDENCE/bundle_analysis/E011_erp_endpoint_catalog.md` |
| HRMS catalog (static) | 1,430 entries (9 are base-URL variables; about 1,386 distinct paths), 82 groups; no HTTP methods recorded | `10_REA_EVIDENCE/bundle_analysis/E006_hrms_endpoint_catalog.md` |
| Inline (non-catalog) ERP paths | several hundred more in lazy chunks (not all captured) | — |
| Runtime-observed (tour) | see `api_inventory.md` | `10_REA_EVIDENCE/sanitized_metadata/` |

## 6. Recommendation (PROPOSED)
Do not replicate the multi-service sprawl. Build a **modular monolith** with clear module boundaries (identity, org structure, academics, assessment, attendance, communication, finance, HR) and one database. Tenant (brand) → school → branch → academic year scoping should be enforced in a central data-access layer. Details: `11_IMPLEMENTATION_PLAN/recommended_architecture.md`.
