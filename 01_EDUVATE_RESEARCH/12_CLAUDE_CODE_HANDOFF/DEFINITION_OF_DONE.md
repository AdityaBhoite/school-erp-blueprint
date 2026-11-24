# Definition of Done

A feature/step is **done** only when all of the following hold:

## Functional
- [ ] All acceptance criteria IDs for the step (see `08_TESTING/acceptance_criteria.md`) are implemented and demonstrably pass.
- [ ] The workflow(s) in `06_WORKFLOWS/` for the feature work end-to-end, including the documented exceptions and state transitions.
- [ ] Empty, loading, error and no-permission states are implemented for each screen.

## Quality
- [ ] Unit + integration tests written; coverage ≥ 85% for domain modules touched; all tests green in CI.
- [ ] Authz matrix updated: new endpoints have policies; negative tests pass (403/404 across roles and tenants).
- [ ] OpenAPI regenerated; contract tests green; no breaking change without versioning.
- [ ] Lint, type-check and formatting clean; no TODOs without a linked issue.
- [ ] Accessibility: axe has 0 serious/critical issues on new screens; keyboard navigation works.
- [ ] Responsive at 390 / 768 / 1440 px.
- [ ] Performance budgets from `07_NON_FUNCTIONAL_REQUIREMENTS/performance.md` met for new endpoints/screens.

## Security & data
- [ ] Inputs validated server-side; outputs minimised; PII masked where specified.
- [ ] Audit entries are written for state-changing operations.
- [ ] Migrations are reversible-safe (expand/contract) and tested on seeded data.
- [ ] No secrets or real personal data in code, tests, fixtures or logs.

## Documentation
- [ ] `docs/progress.md` updated; ADRs recorded for decisions on open questions.
- [ ] User-facing help text and permission descriptions added for new features.
- [ ] Seed data extended so the feature can be demoed.
