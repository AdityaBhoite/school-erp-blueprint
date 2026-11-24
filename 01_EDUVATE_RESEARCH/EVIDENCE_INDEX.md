# Evidence Index

Classification legend: OBSERVED (directly witnessed at runtime), STATIC (found in shipped frontend code), INFERRED, PROPOSED, UNKNOWN.

REA Evidence IDs are the content-addressed IDs REA returned. Raw captures containing personal data or proprietary source are **not** stored in this folder (see "Retention" column).

| ID | Date (UTC) | REA operation | REA evidence_id (prefix) | Scope / route | Class | What it shows | Retention |
|---|---|---|---|---|---|---|---|
| E001 | 2026-11-22 20:55 | list_browser_targets | ev_eeb6798f… | origin filter = orchids.letseduvate.com | OBSERVED | 1 page target "HRMS" at `/recruitment/` | summary in log |
| E002 | 2026-11-22 20:55 | inspect_web_page (a11y text, 3 s) | ev_4bacd0b2… | `/recruitment/` (Profile) | OBSERVED | DOM 2,695 nodes, a11y 2,502 nodes, 84 scripts, left rail and top bar, profile field schema | raw: scratch only (PII); structure in docs |
| E003 | 2026-11-22 20:56 | capture_web_screenshot | ev_0c0e12a1… | `/recruitment/` (Profile) | OBSERVED | Visual design: crimson primary colour, icon rail, cards | not retained (PII); see design_system.md |
| E004a | 2026-11-22 20:57 | analyze_web_bundle (summary) | ev_bcba8c3f… | `/recruitment/` | STATIC | 83 scripts parsed; main chunk timed out at 30 s; 0 endpoint rows | limitation recorded |
| E004 | 2026-11-22 20:58 | inspect_web_page (script sources) | ev_15960ddf… | `/recruitment/` | OBSERVED | 84 script sources (5.0 MB) | exported to scratch only (proprietary) |
| E004x | 2026-11-22 20:59 | export_web_scripts | ev_f6b1d27b… | — | STATIC | Digest-verified script export manifest | scratch only |
| E005 | 2026-11-22 | local static read of E004x | — | unified shell nav config | STATIC | 19 categories, 20 mega-menu sections, 772 nav items across /erp, /crm, /recruitment | `10_REA_EVIDENCE/bundle_analysis/E005_unified_navigation_megamenu.md` |
| E006 | 2026-11-22 | local static read of E004x | — | HRMS endpoint catalog | STATIC | 1,430 endpoint templates in about 85 groups | `10_REA_EVIDENCE/bundle_analysis/E006_hrms_endpoint_catalog.md` |
| E007 | 2026-11-22 | local static read of E004x | — | HRMS route table | STATIC | 82 route entries with is_view/is_add/is_modify user-level arrays | `10_REA_EVIDENCE/bundle_analysis/E007_hrms_route_permissions.md` (employee IDs redacted) |
| E008 | 2026-11-22 | local static read of E004x | — | runtime config | STATIC | Brand hosts, `/qbox` API base, `X-DTS-BRAND`, `X-Is-Switch`, JWT access/refresh, analytics SDK | documented in 04_BACKEND_ANALYSIS |
| E009 | 2026-11-22 | local static read of E004x | — | access control | STATIC | `k12_access_matrix` (1 h TTL), per-app user levels, erpIds overrides, student = ERP level 13 | documented in role_permission_matrix.md |
| E010 | 2026-11-22 21:07 | inspect_web_page (a11y + script sources + JSON shapes) | ev_f33366ee… | `/erp/user-profile` | OBSERVED | ERP app: 16 scripts, vendor chunks (antd, redux, react, date, utils) | raw: scratch only |
| E010x | 2026-11-22 21:08 | export_web_scripts | ev_e7637ca2… | — | STATIC | ERP script export (2.17 MB main chunk) | scratch only |
| E011 | 2026-11-22 | local static read of E010x | — | ERP `endpoints` + `config.apiGateway` | STATIC | 1,174 endpoint entries, service map | `10_REA_EVIDENCE/bundle_analysis/E011_erp_endpoint_catalog.md` |
| E012 | 2026-11-22 | local static read of E010x | — | ERP router | STATIC | 688 route→component pairs | `10_REA_EVIDENCE/bundle_analysis/E012_erp_route_table.md` |
| E013 | 2026-11-22 21:16 | inspect_web_page + capture_web_screenshot (S002) | ev_4ce33bb2… / ev_6195e5cc… | `/erp/acms/calendar` | OBSERVED | Academic calendar structure and visual | `09_VISUAL_EVIDENCE/screenshots/academic-calendar-month-view.png` (redacted) |
| E014 | 2026-11-22 21:18 | inspect_web_page + screenshot (S003) | ev_14b22cb3… / ev_4761bce4… | `/erp/acms/calendar` | OBSERVED | Sidebar expansion not retained in snapshot | log only |
| E015 | 2026-11-22 21:19 | inspect_web_page (16 s hover window) | ev_3152e62f… | `/erp/acms/calendar` | OBSERVED | Hover mega-menu not present in a11y snapshot | log only |
| E016 | 2026-11-22/23 | read-only CDP navigator (custom; same endpoint) | — | 70 routes (`/erp/*`, `/recruitment/*`) | OBSERVED | Final URLs (access/denied), a11y outlines, screenshots, 94 API endpoints with value-free shapes | `10_REA_EVIDENCE/sanitized_metadata/E016_runtime_api_shapes.json`; screenshots (redacted); `02_FRONTEND_ANALYSIS/screen_inventory.md` |
| E017 | 2026-11-23 | read-only CDP capture (config values only) | — | `/erp/communication-inbox`, `/erp/acms/assessments` | OBSERVED | 80 ERP user-level names, comms roles/enums, own scope (Teacher, limited) | `10_REA_EVIDENCE/sanitized_metadata/E017_role_catalogue.md` |
| E018 | 2026-11-23 | list_browser_targets | ev_2546f7a3… | origin filter | OBSERVED | Endpoint health check; tab confirmed back on `/erp/acms/calendar` | log only |
| E019 | 2026-11-23 | read-only CDP route crawl (teacher; navigate-only) | — | 408 of 426 safe routes (`/erp/*`, `/recruitment/*`, CRM); stopped on server 429 | OBSERVED | Per-route outcome (42 rendered / 359 role-redirect / 7 other), 114 endpoints observed (77 new → 171 total), 72 value-free response schemas; runtime-confirmed service boundaries (transport→finance, lesson-plan→MGMT, chat→Mattermost) | `10_REA_EVIDENCE/sanitized_metadata/E019_phase3_crawl_api_shapes.json`; `13_AUTONOMOUS_DEEP_ANALYSIS/ROUTE_CRAWL_RESULTS.csv`, `RUNTIME_VERIFICATION_REPORT.md` |

> **Doc erratum (2026-11-24):** the Phase 3 call-site total "11,146" is corrected in `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` — the exported `API_CALLSITE_INDEX.csv` holds **8,530** records and the catalog attributes **10,006** occurrences (Σ `callsite_count`) across 5,364 endpoints; neither is declared the definitive unique total. HTTP-method resolution figures (5,427 resolved) are unchanged.
