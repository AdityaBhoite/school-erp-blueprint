# M01 — App Shell, Navigation and Context

**Evidence:** E002, E010, E013–E016 (OBSERVED), E005 (STATIC nav config). Screenshots: `academic-calendar-month-view.png`, `attendance-mark-empty.png`, `diary-teacher-week.png`.

## Navigation
- Left icon rail with a tooltip/accessible name per category; categories filtered by the user's permissions. The observed teacher sees: Search, Academics, Comms, Dashboard, HR & Hiring, Payroll, People, Tools.
- Clicking a category navigates to its default route; a mega-menu lists sub-sections → items. "Expand sidebar" toggles labels.
- Cross-app items (ERP ↔ HRMS ↔ CRM) cause a full page load in the original; the replacement uses a single SPA.

## Top bar (left → right)
| Control | Behaviour | Evidence |
|---|---|---|
| Network-quality badge ("Excellent (10.0 Mbps)") | Shows measured connection quality | O |
| Search type select (`CAND`, …) + search box ("Enter Candidate ID") | Global lookup by type | O |
| Branch select | Changes data scope for all screens | O |
| Academic year select ("2026-27") | Changes session scope | O |
| Timetable icon, Calendar icon | Quick open of the user's timetable/calendar | O |
| Notifications bell (count badge) | Unread counts from `/mongoapp/notifications/counts/` (`total_unread`, `approval_unread`) and a paginated list | O (count and list APIs) / I (opening the list: the tour never clicked) |
| Avatar (initial) → profile menu | Profile, change password, logout | O (avatar) / I (menu items: a navigate-only tour cannot open a menu; LOW) |
| Floating "Help — Ask our Assistant" | Opens the AI help assistant; dismissible | O |

## Shell calls observed during the tour (to consolidate in the replacement)
Per-endpoint scope, OBSERVED in E016 (audit ER-08): analytics `POST /track/session` and `/track/batch` on 39 accessible screens; `GET /mongoapp/notifications/counts/` and `GET /mongoapp/notifications/?page&page_size` on 38; finance `GET /apiV1/expense-delegation-context/?erp_id`, `GET /event-feedback/staff/my/pending/`, `POST /erp_user/fcm-register/` and Clarity beacons on the 24 accessible ERP screens; `GET /home-banner/announcement/active/`, `GET /home-banner/promotional/active/` and HRMS `GET /grievance-module/employee-ticket-summary/` only on `/erp/user-profile`; HRMS `GET /payroll/kp/access/` and `GET /lms/training-reminders/` on the 15 HRMS screens. Per-page call counts and duplicates were not retained (UNVERIFIABLE, LOW). **Replacement:** one `GET /api/v1/me/bootstrap` + `GET /api/v1/notifications/summary`.

## Behaviour rules
- Unauthorized route → original silently redirects to `/erp/user-profile` (O). Replacement: "No access" page (P).
- Context (branch, year) persists across navigation (O).

## Replacement acceptance
RBAC-01, RBAC-05, COM-03, PR-5.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
Observed on **all 42 rendered routes** — the app-shell/bootstrap call set fired on every authenticated ERP navigation (not route-specific):
- `GET ERP /mongoapp/notifications` and `/mongoapp/notifications/counts` — notification bell
- `GET ERP /event-feedback/staff/my/pending` (teacher) / `/event-feedback/my/next-nudge` — the recurring feedback nudge
- `POST ERP /erp_user/fcm-register` — FCM push-token registration on load
- `GET FIN /apiV1/ticket` + `/apiV1/{id}` — support-ticket badge (served by the **finance** service)
- `GET CRM /lead-mgmt/pending-actions` — CRM pending-actions badge
- `POST ANALYTICS /api/v1/track/session` and `/track/batch` — first-party analytics (`analytics.letseduvate.com`)

All value-free, all GET/POST, all returning 200/201 for a teacher. A replacement shell should expect this fan-out per page; consider consolidating it. Source: `13_AUTONOMOUS_DEEP_ANALYSIS/ROUTE_CRAWL_RESULTS.csv`, `E019_phase3_crawl_api_shapes.json`.
