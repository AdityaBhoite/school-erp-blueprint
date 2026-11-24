# Investigation Log — Orchids Eduvate ERP

Investigator: Claude Code (Opus 5.5) using REA 6.9.0 MCP tools.
Target: `https://orchids.letseduvate.com` (authenticated Chrome session, operated by the user).
Connection: Chrome DevTools Protocol, browser WebSocket endpoint on loopback (identifier withheld).

All times UTC. Evidence IDs (E###) are defined in `EVIDENCE_INDEX.md`.

---

## Session 1 — 2026-11-22/23

### Stage 0: Connection
- `list_browser_targets` restricted to the exact origin `https://orchids.letseduvate.com`.
  - 1 eligible page target, title **"HRMS"**, path `/recruitment/` (E001).
  - 23 targets from other origins, 4 unsupported URLs and 65 non-page targets were excluded by REA's filter and **not inspected**.
- Browser: Chrome 157.

### Stage 1: Initial page inspection (E002)
- `inspect_web_page` (3 s observation window, with accessibility text).
- The landing page is the **logged-in employee's own HRMS Profile page** (`/recruitment/` → Profile).
- The page shows real personal data (DOB, address, phone, family, masked bank and Aadhaar details). **Raw capture kept outside the deliverable** (session scratch only). Only field labels/structure are documented.
- No network requests occurred during the 3 s window (`prior_activity_available: false`). This is a limitation of passive observation, not evidence that there are no APIs.
- 84 scripts, all same-origin under `/recruitment/assets/*` → Vite-built, hashed chunks, ES modules.
- Accessibility tree: 2,502 nodes. Persistent left rail with 8 sections, top bar, profile content, and an embedded "Recruiter Performance" widget.

### Stage 2: Screenshot (E003)
- `capture_web_screenshot` 1920×928. Contains personal data, so it is **not copied to 09_VISUAL_EVIDENCE**. Design-token observations were recorded in `02_FRONTEND_ANALYSIS/design_system.md`.

### Stage 3: Bundle analysis
- `analyze_web_bundle` (summary) → E004a. The 2.86 MB main chunk hit REA's 30 s per-script deadline; endpoint collection was empty. Recorded as a limitation.
- Re-inspected with `include_script_sources: true` (E004). The result exceeded the 10 MiB MCP frame, so it was exported with `export_evidence_bundle` and the scripts with `export_web_scripts` (E004x). 84 scripts, 5.0 MB, exported to a local scratch directory (not included in the deliverable: proprietary source).
- Local static reading of the exported bundle (no execution) recovered:
  - **E005** Unified platform navigation config: 19 top-level categories and 20 mega-menu sections with 772 items spanning three applications (`/erp`, `/crm`, `/recruitment`).
  - **E006** HRMS endpoint catalog: 1,430 named endpoint templates in about 85 groups.
  - **E007** HRMS route table: 82 top-level route entries with `is_view/is_add/is_modify` user-level arrays.
  - **E008** Platform/runtime config: multi-brand hosts, `/qbox` API base path, `X-DTS-BRAND` header, Bearer JWT with refresh, impersonation header, analytics SDK.
  - **E009** Client-side access-control model: `k12_access_matrix` cached for 1 h, user levels per app, ERP user_level 13 = student.

### Key early conclusions
1. "Orchids ERP" is one deployment ("brand") of the **Eduvate K12 platform**. The same code serves several school brands (orchids, orchidsworld, taproot, tilia, mcollege), each with its own host and `/qbox` API.
2. The product is **three cooperating SPAs on one origin**: ERP (`/erp`), CRM/Marketing (`/crm`) and HRMS/Recruitment/Payroll (`/recruitment`). They share a unified shell (left rail, mega-menu, global search, AI help assistant, notifications).
3. The backend is a REST API under `/qbox/...` with trailing-slash paths. Django REST Framework style is INFERRED from the conventions (trailing slashes, `access`/`refresh` JWT pair, `__` lookup fields such as `session_year__is_current_session`).

### Next
- The user navigates to the ERP app (`/erp/dashboard`) so the ERP bundle (academics, students, attendance, fees) can be captured.

### Stage 4: ERP app capture (E010, E010x)
- The user navigated to `/erp/` (it landed on `/erp/user-profile`). A long passive inspect across that navigation failed with `target_changed`: REA cannot span a full document change, so later navigation windows were kept within one document.
- `inspect_web_page` with script sources → 16 ERP scripts (main chunk 2.17 MB with readable identifiers). Exported with `export_web_scripts`.
- Static recovery: ERP `config.apiGateway` (service map; secret-bearing keys recorded by name only), `endpoints` catalog (1,174 entries → **E011**), route table (688 routes → **E012**), auth/session fields, redux-saga.

### Stage 5: Menu capture attempts (E013–E015)
- Clicking "Academics" navigates to the role's default `/erp/acms/calendar` (screenshot S002 kept, redacted).
- The hover mega-menu and expanded sidebar did not persist into passive snapshots. Item names come from the static nav config (E005).

### Stage 6: Autonomy request and tooling change
- The user asked to proceed without manual navigation.
- `capture_browser_scenario` connect mode rejected the browser WebSocket endpoint ("CDP endpoint must be an explicit-port HTTP URL"), confirming the brief's warning. The Claude-in-Chrome extension was not connected.
- REA's installed code was checked: connect mode only seeds provided storage (no clearing). It was not usable with this endpoint anyway.
- A small **read-only CDP navigator** (`Page.navigate` only; no clicks, typing or form submission) was written and run against the same tab after the user approved Chrome's debugging prompt. Response bodies were converted to value-free type shapes in memory; header values were never read.
- **E016**: 71 navigations over 70 unique routes (53 ERP + 17 HRMS; `/erp/acms/calendar` was visited twice, once as a test run and once to return the tab). 39 unique routes accessible, 31 redirected to profile (no permission), 94 unique API endpoints with shapes. Sanitized output: `10_REA_EVIDENCE/sanitized_metadata/E016_runtime_api_shapes.json`. Ten screenshots were redacted (PowerShell/System.Drawing) and saved.
- **E017**: targeted capture of non-personal configuration values from `/academic-communication/meta/` (80 ERP user-level names, communication roles/enums) and this account's own scope (`limited`, level 11 = Teacher). The first attempt failed (WebSocket handshake rejected; the approval had attached to an earlier killed attempt). The retry succeeded after approval.

### Stage 7: Documentation
- All folders populated: 19 module specs, 15 workflows, data model, API inventory/contracts, NFRs, testing, implementation plan, handoff.
- Privacy scan of the deliverable folder for names, emails, phone numbers, employee IDs, the endpoint identifier and secrets (see final check in the evidence index).

## Limitations (carried into COVERAGE_MATRIX)
- Single role (Teacher). No parent/student/admin/accountant accounts.
- No write operations exercised; request bodies unobserved.
- Lazy route chunks of unvisited screens were not captured, so the finance API catalog (finance service) is largely unknown.
- Passive windows only see traffic during attachment; the tour adds load-time traffic for visited routes only.
