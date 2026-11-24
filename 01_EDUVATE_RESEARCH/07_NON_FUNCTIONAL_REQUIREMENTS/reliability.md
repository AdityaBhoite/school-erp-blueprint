# Reliability Requirements (PROPOSED)

| ID | Requirement |
|---|---|
| RL-1 | Availability SLO 99.9% monthly during school hours (06:00–20:00 tenant local time); planned maintenance outside these hours. |
| RL-2 | RPO ≤ 15 minutes (continuous WAL archiving / PITR); RTO ≤ 4 hours. |
| RL-3 | Daily encrypted backups, 35-day retention; monthly restore drills. |
| RL-4 | Idempotent write APIs for payments, attendance and marks (idempotency keys); payment webhooks deduplicated. |
| RL-5 | Graceful degradation: if notifications/analytics fail, core flows (attendance, diary, fees) still work. |
| RL-6 | Background jobs are retried with exponential backoff and dead-letter queues; failures are visible in an admin "jobs" screen. |
| RL-7 | Optimistic concurrency (version column) on editable records (timetable, marks, fee plans) to prevent lost updates. |
| RL-8 | Academic-year rollover is transactional and resumable, with a dry-run preview (the original exposes "section carry-forward preview/apply/history"). |
| RL-9 | Soft delete with restore for master data (the original has delete/restore pairs for branches, grades, sections, subjects, mappings, roles, modules). |
| RL-10 | Observability: structured logs with request IDs, metrics (RED), traces, error tracking, uptime checks, and alerts on SLO burn. |
