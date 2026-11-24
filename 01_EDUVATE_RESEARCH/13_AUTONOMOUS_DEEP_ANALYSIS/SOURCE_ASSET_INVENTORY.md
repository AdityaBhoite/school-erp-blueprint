# Source Asset Inventory

Static corpus collected 2026-11-23 for Phase 3. Scripts were fetched over HTTPS from the three apps' published asset directories (no cookies, no code execution) and analysed with a Babel-AST pass. Proprietary bytes are kept only in a temporary analysis directory and are **not** part of this handoff.

## 1. How the corpus was built
- Entry documents (`/erp/`, `/recruitment/`, `/crm/`) were fetched; their `<script>`, `modulepreload` and stylesheet references were the seeds.
- From each script, static `import`/`import()` specifiers, Vite `__vite__mapDeps` chunk lists and `sourceMappingURL` comments were followed breadth-first, staying inside each app's `assets/` path. 50 rounds, 3 workers, ~120 ms between requests.
- Each file was SHA-256 hashed; duplicates are analysed once.

## 2. Corpus size
| App | Files | MB (uncompressed) | Entry build |
|---|---|---|---|
| ERP (`/erp`) | 1204 | 39.5 | Vite, dot-hash chunks, manual vendor chunks |
| HRMS (`/recruitment`) | 516 | 17.6 | Vite, dash-hash chunks, per-icon chunks |
| CRM (`/crm`) | 234 | 11.1 | Vite, dash-hash chunks |
| **Total** | **1954** | **68.2** | |

- Distinct SHA-256: 1947. Duplicate files (same bytes under another path): 7.
- Parse outcome: **1954/1954 parsed cleanly, 0 parse errors, 0 recovered-error files** (Babel `errorRecovery` on, `sourceType:"unambiguous"`, JSX plugin).
- Source maps: **none referenced** by any script (`sourceMappingURL` absent in all 1954 files), so no original source names were recoverable. Readable identifiers survive only in the ERP main chunk and the endpoint-catalog chunks.

## 3. What this corpus adds over the Phase 1/2 captures
- Phase 1/2 analysed the entry bundle plus chunks loaded during manual navigation (84 HRMS + 16 ERP scripts). This pass has **every statically reachable chunk** of all three apps.
- The Phase 1/2 "2,604 catalog entries" came from two endpoint objects. This pass finds **9 endpoint/config catalogs** (3532 entries total) plus call-site and inline paths across the whole corpus.

## 4. Endpoint catalogs recovered (static objects mapping names to paths)
| App | Chunk | Binding | Entries | Path-like |
|---|---|---|---|---|
| recruitment | `index-GEUUShrI.js` | `rt` | 1432 | 1428 |
| erp | `index.DvwOJRTc.js` | `endpoints` | 1174 | 1128 |
| crm | `index-C6Fgq7td.js` | `te` | 586 | 581 |
| erp | `endpoints.C5m5-CbH.js` | `e` | 83 | 83 |
| erp | `endpoints.BNh690Q7.js` | `s` | 75 | 75 |
| erp | `utils.D4awd6_X.js` | `R` | 55 | 55 |
| erp | `endpoints.DWDEPYcE.js` | `$` | 52 | 52 |
| erp | `index.CE3_qYP9.js` | `wB` | 41 | 41 |
| erp | `index.DvwOJRTc.js` | `config$3` | 34 | 27 |

The two largest (`endpoints` in the ERP main chunk, 1,174; `rt` in the HRMS main chunk, 1,432) are the Phase 1/2 catalogs. The others are **new**: three L&D/LMS catalogs (`byte-learn`, `lnd/programs`, `lnd/paths`), a CRM catalog (`te`, 586), an LMS util catalog (`R`, 55) and an image/asset map.

## 5. Retention
Raw `.js` bytes live only in the session scratch directory and are deleted at the end of this task. This folder keeps only value-free, derived metadata (paths, methods, field names, structure). No proprietary source is included, in line with `12_CLAUDE_CODE_HANDOFF/DEVELOPMENT_RULES.md`.
