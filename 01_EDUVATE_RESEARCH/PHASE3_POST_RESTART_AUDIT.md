# Phase 3 — Post-Restart Forensic Audit

**Audit date:** 2026-11-24
**Auditor:** Claude (read-only forensic verification)
**Scope:** Verify, from the files on disk only, the state of the Phase 3 autonomous Orchids ERP investigation after an unplanned Windows restart.
**Method:** Static inspection of `D:\REA Claude\orchids-erp-research\` and `13_AUTONOMOUS_DEEP_ANALYSIS\`. No reconnection, no browser, no crawl, no re-analysis, no downloads, no rebuilds, no deletions, no modification of existing research documents. Figures below were recomputed independently from the shipped CSV/JSON, not copied from the prose reports.

> **Classification legend:** **VERIFIED** = independently reproduced from the shipped data. **PARTIAL** = substantially confirmed but with a documented caveat or evolved figure. **MISSING** = an expected artifact is absent. **UNVERIFIED** = a reported figure cannot be reproduced from the shipped files (evidence is in now-gone scratch data).

---

## Headline finding

**Phase 3 was already completed and fully consolidated before the restart. Nothing was left mid-run, and no deliverable was lost or truncated.**

- The **last write to the entire project was 2026-11-24 00:27:39** (`EVIDENCE_INDEX.md`), the final step of an orderly completion sequence (crawl outputs 00:13 → analysis reports 00:17–00:21 → package docs + module specs 00:21–00:25 → `PHASE3_COMPLETION_REPORT.md` 00:27:14 → `EVIDENCE_INDEX.md` 00:27:39).
- There is **no trace of an interrupted process**: no partial CSV, no `.tmp`/`.part`, no checkpoint, no progress file, no background-job log, and no second-crawl output anywhere in the project.
- The route crawl did **not** cover all 426 routes, but that is **by design** — it stopped cleanly on the server's **429 rate limit** at 408/426 and the 18 unreached routes are explicitly listed. This is a rate-limit stop, **not** restart data loss.
- The previous session's **post-crawl processing ran to completion** — the endpoint catalog was consolidated to 6,577 rows, all 11 markdown reports were regenerated, 12 module specs were annotated, and both a completion report and an evidence index were written.

The task brief described the pre-restart state as "the full 426-route crawler launched in the background, a post-crawl script merely prepared, and one shell process still running." **The on-disk evidence does not match a mid-run interruption.** The most evidence-supported reading is that the crawl had already stopped at the 429 and the post-processing (the "running process") had finished, producing every output that now exists. If a *second* full crawl was launched after 00:27:39, it left **zero persistent artifacts** in the project.

---

## 1. Work confirmed complete — **VERIFIED**

| Item | Evidence | Status |
|---|---|---|
| Static JS analysis of all 3 SPAs | `SOURCE_ASSET_INVENTORY.md`: 1204 ERP + 516 HRMS + 234 CRM = **1,954** files, 0 parse errors | VERIFIED (sub-counts sum exactly) |
| Endpoint catalog built & consolidated | `API_ENDPOINT_CATALOG.csv` = **6,577 rows** (incl. 38 runtime-only post-crawl) | VERIFIED |
| HTTP-method resolution | **5,427** rows carry a resolved method (`method_count>0`) | VERIFIED |
| Route inventory + safety classification | `ROUTE_COMPONENT_MATRIX.csv` & `ROUTE_DISCOVERY_SAFETY.csv` = **729 rows** each | VERIFIED |
| Crawl queue derivation | 442 SAFE_READONLY → **426** (after component dedup) → **390** new vs E016 | VERIFIED (all reproduced) |
| Route crawl executed & post-processed | `ROUTE_CRAWL_RESULTS.csv` = **408 rows**, every row classified | VERIFIED |
| Runtime catalog promotion | **171** rows `RUNTIME_OBSERVED`, all with HTTP statuses | VERIFIED |
| Static-vs-runtime method agreement | Independently reconstructed: **89/89**, 0 disagreements (see §6) | VERIFIED |
| Reports & completion report written | 11 reports in folder 13 + root `PHASE3_COMPLETION_REPORT.md` + `EVIDENCE_INDEX.md` | VERIFIED |

## 2. Work partially complete — **PARTIAL**

| Item | Detail | Status |
|---|---|---|
| Route crawl coverage | 408 of 426 attempted; **18 never reached** after the 429 stop (listed in `REMAINING_UNKNOWNS.md`). Full runtime coverage was never claimed. | PARTIAL (by design) |
| Finance-service surface | Reports state **141** finance endpoints recovered; the final catalog shows **151 FIN** (135 static + 16 runtime-observed). The +10 is attributable to the crawl's runtime finance/transport additions. The exact pre-crawl FIN count is not reproducible (pre-crawl catalog not retained). | PARTIAL (consistent, evolved) |
| Runtime role coverage | Only the **teacher** role (ERP level 11) was exercised. 359 routes are role-gated for a teacher; their own screens need a higher-privilege account (out of scope). | PARTIAL (one role, as designed) |

## 3. Work not completed — **documented, not a defect**

| Item | Reason |
|---|---|
| 18 queued routes never crawled | Crawl stopped on the server's 429 rate limit; re-running was declined to respect the limit. |
| Other roles (admin / accountant / principal / parent / student) | Only a teacher account exists; impersonation is out of bounds. |
| Server-side enforcement check (SEC-05) | The observed gate is client-side; no endpoint was probed directly. |
| Write / interaction flows (bodies, state transitions, errors) | The crawl is navigate-only; static body-key names (1,720 endpoints) are the passive ceiling. |

## 4. Files successfully preserved — **VERIFIED**

All **17** files in `13_AUTONOMOUS_DEEP_ANALYSIS\` are present and **non-empty**, plus the root Phase 3 reports. Full SHA-256 inventory: `PHASE3_PROOF_PACKAGE\SHA256SUMS.txt`.

| File | Bytes | Last modified |
|---|---:|---|
| API_ENDPOINT_CATALOG.csv | 1,229,192 | 2026-11-24 00:13:56 |
| API_CALLSITE_INDEX.csv | 1,607,650 | 2026-11-23 21:29:43 |
| ROUTE_COMPONENT_MATRIX.csv | 170,575 | 2026-11-23 21:31:47 |
| ROUTE_CRAWL_RESULTS.csv | 64,602 | 2026-11-24 00:13:56 |
| ROUTE_DISCOVERY_SAFETY.csv | 44,740 | 2026-11-23 21:32:20 |
| COVERAGE_REPORT.md | 5,890 | 2026-11-24 00:18:40 |
| RUNTIME_VERIFICATION_REPORT.md | 5,768 | 2026-11-24 00:17:21 |
| STATIC_VS_RUNTIME_COMPARISON.md | 3,900 | 2026-11-24 00:17:55 |
| REMAINING_UNKNOWNS.md | 4,359 | 2026-11-24 00:19:13 |
| RESPONSE_SCHEMA_CATALOG.md | 6,558 | 2026-11-24 00:19:58 |
| ROLE_ROUTE_API_MATRIX.md | 7,739 | 2026-11-24 00:20:54 |
| API_METHOD_RESOLUTION.md | 3,254 | 2026-11-23 21:42:13 |
| APPLICATION_CHUNK_GRAPH.md | 3,501 | 2026-11-23 21:42:13 |
| SOURCE_ASSET_INVENTORY.md | 3,257 | 2026-11-23 21:42:13 |
| API_ROUTE_FEATURE_TRACE.md | 4,197 | 2026-11-23 21:43:32 |
| ROUTE_DISCOVERY_REPORT.md | 3,389 | 2026-11-23 21:44:43 |
| README.md | 4,366 | 2026-11-24 00:21:17 |
| (root) PHASE3_COMPLETION_REPORT.md | 10,802 | 2026-11-24 00:27:14 |
| (evidence) 10_REA_EVIDENCE\…\E016_runtime_api_shapes.json | 106,272 | 2026-11-23 03:35:28 |
| (evidence) 10_REA_EVIDENCE\…\E019_phase3_crawl_api_shapes.json | 53,180 | 2026-11-24 00:13:56 |

**Privacy sweep (re-run during this audit):** no emails, bearer tokens, session/CSRF cookies, JWTs, WebSocket debugger IDs, or 9+ digit identifier runs were found in any packaged file.

## 5. Actual static-analysis metrics (recomputed) — **VERIFIED except where noted**

| Reported | Measured from shipped files | Status |
|---|---|---|
| 1,954 parsed JS files, 0 parse errors | 1204+516+234 = **1,954** (documented; corpus not retained, so not re-parseable here — by design) | VERIFIED (consistent) |
| 6,539 distinct endpoint candidates (static) | **6,539** static + 38 runtime-only = **6,577** catalog rows (exact) | VERIFIED |
| Classification split | STATIC_CALLSITE_CONFIRMED **5,256** · STATIC_PATH_ONLY **1,033** · DYNAMIC_TEMPLATE **117** · RUNTIME_OBSERVED **171** (sum 6,577) | VERIFIED |
| 5,381 methods resolved statically | **5,427** resolved in the current (post-crawl) catalog; 5,381 is the documented static-only figure | VERIFIED (both documented) |
| **11,146 resolved HTTP call sites** | **Cannot reproduce.** Shipped `API_CALLSITE_INDEX.csv` = **8,530 rows**; catalog `callsite_count` sums to **10,006**. The 11,146 figure originates from scratch aggregates (`crawl_summary.json`/AST counters) not retained in the package. **Corrected 2026-11-24** by `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` (see Changelog). | **UNVERIFIED → corrected** |
| 729 routes | **729** (ERP 687 + HRMS 25 + CRM 17) | VERIFIED |
| 2,877 endpoints with static response-field names | **2,877** (exact) | VERIFIED |
| 1,720 endpoints with static request-body keys | **1,720** (exact) | VERIFIED |
| 141 finance-service endpoints | **151 FIN** in the final catalog (141 static-recovered + crawl additions) | PARTIAL (reconciled) |

## 6. Actual runtime crawl metrics (recomputed) — **VERIFIED**

| Metric | Measured from `ROUTE_CRAWL_RESULTS.csv` / catalog |
|---|---|
| Total unique queued routes | **426** (from `ROUTE_DISCOVERY_SAFETY.csv`: 442 SAFE_READONLY → 426 after component dedup) |
| Routes attempted | **408** |
| Routes successfully rendered | **42** (ERP 35, HRMS 3, CRM 4) — outcome `RENDERED` / access `ACCESSIBLE` |
| Routes redirected to profile (role-restricted, INFERRED) | **359** — final_path `/erp/user-profile` |
| Routes redirected to login | **0** (session stayed authenticated) |
| Routes blocked by role permissions | **359** (same set; client-redirect gate, labelled INFERRED — not a server 403) |
| Routes redirected elsewhere | **7** (`REDIRECT_OTHER`) |
| Routes skipped for safety (excluded from queue by design) | **287** (PARAM 149 + STATE_CHANGING 98 + SENSITIVE 40) |
| Routes failed / navigation errors | **0** |
| Routes never attempted (after the 429 stop) | **18** (all explicitly listed in `REMAINING_UNKNOWNS.md`) |
| Every attempted route has a final classification? | **Yes — 408/408** |
| Original runtime baseline (E016) | **94** endpoints (E016 JSON array length = 94) |
| Runtime-observed total after crawl | **171** (94 + 77 new) |
| Static-vs-runtime method agreement (reconstructed) | **89/89**, 0 disagreements — from 94 E016 endpoints minus 1 Clarity (3rd-party) and 4 Mattermost/static-asset = 89, each matched to an agreeing static call-site method |
| Static candidate accidentally flagged runtime? | **None.** 0 rows with `runtime_observed=True` but `classification≠RUNTIME_OBSERVED`; 0 static rows carrying `runtime_statuses`; all 171 runtime rows carry statuses. |

## 7. Route coverage percentage

- **Queue attempted:** 408 / 426 = **95.8%** (18 unreached = 4.2%, rate-limit stop)
- **Attempted routes with a final classification:** 408 / 408 = **100%**
- **Rendered (teacher-reachable):** 42 / 408 attempted = **10.3%** (42 / 426 queue = 9.9%)
- **Role-gated for a teacher:** 359 / 408 = **88.0%** (the headline runtime finding: client-side role-based route guarding)
- **Static route inventory mapped:** 729 / 729 = **100%** (route → component → chunk → service)
- **Runtime-verified share of catalog:** 171 / 6,577 = **2.6%** (static discovery is not runtime verification; the package states this throughout)

## 8. Missing or incomplete evidence

| Item | Status | Note |
|---|---|---|
| `11,146` call-site figure | **UNVERIFIED → corrected** | Not reproducible from shipped files; the exported index holds 8,530 rows and the catalog sums to 10,006. See §5. Resolved 2026-11-24 via `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` (see Changelog). |
| Scratch tooling (`crawler.mjs`, `process_crawl.py`, `compare_methods.py`) | **MISSING** | Lived in the ephemeral session scratchpad (temp), cleared by the restart. By design — never part of the handoff. Reproducibility notes remain in `PHASE3_COMPLETION_REPORT.md` §6. |
| Scratch aggregates (`crawl_summary.json`, `method_compare.json`, `queue.json`) | **MISSING** | Same ephemeral scratchpad; gone. Their derived results are preserved in the committed CSVs/reports. |
| Raw JS corpus (68 MB) & raw crawl capture | **MISSING (by design)** | Deliberately retained only in temp and deleted per the package's data-handling rule; no proprietary bytes in the handoff. |
| 18 unreached routes' runtime behaviour | **PARTIAL** | Rate-limit stop; need a later rate-respecting re-run. |
| Pre-crawl catalog snapshot | **MISSING** | No pre-Phase-3 backup exists in the project, so a few evolved counts (e.g. FIN 141→151) cannot be split pre/post-crawl from disk. |
| Background job logs / checkpoints | **MISSING (none ever persisted)** | No `.log`/checkpoint/progress file exists anywhere in the project. |

## 9. Was the previous post-processing completed? — **VERIFIED: YES**

The post-crawl processor did **not** stop at the restart — its outputs all exist and are internally consistent:
- `API_ENDPOINT_CATALOG.csv` consolidated to **6,577** rows (39 promoted + 38 runtime-only added), written 00:13:56.
- All 11 folder-13 reports regenerated 00:17–00:21.
- 12 module specs annotated with runtime notes 00:24–00:25.
- `PHASE3_COMPLETION_REPORT.md` (00:27:14) and `EVIDENCE_INDEX.md` (00:27:39) written last.
- Cross-document counts reconcile (6,577; 5,427; 171; 408; 89/89), which would not hold if processing had been cut off mid-write.

**Conclusion:** the post-processing was executed to completion; the restart occurred after Phase 3 finished (or any later action produced nothing persistent).

## 10. Recommended next action

1. **Treat Phase 3 as complete and intact.** No recovery or re-run is required to preserve the current deliverables; the package is internally consistent and privacy-clean.
2. **Accept this proof package** (`PHASE3_PROOF_PACKAGE.zip`) as the verified evidence snapshot, with the SHA-256 inventory for integrity.
3. **DONE (2026-11-24) — the single UNVERIFIED figure (11,146 call sites) has been corrected** by `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` and in every citing document. The shipped `API_CALLSITE_INDEX.csv` (8,530 records) and the catalog's Σ `callsite_count` (10,006) are now documented with their distinct measurement bases; neither is presented as the definitive unique-call-site total. See Changelog.
4. **Do not re-crawl now.** If the 18 unreached routes and other roles are wanted later, that is a *new, authorized* phase — a fresh, rate-limit-respecting run with a higher-privilege account — and remains out of the current read-only scope.
5. **No modification of existing research documents** was made by this audit; any correction (e.g. the call-site footnote, FIN 141→151) should be an additive erratum per the project's evidence-discipline rule.

---

### Appendix — what this audit did and did not do
- **Did:** enumerate every file with size/mtime/empty-flag; recompute catalog classification, method resolution, response/body-key counts, service breakdown, route-safety and queue derivation, crawl outcomes, and runtime integrity directly from the CSV/JSON; independently reconstruct the 89/89 method agreement; scan for PII/secrets; compute SHA-256 hashes; assemble the proof package.
- **Did not:** reconnect to Orchids, launch a browser, crawl, re-run static analysis, download bundles, rebuild reports, delete anything, or modify any existing research document.

---

## Changelog

### 2026-11-24 — Call-site count erratum applied
Scope: documentation-only correction of the single UNVERIFIED figure (the "11,146 resolved HTTP call sites"). No browser, crawl, static analysis, or download was performed, and no endpoint, route, HTTP-method, schema, or runtime finding was changed.

- **Added** `13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` — the authoritative correction. It records the two **reproducible** measurements with their distinct bases: the exported `API_CALLSITE_INDEX.csv` = **8,530** call-site records (8,527 with a method, 8,513 to a known client, 5,364 endpoints covered), and the catalog's Σ `callsite_count` = **10,006** (same 5,364 endpoints). It explains why they differ (the index de-duplicates by `(service, path, method, file, line)`; `callsite_count` counts raw occurrences) and why **11,146** exceeds both (it counted resolved call sites never attributed to a catalogued endpoint, from non-retained counters). It states explicitly that **neither 8,530 nor 10,006 is the definitive number of unique HTTP call sites** — uniqueness is key-dependent (8,530 records collapse to 2,591 distinct `(file, line, method)` positions).
- **Removed the 11,146 claim** (and its 11,120 / 11,103 derivatives) as a verified result from: `COVERAGE_REPORT.md`, `API_METHOD_RESOLUTION.md`, `APPLICATION_CHUNK_GRAPH.md`, `04_BACKEND_ANALYSIS/api_inventory.md`. Each now cites the reproducible figures and points to the erratum; where the static pass's own counters are retained for provenance (the §2 basis breakdown), they are explicitly flagged as non-reproducible.
- **Preserved** the HTTP-method resolution figure **5,427 / 6,577 (82.5%)** and its classification (5,256 / 1,033 / 117 / 171; 5,381 static-only) everywhere — unchanged.
- **Updated** `PHASE3_COMPLETION_REPORT.md` (call-site note) and `EVIDENCE_INDEX.md` (erratum pointer).
- **Regenerated** `PHASE3_PROOF_PACKAGE/` (now including `CALLSITE_COUNT_ERRATUM.md` and the corrected documents) and `SHA256SUMS.txt`, and rebuilt `PHASE3_PROOF_PACKAGE.zip`.
