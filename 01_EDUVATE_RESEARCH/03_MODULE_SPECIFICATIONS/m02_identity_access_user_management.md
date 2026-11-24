# M02 — Identity, Access Control and User Management

**Evidence:** STATIC (endpoint groups `loginAPIs`, `auth`, `userManagement`, `userManagementRevamp` (62), `roleManagement`, `moduleManagement`, `permissionClass`, `accessControl`, `userLevel`; routes `/user-management/*`, `/access-control`, `/master-management/permissions`), OBSERVED (`/erp/user-profile`, `/erp_user/user-mgmt/user-info/`, `/user-branches/`, `/user-grades/`).

## ERP user profile (`/erp/user-profile`, OBSERVED)
Profile summary plus actions **Change Password** and **Reset Email Password** (OBSERVED in the retained screen inventory). The previous draft also listed **Update Address**, an **Employee Care** card (My Care → grievance tickets) and banner carousels; these cannot be verified from retained evidence (LOW; audit ER-11).
`GET /qbox/erp_user/user-mgmt/user-info/?session_year` → `{id, erp_id, name, email, contact, profile(url), address, pin_code, date_of_birth, session_details{}}`.

## User management (STATIC; denied for the teacher)
| Screen | Route | Notes |
|---|---|---|
| View users | `/user-management/view-users` (tabs incl. `?tab=4` User Level; `?userType=Staff`) | search/filter users by branch/grade/role |
| Create/edit student | `/user-management/create-student`, `/edit-student/:userId` | ERP + finance student records |
| Create/edit staff | `/create-staff`, `/edit-staff/:userId`, unified staff edit | designation, branches, grades, subjects, user level |
| Non-teaching staff | `/create-non-teaching-staff`, `/edit-non-teaching-staff/:userId`, activate/deactivate | |
| User groups | `/create-user-group` | for communication targeting |
| Sibling mapping | `/user-management/sibling-mapping` | finance sibling search/map |
| Section shuffle | `/user-management/section-shuffle` | |
| Access blocker | `/user-management/access-blocker` | temporary block |
| TC request | `/user-management/tc-request` | |
| Virtual school / OCAP students | `/virtual-school`, `/ocap-students` | online-only students |
| Staff ID card / Student ID card | `/staff-id-card`, `/student-id-card` | |

Key endpoints: `/erp_user/user-mgmt/student/`, `/student-session/`, `/student-status/`, `/staff/`, `/staff-status/`, `/user-session-year/`, `/user-branches/`, `/user-grades/`, `/user-sections/`, `/user-subjects/`, `/erp_user/fetch-user-levels/`, `/get-user-level/`, `/fetch-parent-data/`, `/search-parent/`, `/add-child-to-parent/`, `/update-parent`, `/user-update-history/`, `/manager-employee-mapping/`, `/password-reset/`, Vonage/click-to-call.

## Access control (STATIC)
- **Access matrix** admin (`/access-control`): `/erp_user/access-control/matrix/`, `nav-tree/`, `rules/`, `audit/`, `seed/`, `sync-registry/`. The matrix maps navigation item IDs to user levels and individual ERP IDs with view/add/modify/delete flags.
- **Roles & modules** (legacy): roles_all, role-search, create_role, update_role_module, delete/restore role; modules_all, delete/restore modules.
- **Level mapping/permissions**: `/erp_user/level-mapping/`, `/permission-checker/`, `/assign-perm/`.
- **User levels**: `/erp_user/user-level/` CRUD; central `/central-admin/user_level_list/`.

## Replacement
See `04_BACKEND_ANALYSIS/authentication.md` §4 and `role_permission_matrix.md`. ACs: AUTH-*, RBAC-*, PPL-*.
