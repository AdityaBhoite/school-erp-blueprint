# Project Continuation Assessment

**Date:** 2026-11-24 · **Owner:** AdityaBhoite · **Repo:** `school-erp-blueprint` (private)
**Purpose:** Verify what Stage 1 genuinely delivered, register exactly what remains unverified about Eduvate, and stand up the Stage 2 and Stage 3 frameworks — **without** repeating any completed research and **without** beginning implementation.

> This report reads the existing research as the **canonical Eduvate evidence base**. No static JavaScript analysis, API extraction, route crawl, documentation generation, or REA/installation diagnostic was re-run to produce it. Every figure below is quoted from the shipped research documents, cited to its source file.

---

> **Stage 1B update (2026-11-24):** The remaining-Eduvate investigation ran without repeating any completed work. New analysis lives in [`01_EDUVATE_RESEARCH/14_EDUVATE_COMPLETENESS/`](01_EDUVATE_RESEARCH/14_EDUVATE_COMPLETENESS/): the **CRM** application (610 endpoints) and the **finance service** API (151 endpoints) were synthesised from existing metadata; a precise **role access-requirements matrix** and a **workflow evidence-gap** classification were produced; the **18 routes** were predicted and queued; and the **finance SPA** frontend was determined to need an authenticated handoff (its root is 404 anonymously). Two read-only captures remain **pending an authenticated browser** (the extension is not connected): the 18-route + finance-frontend crawl, and per-role crawls. See `14_EDUVATE_COMPLETENESS/COMPLETENESS_ASSESSMENT.md` and `FINAL_EDUVATE_COVERAGE.md`. **Stage 2 stays held.**

---

## A. Existing research — verified as canonical

The canonical evidence base is `D:\REA Claude\orchids-erp-research` (149 files, untouched), mirrored read-only into this repo under [`01_EDUVATE_RESEARCH/`](01_EDUVATE_RESEARCH/) (126 files). It was produced by a read-only, legitimate-access investigation using one **Teacher (ERP level 11)** account, with strict evidence labelling (OBSERVED / STATIC / INFERRED / PROPOSED / UNKNOWN).

What it established, confirmed by re-reading (not re-running):

| Area | State | Source |
|---|---|---|
| Application surfaces | 3 SPAs mapped: **ERP** (`/erp`), **Recruitment/HRMS** (`/recruitment`), **CRM** (`/crm`) | `PROJECT_ROADMAP.md`, `02_FRONTEND_ANALYSIS/technology_stack.md` |
| Static code analysis | **1,954 JS chunks**, 0 parse errors; raw corpus deleted, only value-free derived metadata retained | `13_AUTONOMOUS_DEEP_ANALYSIS/` |
| API catalogue | **6,577 rows**; HTTP methods resolved for **5,427**; **1,158** left UNKNOWN (no resolved call site), never guessed | `COVERAGE_MATRIX.md`, `REMAINING_UNKNOWNS.md` |
| Routes & navigation | ~729 routes mapped to chunk + APIs (688 ERP+HRMS in the route inventory); 772 menu items | `ROLE_ROUTE_API_MATRIX.md §5`, `COVERAGE_MATRIX.md` |
| Runtime evidence (teacher) | Read-only crawl of **408 / 426** safe routes: **42 rendered, 359 role-redirected to `/erp/user-profile`, 7 other**; stopped cleanly on the server's 429 rate limit | `REMAINING_UNKNOWNS.md`, `ROLE_ROUTE_API_MATRIX.md §6` |
| Runtime endpoints | **171** observed (up from 94); **72** new value-free response schemas; **no** static-vs-runtime method contradictions (104/107 direct matches) | `HANDOFF_READINESS.md`, `STATIC_VS_RUNTIME_COMPARISON.md` |
| Specifications | 19 module specs, 15 workflow specs, core data model + ER diagrams, 75 security requirements, acceptance criteria & test catalog, self-contained build master prompt | `03_MODULE_SPECIFICATIONS/`, `06_WORKFLOWS/`, `05_DATA_MODEL/`, `SECURITY_REQUIREMENTS.md`, `12_CLAUDE_CODE_HANDOFF/` |
| Integrity | Phase 3 forensic audit + SHA-256 proof package; call-site erratum applied (11,146 withdrawn as a verified total; 8,530 index / 10,006 catalog-sum documented with their bases) | `PHASE3_POST_RESTART_AUDIT.md`, `CALLSITE_COUNT_ERRATUM.md` |

**Conclusion:** Stage 1 is complete and internally consistent as a *technical discovery of the shipped client, through one teacher account*. It is the correct and sufficient Eduvate baseline for the comparative stage. It is **not** a full-system specification, and the research itself never claims to be — the gaps below are the documented edges of that baseline, not defects.

---

## B. Remaining Eduvate gaps

Every gap is classified by **how it could be closed**, so the strategy is explicit and no prohibited action is implied:

- **RE-CRAWL (teacher)** — recoverable with the *existing* Teacher account via a targeted, rate-limit-respecting navigation. No new role, no write. *Requires explicit approval to reconnect to Orchids/Chrome.*
- **SESSION-REQUIRED** — needs a legitimately-provisioned login for a *higher-privilege role* to render screens and capture runtime data/contracts. Owner-authorised access only; **no privilege escalation, impersonation, or ID enumeration.**
- **STATIC-ONLY (already captured)** — the candidate surface (route paths, menu entries, endpoint templates) is already in the research; the *experience, data shapes and confirmed behaviour* are not and cannot come from passive code reading.
- **STATIC-FETCHABLE (not yet done)** — a shipped bundle that was never fetched (finance SPA, CRM app) could be analysed statically. *Requires approval for a new download; some of it may still need the matching role to be meaningful.*
- **WRITE-REQUIRED** — needs performing a create/update/approve/pay action; deliberately never done and out of scope for passive analysis.

### B.1 The six focus areas

| Focus area | What we already have (evidence) | What is missing | How it could be closed |
|---|---|---|---|
| **Admin experience** (Super Admin L1, Principal L8, Campus Incharge L9, Academic Coordinator L10, School Manager L26) | Nav config, route paths, **65 inline `user_level` checks** enumerating these levels, path-only endpoint templates; crawl proved **88%** of ERP routes role-redirect a teacher | Actual admin screens, their rendered data, admin-only runtime API contracts and workflows | **SESSION-REQUIRED** (admin-class account). Residual paths/menus are **STATIC-ONLY (captured)**. |
| **Accountant & Finance** (Branch Accountant L32, Zonal FinOps L25, Regional FinOps L80) | Finance menus/routes; **151** finance endpoints *the ERP app calls* (path-level); WF-09/WF-10 **PROPOSED**. Handoff rates Finance **RED** — "no finance screen, rule or core API was observed or recovered" | All fee rules, receipts/tax logic, concessions, refunds, the finance screens, and the finance app's own request/response contracts | **SESSION-REQUIRED** (accountant account) **+** **STATIC-FETCHABLE**: `orchids.finance.letseduvate.com` is a **separate SPA whose bundle was never fetched** — a distinct, not-yet-done static gap. **+** fee *rules* are **business decisions** (G-64…G-69), not discoverable. |
| **Parent portal** (L12) | Nav config + candidate endpoints only; **no parent account was ever available** | The entire parent experience: screens, data, contracts, workflows | **SESSION-REQUIRED** (parent account). Everything else is **STATIC-ONLY (captured)**; Risk R-6 notes it is "entirely designed." |
| **Student portal** (L13) | Nav config + candidate endpoints only; **no student account** | The entire student experience | **SESSION-REQUIRED** (student account); otherwise **STATIC-ONLY (captured)**. |
| **Transport administration** (M16) | Teacher **rendered** the *view* pages at runtime: `/transport-buses`, `/transport-drivers`, `/transport-routes`, `/transport-stop-points` (one 404 on a sub-resource); admin flows are **STATIC** | Route/vehicle/driver **management**, student-to-stop assignment, and all transport **write** flows | **SESSION-REQUIRED** (transport/admin role) **+ WRITE-REQUIRED** for the mutations. |
| **The 18 rate-limited routes** | Listed exactly in `REMAINING_UNKNOWNS.md`; several are teacher-facing and "would likely have rendered — their non-coverage is the rate-limit stop, not role restriction" | Render-vs-redirect outcome and runtime shapes for these 18 | **RE-CRAWL (teacher)** — the **only** gap needing neither a new role nor a write. |

**The 18 unreached routes:** `/communication-inbox`, `/dashboard`, `/event-manager`, `/feature-request/dashboard`, `/feedback`, `/goal-tracker`, `/homework`, `/homework-dashboard`, `/meeting-scheduler`, `/myschoolgate`, `/observation`, `/online-books`, `/school-clicker`, `/school-wall`, `/teacher-observation`, `/test-module`, `/test-result`, `/ticket-dashboard`.

### B.2 Cross-cutting gaps (apply across roles)

| Gap | Classification | Note |
|---|---|---|
| Request bodies for create/update/approve/pay | **WRITE-REQUIRED** | Static body **key names** (1,720 endpoints) are the passive ceiling |
| State-machine transitions (publish marks, approvals, payments) | **WRITE-REQUIRED** | Observing them means performing them |
| Server-side authorization enforcement (SEC-05) | **SESSION + controlled test** | Only client-side redirect gating was observed; server 403 behaviour untested |
| CRM app (`/crm`) internals | **STATIC-FETCHABLE** | Bundle never loaded; this account has no CRM flag |
| Finance SPA internals | **STATIC-FETCHABLE + SESSION** | Separate bundle, never fetched |
| DB schema, server framework internals | **Not recoverable** | Not in the client; Django/DRF stays INFERRED |
| 1,158 endpoints' HTTP methods | **Not recoverable passively** | No resolved call site; left UNKNOWN |

### B.3 Not a discovery gap — business decisions

All items in `OPEN_QUESTIONS.md` (Q01–Q22) and the decision gaps in `DOCUMENTATION_GAPS.md` (fee rules, grading/weighting, retention, admission forms, product name, optional-module selection, etc.) are **owner/customer choices**, not things that more investigation of Eduvate can answer. They are inputs for Stage 4, not Stage 1 shortfalls.

### B.4 Explicitly out of bounds (unchanged)

Privilege escalation, authentication bypass, ID enumeration, direct API invocation, request replay, fuzzing, and any write were **never performed** and are **not** gaps to be "closed" by doing them. Any deeper role work proceeds **only** with owner-authorised, legitimately-provisioned accounts.

---

## C. Stage 2 readiness

The framework for analysing your existing in-house ERP is prepared in [`02_EXISTING_BUILD/README.md`](02_EXISTING_BUILD/README.md): the twelve analysis dimensions, the read-only method, the output documents it will produce, and exactly what you need to provide. **Stage 2 does not begin until you supply the project's directory or repository location.** The existing build's source will not be modified during analysis.

## D. Stage 3 framework

The comparative-analysis framework is prepared in [`03_COMPARATIVE_ANALYSIS/README.md`](03_COMPARATIVE_ANALYSIS/README.md): the ten comparison dimensions, the seven-way classification (**RETAIN FROM EDUVATE · RETAIN FROM EXISTING BUILD · IMPROVE · COMBINE · REDESIGN · NEW FEATURE · DISCARD**), and a reusable per-feature comparison template. It stays empty of verdicts until both Stage 1 (have) and Stage 2 (pending) evidence are in hand.

---

## E. Continuation report

### E.1 What is genuinely complete
- **Stage 1 technical discovery of the Eduvate client, via one teacher account** — verified, checksummed, and published privately. Strongest for the **daily-teaching core** (attendance, timetable, calendar, assessment/ACMS, homework, lesson plan, diary, online class, chat, transport *views*), which is runtime-backed.
- The **full static surface map**: routes, endpoint catalogue, call-site index (with erratum), response-field catalogues, role/level signals, module and workflow specs, core data model, and security requirements.
- A **self-contained build handoff** package (for later stages), with a forensic audit and SHA-256 proof package.

### E.2 What remains outstanding
- **Within Stage 1 (optional deepening):** admin, accountant/finance, parent, and student experiences; transport administration; the 18 rate-limited routes; write flows; server-side enforcement; the un-fetched finance and CRM bundles — each classified in §B by how it could be closed. None blocks the comparative stage; they would raise fidelity on the role surfaces the teacher account could not reach.
- **Stage 2:** not started — awaiting the existing ERP location.
- **Stage 3 / 4 / 5:** frameworks prepared (Stage 3), not yet executed; design (Stage 4) and implementation (Stage 5) gated behind an approved comparison.

### E.3 What access or information is required
1. **To start Stage 2 (the current blocker):** the directory path or repository URL of your existing ERP, plus how to run it if a runtime look is wanted later (read-only).
2. **To deepen Eduvate (optional, only if you want it before Stage 3):** owner-authorised, legitimately-provisioned logins for the missing roles (admin / accountant / parent / student / transport); **and/or** explicit approval to reconnect for a targeted, rate-limit-respecting re-crawl of the 18 routes with the *existing* teacher account; **and/or** approval to fetch and statically analyse the finance and CRM bundles.
3. **For Stage 4 (later, not now):** the business decisions in `OPEN_QUESTIONS.md` / `DOCUMENTATION_GAPS.md` (fee rules, grading, retention, admissions, product name, module selection).

### E.4 Recommended next milestones
| # | Milestone | Depends on | Gate |
|---|---|---|---|
| **M1** | Stage 2 — independent analysis of the existing build | You provide its location | Analysis complete & reviewed |
| **M2** | Stage 3 — comparative analysis & classification across the 10 dimensions | M1 + Stage 1 | **Approved comparative architecture** |
| **M3** | Stage 4 — unified, clean-room product blueprint | M2 approved | Blueprint approved |
| **M4** | Stage 5 — begin implementation | M3 approved | — |
| **M0 (optional, parallel)** | Eduvate deepening on authorised roles / 18-route re-crawl / finance bundle | Your authorisation per §E.3.2 | Feeds M2 fidelity |

**Holding point:** Per your instruction, I will **not** begin Stage 2, invent requirements, repeat completed research, or write any ERP code. I am waiting for you to provide the existing ERP project location. All existing work is preserved; the canonical research directory remains untouched.
