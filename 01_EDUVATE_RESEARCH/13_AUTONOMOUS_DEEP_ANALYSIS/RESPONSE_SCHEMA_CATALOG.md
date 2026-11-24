# Response & Request Schema Catalog

Three independent schema sources, kept separate:

1. **Runtime value-free shapes (E016)** — 88 JSON response shapes captured on the wire for the teacher account in Phase 1/2. These live in `04_BACKEND_ANALYSIS/api_contracts.md` and `10_REA_EVIDENCE/sanitized_metadata/E016_runtime_api_shapes.json`. Unchanged.
2. **Runtime value-free shapes (E019, Phase 3 crawl)** — **72 new** JSON response shapes captured on the wire during the read-only route crawl (2026-11-23), with field names **and inferred types/formats** (`string`, `number`, `string(date)`, `string(email)` as a format descriptor only, etc.). In `10_REA_EVIDENCE/sanitized_metadata/E019_phase3_crawl_api_shapes.json`. See §5.
3. **Static field inventories (AST pass)** — for 2877 endpoints the code reads specific fields off the response (`.then(r => r.data.results)`), and for 1720 endpoints the request body object's keys are visible at the call site. These are **field *names*, inferred from how the client consumes/builds the payload** — not full shapes, and never values.

## 1. What static field recovery can and cannot say
- It lists field names the frontend **reads** (response) or **sends** (request body/query) at a resolved call site.
- It does **not** give types, nesting beyond what the code dereferences, required/optional status, or enum values. Those stay UNKNOWN unless E016 observed them.
- Absence of a field here means "not dereferenced at a resolved call site", not "not returned".

## 2. Coverage
| Field source | Endpoints |
|---|---|
| Runtime response shape (E016, Phase 1/2) | 88 |
| Runtime response shape (E019, Phase 3 crawl) | 72 new (51 with a structured object shape) |
| Static response-field names (AST pass) | 2877 |
| Static request-body key names (AST pass) | 1720 |
| Static query-param key names (AST pass) | 1587 |
| Endpoints with **some** schema evidence (any of the above) | ~3840 |

## 3. Examples (static field names; value-free)
Format: `METHOD service path` — request body keys → response field names.

### Attendance / academic
- `GET|POST ERP /academic/branch-wise-lesson-plan` — body: acad_session|branch|e|eduvate_sy|module → resp: data|data.acms_versions|data.message|data.result|data.status_code
- `GET ERP /academic/ebook-dropdown` → resp: data|data.result|data.status_code
- `GET ERP /academic/ebook_user` → resp: data|data.page_number|data.time_spent
- `POST ERP /academic/event-change-approval` — body: action|event_id → resp: data
- `GET ERP /academic/event-dash` → resp: data
- `GET ERP /academic/event-download` → resp: data|status|statusText
- `GET|POST ERP /academic/event-feedback` — body: answers|event_id → resp: data
- `GET ERP /academic/event-feedback-status` → resp: data

### Finance (fees / concessions / wallet) — recovered this pass from ERP-side call sites
- `GET FIN /apiV1/activity-category` → resp: data
- `POST FIN /apiV1/bus-cleanliness/remind-bulk` → resp: data|headers|response
- `GET FIN /apiV1/bus-cleanliness/report/download` → resp: data|headers|headers.content-type|response|response.data|response.data.text
- `POST FIN /apiV1/checklist-config/questions/bulk-upload` — body: file|usage → resp: data
- `GET FIN /apiV1/checklist-config/questions/template` → resp: data
- `GET FIN /apiV1/checklist-config/questions/uploads` → resp: data
- `POST FIN /apiV1/checklist-file-upload` — body: file → resp: data|data.message|data.name|data.path
- `GET FIN /apiV1/checklist-user` → resp: data
- `GET FIN /apiV1/crm-child-admission-plan` → resp: data
- `GET FIN /apiV1/crm-child-fee-details` → resp: data

### Timetable / assessment
- `GET ERP /timetable/branch-grades` → resp: data|data.result
- `GET ERP /timetable/branch-staff-timetable` → resp: data|data.result
- `GET|POST ERP /timetable/draft-teachers-data` → resp: data|data.result|data.status_code|response
- `PUT ERP /timetable/draft-teachers-data/{id}` → resp: data|data.result|data.status_code|id
- `GET ERP /assessment/acms-assessments-for-config` → resp: data
- `GET|POST|PUT ERP /assessment/assessment-document-upload` → resp: data
- `GET ERP /assessment/assessment-list` → resp: data
- `GET ERP /assessment/branch-details/v2` → resp: data|data.result

## 4. Reconciliation with runtime
Where a static call site and an E016 runtime shape describe the same endpoint, the static field names are a subset of the runtime shape's keys (the frontend reads fewer fields than the server returns). No contradictions were found. Full machine data: the `response_fields`, `body_keys` and `query_keys` columns of `API_ENDPOINT_CATALOG.csv`.

## 5. Phase 3 runtime shapes (E019) — selected, value-free
Most ERP endpoints return the standard envelope `{status_code, message, result}` (DRF-style), confirming the inferred backend convention at runtime. Richer shapes were captured for dashboards and lists. Types shown are inferred from the observed value's form; `string(email)`/`string(date)` are **format descriptors**, never the value.

### Student information / academics / timetable (teacher-reachable)
- `GET ERP /erp_user/user-mgmt/student-information` → `status_code:number, message:string, result:object` (envelope)
- `GET ERP /erp_user/user-mgmt/student-subjects` → `{status_code, message, result}`
- `GET ERP /timetable/student-timetable` → `{status_code, message, result}`
- `GET ERP /timetable/unfilled-section-slots` → `{status_code, message, result}`
- `GET ERP /academic/lp-view` → `{message, result, status_code}`
- `GET ERP /academic/{id}` → `{status_code, message, result}`

### Assessment (ACMS) dashboards — richer shapes
- `GET ERP /acms/assessments/dashboard_home` → `upcoming_exams, upload_deadlines, top_bottom_branches, year_timeline, computed_at`
- `GET ERP /acms/assessments/{id}` → `tests, total, offset, limit, facets, computed_at` (paginated list with facets)
- `GET ERP /api/user-progress/admin/dashboard` → `total_students, students`

### Chat (Mattermost) — runtime-confirmed third-party surface
- `GET CHAT /api/v4/channels/{id}/stats` → `channel_id, member_count, guest_count, pinnedpost_count, files_count`
- `GET CHAT /api/v4/teams` → list; `GET /api/v4/users/me` → Mattermost user object (field names only; `email` captured as `string(email)` descriptor, no value)

Full set (field names + inferred types) in `E019_phase3_crawl_api_shapes.json`. Pagination convention observed at runtime: `{total, offset, limit}` / `{count, next, previous, results}` — consistent with the static finding.
