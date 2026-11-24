# Route Discovery Report

Routes were recovered statically from the three apps' router configurations (AST, not regex), deduplicated, linked to their lazy chunks and APIs, and classified for safe automated navigation.

## 1. Routes discovered
| App | Distinct routes | Source |
|---|---|---|
| ERP (`/erp`) | 687 | `{path, Component: lazy(() => import(chunk))}` objects + `jsx(Route,{path,element})` |
| HRMS (`/recruitment`) | 25 | route objects (also carry `permission` arrays) |
| CRM (`/crm`) | 17 | route objects |
| **Total** | **729** | |

(Phase 1/2 reported 688 ERP routes; the small delta is nested/children routes this AST pass resolves. 729 unique `(app, path)` pairs after dedupe.)

- **692** routes resolved to at least one concrete lazy chunk file.
- **511** routes call at least one API directly in their own chunk; the rest draw their APIs from imported shared service modules (named per route in `ROUTE_COMPONENT_MATRIX.csv`).

## 2. Safety classification (for automated crawling)
Every route was classified before any navigation. The crawler visits only `SAFE_READONLY` routes and never clicks, types or submits.

| Class | Count | Rule |
|---|---|---|
| SAFE_READONLY | 442 | No ID parameter, no state-changing verb, no sensitive-data keyword |
| PARAM_SKIP | 149 | Needs a record ID (`:id`, `{id}`) — would require guessing a real student/staff/txn ID |
| STATE_CHANGING_SKIP | 98 | Path contains a mutating verb (create/edit/add/delete/save/submit/publish/approve/pay/refund/upload/promote/assign/…) |
| SENSITIVE_SKIP | 40 | Path implies personal data (payslip, salary, bank, aadhaar, pan, profile, it-declaration, employee/student detail, medical) |

## 3. Crawl queue
- After deduplicating by page component, **426 unique-screen SAFE_READONLY routes** form the queue (`scratchpad/crawl/queue.json`).
- **390 of these were never visited in the Phase 1/2 tour (E016)** — they are genuinely new coverage.
- Queue composition: 418 ERP, 4 HRMS, 4 CRM (most HRMS/CRM safe reads are self-service pages already covered by E016, or sensitive, or need IDs).
- Order: new ERP screens first, then previously-seen screens (re-verify), then HRMS/CRM. A 5-route pilot of known-good teacher screens (`/diary`, `/timetable`, `/announcement`, `/online-class`, `/lesson-plan`) gates the rest.

## 4. Crawler safety contract (enforced in code)
The crawler (`scratchpad/tools/crawler.mjs`) uses **only** `Page.navigate` plus passive `Network`/`Page`/`Accessibility` reads. It will **never**:
- click, type, submit, or dispatch any event;
- call `Runtime.evaluate` or execute page JavaScript;
- replay, repeat or craft any API request, or enumerate IDs;
- persist any header, cookie, token, storage or body value.

It records value-free metadata only (method, sanitized path with IDs masked, query-param *names*, status, MIME, bytes, initiator chunk, and value-free JSON response *shapes*), stops on a 429 / repeated 5xx / login-redirect / repeated 401, and closes only the tab it created. UI labels are kept only when the exact text already exists as a string literal in the shipped code (so no personal data is transcribed from a page).

## 5. Execution status
See `RUNTIME_VERIFICATION_REPORT.md`. The crawl connects to the user's existing authenticated Chrome via the loopback CDP endpoint; execution depends on the one-time Chrome debugging authorization described there.
"""