# 02 — Existing Build Analysis (Stage 2)

*Status: **framework ready — awaiting the existing project's location.** No analysis has begun.*

This folder holds the independent analysis of your existing in-house ERP. The **method and output structure are fixed below** so the work can start the moment you provide the source; nothing here is pre-judged or assumed, and the analysis will stand entirely on the existing build's own code and evidence — **not** on parity with Eduvate.

## What you need to provide to start

1. The **directory path or repository URL** of the existing ERP.
2. Whether it is a monorepo or split repos; which folders are frontend / backend / infra.
3. How to run it (optional, for a later read-only runtime look): start commands, env var *names* (never values/secrets), and a dev/seed database if one exists.
4. Anything off-limits (paths to skip, data not to read).

## Ground rules

- **Read-only.** The existing source is **never modified** during assessment. No commits, no refactors, no "fixes."
- **No secrets leave the machine.** Credentials, tokens, `.env` values, and personal data are never copied into this repo or sent anywhere. Findings cite file paths and structure, not secret values.
- **Evidence labels** carry over from Stage 1: OBSERVED (ran/saw it) / STATIC (read in code) / INFERRED / UNKNOWN. No guessed behaviour presented as fact.
- Independent: the existing build is judged on its own merits before any comparison.

## Analysis dimensions and output documents

Each dimension below becomes one document in this folder when Stage 2 runs:

| # | Dimension | Output file | What it will capture |
|---|---|---|---|
| 1 | Frontend architecture | `01_frontend_architecture.md` | Framework, structure, routing, state management, build tooling, component organisation |
| 2 | Backend architecture | `02_backend_architecture.md` | Language/framework, service boundaries, request lifecycle, background jobs, deployment model |
| 3 | Database schema | `03_database_schema.md` | Engine, tables/collections, keys, relationships, migrations, multi-tenancy approach (if any) |
| 4 | Authentication & authorization | `04_auth.md` | Login mechanism, session/token model, role model, where access is enforced (client vs server) |
| 5 | Implemented modules | `05_modules.md` | Every module/feature actually present, and its completeness |
| 6 | APIs | `06_apis.md` | Endpoints, methods, request/response shapes, conventions, versioning |
| 7 | UI & UX | `07_ui_ux.md` | Screen inventory, design system/tokens, navigation, accessibility, responsiveness |
| 8 | Business workflows | `08_workflows.md` | End-to-end flows implemented (admissions, attendance, fees, etc.) and their state machines |
| 9 | Data models | `09_data_models.md` | Domain entities, their fields, and relationships as the code defines them |
| 10 | Technical debt | `10_technical_debt.md` | Dead code, duplication, TODO/FIXME, risky patterns, outdated dependencies |
| 11 | Performance & security | `11_performance_security.md` | N+1s, missing indexes, auth gaps, injection surfaces, secrets handling, dependency CVEs |
| 12 | Functionality status | `12_functionality_status.md` | What works, what is partial, what is stubbed/incomplete |

Plus a `00_EXISTING_BUILD_SUMMARY.md` tying the twelve together with an overall maturity read.

## What this stage does **not** do

- It does not modify, run migrations against, or deploy the existing build.
- It does not copy proprietary source or secrets into this repo.
- It does not decide what to keep or discard — that is **Stage 3** ([`../03_COMPARATIVE_ANALYSIS/`](../03_COMPARATIVE_ANALYSIS/)).
- It does not begin building the new product.

See [`../PROJECT_ROADMAP.md`](../PROJECT_ROADMAP.md) and [`../CONTINUATION_ASSESSMENT.md`](../CONTINUATION_ASSESSMENT.md).
