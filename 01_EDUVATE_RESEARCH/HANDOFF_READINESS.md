# Handoff Readiness

Assessment date: 2026-11-23. Based on `AUDIT_REPORT.md` and `DOCUMENTATION_GAPS.md`.

> **Phase 3 deep analysis + runtime crawl (2026-11-23) raised evidence quality for the daily-teaching core.** The read-only crawl runtime-confirmed the teacher-facing surface (attendance, timetable, calendar, assessment/ACMS, homework, lesson plan, diary, online class, chat, transport views), lifting runtime-observed endpoints 94 → 171 with method-bearing contracts and 72 new value-free response schemas (`13_AUTONOMOUS_DEEP_ANALYSIS/`). **Net effect: Phase 2 evidence is now GREEN-with-runtime-backing**, and endpoint/method risk for Phases 1–4 drops. Unchanged: no new *role* was exercised (teacher only) and **server-side enforcement is still unverified** (SEC-05), so authorization design remains PROPOSED. The verdict below is unchanged.

## Verdict: **conditionally ready**

A fresh Claude Code session can **start now** on Phase 0 (Foundations), Phase 1 (Org & People) and Phase 2 (Daily teaching core), using `CLAUDE_CODE_MASTER_PROMPT.md`. Those phases rest on the strongest evidence and have documented defaults for every open question.

Phases 3, 4 and 6 can start with defaults but need owner or customer confirmation before release. **Phase 5 (Fees) is not ready**: it must not be built from defaults alone. Phase 7 waits for the owner's module selection (Q18).

Legend: **GREEN** start now · **AMBER** start with documented defaults; confirmation needed before release · **RED** blocked; needs external input first.

---

## 1. Readiness by phase

| Phase | Scope | Evidence base | Spec completeness | Blocking inputs | Status |
|---|---|---|---|---|---|
| 0 Foundations | Monorepo, CI, auth, tenancy + RLS, RBAC engine, audit, files, notification infrastructure, shell | PROPOSED, with no dependency on the original; shell patterns OBSERVED | Strong: blueprint, rules, DoD, `SECURITY_REQUIREMENTS.md`, AUTH/TEN/RBAC ACs | None. Defaults exist for the login identifier (G-26), session timeouts and MFA scope (G-85). | **GREEN** |
| 1 Org & People | Branch, years, grades, sections, subjects, mappings; staff, students, guardians, enrolment; imports | Fields OBSERVED; management STATIC; screens never seen | Medium: no admin screen designs (G-29) or import templates (G-08) | Government-ID collection (G-84; default: do not collect). Zone/City usage (G-30; default: optional and independent). ID formats (G-60; default: tenant-configurable pattern). Boards per branch (Q15). | **GREEN** with defaults |
| 2 Daily teaching core | Calendar, timetable, substitution, diary, homework, attendance, teacher and parent dashboards | **Strongest:** calendar, diary, timetable data and the attendance and homework screens OBSERVED (reads) | Good, with known gaps: split period (G-36), homework aggregate (G-41), staff absence (G-31), dashboard metrics (G-72) | Q01–Q04, Q10 and substitution rules (G-37) have defaults and must be confirmed with the first customer before the pilot | **AMBER → GREEN** (build now, confirm before pilot) |
| 3 Communication | Announcements, notifications, tickets, channel adapters | Announcement and notification reads OBSERVED; tickets STATIC (denied) | Medium: audience model (G-53), ticket categories and routing (G-57/58), notification matrix (G-06) | Q09 (ticket states/SLA), Q11 (teachers create announcements?), Communication Inbox in scope? (G-55), SMS/WhatsApp cost and registration (G-06) | **AMBER** |
| 4 Assessment & report cards | Exam types, grading, assessments, marks, report cards | Assessment lists and marks dashboard OBSERVED; report-card pipeline STATIC | Good up to marks entry; report cards depend on a customer sample (G-49) | Steps 4.1–4.3 can use defaults. **Step 4.4 needs Q05, Q06 and sample report cards.** | **AMBER** |
| 5 Fees | Fee setup, dues, payments, receipts, concessions, refunds | Menus and routes only. **No finance screen, rule or core API was observed or recovered.** | Weak: every rule is PROPOSED | Fee computation (G-64), gateway (G-65), receipt and legal requirements including tax (G-66), concessions (G-67), refunds and wallets (G-68), offline modes and accounting export (G-69) | **RED** |
| 6 Lifecycle & admissions | Registration, admission, approvals, promotion, TC, rollover | STATIC only | Weak for admissions; adequate for generic approvals and rollover | Admission form fields (G-59), TC format (G-61), promotion rules (G-62). The **generic approval framework (6.2) and rollover (6.3) are AMBER**; admissions (6.1) is RED until G-59 is closed. | **AMBER / RED** |
| 7 Extended | Transport, online classes, lesson plans, light HR, reports gallery, mobile | STATIC (transport); OBSERVED lists (online class, HR self-service) | Weak: no ACs for M09, M11, M16 or M18 | Q18 (which modules the first customer needs) | **RED (deferred)** |

---

## 2. Readiness by module

| Module | Phase | Status | Main blocker or condition |
|---|---|---|---|
| M01 Shell | 0 | GREEN | — |
| M02 Identity & access | 0–1 | GREEN | Login identifier policy (default available) |
| M03 Org structure | 1 | GREEN | Screens are to be designed (no evidence) |
| M04 Calendar | 2 | GREEN | Q10 editor roles (default available) |
| M05 Timetable | 2 | AMBER | Split-period semantics; substitution rules |
| M06 Diary | 2 | GREEN | Editor UI to be designed |
| M07 Homework | 2 | AMBER | Aggregate boundary (G-41); late policy (Q04) |
| M08 Attendance | 2 | AMBER | Q01–Q03 confirmation |
| M09 Lesson plans | 7 | RED (deferred) | No workflow or ACs |
| M10 Assessment & report cards | 4 | AMBER | Q05, Q06 and a report-card sample before 4.4 |
| M11 Online class & e-books | 7 | RED (deferred) | Q18 |
| M12 Communication | 3 | AMBER | Q11, G-55, notification matrix |
| M13 Tickets | 3 | AMBER | Q09 statuses, routing rules |
| M14 Students lifecycle | 6 | AMBER / RED | Admission forms, TC format |
| M15 Finance | 5 | **RED** | Customer fee rules, gateway, legal receipt content |
| M16 Transport | 7 | RED (deferred) | Q18 |
| M17 Dashboards | 2 / 5 | AMBER | Metric definitions (G-72) |
| M18 HR self-service | 2 (absence only) / 7 | AMBER | Minimal `StaffAbsence` in Phase 2.3; full light HR deferred |
| M19 Other contexts | — | Out of scope | — |

---

## 3. Handoff quality gates

| Gate | Result | Evidence |
|---|---|---|
| Evidence labels consistent; code-derived features never presented as observed | **PASS with errata.** 48 corrections, made binding through the master prompt. | AUDIT_REPORT §A, §G |
| The 94 observed endpoints match the raw capture | **PASS** (checked by script) | AUDIT_REPORT §1, §C.1 |
| Static catalog properly caveated (no methods, incomplete) | **PASS with errata** | AUDIT_REPORT §C.2 |
| Original and replacement architectures separated, with reasons | **PASS**; open choices resolved | AUDIT_REPORT §B.3 |
| Core data model with keys, tenant scoping, hierarchy, year, enrolment and RBAC | **PASS** for Phases 0–2 (`er_diagram_core_v2.mmd`); later phases extend it | AUDIT_REPORT §D |
| Explicit security requirements | **PASS** (75 requirements, 10 proposed ACs) | `SECURITY_REQUIREMENTS.md` |
| AC → test traceability | **PARTIAL.** 4 ACs have no test; 4 modules have no ACs (G-09, G-10). | AUDIT_REPORT §1 |
| Self-contained build prompt | **PASS** | `CLAUDE_CODE_MASTER_PROMPT.md` |
| No personal data or secrets in the package | **PASS** (pattern scan) | AUDIT_REPORT §1 |
| IP safeguards (original code, brand and assets not reusable) | **PASS** | `12_CLAUDE_CODE_HANDOFF/DEVELOPMENT_RULES.md` §1–4 |
| Open questions carry defaults | **PASS** (Q01–Q22) plus new decisions in DOCUMENTATION_GAPS | `OPEN_QUESTIONS.md` |

---

## 4. Decisions to collect, by deadline

Every item has a default the build uses until it is answered, **except** those marked "no default".

| By | Decision | Default if unanswered |
|---|---|---|
| Phase 0 | Working product name (Q19) | Placeholder `School ERP`; no reference to the studied product |
| Phase 0 | Login identifier policy (G-26) | System-generated opaque login ID; email accepted for staff and guardians; phone and OTP for guardians as an option |
| Phase 0 | Session timeouts and MFA scope (G-85) | SR-SES-3 and SR-SES-7 defaults |
| Phase 1 | Collect government IDs? (G-84) | **Do not collect** |
| Phase 1 | Zone/City usage; branch types (G-30) | Optional, independent groupings; tenant-defined branch types |
| Phase 1 | Admission number and ID format (G-60) | Tenant-configurable pattern with a sequential default |
| Before the Phase 2 pilot | Attendance mode, statuses, edit window (Q01–Q03); late homework (Q04); calendar roles (Q10); substitution rules (G-37) | As in `OPEN_QUESTIONS.md`; substitution assigned manually by a coordinator |
| Phase 3 | Ticket states and SLA (Q09); teacher announcements (Q11); Communication Inbox in scope (G-55); SMS/WhatsApp channels (G-06) | `OPEN_QUESTIONS.md` defaults; Inbox **out** of scope; in-app, push and email only |
| Phase 4.4 | Grading (Q05), weighting (Q06), report-card samples (G-49) | **No default for the layout.** Grading defaults to configurable bands. |
| Phase 5 | Fee rules, gateway, receipts and tax, concessions, refunds, offline modes, accounting export (G-64…G-69) | **No default.** Do not build Phase 5 until these are answered. |
| Phase 6.1 | Admission form, documents, TC format (G-59, G-61) | **No default** |
| Phase 7 | Optional modules (Q18) | Deferred |
| Before production | Hosting region and compliance (Q20); retention (Q14, G-86); consent model (G-87) | India region; no production use of real data until decided |

---

## 5. Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R-1 | Finance is designed without real rules and needs rework | High (if started early) | High | Phase 5 gated on a customer workshop (G-64…G-69) |
| R-2 | Scope creep from static evidence (772 menu items, about 2,430 endpoint templates, 688 routes) | Medium | High | The master prompt makes scope come only from the tier tables. STATIC is not a requirement. |
| R-3 | Readers follow the uncorrected source labels | Medium | Medium | Errata binding through the master prompt; optionally apply in place (G-13) |
| R-4 | Look-alike UX invites IP concerns (layout, crimson brand) | Low–Medium | High | Original theme and names; tenant branding; DEVELOPMENT_RULES; no copied copy text |
| R-5 | RLS leaks through pooled connections or an ORM pattern | Medium | High | SR-TEN-3/4 tests in Phase 0.2, before any domain module |
| R-6 | The parent and student experience is entirely designed, because no account was observed | High | Medium | Usability sessions with parents and students during the Phase 2/3 pilots |
| R-7 | Substitution blocked by the missing staff-absence model | Resolved | — | `STAFF_ABSENCE` in v2 ER; build in Phase 2.3 |
| R-8 | Notification cost and regulation (SMS DLT templates, WhatsApp template approval) delays Phase 3 | Medium | Medium | Owner decision on channels before Phase 3; in-app, push and email first |

---

## 6. What the fresh session needs

**Primary:**
1. `CLAUDE_CODE_MASTER_PROMPT.md` — self-contained; start here.
2. `SECURITY_REQUIREMENTS.md`
3. `AUDIT_REPORT.md` §G (errata) and §B.3 (architecture decisions)
4. `05_DATA_MODEL/er_diagram_core_v2.mmd`

**Detailed specs** (read per step, as the master prompt directs):
- `12_CLAUDE_CODE_HANDOFF/`: blueprint, rules, DoD, sequence and system requirements. Its old master prompt is superseded.
- `03_MODULE_SPECIFICATIONS/`, `06_WORKFLOWS/`, `05_DATA_MODEL/`, `08_TESTING/`
- `OPEN_QUESTIONS.md`, `DOCUMENTATION_GAPS.md`

**Evidence** (consult only to understand a behaviour; never copy): `04_BACKEND_ANALYSIS/`, `02_FRONTEND_ANALYSIS/`, `09_VISUAL_EVIDENCE/`, `10_REA_EVIDENCE/`
