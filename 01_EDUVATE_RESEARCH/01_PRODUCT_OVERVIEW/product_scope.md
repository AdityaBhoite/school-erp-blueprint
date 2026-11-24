# Product Scope

## 1. What the product is (OBSERVED + STATIC, HIGH)
"Orchids ERP" is the **Orchids – The International School** deployment of **Eduvate**, a multi-brand K-12 school-chain platform. It runs every function of a chain of CBSE/ICSE-style schools with many branches across Indian cities (for example "OIS Ambegaon Pune"):

- **School academic operations**: academic calendar, curriculum and lesson plans, timetable and substitutions, daily diary (classwork and homework), homework, student attendance, online classes, e-books, classroom clickers, assessments, question banks, OMR uploads, marks entry, report cards, observations of teaching quality, house system, board-exam preparation.
- **Student lifecycle**: admission forms and registration, enrolment into grade/section, section shuffles, promotion, temporary and permanent status (blocking, leaving), transfer certificates, ID cards, sibling mapping, special educational needs (SEN).
- **Fees and finance**: fee terms, fee plans and groups, concessions, fines, wallets (academic, store, transport, imprest), online, cheque, kiosk and cash payments, receipts, refunds, reconciliation, dashboards, expenses, payables, branch P&L, rent, education-loan partners.
- **Communication**: announcements, parent tickets (helpdesk with CSAT), chat, WhatsApp, notifications, school wall, feedback/PTM feedback, visitor gate management (MySchoolGate).
- **Transport**: routes, stops, buses, drivers, GPS tracking, transport fees, P&L and optimisation.
- **Operations**: store/e-commerce (books and uniform kits), inventory, procurement (PR → PO → GRN → invoice), infrastructure helpdesk, expansion and real estate, contracts.
- **HRMS** (separate app): recruitment (manpower requisitions, candidates, interviews, AI interviews, assessments), onboarding, background verification, employee profile, attendance and leave, shifts, holidays, transfers, separations, performance management, training/LMS, assets, travel, reimbursements, grievances, documents, payroll, tax declarations, payslips.
- **CRM / Marketing** (separate app): lead management, enquiry and application forms, events, PRM/CCA lead assignment, budgets and incentives, call auditing, reports.
- **Internal tools**: bug tracker, feature requests, goal and work trackers, project/scrum boards, L&D programs ("ByteLearn").

## 2. Scale indicators
| Indicator | Value | Class |
|---|---|---|
| Navigation items (all apps) | 772 menu items in 135 sub-sections across 20 sections | STATIC, HIGH |
| ERP client routes | 688 | STATIC, HIGH |
| ERP API endpoints in the client catalog | 1,174 (more inline) | STATIC, HIGH |
| HRMS API endpoints in the client catalog | 1,430 | STATIC, HIGH |
| ERP user levels (role IDs) | 80 named levels, highest ID 96 (E017). An earlier "up to 113" figure came from access-matrix seeds that were not retained (audit ER-13). | OBSERVED, HIGH (80 names); UNVERIFIABLE (113) |
| Brands served by the codebase | ≥5 | STATIC, HIGH |

## 3. Recommended scope for the independent replacement (PROPOSED)

The original's breadth comes from years of chain-specific growth. A replacement should be a **modern school ERP** whose core matches the school-operations functionality, with optional modules added later.

| Tier | Modules | Rationale |
|---|---|---|
| **Core (MVP)** | Tenant/school/branch setup, academic years, grades, sections, subjects and mappings; users, roles and permissions; staff directory; student information and enrolment; parent/guardian accounts; academic calendar and holidays; timetable (period system, teacher allocation); student attendance (mark/view/reports); daily diary and homework; announcements and notifications; parent helpdesk tickets; teacher/student/parent dashboards | Daily-use features seen at runtime; every role depends on them |
| **Core+ (v1)** | Assessments (exam types, test schedules, marks entry, grading systems), report cards (pipeline and publish), lesson plans and curriculum (chapters, key concepts, yearly curriculum plan), fee management (fee plans, terms, assignments, concessions, fines, online payment, receipts, dues, refunds), admissions and registration, student lifecycle (promotion, shuffle, status, TC), reports and exports | Central to a school ERP; strong static evidence |
| **Extended (v2)** | Transport (routes, stops, vehicles, drivers, transport fees, GPS integration), online classes, e-books/library, staff attendance and leave, observations/quality, house system, events and event feedback, PTM feedback, visitor gate, WhatsApp/SMS channels, chat | Present and useful, but not daily for every user |
| **Optional bounded contexts** | HRMS and payroll, recruitment, CRM/marketing, store and e-commerce, procurement, expansion/real estate, contracts, internal tools (bug tracker, work tracker), L&D LMS, AI features | Chain-operator specific; can be separate products |

> **Tier precedence (audit ER-46):** where this table differs from `11_IMPLEMENTATION_PLAN/implementation_priorities.md` (MoSCoW) and `development_roadmap.md`, those files win. Parent tickets and dashboards are **Should** (Phases 3 and 2.7). The lesson-plan library is **Could** (Phase 7).

## 4. Explicit non-goals
- No reuse of Orchids/Eduvate branding, logos, copy, proprietary source, or data.
- No replication of internal-only tools (tech training, bug tracker, Figma testing, Claude subscription reimbursement).
- No dependency on the original's microservice topology.
