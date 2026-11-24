# Architecture Blueprint

Read together with `../11_IMPLEMENTATION_PLAN/recommended_architecture.md` (rationale). This file is the **prescriptive** version.

## 1. Repository layout (pnpm + Turborepo monorepo)
```
school-erp/
├── apps/
│   ├── api/                 # NestJS modular monolith (HTTP)
│   │   └── src/modules/
│   │       ├── iam/         # auth, users, roles, permissions, sessions, audit
│   │       ├── org/         # tenant, branch, academic-year, grade, section, subject, mappings, rollover
│   │       ├── people/      # staff, students, guardians, enrolment, imports
│   │       ├── calendar/    # events, publication, holidays
│   │       ├── timetable/   # period systems, slots, publish, substitutions
│   │       ├── curriculum/  # volumes, chapters, topics, lesson plans, completion
│   │       ├── diary/       # diary entries, homework, submissions, evaluation
│   │       ├── attendance/  # student attendance, reports
│   │       ├── assessment/  # exam types, grading, assessments, papers, marks, report cards
│   │       ├── comms/       # announcements, notifications, banners, tickets, channels
│   │       ├── finance/     # fee heads, terms, plans, dues, payments, receipts, concessions, wallets
│   │       ├── transport/   # (phase 7)
│   │       └── reporting/   # dashboards, exports
│   ├── worker/              # BullMQ processors (same modules, job entrypoints)
│   └── web/                 # React + Vite SPA
│       └── src/
│           ├── shell/       # layout, rail, mega-menu, context selectors, notifications
│           ├── features/<module>/  # routes, pages, components, api hooks
│           └── ui/          # design-system components and tokens
├── packages/
│   ├── contracts/           # Zod schemas + generated OpenAPI + TS client
│   ├── permissions/         # permission catalogue, nav registry (shared FE/BE)
│   ├── db/                  # Prisma schema/migrations (or Drizzle), seeders
│   └── config/              # eslint, tsconfig
└── docs/                    # plan, progress, ADRs
```

## 2. Module internal structure (backend)
```
modules/<name>/
  <name>.module.ts
  api/          controllers (thin), DTO = zod schemas from @contracts
  application/  use-cases/services (transaction boundaries, authz checks via PolicyService)
  domain/       entities, value objects, state machines, calculators (pure, unit-tested)
  infra/        repositories (Prisma), external adapters
  events/       domain events published via outbox
```
Rules: controllers → application → domain; infra implements repository interfaces; other modules depend only on `application` public interfaces exported in `index.ts`.

## 3. Request pipeline
1. `TenantMiddleware`: resolve tenant from host/subdomain → `req.ctx.tenantId`; `SET LOCAL app.tenant_id` in the transaction.
2. `AuthGuard`: verify the access cookie → `req.ctx.user`.
3. `PermissionGuard`: `@RequirePermission('attendance.mark')` metadata.
4. `ScopeService` in use-cases: `assertInScope(user, {branchId, classSectionId, subjectId, studentId})`.
5. `ZodValidationPipe` for body/query/params.
6. Audit interceptor for mutating routes (before/after snapshot provided by the use-case).
7. Error filter → `{error:{code,message,details,requestId}}`.

## 4. API conventions
- Base `/api/v1`. Resources plural, kebab-case. Nested only one level (`/class-sections/{id}/students`).
- List: `GET /resource?page=1&pageSize=50&sort=-createdAt&filter[...]` → `{data:[...], page:{number,size,total}}`.
- Get: `GET /resource/{id}` → `{data:{...}}`. Create `POST` → 201 `{data}`. Update `PATCH`. Delete `DELETE` (soft) + `POST /resource/{id}/restore`.
- Commands as sub-resources: `POST /calendar/publications/{yearId}/publish`, `POST /assessments/{id}/marks:publish`, `POST /attendance/bulk`.
- Idempotency: `Idempotency-Key` header on POST for payments, bulk attendance and marks.
- Dates: ISO 8601; dates without time as `YYYY-MM-DD`; times in the tenant time zone with an explicit `tz` setting per tenant.
- Errors: 400 malformed, 401 unauthenticated, 403 forbidden, 404 not found (also cross-tenant), 409 conflict, 422 validation (field errors), 429 rate limit.

## 5. Data conventions
- Postgres; `id uuid` (UUIDv7) primary keys; `tenant_id uuid not null` on all tenant tables with RLS policy `tenant_id = current_setting('app.tenant_id')::uuid`.
- `created_at, updated_at, created_by, updated_by, deleted_at` on mutable tables; `version int` for optimistic locking on editable aggregates.
- Enums as Postgres enums or check constraints; money as `numeric(12,2)` with a currency per tenant; never floats.
- Append-only tables (attendance history, audit, payments, wallet txns) are never updated in place.

## 6. Frontend conventions
- TanStack Router file routes per feature; route `beforeLoad` checks the permission from `@permissions` (UX only).
- TanStack Query for server state (dedupes requests); no global client state other than session/context (Zustand or Context).
- Shell context: current branch + academic year in the URL search params or context store; all queries include them.
- Shared UI: `PageHeader`, `FilterBar` (required filters with `*`), `DataTable` (server pagination, column chooser, export), `EmptyState`, `EntityDrawer`, `FormSection`, `StatusTag`, `MonthCalendar`, `WeekStrip`, `PeriodCard`, `ApprovalTimeline`, `MaskedField`.
- Theming via CSS variables from tenant branding.

## 7. Async & integrations
- Outbox table → worker → handlers (notifications fan-out, SMS/WhatsApp/email, push, PDF generation, imports/exports, payment reconciliation).
- Provider interfaces: `EmailProvider`, `SmsProvider`, `WhatsAppProvider`, `PushProvider`, `PaymentGateway`, `VideoMeetingProvider`, `StorageProvider`. Ship console/dev adapters plus one real adapter each.

## 8. Environments & CI
GitHub Actions: install → lint → typecheck → unit → integration (Testcontainers) → build → e2e (Playwright against a docker-compose stack) → OpenAPI diff → secret scan → docker build. Deploy staging on main; production on tag.
