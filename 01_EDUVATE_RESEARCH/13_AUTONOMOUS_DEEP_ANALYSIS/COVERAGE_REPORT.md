# Coverage Report

Precise counts for Phase 3. Static discovery and runtime verification are reported separately and never equated.

## 1. Static analysis
| Metric | Count |
|---|---|
| App JS files fetched and parsed | 1,954 |
| Parse errors | 0 |
| Error-recovered files | 0 |
| Duplicate files (same bytes, deduped) | 7 |
| Uncompressed corpus | ~68 MB |
| Source maps available | 0 (none referenced) |
| Endpoint/config catalogs recovered | 9 (3,532 entries) |
| **Distinct endpoint candidates (static discovery)** | **6,539** |
| Catalog rows after runtime-only additions (post-crawl) | 6,577 |
| — STATIC_CALLSITE_CONFIRMED (method from code) | 5,256 |
| — STATIC_PATH_ONLY (method unknown) | 1,033 |
| — DYNAMIC_ENDPOINT_TEMPLATE | 117 |
| — RUNTIME_OBSERVED (E016 + Phase 3 crawl) | 171 |
| HTTP methods resolved (current catalog) | 5,427 (82.5%) |
| HTTP methods unresolved | 1,150 |
| Multi-method endpoints (same path, >1 verb) | 1,164 |
| Primary-method spread | GET 3,237 · POST 1,403 · DELETE 414 · PUT 247 · PATCH 79 · HEAD 1 |
| Exported call-site records (`API_CALLSITE_INDEX.csv`) | 8,530 (unique per service+path+method+file+line) |
| — with a resolved method | 8,527 |
| — resolved to a known HTTP client | 8,513 |
| — routed through a named wrapper helper | 18 |
| — distinct endpoints covered | 5,364 |
| Catalog call-site attributions (Σ `callsite_count`) | 10,006 (same 5,364 endpoints) |
| Endpoints with static response-field names | 2,877 |
| Endpoints with static request-body key names | 1,720 |
| Endpoints with static query-param key names | (per-row in CSV) |
| Distinct routes | 729 (ERP 687, HRMS 25, CRM 17) |
| Routes resolved to a lazy chunk | 692 |
| Routes with ≥1 own-chunk API | 511 |
| Inline user-level check sites (ERP) | 65 |
| Distinct user levels referenced in checks | 26 (named in E017) |
| Hard-coded identifier allow-list sites | 46 (115 entries total; values not recorded) |
| Services attributed | ERP 4,015 · HRMS 1,527 · CRM 609 · FIN 141 · REPORTS 61 · ACTIVITIES 49 · MGMT 40 · WHATSAPP 28 · CLASSES 15 · others |
| Unknown-service endpoints | 31 |

> **Erratum (2026-11-24):** earlier drafts reported "11,146 resolved HTTP call sites" (with 11,120 / 11,103 sub-counts) from the static pass's in-memory counters, which were not retained. That figure is **not reproducible** from the shipped files and is superseded by the two reproducible measurements above (8,530 exported records; Σ `callsite_count` = 10,006). Neither is "the" unique-call-site total. The HTTP-method resolution figures (5,427 resolved / 82.5%) are unaffected. See `CALLSITE_COUNT_ERRATUM.md`.

### Duplicates / false positives / dynamic
- **Duplicate definitions:** collapsed by `(service, normalized-path)`. Multi-method rows are one endpoint with several verbs, not duplicates.
- **False positives filtered:** asset/CDN/image strings, config values (incl. secrets, recorded by key name only), and non-API hosts are excluded from the endpoint catalog (external hosts tagged `EXTERNAL:`).
- **Dynamic templates:** 117 endpoints whose path is built at runtime (`/x/{id}/y/`), normalized with `{id}`.

## 2. Runtime exploration (EXECUTED 2026-11-23, read-only, one teacher account)
| Metric | Count |
|---|---|
| Safe unique-screen routes queued | 426 (390 new vs E016) |
| Routes **attempted** this run | **408** (stopped cleanly at the server's 429 rate limit; 18 never reached) |
| Routes **rendered** (accessible) | **42** (ERP 35, HRMS 3, CRM 4) |
| Routes redirected to profile (**role-restricted**, inferred) | **359** |
| Routes redirected elsewhere | 7 |
| Routes redirected to login | 0 (session stayed authenticated) |
| Navigation errors | 0 |
| Routes **not reached** (after the 429 stop) | 18 |
| Distinct endpoints observed this crawl | 114 (93 on rendered routes, 21 on redirect pages) |
| APIs **newly** observed vs E016 | **77** (65 on rendered routes) |
| Runtime-observed endpoints total (E016 + crawl) | 94 → **171** |
| Response schemas newly documented | **72** (value-free; → `E019_phase3_crawl_api_shapes.json`) |
| Method agreement (static call site vs runtime) | 104/107 direct; 3 benign REST multi-method; 0 contradictions |
| Routes excluded from the queue for safety (not attempted by design) | 287 (PARAM 149 + STATE_CHANGING 98 + SENSITIVE 40) |

Prior runtime coverage (E016, carried forward and now superseded by the larger set): 70 unique routes, 39 accessible, 31 denied, 94 endpoints. The Phase 3 crawl is additive to it.

## 3. Documentation
| Metric | Count |
|---|---|
| Audit errata applied in place to canonical docs | 97 edits across 35 files (ER-01…ER-48) |
| New Phase 3 deliverables | 15 files in `13_AUTONOMOUS_DEEP_ANALYSIS/` |
| Phase 1/2 gaps closed by static analysis | HTTP methods for 5,381 endpoints (gap: "catalogs record no methods"); finance-service API surface (141 endpoints, previously "not recovered"); full route→API graph; call-site provenance for every resolved endpoint |
| Gaps still requiring another role | Admin/accountant/parent/student runtime behaviour; server-side enforcement (SEC-05) |
| Gaps requiring business decisions | Unchanged (see `DOCUMENTATION_GAPS.md`) |
| Gap closed by the live crawl | Runtime confirmation + response shapes for teacher-reachable screens: 42 routes rendered, 77 new endpoints, 72 new schemas; role-guard behaviour now runtime-evidenced |
| Gap only partially closed | 359 ERP routes are role-gated for a teacher; their own screens/data need a higher-privilege account (out of scope). 18 routes unreached (429 stop) |

## 4. Honesty notes
- 6,539 static endpoints ≠ 6,539 working APIs. Only **171** are runtime-verified (94 prior + 77 from the crawl); 5,256 have a code-confirmed method but unproven reachability; 1,033 are path-only.
- The crawl exercised **one role**. 42 routes rendered; 359 redirected a teacher to the profile page (role-gated, inferred from a client redirect, not a server 403). This does not verify those 359 screens — only that a teacher cannot reach them.
- The crawl **did not** reach full coverage: it stopped on the server's 429 after 408 of 426 routes. 18 routes were not visited. Nothing here claims full runtime coverage.
- Static imports prove a reference, not that a screen exercises every imported export; route→API own-chunk counts are precise, imported-module API reach is a named surface, not an assertion of use.
- Minified single-letter identifiers leave some data flow unresolved; these surface as `UNKNOWN` method or `UNKNOWN_SERVICE`, never as a guess.
"""