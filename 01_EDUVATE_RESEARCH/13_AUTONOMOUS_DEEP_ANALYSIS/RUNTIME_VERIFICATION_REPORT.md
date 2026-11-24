# Runtime Verification Report

Automated, **read-only** route crawl of the authenticated Chrome session (one **teacher** account, ERP user level 11), to turn STATIC_PATH_ONLY / STATIC_CALLSITE_CONFIRMED candidates into RUNTIME_OBSERVED where this role can reach them. Navigation only (`Page.navigate`); passive Network / Page / Accessibility reads; no clicks, no form submits, no writes. Bodies were reduced to value-free type/format shapes inside the capture; no values, headers, cookies or tokens were written to disk, and the browser endpoint identifier is withheld.

## Status: EXECUTED — 2026-11-23 (completed, stopped cleanly on the server's rate limit)

### Run outcome
- **Pilot:** 5 known teacher screens → passed the gate (≥3 rendered + a core API returning 200). Proceeded to the full queue automatically.
- **Full crawl:** 408 of 426 queued SAFE_READONLY routes were attempted before the crawler hit its **429 (rate-limit) stop condition** at route 403 of the ERP batch and halted as designed. **18 routes were never reached** (not a failure of those routes — the crawl stopped before them). They are listed as skipped-for-safety in `REMAINING_UNKNOWNS.md`.
- **No server errors:** 0 sustained 5xx; the run was not stopped by error, only by rate limiting. The crawler created and closed its own tab; the user's original tab was untouched.

### Route outcomes (408 attempted)
| Outcome | Count | Meaning |
|---|---:|---|
| RENDERED (ACCESSIBLE) | **42** | Requested screen mounted; API traffic attributable to that route |
| REDIRECT_PROFILE (role-restricted, **inferred**) | **359** | Route guard bounced the navigation to `/erp/user-profile`; for a single teacher account this is the role/permission gate. API calls on these records belong to the profile page, **not** the requested route |
| REDIRECT_OTHER | **7** | Redirected elsewhere (e.g. `/erp/diary` → `/erp/dashboard`) |
| REDIRECT_LOGIN | 0 | Session stayed authenticated throughout |
| NAVIGATION_ERROR | 0 | — |
| **Not reached (stopped at 429)** | **18** | Queue positions after the rate-limit stop |

By app: **ERP** 400 attempted (35 rendered, 358 role-restricted, 7 other); **HRMS/recruitment** 4 (3 rendered, 1 role-restricted); **CRM** 4 (4 rendered).

> **Role-restriction is the headline runtime finding.** ~88% of attempted ERP routes redirect this teacher to the profile page. This is strong runtime evidence of **client-side role-based route guarding**, but the redirect target (`/erp/user-profile`) is the same for all of them, so it tells us *that* a route is gated for this role, not the destination screen's own data. The label is INFERRED (a client redirect, not a server 403). See `ROLE_ROUTE_API_MATRIX.md`.

### APIs observed at runtime this crawl
- **114 distinct endpoints** observed (method+path), across 9 services. **93** were seen on a RENDERED route (route-attributable); **21** only on redirect/profile pages.
- **77 are new** versus the pre-crawl runtime baseline (E016's 94 endpoints) — **65** of those on rendered routes. Runtime-observed endpoints in the catalog rose **94 → 171**.
- HTTP status mix (distinct endpoint×status): 200 ×106, 201, 204, 400, 401, 403 ×5, 404 ×4, 406, 500 ×1, 429 ×1. The 403/401/404s are genuine server responses to a teacher-role fetch and are recorded as such.
- **72 new response schemas** captured as value-free field/type shapes → `../10_REA_EVIDENCE/sanitized_metadata/E019_phase3_crawl_api_shapes.json`, folded into `RESPONSE_SCHEMA_CATALOG.md`.
- Notable: the **CHAT** service answers on `/api/v4/channels`, `/api/v4/teams`, `/api/v4/system/notices` — a **Mattermost** deployment (previously inferred; now runtime-confirmed by API surface).

### Method validation (static call sites vs runtime)
Of **107** endpoints with both a static call-site method and a runtime method, **104 agree directly**. The 3 remaining are the benign multi-method REST pattern (static resolved a write method — POST/PUT — on a collection path; the crawl observed GET/list on the same path). **Zero** true contradictions (no case of static GET vs runtime POST). Methods are never inferred from path shape. Detail in `STATIC_VS_RUNTIME_COMPARISON.md`.

### Outputs written
- `ROUTE_CRAWL_RESULTS.csv` — one row per attempted route: outcome, `access` classification, `route_attributable` flag, final path, API-call count, app-API-call count, observed statuses, and static UI labels (tabs/buttons/columns) kept only where they are exact string literals from shipped code.
- `API_ENDPOINT_CATALOG.csv` — 39 rows promoted to RUNTIME_OBSERVED, 38 runtime-only endpoints added (6,539 → 6,577 rows).
- `E019_phase3_crawl_api_shapes.json` — value-free new-endpoint shapes.
- `crawl_summary.json`, `method_compare.json` — machine aggregates (scratchpad).

### Honesty boundaries
- This is **one role's** reachability. The 359 role-restricted routes are **not** verified as functional — only as gated for a teacher. A higher-privilege account would render many of them; that was out of scope (one account only).
- 18 queued routes were **not visited** (rate-limit stop). The crawl is **not** full runtime coverage, and nothing here claims it is.
- Static discovery counts (6,577 candidates) remain separate from runtime-observed counts (171). Static ≠ runtime-verified.

---
### Appendix — connection history (resolved)
The crawl was initially blocked: since Chrome 136 the DevTools WebSocket upgrade on the default user-data directory is withheld until the user authorizes the debugging session (the `/json` discovery documents are withheld in this mode too). After the user approved the connection, the pilot attached in ~3 s and the full crawl ran unattended. No second Chrome instance or profile was launched.
