> **SUPERSEDED (2026-11-23).** Use `../CLAUDE_CODE_MASTER_PROMPT.md` (v2) instead. This copy is kept unchanged below for reference only.
> Reason: the documentation audit (`../AUDIT_REPORT.md` §B B-04, errata ER-44) found that this version is not self-contained and treats STATIC findings as requirements.

# CLAUDE CODE MASTER PROMPT — Build an independent School ERP

You are building a **new, independently implemented, production-ready school ERP**. Its functional design comes from a documented reverse-engineering study of an existing commercial K-12 school-chain platform. You do **not** have, and must not seek, access to that original system, its code, data or assets.

## 0. Mandatory reading (in this order, before writing any code)
Read every file below completely. Summarise your understanding back in a short plan before starting Phase 0.

1. `../README.md` — what this package is and how evidence is classified.
2. `../EXECUTIVE_SUMMARY.md`
3. `SYSTEM_REQUIREMENTS.md` (this folder) — functional and non-functional requirements, with IDs.
4. `ARCHITECTURE_BLUEPRINT.md` (this folder) — stack, structure and conventions you must follow.
5. `IMPLEMENTATION_SEQUENCE.md` (this folder) — the build order with per-step deliverables.
6. `DEVELOPMENT_RULES.md` (this folder) — non-negotiable rules (IP, privacy, security, quality).
7. `DEFINITION_OF_DONE.md` (this folder).
8. `../01_PRODUCT_OVERVIEW/` — `product_scope.md`, `feature_catalog.md`, `information_architecture.md`, `user_personas.md`, `role_permission_matrix.md`.
9. `../03_MODULE_SPECIFICATIONS/module_index.md` and **every** module spec it links to.
10. `../06_WORKFLOWS/workflow_index.md` and **every** workflow file.
11. `../05_DATA_MODEL/` — `domain_model.md`, `entity_dictionary.md`, `relationships.md`, `er_diagram.mmd`.
12. `../04_BACKEND_ANALYSIS/` — `backend_architecture.md`, `api_inventory.md`, `api_contracts.md`, `authentication.md`, `integrations.md`.
13. `../02_FRONTEND_ANALYSIS/` — `design_system.md`, `screen_inventory.md`, `component_inventory.md`, `route_inventory.md`, `technology_stack.md`.
14. `../07_NON_FUNCTIONAL_REQUIREMENTS/*`.
15. `../08_TESTING/` — `test_strategy.md`, `acceptance_criteria.md`, `test_case_catalog.md`.
16. `../11_IMPLEMENTATION_PLAN/*`.
17. `../OPEN_QUESTIONS.md` — decisions you must not silently assume; use the proposed defaults and record them in `docs/decisions/`.
18. `../09_VISUAL_EVIDENCE/screen_reference_index.md` — look at the screenshots for UX patterns only.

Evidence labels in these documents: **OBSERVED** / **STATIC** / **INFERRED** / **PROPOSED** / **UNKNOWN**. Treat OBSERVED/STATIC as product requirements to match functionally, INFERRED as defaults you may refine, PROPOSED as the target design, and UNKNOWN as open decisions (choose the documented default and log the decision).

## 1. What to build
A multi-tenant school ERP for school groups with multiple branches, implemented as a **modular monolith** (TypeScript, NestJS, PostgreSQL with RLS, Redis/BullMQ, React + Vite + TanStack + Ant Design), covering in order:
Foundations → Org & People → Daily Teaching Core (calendar, timetable, diary/homework, attendance) → Communication → Assessment & Report Cards → Fees → Lifecycle/Admissions → Extended modules.

## 2. How to work
- Follow `IMPLEMENTATION_SEQUENCE.md` step by step. Do not start a step until the previous step's Definition of Done is met.
- For each step: write/extend the data model and migration → API with Zod schemas + OpenAPI → authorization rules → UI → tests mapped to acceptance-criteria IDs → docs.
- Keep a `docs/progress.md` checklist that mirrors the sequence and tick items with links to tests.
- Write tests first for calculators and state machines (attendance %, fees/dues, fines, grading, ticket and approval states).
- Use synthetic seed data only (`seed/` scripts). Never use real names, phone numbers or IDs from any source.
- When requirements conflict or are UNKNOWN, choose the documented PROPOSED default, record an ADR in `docs/decisions/NNNN-title.md`, and continue.

## 3. Hard constraints
- Original code, logos, brand names (Orchids, Eduvate, letseduvate), colours as brand identity, copy text, icons and assets must **not** be copied. Build an original product name and visual identity (tenant-brandable).
- No secrets in frontend code. Authorization is enforced server-side for every endpoint.
- Tenant isolation is tested for every endpoint.
- Accessibility WCAG 2.1 AA; responsive down to 390 px.

## 4. First actions
1. Read the documents (section 0).
2. Produce `docs/plan.md`: modules, phases and the first two weeks of tasks.
3. Scaffold the monorepo per `ARCHITECTURE_BLUEPRINT.md`, CI included.
4. Implement Phase 0 (auth, tenancy, RBAC, audit, shell) with tests AUTH-*, TEN-*, RBAC-*.
