# School ERP Reverse-Engineering Study & Build Handoff

This folder documents a passive, read-only study of a commercial K-12 school-chain ERP (the "Orchids" deployment of the Eduvate platform, at `orchids.letseduvate.com`). It also contains a complete blueprint for building an **independent** replacement.

- Investigation dates: 2026-11-22/23
- Tooling: REA 6.9.0 (MCP) for target discovery, page inspection, screenshots, script export and static analysis; a small read-only Chrome DevTools Protocol navigator for the screen tour (see INVESTIGATION_LOG).
- Account used: one authenticated **teacher** (ERP user level 11). No other roles were impersonated.

## Start here
1. `EXECUTIVE_SUMMARY.md` — what was found, in two pages.
2. `CLAUDE_CODE_MASTER_PROMPT.md` (folder root) — **for the build team / coding agent** (v2; supersedes the copy in `12_CLAUDE_CODE_HANDOFF/`).
3. `COVERAGE_MATRIX.md` and `OPEN_QUESTIONS.md` — what is known, partial or unknown.

> **Audit (2026-11-23):** `AUDIT_REPORT.md`, `DOCUMENTATION_GAPS.md`, `HANDOFF_READINESS.md` and `SECURITY_REQUIREMENTS.md` audit this package. Where the errata register in `AUDIT_REPORT.md` §G conflicts with a count or label in this or any other file, the errata wins.

## Evidence classification (used everywhere)
| Label | Meaning |
|---|---|
| **OBSERVED** | Seen at runtime: screen, accessibility tree, network call (method/path/query-param names/status/value-free JSON shape) |
| **STATIC** | Found in shipped frontend JavaScript/config (routes, menus, endpoint catalogs, permissions). Not proof the feature is reachable |
| **INFERRED** | Deduced from evidence (e.g. Django from URL/field conventions) |
| **PROPOSED** | Design recommendation for the replacement |
| **UNKNOWN** | Not established; see OPEN_QUESTIONS |

Confidence: HIGH / MEDIUM / LOW. Evidence IDs (E###) are listed in `EVIDENCE_INDEX.md`.

## Folder map
| Folder | Contents |
|---|---|
| `01_PRODUCT_OVERVIEW` | scope, feature catalog (47 features), information architecture, personas, role-permission matrix |
| `02_FRONTEND_ANALYSIS` | stack, routes, screens, components, design system |
| `03_MODULE_SPECIFICATIONS` | 19 module specs (M01–M19) |
| `04_BACKEND_ANALYSIS` | service map, API inventory (171 runtime-observed after the Phase 3 crawl — 94 in Phase 1/2 + 77 from the crawl; 6,577-row method-bearing catalog in folder 13), observed API contracts, auth, integrations |
| `05_DATA_MODEL` | domain model, entity dictionary (proposed schema with provenance), relationships, Mermaid ER diagram |
| `06_WORKFLOWS` | 15 reconstructed workflows with Mermaid diagrams |
| `07_NON_FUNCTIONAL_REQUIREMENTS` | security (incl. findings), performance, scalability, reliability, deployment |
| `08_TESTING` | test strategy, measurable acceptance criteria, test case catalog |
| `09_VISUAL_EVIDENCE` | 10 redacted screenshots + index |
| `10_REA_EVIDENCE` | `bundle_analysis/` (static extractions), `sanitized_metadata/` (value-free runtime shapes, role catalogue), `captures/` (README: raw captures withheld) |
| `11_IMPLEMENTATION_PLAN` | architecture recommendation, roadmap, dependencies, priorities |
| `12_CLAUDE_CODE_HANDOFF` | master prompt, requirements, blueprint, sequence, rules, definition of done |
| `13_AUTONOMOUS_DEEP_ANALYSIS` | Phase 3 deep analysis (2026-11-23): 6,577-row endpoint catalog with resolved HTTP methods, route→component→API graph, call-site index, role/ID-list evidence, method validation — **plus an executed read-only route crawl**: 171 runtime-observed endpoints (was 94), 42 teacher routes rendered / 359 role-redirected, 72 new value-free response schemas |

## Data-handling statement
- No passwords, cookies, tokens, authorization headers or storage values were captured. The browser endpoint identifier is withheld.
- Network bodies were reduced to **value-free type shapes** inside the capture script; values were never written to disk. The single exception is non-personal configuration labels (role/level names, communication enums; E017).
- Raw REA captures and exported proprietary JavaScript were kept only in a temporary session directory, not in this folder.
- Screenshots were redacted (names, free text, avatar). Screens with personal data were not retained.
- Security observations (e.g. secrets in the client bundle) are recorded by **key name only**.

## Intellectual-property statement
This package describes functionality and generic UX patterns. It contains no proprietary source code. The replacement must use original code, branding and assets (`12_CLAUDE_CODE_HANDOFF/DEVELOPMENT_RULES.md`).
