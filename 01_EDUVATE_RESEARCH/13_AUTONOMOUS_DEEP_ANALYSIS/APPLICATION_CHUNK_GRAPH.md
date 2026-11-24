# Application Chunk Graph

Static module graph of the three apps, from AST-resolved `import` and `import()` edges. No code was executed; reachability here is **static**, not proof a chunk loads at runtime.

## 1. Edges
| Edge kind | Count |
|---|---|
| Static `import` edges (resolved to a sibling chunk) | 16411 |
| Dynamic `import()` edges (lazy, route- or feature-gated) | 1500 |
| Files that are a lazy-import target of at least one route | 692 routes resolved to chunks |

## 2. Entry → vendor → feature shape
- Each app has one entry chunk (`index.*`) that imports a small set of manual **vendor** chunks (`vendor-react`, `vendor-antd`, `vendor-redux` (ERP only), `vendor-date`, `vendor-utils`, `vendor-icons` (CRM)) and holds the shared shell, the HTTP client(s) and the endpoint catalog.
- Feature screens are **lazy** `import()` targets named in the router (ERP: `__vite__mapDeps`; HRMS/CRM: direct `import()` in route objects). 729 distinct routes map to lazy chunks.
- Shared **service modules** (e.g. `acmsApi.*`, `api.*`, `tickets.*`, `videoMonitoringApi.*`, `testService.*`) are imported by many feature chunks and hold the bulk of the API call sites. They are the real API surface; a feature chunk draws a handful of calls from them.

## 3. Most-imported chunks (static fan-in, by basename)
| Chunk (basename) | Imported by N files |
|---|---|
| `vendor-redux.-BjkKS5F.js` | 1097 |
| `index.DvwOJRTc.js` | 1069 |
| `vendor-antd.D7bp11U3.js` | 1017 |
| `vendor-utils.CX9J2MHC.js` | 906 |
| `vendor-date.CLKsajdH.js` | 904 |
| `vendor-react.DVAEG1ln.js` | 884 |
| `index-GEUUShrI.js` | 494 |
| `api.CFQDbzOL.js` | 303 |
| `index.-Y0_Jsuw.js` | 299 |
| `index.B11KOZiK.js` | 213 |
| `vendor-react-BsSPVCJn.js` | 200 |
| `index-Cb83Jy_n.js` | 193 |
| `index-C6Fgq7td.js` | 188 |
| `vendor-antd-C4Q231SR.js` | 183 |
| `vendor-utils-Dgyxu5LW.js` | 167 |

## 4. HTTP clients discovered
Call-site resolution treated a receiver as an HTTP client only when it resolves to an `axios.create({...})`/interceptor marker or an imported binding of one. Clients found:

- `assets/vendor-utils.CX9J2MHC.js#hn` (in `vendor-utils.CX9J2MHC.js`)
- `assets/edunavClient.FXKS84SR.js#n` (in `edunavClient.FXKS84SR.js`)
- `assets/index.De3jKGHw.js#A` (in `index.De3jKGHw.js`)
- `assets/index.DvwOJRTc.js#axiosInstance` (in `index.DvwOJRTc.js`)
- `assets/index.DvwOJRTc.js#apiClient` (in `index.DvwOJRTc.js`)
- `assets/index.No6wKiNm.js#h` (in `index.No6wKiNm.js`)
- `assets/testService.CqCNuRyx.js#s` (in `testService.CqCNuRyx.js`)
- `assets/testService.CqCNuRyx.js#u` (in `testService.CqCNuRyx.js`)
- `assets/VendorPortal.Cy82tdEj.js#m` (in `VendorPortal.Cy82tdEj.js`)
- `assets/index-GEUUShrI.js#He` (in `index-GEUUShrI.js`)
- `assets/vendor-utils-Dgyxu5LW.js#hn` (in `vendor-utils-Dgyxu5LW.js`)
- `assets/api.B2JG4DyW.js#e` (in `api.B2JG4DyW.js`)
- `assets/index.BYfxtC18.js#R` (in `index.BYfxtC18.js`)

The ERP app funnels almost all traffic through one axios instance (`axiosInstance`) whose request interceptor attaches the module header, the brand header (`X-DTS-BRAND`) and the impersonation header (`X-Is-Switch`) — confirming the Phase 1/2 static findings at the call-site level: in the exported call-site index, **8,513 of 8,530 records resolve to a known HTTP client**. (An earlier "11,103 of 11,146" figure came from the static pass's non-retained counters and is not reproducible — see `CALLSITE_COUNT_ERRATUM.md`.)

## 5. Limitation
Minified single-letter bindings mean some cross-module data flow is unresolved. Static imports prove a *reference*, not that the importing screen exercises every export. Dynamic `import()` with a computed specifier (rare here) stays unresolved.
