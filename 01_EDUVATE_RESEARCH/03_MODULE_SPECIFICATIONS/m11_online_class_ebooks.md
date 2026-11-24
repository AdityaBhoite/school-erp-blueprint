# M11 — Online Classes, E-books, Classroom Clickers

## Online class (`/erp/online-class`, OBSERVED). Screenshot `online-class-list.png` (host names redacted).
- Tabs: **Regular Online Classes** | **OCAP Zoom Classes** (OCAP = online-class programme for virtual students, S).
- Filters: Select Grade, Section (optional), Subject (optional), date range; buttons **Clear All**, **Get Classes**, **Create Class**, **Download** (disabled until data).
- Sub-tabs: **Today / Upcoming / Completed / Cancelled**.
- Table: Start Time, Title, Grade & Section, Subject, Host, Class Date, Status (live countdown timer), Action (**Host** button enabled near the start time).
- API: `GET /qbox/erp_user/students-online-class/?start_date&end_date&status&page&page_size` (shape in wf15).

## Online books (`/erp/online-books`, OBSERVED)
- `GET mgmt /lesson_plan/list-volume/` (volumes), `GET /qbox/academic/ebook-dropdown/?acad_session&book_type` → grades + `is_acms`; `GET /qbox/academic/v1/ebook_school_wise_filter/?…` returned **404** (OBSERVED) — the e-book listing endpoint was unavailable for this account/config.
- E-books are hosted on a CDN (CloudFront `IBOOK_BUCKET`), with an iBook viewer (S `ibook`, `newEbook`, `newibook` groups).

## School clicker (`/erp/school-clicker`, OBSERVED)
- `GET /qbox/school-clicker/config/check/?acad_session` → `{enabled, branch_id}`; `GET /qbox/school-clicker/clickers/?acad_session&page&page_size` (DRF pagination); `GET /qbox/acms/curriculum/volumes/` → `[{id, name, is_current}]`.
- Classroom response devices ("clickers") linked to content; launch tokens (`/clicker/generate-launch-token/`), dispatch dashboard (finance), disparity analysis (ACMS).

Replacement priority: Could.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/online-class` and `/erp/language-app-dashboard` **rendered** for a teacher. Route-specific endpoints observed:
- `GET ERP /erp_user/students-online-class` — the teacher's online-class student list
- `GET ERP /erp_user/user-information` — presenter/user context
- `GET ERP /academic/{id}` — class/academic detail (envelope)

Confirms online-class is roster-driven off `erp_user`. Join/launch actions need an interaction and were not triggered. Status: route RUNTIME_OBSERVED.
