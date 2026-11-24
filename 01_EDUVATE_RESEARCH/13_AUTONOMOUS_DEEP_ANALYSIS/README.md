# Phase 3 — Autonomous Deep Analysis

Created 2026-11-23. This folder extends the Phase 1/2 investigation and the documentation audit with an **exhaustive static JavaScript analysis** of all three Eduvate apps and an **automated endpoint call-site / HTTP-method resolution**, plus a **controlled read-only route crawl that was executed** (2026-11-23).

It does not restart the investigation and does not re-contact the live system except through that one controlled, read-only, navigate-only browser crawl (`RUNTIME_VERIFICATION_REPORT.md`), which ran as a teacher and stopped cleanly on the server's rate limit after 408 of 426 routes.

## What is new in Phase 3
| Phase 1/2 | Phase 3 |
|---|---|
| Entry bundle + chunks loaded during manual navigation (≈100 scripts) | **All 1,954 statically reachable chunks** of `/erp`, `/recruitment`, `/crm` (68 MB), 0 parse errors |
| 2,604 catalog entries, **no HTTP methods** | **6,539 distinct endpoints**, **methods resolved for 5,381 (82%)** from code call sites |
| Methods inferred from names | Methods read from the actual `client.get/post(...)`, config objects and wrapper defaults; **89/89 runtime methods confirmed, 0 disagreements** |
| 94 runtime endpoints | 94 re-used as ground truth; then **the crawl added 77 more → 171 runtime-observed**, with 72 new value-free response schemas (E019) |
| Finance/transport core API "not recovered" | **141 finance-service endpoints** recovered from ERP-side call sites, with methods and field names |
| Route list (688) | **729 routes** linked to component → chunk → service modules → APIs; 2,877 endpoints carry static response-field names |
| No runtime reachability data | **42 routes rendered / 359 role-redirected** for a teacher — first runtime measurement of ERP role-gating |

## Files
| File | Contents |
|---|---|
| `SOURCE_ASSET_INVENTORY.md` | How the 1,954-file corpus was built; catalogs found; retention |
| `APPLICATION_CHUNK_GRAPH.md` | Static module graph, HTTP clients, fan-in |
| `ROUTE_DISCOVERY_REPORT.md` | Route inventory, safety classification, crawl queue design |
| `ROUTE_COMPONENT_MATRIX.csv` | One row per route → chunk, own APIs, services, imported service modules, permission (where declared) |
| `ROUTE_DISCOVERY_SAFETY.csv` | Every route with its safety class and E016-visited flag |
| `API_ENDPOINT_CATALOG.csv` | One row per distinct endpoint: service, path, classification, methods, runtime status, query/body/response field names, catalog keys |
| `API_CALLSITE_INDEX.csv` | One row per exported static call-site record (8,530 rows; see `CALLSITE_COUNT_ERRATUM.md`): file, line, method, basis, client, wrapper, catalog key, field names |
| `API_METHOD_RESOLUTION.md` | How methods were resolved and validated |
| `RESPONSE_SCHEMA_CATALOG.md` | Static field inventories vs runtime shapes |
| `API_ROUTE_FEATURE_TRACE.md` | Worked traces for selected features |
| `ROLE_ROUTE_API_MATRIX.md` | Static role/level checks, hard-coded ID lists, flags; **§6 runtime role-restriction evidence** |
| `RUNTIME_VERIFICATION_REPORT.md` | Automated route crawl results — **EXECUTED**: 42 rendered, 359 role-redirected, 77 new endpoints |
| `ROUTE_CRAWL_RESULTS.csv` | One row per attempted route: outcome, access class, route-attributable flag, API counts, statuses, static UI labels |
| `STATIC_VS_RUNTIME_COMPARISON.md` | Static candidates vs what was observed (post-crawl) |
| `REMAINING_UNKNOWNS.md` | What is still unknown and why (incl. the 18 unreached routes) |
| `COVERAGE_REPORT.md` | Precise counts for every dimension |
| `CALLSITE_COUNT_ERRATUM.md` | Correction of the "11,146 call sites" figure; the reproducible 8,530 / 10,006 measurements and their bases |

## Evidence discipline (unchanged from the rest of the package)
- **RUNTIME_OBSERVED** = seen on the wire. **STATIC_CALLSITE_CONFIRMED** = method fixed from code. **STATIC_PATH_ONLY** = path only, method UNKNOWN. **DYNAMIC_ENDPOINT_TEMPLATE** = path built at runtime. Static discovery is never equated with runtime verification.
- No proprietary source is stored here — only derived, value-free metadata (paths, methods, field names, structure). No secrets, tokens, personal data, or the browser debugging identifier appear in any file.

## Relationship to the rest of the package
This folder is **additive**. The canonical specs (`03_`–`12_`) were corrected in place by the audit (errata ER-01…ER-48; see `AUDIT_REPORT.md` §G and §H). Where this folder's counts differ from older text, the method is documented here and in `COVERAGE_REPORT.md`.
"""