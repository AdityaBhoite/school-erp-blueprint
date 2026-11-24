# M03 — Organisation and Academic Structure (Masters)

**Evidence:** STATIC (`masterManagement` 81 endpoints, `mappingStudentGrade`, `academics` groups; Admin → Master Data menu: Academic Blueprint, Mappings, Signature Upload, Language, System Config, Permissions, QR Code, SOP documents; `/master-management/{city, zone, content-mapping, staff-distribution, infra-blueprint, …}`), OBSERVED fields (branch code/type, brand, session year, grade global id, section mapping, subject mapping).

## Entities and screens
| Master | Screens/endpoints | Operations |
|---|---|---|
| Zone, City | `/master-management/zone`, `/city`; `/erp_user/zone-view/`, `/city-view/`, `/acms/cities/` | CRUD, active flag |
| Branch | `/erp_user/create-branch/`, `/update-branch/`, `/delete-branch-mapping/`, `/restore-branch(-mapping)/`, `/list-all-branch/`, `/branch-dropdown/` | CRUD + soft delete/restore |
| Academic year | `/erp_user/academic-year-list/`, `create-`, `update-`, `restore-academic-year/`, `/default-current-session-year/` | CRUD, current flag |
| Branch × year | `/erp_user/branch_acad_maps_all/`, `/create-branch-mapping`, `/branch-acad-sess/` | map |
| Grade | `/erp_user/create-grade/`, `update-grade/`, `/grades-list/`, central `/v1/grades-list/` | CRUD |
| Grade mapping | `/erp_user/grademapping/` | per branch-year |
| Section | `/erp_user/create-section/`, `update-section/`, `/list-section/`, `/sections_all/` | CRUD |
| Section mapping | `/create-grade-section-mapping`, `/list-section-mapping/`, delete/restore (single and multiple), `/v2/sectionmapping/`, `/user-mgmt/section-mapping/` | map |
| Subject | `/create-subject/`, `update-subject/`, `restore-subject/`, `/subjects-list/`, `/subject-all/`, central `/v1/subjects-list/` | CRUD |
| Subject mapping | `/create-subject-mapping/`, `/list-subjectmapping/`, delete/restore, `/academic/school-subjects-mapping/`, `/school-gs-mapping-details/` | map |
| Board | `/academic/get-board-list/` | list |
| Staff distribution / workload | `/staff-mgmt/grade-level/`, `/grade-level-subject/`, `/teacher-workload/`, `/teacher-requirement(-report)/`, `/teacher-utilization-report/` | planning |
| System config | `/erp_user/erp_system_config/`, `/web-system-config/`, `/period/period-erp-system-config/` | key/value flags per branch |
| Signatures | `/master-management/signature-upload`, principal signature endpoints | upload |
| Language | `/master-management/language`, `/language-config` | 2nd/3rd language offerings |
| Carry-forward | `/acms/section-carry-forward/{candidates, preview, apply, history}` | rollover |

## Rules
- Hierarchy and IDs: see `05_DATA_MODEL/domain_model.md`.
- Soft delete with restore for all masters; bulk delete/restore of mappings.
- One current academic year (default).

Workflow: `wf01_academic_year_setup_rollover.md`. ACs: ORG-01..03.
