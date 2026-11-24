# Authentication, Session and Authorization Mechanics

Evidence: E008/E009 (HRMS bundle), E010x (ERP bundle), E016 (runtime tour). No credentials, tokens, cookies or header values were captured or stored. Mechanisms are described at a conceptual level.

## 1. Identity
| Fact | Class | Confidence |
|---|---|---|
| Every user has an **ERP ID** (`erp_id`), format `<digits>_<BRANDCODE>` (for example `…_OIS` for Orchids International School). Used as username and as cross-service key. | OBSERVED (profile shows `@<erp_id>`) | HIGH |
| Staff also have an official email `<erp-digits>@<school-domain>`. | OBSERVED | HIGH |
| Separate login endpoints exist for staff and students: `POST /erp_user/user-mgmt/staff-login/`, `/erp_user/user-mgmt/student-login/`, and a legacy `/auth/login/`. HRMS has `/user-mgmt/user-login/`, `/user-mgmt/current-user/`, OTP endpoints (`/user-otp/`, `/user-otp-verify/`), `/gmail-login/` and `/user-password-modify/`. | STATIC | HIGH |
| Parents log in with their own account linked to child records (`/erp_user/fetch-parent-data/`, `/add-child-to-parent/`, `siblingsData` in session state). | STATIC | MEDIUM |
| A `is_2fa_enabled` flag is carried in session state. The second factor is probably OTP (OTP endpoints exist in the HRMS catalog only; the ERP catalog has none; audit ER-39). | STATIC/INFERRED | MEDIUM |
| `force_update` flag in the session forces a password or profile update on login. | STATIC | MEDIUM |

## 2. Tokens and session
| Fact | Class | Confidence |
|---|---|---|
| Login returns an **access token and a refresh token** (`access`/`refresh`, also `access_token`/`refresh_token`). Requests carry `Authorization: Bearer <access>`. | STATIC | HIGH |
| Refresh: `POST {ERP_API}/erp_user/access-token/` with `{refresh}` (HRMS: `/user-mgmt/access-token/`). A request interceptor serialises concurrent refreshes with a queue so only one refresh is in flight. | STATIC | HIGH |
| Tokens and the user profile are kept in **localStorage**, one record per app: `user_details` (ERP), `recruitmentLoginDetails` (HRMS), `crmLoginDetails` (CRM). The shell reads all three to decide which apps to show. | STATIC | HIGH |
| On first load, some API calls fired before the token was ready returned **403** and were retried successfully (200) after initialisation. | OBSERVED | HIGH |
| Session state fields (ERP Redux `user/SET_STATE`): `id, first_name, last_name, role_id, user_role, erp_user_id, erp, branch, grades, email, token, refresh_token, is_superuser, gender, user_level, access_level, navigation_data, siblingsData, force_update, is_2fa_enabled, is_virtual_student, is_recruitment_enabled, is_crm_enabled, current_session, is_taproot_user, show_bug_tracker`. Student/staff session data includes per-session-year records with `session_year__is_current_session`. | STATIC | HIGH |
| **Impersonation ("switch user")** exists: an `impersonating` flag in localStorage plus a `X-Is-Switch: true` request header. Some menus are hidden while switched (`hideForSwitchUser`). | STATIC | HIGH |
| Cross-app single sign-on: the HRMS app can obtain ERP tokens (`/erp_user/access-token/`) and vice versa. Users with ERP `user_level == 13` (students) never see HRMS/CRM. | STATIC | HIGH |

## 3. Authorization model (client side; the server must enforce too)
1. **User level** (numeric) per app: ERP levels, CRM levels 1–27 and HRMS levels 1–36 (ranges read from default access-matrix seeds in the bundle; the seeds were not retained, so the ranges are UNVERIFIABLE; audit ER-13). ERP level names were captured at runtime (E017, 80 named levels): e.g. 1 Super Admin, 5 School Admin, 8 Principal, 10 Academic Coordinators, **11 Teacher** (the observed account), 12 Parent, **13 Student**, 32 Branch Accountant. Full list in `10_REA_EVIDENCE/sanitized_metadata/E017_role_catalogue.md`.
2. **Access matrix**: per app, per navigation item ID → `{userLevel: [ids], erpIds: [individual overrides], permissions: {is_view, is_add, is_modify, is_delete}}`. Fetched from `/erp_user/access-control/matrix/` (with `nav-tree`, `rules`, `audit`, `seed`, `sync-registry` admin endpoints). Cached in localStorage as `k12_access_matrix` with a **1-hour TTL** (`k12_access_matrix_ts`), plus `k12_route_catalog` and `k12_sub_item_access`.
3. **Route permissions in code** (HRMS): each route declares `permission: {is_view: [levels], is_add: [...], is_modify: [...], erpIds?: [...], require_keyperson?, require_reporting_manager?}`.
4. **Feature flags per user**: `is_recruitment_enabled`, `is_crm_enabled`, `is_hrbp_support`, `allowSeoTeam`, `allowAcmsOpsTeam`, `requiresKeyperson`, `show_bug_tracker`.
5. **Legacy RBAC** objects still exist: Roles (`/erp_user/roles_all/`, `create_role`, `update_role_module`), Modules (`/erp_user/modules_all/`), level-to-permission mapping (`/erp_user/level-mapping/`, `/permission-checker/`, `/assign-perm/`).
6. **Data scoping** by branch and academic session: the header has a Branch selector and an Academic Year selector (OBSERVED), and APIs take `branch_id`/`session_year`.

Unauthorized client routes **silently redirect to `/erp/user-profile`** (OBSERVED for `/erp/dashboard` and `/erp/homework-dashboard` with the teacher account).

## 4. Requirements for the replacement (PROPOSED)
- Use server-side session or short-lived JWT access tokens (≤15 min) plus rotating refresh tokens in **httpOnly, Secure, SameSite cookies**, not localStorage.
- RBAC with named roles, permission strings (`module.resource.action`) and scope (tenant → branch (= one school campus) → academic year → class section / class subject assignment; audit ER-41). Per-user overrides should be auditable grants, not hard-coded employee-ID lists in frontend code.
- Enforce authorization on every API endpoint; the frontend matrix is only for display.
- Optional TOTP/OTP 2FA for staff; OTP or password login for parents; magic links are optional.
- Impersonation only for super-admins, fully audited, with a visible banner, and read-only by default.
