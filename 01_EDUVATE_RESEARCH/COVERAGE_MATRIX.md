# Coverage Matrix

Legend: ● complete for the evidence available · ◐ partial · ○ not covered. "Runtime" = observed with the teacher account; "Static" = shipped frontend code/config.

## Investigation dimensions (Part 3 of the brief)
| # | Dimension | Status | Where | Notes |
|---|---|---|---|---|
| 1 | Product functionality | ◐ | 01_PRODUCT_OVERVIEW, 03 | Full IA from static config; runtime depth for teacher modules |
| 2 | Information architecture | ● | information_architecture.md | 772 menu items |
| 3 | Frontend architecture | ● | technology_stack.md | 3 SPAs, versions, build |
| 4 | Routes & navigation | ● | route_inventory.md, E005/E007/E012 | 688 ERP + HRMS routes |
| 5 | Roles & permissions | ◐ | role_permission_matrix.md, E017 | Teacher observed; other roles static/inferred; writes unverified |
| 6 | Screens & components | ◐ | screen_inventory.md, component_inventory.md | 39 accessible screens observed; admin/finance screens not visible |
| 7 | Backend API architecture | ● | backend_architecture.md | services, conventions, tenancy |
| 8 | API request/response schemas | ◐ | api_contracts.md | 94 runtime GET shapes; request (write) bodies not observed |
| 9 | Domain entities & relationships | ◐ | 05_DATA_MODEL | inferred; core well-evidenced, finance/transport static |
| 10 | Business rules & workflows | ◐ | 06_WORKFLOWS | 15 workflows; write paths unverified |
| 11 | Database model inference | ◐ | entity_dictionary.md, er_diagram.mmd | proposed schema with provenance |
| 12 | UI design system | ● | design_system.md | tokens from code + screenshots |
| 13 | Integrations | ◐ | integrations.md | mostly static; FCM, Clarity, analytics and Mattermost observed |
| 14 | Validation & error handling | ◐ | module specs, api_contracts | required filters, envelopes and error codes observed; form validation not exercised |
| 15 | Reporting & analytics | ◐ | m17 | endpoints/menus static; dashboards denied for teacher |
| 16 | Security & access control | ◐ | security.md, authentication.md | server-side enforcement UNKNOWN (SEC-05) |
| 17 | Functional & non-functional requirements | ● | SYSTEM_REQUIREMENTS.md, 07 | |
| 18 | Testing & acceptance | ● | 08_TESTING | ACs + test catalog |

## Module coverage
| Module | Runtime screens | Runtime APIs | Static routes/APIs | Workflow | Spec | Overall |
|---|---|---|---|---|---|---|
| Shell & navigation | ● | ● | ● | — | m01 | ● |
| Identity & users | ◐ (profile) | ◐ | ● | — | m02 | ◐ |
| Org structure | ○ (denied/not visible) | ◐ (fields) | ● | WF-01 | m03 | ◐ |
| Academic calendar | ● | ● | ● | WF-02 | m04 | ● |
| Timetable | ◐ (empty grid) | ● (diary page) | ● | WF-03 | m05 | ◐ |
| Diary | ● | ● | ● | WF-04 | m06 | ● |
| Homework | ◐ (filters) | ◐ | ● | WF-04 | m07 | ◐ |
| Attendance | ◐ (filters, empty roster) | ◐ | ● | WF-05 | m08 | ◐ |
| Lesson plans | ◐ | ◐ | ● | — | m09 | ◐ |
| Assessments & report cards | ◐ | ● (lists, dashboard) | ● | WF-06 | m10 | ◐ |
| Online class / e-books / clickers | ● (lists) | ◐ (e-book listing 404) | ● | WF-15 | m11 | ◐ |
| Communication | ● (lists; compose not seen) | ● (reads) | ● | WF-11 | m12 | ◐ |
| Tickets & feedback | ○ (denied) | ○ | ● | WF-12 | m13 | ◐ |
| Students lifecycle & admissions | ○ (not visible) | ○ | ● | WF-07, WF-08 | m14 | ◐ |
| Finance | ○ (not visible) | ○ (1 shell call) | ● (menus/routes; APIs in lazy chunks not captured) | WF-09, WF-10 | m15 | ◐ |
| Transport | ○ | ○ | ● | — | m16 | ◐ |
| Dashboards & reports | ○ (denied) | ○ | ● | — | m17 | ◐ |
| HRMS self-service | ● | ● | ● | WF-13, WF-14 | m18 | ● |
| CRM, recruitment, payroll, operations, tools | ○ | ○ | ● (menus) | — | m19 | ◐ (summary only, by design) |

## Not covered and why
- **Write operations** (create/update/delete/approve): deliberately not exercised (brief: no submissions or modifications).
- **Admin, finance, students, transport screens**: not visible to the teacher role; privilege escalation was not attempted.
- **CRM app** (`/crm`): bundle not loaded; this account has no CRM access flag.
- **Parent and student portals**: no such accounts available. Specified from static evidence (nav config, endpoints).
- **Mobile apps**: out of reach (web only).
