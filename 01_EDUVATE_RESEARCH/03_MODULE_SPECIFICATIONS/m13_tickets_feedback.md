# M13 — Parent Helpdesk Tickets and Feedback

**Access for the observed teacher:** `/erp/ticket`, `/erp/ticket-dashboard` and `/erp/feedback` were **denied** (redirected to profile, OBSERVED). Specification is from STATIC evidence.

## Tickets (STATIC)
Routes: `/ticket` (list), `/ticket-view/:id`, `/ticket-config`, `/ticket-dashboard`, `/ticket-report`, `/ticket/:label/:branchIds/:startDate/:endDate/:csatScore` (dashboard drill-down), `/refund-ticket`. Student/parent nav: Communication → Parent Communication (`/ticket`), Refunds (`/refund-ticket`). Finance API `/apiV1/ticket/`.
Features: configurable categories and routing, threads, statuses, CSAT score, dashboard by label/branch/date/CSAT, reports.

## Employee grievances (HRMS "Employee Care", OBSERVED summary)
`GET recruitment /grievance-module/employee-ticket-summary/` → `{has_active_request, unread_count, pending_reply_count, pending_feedback_count, info_requested_co…}` — the staff-side equivalent (My Care / Config / Dashboard).

## Feedback (STATIC + OBSERVED)
- School feedback: tags, questions, positions, student feedback list, reply, comments, report, question-wise responses, call-feedback dashboard (`/school-feed/*`).
- Event feedback for staff: `GET /qbox/event-feedback/staff/my/pending/` (OBSERVED on every page) → pending feedback forms; students: `/student/event-feedback`.
- PTM feedback: branches/grades/sections, verify student, submit, dashboard (stats/filters/export) (`/ptm-feedback/*`).
- Parent feedback analysis dashboard (`/parent-feedback-analysis`).

Workflow: `wf12_parent_ticket.md`. ACs: TKT-01..03.
