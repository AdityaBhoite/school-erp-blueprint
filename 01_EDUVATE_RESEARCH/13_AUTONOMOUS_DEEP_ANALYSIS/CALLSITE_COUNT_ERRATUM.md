# Erratum — Phase 3 "Resolved HTTP Call Sites" Count

**Issued:** 2026-11-24 · **Applies to:** Phase 3 deep static analysis (`13_AUTONOMOUS_DEEP_ANALYSIS/`) and its downstream summaries · **Trigger:** the post-restart forensic audit (`../PHASE3_POST_RESTART_AUDIT.md`) could not reproduce the figure "11,146 resolved HTTP call sites" from any shipped artifact.

This erratum corrects **how the call-site count is reported**. It does **not** change any endpoint, route, HTTP-method, schema, or runtime finding.

## 1. What was wrong
Several documents stated **"11,146 resolved HTTP call sites"** (with derived sub-counts **11,120** carrying a method and **11,103** resolving to a known HTTP client), presented as a result:
- `COVERAGE_REPORT.md` (§1 table)
- `API_METHOD_RESOLUTION.md` (§1 note; §2 basis-breakdown lead-in)
- `APPLICATION_CHUNK_GRAPH.md` (§4)
- `../04_BACKEND_ANALYSIS/api_inventory.md`

The number **11,146 is not reproducible from any shipped file.** It originated in the AST pass's in-memory counters, kept only in the session scratch aggregates (`crawl_summary.json` and the analyzer's own counts), which were **not retained** in the handoff. It must not be cited as an independently verified total.

## 2. What the shipped data actually shows (reproducible, recomputed 2026-11-24)
Two different, reproducible measurements exist. They count different things and are **not** interchangeable; neither is declared "the" definitive number of unique HTTP call sites (see §3).

### Measurement A — exported call-site records: **8,530**
`API_CALLSITE_INDEX.csv` contains **8,530 data rows**. Verified properties:
- Each row is unique on `(service, path, method, file, line)` — distinct count on that 5-tuple = 8,530 = row count, so there are **no duplicate records**.
- **8,527** rows carry a concrete HTTP method; **8,513** resolve to a known HTTP client; 18 route through a named wrapper.
- The rows cover **5,364 distinct `(service, path)` endpoints**.
- 3,801 rows carry a named catalog key (2,144 distinct keys); 4,729 rows resolved from a call expression with no named catalog object.
- The 8,530 records collapse to only **2,591 distinct `(file, line, method)` positions** — minified code places many endpoint calls on one source line, so "one record" ≠ "one unique source position".

### Measurement B — catalog call-site attributions: **10,006**
Summing the per-endpoint `callsite_count` column in `API_ENDPOINT_CATALOG.csv` gives **10,006**, spread across the **same 5,364 endpoints** (endpoints with `callsite_count > 0` = 5,364; maximum on one endpoint = 72).
- This is higher than the 8,530 exported records because `callsite_count` counts raw call-site **occurrences** attributed to an endpoint, whereas the exported index **de-duplicates** records that share the same `(service, path, method, file, line)`. The 1,476 difference is occurrences that collapsed onto an already-emitted record.

### Why 11,146 exceeds both
The non-retained 11,146 counted every resolved call site in the pass, including ones never attributed to a catalogued endpoint (external / `EXTERNAL:` hosts and filtered non-API strings) that both the catalog sum (10,006 — catalogued endpoints only) and the exported index (8,530 — de-duplicated catalogued records) exclude. That ~1,140 excess cannot be checked against any retained file, so 11,146 remains unverifiable.

## 3. How to cite call-site counts from now on
- The **exported call-site evidence is `API_CALLSITE_INDEX.csv` — 8,530 records** (8,527 with a method, 8,513 to a known client), covering 5,364 endpoints.
- The **catalog attributes 10,006 call-site occurrences** to those endpoints (`Σ callsite_count`).
- **Do not** call either number "the total number of unique HTTP call sites": the data does not establish one. Uniqueness depends on the key — 8,530 on `(service, path, method, file, line)` but only 2,591 on `(file, line, method)` — and the 10,006 sum double-counts endpoints that share a source position.
- The HTTP-**method** resolution figure is **unaffected**: **5,427 of 6,577 catalog endpoints (82.5%) carry a resolved method** (classification: STATIC_CALLSITE_CONFIRMED 5,256 · STATIC_PATH_ONLY 1,033 · DYNAMIC_ENDPOINT_TEMPLATE 117 · RUNTIME_OBSERVED 171; 5,381 was the static-only figure). This stands on the catalog, not on any call-site total.

## 4. Files corrected by this erratum (in place, additively noted)
| File | Change |
|---|---|
| `COVERAGE_REPORT.md` | Call-site block replaced with Measurements A & B; 11,146 / 11,120 / 11,103 removed; erratum note added |
| `API_METHOD_RESOLUTION.md` | 11,146 mentions re-cast; basis-breakdown table flagged as from the non-retained counters |
| `APPLICATION_CHUNK_GRAPH.md` | §4 client-funnel ratio restated as 8,513 / 8,530 |
| `README.md` (this folder) | `API_CALLSITE_INDEX.csv` described as an "exported call-site record"; this file listed |
| `../04_BACKEND_ANALYSIS/api_inventory.md` | Phase 3 table row restated; index description corrected |
| `../PHASE3_COMPLETION_REPORT.md` | Call-site note extended with the erratum pointer |
| `../EVIDENCE_INDEX.md` | Erratum pointer added |
| `../PHASE3_POST_RESTART_AUDIT.md` | Finding marked resolved; changelog entry added |

No endpoint, route, method, schema, or runtime figure was altered.
