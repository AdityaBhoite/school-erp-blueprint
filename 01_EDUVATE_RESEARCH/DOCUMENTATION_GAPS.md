# Documentation Gaps

Companion to `AUDIT_REPORT.md` (2026-11-23). This file lists what the handoff is **missing**, why it is missing, and how each gap should be closed. **Nothing here is filled in by invention.** Where only the original system could supply a detail, the gap is recorded and closed by a decision, never by guessing what the original does.

## How a gap gets closed

| Code | Who closes it | How |
|---|---|---|
| **OWNER** | Product owner | Business decision, recorded as an ADR |
| **CUSTOMER** | First school customer | Their policy, rules, formats or samples |
| **LEGAL** | Counsel | Regulatory requirement (DPDP, receipts, certificates) |
| **BUILD** | Build team or coding agent | Designs it (PROPOSED), records an ADR and tests it. No evidence is needed, because the replacement is independent. |
| **DEFER** | — | Not needed until the module's phase starts, or out of scope |

The investigation is **closed**. Gaps that only new observation of the original could fill are listed in §2 and are not to be reopened. The build replaces them with PROPOSED designs.

> **Phase 3 update (2026-11-23):** a full-corpus static pass (`13_AUTONOMOUS_DEEP_ANALYSIS/`) closed the HTTP-method gap (methods resolved for 5,427 of 6,577 catalog endpoints from code call sites) and recovered the finance-service API surface (141 endpoints with methods and field names). This narrows G-19 (the finance *service* API is now mapped, though finance *SPA* screens and fee *rules* remain open) and strengthens the evidence behind ER-07 (static methods are now call-site-resolved, not name-inferred).
>
> **Phase 3 runtime crawl (2026-11-23, executed):** a read-only, navigate-only crawl ran 408 of 426 safe routes as a teacher (stopped on the server's 429; 18 unreached). It raised runtime-observed endpoints **94 → 171**, captured **72** new value-free response schemas, and confirmed static methods against runtime with **0 contradictions** (104/107 direct). It also produced the first **runtime** evidence of ERP role-gating (42 rendered vs 359 role-redirected for a teacher). This closes the runtime-verification gap **for the teacher role only**; admin/accountant/parent/student reachability and **server-side** enforcement (SEC-05) remain open — they need another account, which is out of scope. G-01 (replacement API surface) and G-05 (enum *values*) are unchanged — build decisions and value-free-capture limits.

"Blocks" gives the phase from `11_IMPLEMENTATION_PLAN/development_roadmap.md` (0 Foundations, 1 Org & People, 2 Daily teaching, 3 Communication, 4 Assessment, 5 Fees, 6 Lifecycle, 7 Extended) that cannot finish until the gap is closed. "—" means it blocks nothing.

---

## 1. Cross-cutting gaps

| ID | Gap | Why missing | Close via | Blocks |
|---|---|---|---|---|
| G-01 | **No replacement API surface per module.** There is no endpoint list or OpenAPI draft, only conventions in `ARCHITECTURE_BLUEPRINT.md` §4. | By design: the study documented the original, and its `/qbox` paths must not be mirrored | BUILD: each implementation step defines Zod contracts first; OpenAPI is generated from them | every phase (designed per step) |
| G-02 | **The permission catalogue is not enumerated.** About 30 permission strings are scattered across workflows (`attendance.mark`, `calendar.publish`, `pii.reveal`, …); `role_permission_matrix.md` §3 works at module level with R/C/U/D/A letters. | Never consolidated | BUILD: start from the seed list in `CLAUDE_CODE_MASTER_PROMPT.md` §7 and extend per module | 0 |
| G-03 | **No navigation registry for the replacement** (menu item → route → required permission). Only the original's 772-item menu exists (E005), and it is out of scope. | Not written | BUILD: a small registry in `packages/permissions`, covering only the modules being built | 0 |
| G-04 | **No error-scenario catalogue.** Most module specs list no error cases; they appear only inside ACs. | Writes and errors were never observed; specs did not propose them | BUILD: every step lists its error cases (validation 422, scope 403/404, conflict 409, state-transition errors) with stable error codes | every phase |
| G-05 | **Enum values are unknown in the original** (event types, assessment status, online-class status, attendance statuses, ticket statuses, branch types, teacher types, student types). | The capture was value-free by policy (correct) | BUILD (PROPOSED enums, tenant-configurable where marked) + CUSTOMER confirmation | 2–6 |
| G-06 | **No notification matrix** (event → recipients → channel → timing → template). Fragments only: CAL-02, HW-01, ATT-05, COM-02. | Not consolidated | BUILD drafts it; OWNER approves the channels (SMS/WhatsApp cost, DLT template registration in India) | 2, 3 |
| G-07 | **Tenant defaults** (time zone, locale, currency, week start, school-day hours) are not specified. | Not in scope of the study | BUILD: PROPOSED defaults `Asia/Kolkata`, `en-IN`, INR, Monday; per tenant | 0 |
| G-08 | **Onboarding import templates** (columns and validation for students, guardians, staff, sections) are not specified. PPL-02 defines only behaviour. | Not in scope of the study | BUILD drafts the templates; CUSTOMER validates them against their data | 1 |
| G-09 | 4 acceptance criteria have no test case: AUTH-07, TEN-03, RBAC-06, NFR-01. | Omission | BUILD: add to `08_TESTING/test_case_catalog.md` | 0 |
| G-10 | Specs M04 and M06 do not cite their ACs (CAL-01…04, DIA-01…03). M09, M11, M16 and M18 have no ACs at all. | Omission | BUILD: add ACs when those modules are scheduled. Light-HR absence ACs are needed in Phase 2 (G-31). | 2 (M18), 7 |
| G-11 | Owner decisions with no default possible: product name and branding (Q19), hosting region and compliance (Q20), which optional modules the first customer needs (Q18), languages (Q16). | Business decisions | OWNER | Q19: before pilot · Q20: before production · Q18: before Phase 7 · Q16: — (i18n framework from day one) |
| G-12 | Data retention and deletion policy per data class (Q14), and the consent model for children's data (SR-STU-8). | Legal and business | OWNER + LEGAL | before pilot with real data |
| G-13 | The errata in `AUDIT_REPORT.md` §G are **not applied inside the source files**. Readers of a source file see the uncorrected label. | The audit was deliberately non-destructive | OWNER chooses: leave as a register (the master prompt makes it binding) or apply it in place with a change log | — |

---

## 2. Evidence gaps that stay open (do not try to close them)

These are UNKNOWN about the **original** and will remain so. The replacement does not need them.

| ID | Unknown | Consequence for the build |
|---|---|---|
| G-14 | Every write payload (create, update, delete, approve). No domain request body was observed. | Design write contracts fresh (G-01). Never reconstruct them from endpoint names. |
| G-15 | Server-side authorization behaviour (SEC-05) | Irrelevant: the replacement enforces it (SECURITY_REQUIREMENTS §2.3) |
| G-16 | Screens for admin, finance, students, transport, tickets and dashboards, and the whole parent/student experience | Design them from the module specs, the workflows and the observed UX patterns (shell, filter-first lists, tables, drawers) |
| G-17 | Field enum values and business-rule parameters (G-05) | PROPOSED, configurable |
| G-18 | Accessibility-tree details of the observed screens (not retained). Per-page call counts. | The retained `screen_inventory.md` and the screenshots are enough for UX patterns |
| G-19 | ~~The finance service API (lazy chunks were never loaded)~~ **Partly closed (Phase 3):** 141 finance-service endpoints recovered with methods and field names from ERP-side call sites (`13_AUTONOMOUS_DEEP_ANALYSIS/`). Still open: the finance **SPA**'s own screens (separate `orchids.finance.*` bundle, not fetched) and all core **transport** behaviour APIs. | M15/M16 fee and transport *rules* stay customer decisions; the endpoint surface is now catalogued |
| G-20 | The access-matrix response schema; the meaning of user levels other than Teacher | The replacement uses its own RBAC model |
| G-21 | Mobile app behaviour | PWA first (Q17) |

---

## 3. Module gaps (task E)

Coverage matrix: `AUDIT_REPORT.md` §E. Only the **missing** details are listed here.

### M01 Shell and navigation
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-22 | Global search: which entities (student, staff, guardian), which fields, minimum characters, how scope applies, result layout. FR-SH-04 names only the types. | BUILD | 1 (Should) |
| G-23 | Content and behaviour of the "No access" page (RBAC-05 says only "not a silent redirect") | BUILD | 0 |
| G-24 | Content of the `GET /me/bootstrap` response (PR-5 lists its categories, not its shape) | BUILD | 0 |

### M02 Identity, access and user management
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-25 | User lifecycle workflows: creating and inviting staff, guardian and student accounts; activation; deactivation; handling a person who leaves. There is no workflow file. | BUILD (draft) + CUSTOMER | 1 |
| G-26 | Login identifier policy: format of the generated `login_id` (the original's ERP-ID format must **not** be reused), and whether email or phone login is allowed for each user type | OWNER | 0 |
| G-27 | Password policy details beyond Argon2id and lockout (minimum length, complexity, expiry or none) | BUILD (PROPOSED: length ≥ 10, breached-password check, no forced expiry) + OWNER | 0 |
| G-28 | The role × permission-code matrix. `role_permission_matrix.md` §3 is at module level only. | BUILD (derive from §3 and G-02) | 0 |

### M03 Organisation and academic structure
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-29 | Admin screens: none was observed, so screen designs are needed for branches, years, grades, sections, subjects and mappings | BUILD | 1 |
| G-30 | Field validation: code formats and uniqueness, branch-type values, whether Zone and City are used at all, and whether a Zone → City hierarchy exists (it is an ASSUMPTION in `er_diagram.mmd`) | CUSTOMER / OWNER | 1 |
| G-30b | The full list of system configuration keys. FR-ORG-07 names only examples (week holidays, attendance mode, edit windows, notification toggles). | BUILD | 1–2 |
| G-30c | Multiple boards inside one branch (Q15) | OWNER | 1 |

### M04 Academic calendar
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-32 | Who may create, edit, review and publish (Q10). The review process itself is PROPOSED. | OWNER / CUSTOMER | 2 |
| G-33 | Event types list, colours, and whether tenants can add custom types | BUILD + CUSTOMER | 2 |
| G-34 | Recurrence rules: patterns, how exceptions work, editing one occurrence versus the series (CAL-04 requires per-occurrence exceptions) | BUILD | 2 |
| G-35 | Field validation: title length, dates inside the academic year, whether events may span several days | BUILD | 2 |

### M05 Timetable and substitution
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-36 | **Split-period semantics.** Only the legend label "SPLIT" was seen; no field carries it. | BUILD (PROPOSED: a slot with several class subjects or teachers for sub-groups) + CUSTOMER | 2 |
| G-37 | Substitution rules: eligible substitutes (free period, same subject, workload cap), who assigns, notification | CUSTOMER | 2 |
| G-38 | Changing a published timetable mid-year: effective dates, how versions apply to past diary and attendance records | BUILD | 2 |

### M06 Diary
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-39 | The entry editor UI and its states. It was never opened. | BUILD | 2 |
| G-40 | Edit and delete window for diary entries; whether entries are visible to students and guardians immediately or only after a publish step | CUSTOMER | 2 |

### M07 Homework
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-41 | **Aggregate boundary.** Is homework part of a diary entry (the original's `has_homework`), a standalone aggregate, or both? The entity dictionary has HomeworkSubmission but no Homework entity. | BUILD (PROPOSED: a standalone Homework aggregate, optionally created from a diary entry) | 2 |
| G-42 | Late-submission policy (Q04); evaluation scale (marks, grade, remarks); resubmission | CUSTOMER | 2 |
| G-43 | Contents of the "Upload" and "Worksheet" tabs (only the tab names were seen) | DEFER | — |

### M08 Attendance
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-44 | Daily versus period-wise (Q01); status list (Q02); edit window and backdating roles (Q03) | CUSTOMER | 2 |
| G-45 | Channel, timing and template of absence notifications, and their cost (SMS DLT) | OWNER / CUSTOMER | 2 |
| G-46 | Student leave applications from guardians, and how approved leave affects attendance status. Not documented anywhere. | CUSTOMER | 2 (if required) |

### M09 Lesson plans and curriculum
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-47 | No workflow, validation, error cases or ACs. Who authors content (central vs school) is undecided. | OWNER, then BUILD | 7 |

### M10 Assessment and report cards
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-48 | Grading scheme(s) (Q05) and term weighting (Q06) | CUSTOMER | 4 |
| G-49 | **Report-card template layout and content** (board formats, co-scholastic areas, personality traits, remarks, attendance summary). No sample was captured. | CUSTOMER (provide sample report cards) | 4 |
| G-50 | Exam types list; absent and exempt codes (AB/EX/ML are PROPOSED); approval chain for publishing results | CUSTOMER | 4 |
| G-51 | Marks-entry grid UI. Only the dashboard and the Enter Marks, Template and Upload buttons were seen. | BUILD | 4 |

### M11 Online classes and e-books
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-52 | Permissions, validation, error cases and ACs; provider choice (Zoom or Meet). The behaviour of the e-book listing is UNKNOWN (its endpoint returned 404). | OWNER (Q18), then BUILD | 7 |

### M12 Communication
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-53 | Audience model in detail (roles × branches × grades × sections × individuals; snapshot taken at publish time) | BUILD | 3 |
| G-54 | Whether teachers may create announcements (Q11); whether an approval step comes before publishing | OWNER | 3 |
| G-55 | **Communication Inbox** (central team → schools, with tasks, priorities, required actions and reminders; F-027, Should). No entity exists in the dictionary. Is it in scope? | OWNER | 3 |
| G-56 | Moderation rules for the school wall, and chat (both Could) | DEFER | 7 |

### M13 Tickets and feedback
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-57 | Ticket statuses and SLAs (Q09); category list; routing rules (by branch, category, role); visibility (assignee only, or the branch team) | CUSTOMER | 3 |
| G-58 | No TicketCategory or RoutingRule entity; attachment limits | BUILD | 3 |

### M14 Students: admission and lifecycle
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-59 | Admission and registration form fields, required documents, registration fee. Only route names exist. | CUSTOMER | 6 |
| G-60 | Format of admission numbers and student IDs | OWNER | 1 |
| G-61 | Transfer-certificate format and legal content | CUSTOMER + LEGAL | 6 |
| G-62 | Promotion rules (detention or repeat; who decides); approval chains for status changes (WF-08 static: principal, then central) | CUSTOMER | 6 |
| G-63 | No Registration, Application or TransferCertificate entity; no ACs for admissions | BUILD (after G-59) | 6 |

### M15 Finance (highest-risk module)
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-64 | **Fee computation (Q07):** instalment schedule, rounding, how a partial payment is allocated across heads and terms, carry-forward of back dues, fine formula and cap | CUSTOMER | 5 |
| G-65 | **Payment gateway (Q08)**, settlement and reconciliation format | OWNER | 5 |
| G-66 | Receipt content and legal requirements, including whether any tax applies to any fee head. **Not mentioned anywhere in the package.** | CUSTOMER + LEGAL | 5 |
| G-67 | Concession types, eligibility (sibling, staff ward, scholarship), approval thresholds | CUSTOMER | 5 |
| G-68 | Refund policy and destination; whether wallets are needed at all (the original has four types) | CUSTOMER / OWNER | 5 |
| G-69 | Offline modes in scope (cash, cheque, kiosk), cheque-bounce handling, accounting export format (the original exports to Tally) | CUSTOMER | 5 |
| G-70 | Entity field provenance is overstated (AUDIT_REPORT ER-37). FeeAccount, ReceiptSeries, FineRule and ApprovalRequest are not designed in detail. | BUILD (after G-64…G-69) | 5 |

### M16 Transport
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-71 | Everything beyond menu and route names: workflow, fee slabs, GPS provider, bus attendance, ACs | OWNER (Q18), then CUSTOMER | 7 |

### M17 Dashboards and reports
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-72 | **Metric definitions** for each widget. Only attendance % (ATT-06) and diary completion % (BR-DIA-4) are defined. | BUILD + CUSTOMER | 2 (teacher dashboard), 5 (finance) |
| G-73 | Which dashboards each persona gets in the MVP; refresh frequency; export formats | OWNER | 2 |

### M18 HRMS self-service
| ID | Missing | Close via | Blocks |
|---|---|---|---|
| G-31 | **Minimal staff absence for substitution.** No entity, AC or spec exists, yet TT-04 depends on it. `er_diagram_core_v2.mmd` proposes `STAFF_ABSENCE`. | BUILD (Phase 2.3) | 2 |
| G-74 | Leave types, balances and the approval flow, if light HR is wanted (WF-13 is static) | OWNER (Q18) + CUSTOMER | 7 |

### M19 Other contexts
Out of scope by design. No gaps tracked.

---

## 4. Data-model gaps (task D)

| ID | Gap | Close via | Blocks |
|---|---|---|---|
| G-75 | ER diagram lacks PK, FK and tenant columns | **Closed** by `05_DATA_MODEL/er_diagram_core_v2.mmd` (core, Phases 0–2) | — |
| G-76 | No TeachingAssignment entity, so teacher scope has no data source | **Closed** in v2 (ASSUMPTION: the single source of teacher scope; owner to confirm) | 1 |
| G-77 | The Zone → City hierarchy is unevidenced | v2 models them independently; OWNER/CUSTOMER to confirm (G-30) | 1 |
| G-78 | Entities still to design, by phase. Phase 3: Announcement audience, Notification preferences, TicketCategory/RoutingRule, MessageLog. Phase 4: GradingBand, ReportCardTemplate detail. Phase 5: FeeAccount, ReceiptSeries, FineRule, Refund, ApprovalRequest (generic). Phase 6: Registration, Application, TransferCertificate. Cross-cutting: Attachment, Outbox, ImportJob. | BUILD at each phase | 3–6 |
| G-79 | The broad `er_diagram.mmd` still shows unscoped edges (no year on Assessment or FeePlan) | BUILD: extend v2 per phase rather than editing v1 | 4, 5 |

---

## 5. API documentation gaps (task C)

| ID | Gap | Close via |
|---|---|---|
| G-80 | Replacement contracts (G-01) | BUILD |
| G-81 | A stable error-code catalogue for the replacement (for example `VALIDATION_FAILED`, `OUT_OF_SCOPE`, `STATE_CONFLICT`, `IDEMPOTENCY_REPLAY`) | BUILD (Phase 0) |
| G-82 | Three observed shapes are truncated in `api_contracts.md` | **Not needed:** the full shapes are in `10_REA_EVIDENCE/sanitized_metadata/E016_runtime_api_shapes.json` |
| G-83 | The meaning of observed query parameters (`acad_session` vs `session_year` (Q22), `access_level`, `date_filter`, `bucket`, `view_type`) | Stays UNKNOWN; irrelevant to the replacement, which uses explicit `branchId`/`academicYearId` parameters |

---

## 6. Security gaps (task F)

Requirements are written in `SECURITY_REQUIREMENTS.md`. These decisions are still open:

| ID | Decision | Close via | Blocks |
|---|---|---|---|
| G-84 | Whether government-ID numbers are collected at all (SR-STU-4) | OWNER + LEGAL | 1 |
| G-85 | Session timeouts and MFA scope (SR-SES-3, SR-SES-7; defaults proposed) | OWNER | 0 |
| G-86 | Retention periods, including audit-log retention (SR-AUD-4, Q14) | OWNER + LEGAL | before production |
| G-87 | Consent capture model for children's data (SR-STU-8) | LEGAL | before pilot |
