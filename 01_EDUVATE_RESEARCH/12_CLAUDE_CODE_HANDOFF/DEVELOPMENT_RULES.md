# Development Rules (non-negotiable)

## Intellectual property and privacy
1. Independent implementation only. Do not copy, transcribe or paraphrase proprietary source code, CSS, copy text, icons, images, logos or brand names of the studied product. Functional behaviour and generic UX patterns (tables, filters, calendars, drawers) may be re-implemented.
2. Product name, logo and default colour palette must be original. Branding is tenant-configurable.
3. Never use real personal data. Seeds and fixtures are synthetic (use faker with a fixed seed). No real school or person names.
4. Do not attempt to access the original system, its APIs or its hosts.

## Security
5. Authorization on every endpoint via guards/policies using the permission catalogue and scopes; deny by default.
6. Tenant context set per request; RLS enabled on all tenant tables; integration tests verify isolation.
7. No secrets in the repo or the frontend bundle; use environment variables and a secret manager; CI fails on secret detection.
8. Tokens are in httpOnly Secure cookies; CSRF protection for state-changing requests.
9. Validate all inputs with Zod (API) and mirror them in forms; never trust client-side checks.
10. Audit-log all writes to people, attendance, marks, fees, permissions and settings.
11. PII minimisation: list endpoints return only the fields needed; masked sensitive fields; reveal is permissioned and audited.
12. File uploads: allow-list MIME and extension, size limits, antivirus job, signed URLs.

## Code quality
13. TypeScript strict; no `any` in domain code; ESLint and Prettier clean.
14. Module boundaries: a module may only call another module through its public service interface (no cross-module table access). Enforce with lint rules (eslint-plugin-boundaries) or an architecture test.
15. Every API has a Zod request/response schema and appears in the generated OpenAPI document.
16. Database changes only via migrations; migrations are forward-only and backward-compatible.
17. Background work (notifications, exports, PDFs, imports) runs in workers, never in request threads.
18. Consistent API conventions (see ARCHITECTURE_BLUEPRINT §API); consistent naming (camelCase JSON, kebab-case URLs).
19. UI: use shared components (DataTable, FilterBar, PageHeader, EntityDrawer, FormSection, EmptyState, ConfirmDialog); no one-off styling; design tokens only.
20. i18n: all user-facing strings go through the i18n layer (English default).

## Testing
21. Each acceptance criterion ID has at least one test referencing the ID in its name.
22. Calculators and state machines get unit tests with edge cases (holidays, leap years, partial payments, rounding).
23. Authz matrix tests are generated from the permission catalogue and fail when a new endpoint lacks a policy.
24. E2E tests for each persona journey in `08_TESTING/test_strategy.md`.

## Process
25. Small PRs per feature; conventional commits; CHANGELOG updated.
26. ADRs for every decision on an UNKNOWN/OPEN item.
27. Keep `docs/progress.md` current.
