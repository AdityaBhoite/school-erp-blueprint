# Scalability Requirements

## Observed scale signals (STATIC/OBSERVED)
- Multi-brand (≥5 brands), multi-city, many branches per brand; zones and cities group branches.
- 80 named ERP user levels (OBSERVED, E017; highest ID 96), so a large and fine-grained role catalogue.
- Analytics, notifications and reports are separated into their own services, which signals high read volume for dashboards.

## Assumed sizing for the replacement (PROPOSED; validate with stakeholders)
| Dimension | Design target |
|---|---|
| Tenants (school groups) | 1–500 |
| Branches per tenant | 1–200 |
| Students per branch | 300–5,000 |
| Total active users | 500k (students + parents + staff) |
| Peak concurrency | 8:00–9:30 local time (attendance, diary) and fee due dates; 10% of users concurrently |
| Attendance writes | 1 bulk write per section per day (or per period) |
| Notifications | fan-out to 100k recipients per announcement in < 5 min |

## Requirements
- SC-1: Stateless API servers behind a load balancer; horizontal autoscaling.
- SC-2: PostgreSQL with tenant_id on all tenant tables; indexes on (tenant_id, branch_id, academic_year_id, …); read replicas for reporting.
- SC-3: Background job queue for notifications, report generation, imports, report-card PDF generation and payment reconciliation.
- SC-4: Partition large append-only tables (attendance, audit log, notifications) by month or academic year.
- SC-5: Analytics and dashboards read from materialised views or a separate analytical store, refreshed incrementally.
- SC-6: Object storage for files; no file blobs in the database.
- SC-7: Ability to move a large tenant to a dedicated database later (tenant-routing layer) without code changes.
