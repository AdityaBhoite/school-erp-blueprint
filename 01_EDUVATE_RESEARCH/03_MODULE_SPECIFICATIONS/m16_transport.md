# M16 — Transport

**Access:** not visible to the observed teacher. STATIC (menu + 50+ routes).

| Area | Routes (abridged) |
|---|---|
| Hub & dashboards | `/transport-dashboard`, `/transport-operations`, `/transport-analytics`, `/transport-management-pnl`, `/transport-bus-pnl(-dashboard)`, `/transport-branch-pnl`, `/transport-capacity-forecast`, `/transport-bus-utilization` |
| Masters | `/transport-routes`, `/transport-stop-points`, `/transport-buses`, `/transport-vehicle-master`, `/transport-bus-fleet`, `/transport-drivers`, `/transport-staff-profiles`, `/transport-fuel-rates`, `/vehicles-list-form`, `/stoppage-form`, `/trip-schedule-form`, `/route-builder(/:id)` |
| Optimisation | `/transport-optimization`, `/transport-route-optimizer`, `/transport-route-solution/:id`, `/transport-parking-optimizer`, `/branch-wise-stop-point-analysis`, `/transport-cross-branch-sharing` |
| Tracking | `/transport-gps`, `/transport-live-tracking`, `/transport-movement-history`, `/iframe/track/:busNo`, `/iframe/track/trip/:tripScheduleId`, `/student-bus-attendance/:id/:startDate/:endDate` |
| Fees | `/transport-fee-plan`, `/transport-fee-term`, `/transport-fee-group…`, `/transport-fee-slabs`, `/transport-fee-assign`, route-/plan-/grade-/installment-wise reports, `/transport-fee-follow-up`, `/transport-fee-plan-change`, transport payments and receipts |
| Requests | `/transport-inquiry`, `/transport-interest-form`, `/transport-form-interest`, `/transport-preference-landing`, `/bus-transfer-request`, transport opt-out and wallet approvals |
| Compliance | `/transport-rc-compliance` (registration certificate), `/bus-cleanliness`, `/transport-audit`, `/transport-bus-costs`, `/transport-bus-mileage`, `/transport-bus-reports` |
| Modules | Bus Module, DG Module (generator) |

Replacement (Could): routes, stops (geo), vehicles (documents with expiry), drivers/attendants, trips, student allocation and requests, transport fee plans (distance/stop slabs), parent live-tracking via GPS-provider integration, bus attendance.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
All four transport view routes **rendered** for a teacher: `/transport-buses`, `/transport-drivers`, `/transport-routes`, `/transport-stop-points`. Route-specific endpoints observed:
- **Transport data is served by the FINANCE service** (`orchids.finance.letseduvate.com`), runtime-confirmed: `GET FIN /api/v2/transport/transport-stop-point`, `GET FIN /api/v2/transport/get-inquiry-count`
- `GET ERP /erp_user/user-mgmt/user-grades` — scope selector

This resolves an earlier ambiguity: the core transport APIs live under the **finance** service's `/api/v2/transport/*` namespace, not the ERP service. `/transport-drivers` returned a **404** on one sub-resource. Build note: model transport against the finance service boundary. Status: RUNTIME_OBSERVED. (Previously, core transport behaviour APIs were flagged "not recovered" — this partially closes that.)
