# Deployment Requirements

## Observed (INFERRED from config, HIGH)
- Google Cloud Platform (asia-south1 Cloud Functions, GCS buckets), CDNs (`acad-cdn`, `mgmt-cdn`, CloudFront for e-books).
- One host per brand; separate service hosts (finance, recruitment, marketing, mgmt, reports, classes, events, analytics, chat).
- Staging and pre-prod environments exist for at least one service (`edunav-stage`, `edunav-preprod`).

## Proposed for the replacement
| Area | Recommendation |
|---|---|
| Environments | local → dev → staging (anonymised seed data) → production; preview environments per PR for the frontend |
| Packaging | Docker images; one backend image (modular monolith) + worker image (same code, different entrypoint) + frontend static bundle |
| Runtime | Managed containers (Cloud Run / ECS Fargate / Kubernetes when needed) |
| Database | Managed PostgreSQL 16+ with PITR, read replica for reporting |
| Cache/queue | Redis (cache, rate limits, job queue) or managed queue (SQS/Cloud Tasks) |
| Storage | S3-compatible object storage + CDN, signed URLs |
| Secrets | Cloud secret manager; never in the repo or the frontend |
| CI/CD | GitHub Actions: lint, type-check, unit, integration, e2e (Playwright), SAST, dependency audit, migration check; trunk-based with protected main; blue/green or rolling deploys |
| Migrations | Forward-only, backward-compatible (expand/contract); run as a release step |
| Data residency | India region by default (DPDP); configurable per tenant |
| Observability | OpenTelemetry → managed APM; Sentry-style error tracking; uptime monitoring |
| Mobile | PWA first; native apps (React Native/Flutter) later reusing the same API |
