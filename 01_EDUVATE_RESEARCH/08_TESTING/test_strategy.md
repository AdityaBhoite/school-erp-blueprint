# Test Strategy (replacement product)

## Principles
- All tests use **synthetic data** only (seed: 1 tenant "Demo School Group", 2 branches, academic year 2026-27, grades 1–10 with sections A–B, 40 students per section, about 30 staff per branch, about one guardian per student with roughly 15% siblings sharing guardians; corrected from "60 guardians" by audit ER-48). Never import data from the original.
- Every acceptance criterion in `acceptance_criteria.md` maps to at least one automated test (unit, integration or e2e) listed in `test_case_catalog.md`.
- Authorization and tenant isolation are tested **negatively** for every endpoint (a generated test matrix: role × endpoint × scope).

## Test levels
| Level | Tooling | Scope | Gate |
|---|---|---|---|
| Static | ESLint, TypeScript strict, Prettier, dependency audit, secret scan, bundle check (no secrets) | all code | PR |
| Unit | Vitest/Jest | domain services, validators, fee/attendance/grade calculators, permission evaluation | PR, ≥85% lines in domain modules |
| Contract | OpenAPI schema diff + Zod response validation | all endpoints | PR |
| Integration | Supertest + Testcontainers (Postgres, Redis) | API + DB + RLS + jobs | PR |
| Authorization matrix | generated tests from the permission catalogue × seeded role users | every endpoint | PR |
| E2E | Playwright (desktop 1440 and mobile 390 viewports) | critical journeys per persona | main branch, nightly |
| Accessibility | axe-core in Playwright | all pages | nightly; 0 serious violations |
| Performance | k6 | attendance bulk, diary list, dashboards, fee payment webhook | pre-release |
| Security | OWASP ZAP baseline, dependency CVEs, manual review of authz | API | pre-release |
| Data migration/rollover | dry-run tests with fixtures | year rollover, promotion | pre-release |

## Critical e2e journeys
1. Teacher: login → calendar → diary (write classwork/homework for today's period) → attendance (mark section) → view timetable.
2. Parent: login → see child's homework, attendance, announcements → pay a fee term → download receipt → raise ticket → give CSAT.
3. Student: login → today's timetable → submit homework → view marks/report card.
4. Admin: create year → grades/sections → import students → assign class teacher → publish timetable.
5. Coordinator: create assessment → upload marks → publish → generate report cards.
6. Accountant: configure fee plan → assign → record cheque → approve concession → reconciliation export.

## Environments and data
- Ephemeral DB per integration-test worker; e2e against staging with nightly reseed.
- Time-dependent logic (due dates, attendance windows) tested with an injectable clock.
