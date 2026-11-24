# CRM Application Analysis (Priority 3)

**Date:** 2026-11-24 · Part of Stage 1B.
**Method:** Mined from the **existing** derived metadata (`API_ENDPOINT_CATALOG.csv`, `API_CALLSITE_INDEX.csv`, `ROUTE_COMPONENT_MATRIX.csv`) — **no re-download, no repeat of the 1,954-file analysis.** This is a CRM-focused synthesis never previously extracted into its own document.

> **Evidence note:** `service=CRM` has **610 catalogued endpoints**, **803 call sites**, and **344 endpoints with value-free response-field names**; 3 are runtime-observed (the teacher never had a CRM access flag, so CRM is almost entirely STATIC). The CRM *frontend router* present in the shared bundle is thin (19 routes, mostly public auth/partner/enquiry pages + a rule/status builder) — the **logged-in CRM UI is likely a separate surface** not in the analysed bundle. So this maps the CRM **API surface and module structure** with high confidence, and flags the CRM UI as a remaining gap.

## 1. CRM modules (from endpoint first-segment grouping)

| Module | Endpoints | What it does (from path names) |
|---|---|---|
| **lead-mgmt** | 254 | Core B2C lead management: lead journey config, lead bulk-upload, parent-verification, **AI features** (ai-transfer, ai-prompts, ai-knowledge-base, ai-pronunciations), click-to-call |
| **events-mgmt** | 90 | Events & partnership management: approval-matrices, assign-event-user, assign-route, **associate-payment-config/request** with a full approval workflow (pending-approvals, approve, approval-logs), tie-ups, partner-dashboard, event-bulk-upload |
| **objective-mgmt** | 64 | Incentives/objectives for sales staff: admission-based incentive rules, calculation, payout, history, status, adjustments |
| **budget-planning** | 44 | Budget planning: actuals (+bulk-upload), **admission-projections** (base-year-data, settings, per-id override) |
| **user-mgmt** | 42 | CRM-side user management (distinct from ERP M02) |
| **expense-mgmt** | 32 | Expense management: approval-matrices, budgets, budget-amounts, purchase-requisitions, provisional/approved payment-requests |
| **b2b-lead-mgmt** | 29 | B2B lead management: b2b-lead, geo-search, appointment-activity, bulk-upload (owner/source update) |
| **campaign-mgmt** | 26 | Marketing campaign management |
| **mongoapp** | 4 | Notifications service (Mongo-backed) |
| *(utilities)* | ~25 | global-search, click-to-call, gmail-login, ai-assistant, sync-recording, school-gate, school-feed, zone/city/branch-view, academic-year, access-token |

## 2. Route structure (frontend, from ROUTE_COMPONENT_MATRIX, app=crm)
19 routes, almost all **public/unauthenticated or token-handoff**:
- Auth/public: `/login`, `/m/login`, `/forgot-password`, `/reset-password`, `/sso/:token_data/login`, `/student-partner-login`
- Partner/enquiry/public: `/become-partner`, `/partner-welcome`, `/thank-you-page`, `/enquiry-form/enquiry-form-parent/:Id`, `/parent-verification/:token`
- Rules builder: `rule/new`, `rule/:ruleId`, `status/:statusId`, `status/:statusId/rule/new`, `status/:statusId/rule/:ruleId` (chunk `RuleForm-ykixkr0M.js`) — a lead-status/rule configuration UI
- Vendor shell: `vendor-react-BsSPVCJn.js`
**Interpretation:** the shared app serves CRM's *public* funnel pages and a rules builder; the authenticated CRM workspace (lead inbox, dashboards) is not represented here → **CRM UI is a gap** (see §6).

## 3. Components / chunks
Only `RuleForm-*.js` and the React vendor chunk are tied to CRM routes in the shared bundle. The remaining CRM behaviour is reached through API services, not local route chunks — consistent with a separate CRM front end.

## 4. API services (representative, value-free)
- **Leads:** `/lead-mgmt/lead-journey-config`, `/lead-mgmt/lead-bulk-upload`, `/lead-mgmt/ai-transfer`, `/lead-mgmt/ai-prompts`, `/lead-mgmt/ai-knowledge-base`, `/lead-mgmt/parent-verification`
- **B2B:** `/b2b-lead-mgmt/b2b-lead`, `/b2b-lead-mgmt/b2b-lead-geo-search`, `/b2b-lead-mgmt/appointment-activity/{id}`
- **Events/partnerships:** `/events-mgmt/associate-payment-config/{id}/approve`, `/events-mgmt/assign-route`, `/events-mgmt/partner-dashboard`, `/events-mgmt/tie-up`
- **Budget:** `/budget-planning/admission-projections/{id}/override`, `/budget-planning/actuals/bulk-upload`
- **Incentives:** `/objective-mgmt/admission-based-incentive/{id}`, `/objective-mgmt/admission-incentive-payout-list`
- **Expenses:** `/expense-mgmt/budgets/{id}`, `/expense-mgmt/purchase-requisitions`, `/expense-mgmt/approval-matrices/{id}/set-request-type`
- **Methods:** 243 GET, 102 POST, 46 PUT, DELETE present → a full CRUD surface with multi-step **approval matrices** across events, expenses and budgets.

## 5. Data entities (INFERRED from path/field names — not a schema)
Lead, B2B-Lead, LeadJourney/Status/Rule, AppointmentActivity, Event, Tie-up/Partner, AssociatePaymentConfig/Request, ApprovalMatrix, Budget/BudgetAmount/Actual, AdmissionProjection, IncentiveRule/Payout, Expense/PurchaseRequisition, Campaign, CRM User, Notification. **Fields are names only; no values, no server schema.**

## 6. Customer-management & communication workflows (INFERRED)
- **Admissions funnel:** enquiry/lead capture (public forms) → lead journey/status rules → AI-assisted follow-up (transfer/prompts/pronunciations, click-to-call) → conversion → **admission projections & actuals** (budget-planning) → **counselor incentives/payouts** (objective-mgmt).
- **Partnerships/events:** partner onboarding (`/become-partner`) → event setup → associate-payment config/request → multi-level approval → payout.
- **Expense/budget governance:** purchase requisitions and expenses routed through approval matrices against budgets.
- **Communication:** notifications via `mongoapp`; WhatsApp (separate `WHATSAPP` service, 28 endpoints) and chat (`CHAT`, 28) are shared platform services; `click-to-call` and `sync-recording` suggest telephony integration.
> These are **inferred flows**, not observed. Write behaviour, approval rules, and state machines are WRITE-REQ/SERVER-ONLY.

## 7. CRM vs school-ERP boundary, and shared platform services
- **Distinctly CRM (business/sales side, not school operations):** lead-mgmt, b2b-lead-mgmt, events-mgmt, campaign-mgmt, objective-mgmt (incentives), budget-planning, expense-mgmt. These serve **sales/admissions/partnership operations**, not teaching/learning.
- **Shared platform services** (used by ERP, HRMS and CRM alike): authentication/SSO (`/sso/:token_data/login`, access-token, gmail-login), **global-search**, **notifications** (mongoapp + FCM), **WhatsApp/chat** messaging, user-mgmt primitives, and the common React vendor shell. These are the legitimate reuse candidates for a multi-tenant platform.
- **Overlaps worth noting for Stage 3:** `objective-mgmt/admission-*` and `budget-planning/admission-projections` tie the CRM to the school's admissions numbers; `crm-child-fee-link` (a FIN endpoint) links CRM leads to student fees. These are integration seams, not shared code.

## 8. What remains a CRM gap
- The **authenticated CRM workspace UI** (dashboards, lead inbox, pipelines) is not in the analysed bundle (only public funnel + rules builder are). Observing it needs a **CRM-enabled account** (this teacher account had no CRM flag) — read-only, value-free, same protocol as the role crawls.
- All **write payloads, approval rules, AI-model behaviour, and state transitions** are WRITE-REQ/SERVER-ONLY.

See `ROLE_ACCESS_REQUIREMENTS.md` (a CRM-enabled account would unlock §8) and `WORKFLOW_EVIDENCE_GAPS.md`.
