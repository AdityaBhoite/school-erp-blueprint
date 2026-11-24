# Workflow Evidence Gaps (Priority 6)

**Date:** 2026-11-24 · Part of Stage 1B.
**Purpose:** For each business workflow, state which evidence type *can* establish it, and mark what is **write-required** or **fundamentally server-side** (never recoverable from browser inspection of the client).

> **Principle (stated plainly):** downloading and reading the client JavaScript reveals the **frontend's intended calls and shapes**, not the server's rules, validation, state transitions, or data. Client-side reverse engineering does **not** reveal the complete backend. The columns below keep that honest.

## Evidence-type legend
- **STATIC** — derivable from the already-analysed client code/metadata (routes, call sites, field names). *No new work needed.*
- **RUNTIME-RO** — obtainable by **read-only, navigate-only** runtime observation (the Phase 3 method), given the right role. *Needs an authorised session; no writes.*
- **DOCS** — obtainable only from authoritative product documentation or an owner/customer SME. *Not a technical-discovery item.*
- **TEST-ENV** — would require **safe interaction in a non-production test environment** (performing the action on throwaway data). *Explicitly out of scope on production; listed so the boundary is clear.*
- **WRITE-REQ** — can only be seen by performing a consequential write (submit/approve/publish/pay). **Never done.**
- **SERVER-ONLY** — lives in the server/DB and is **not recoverable** from the client at all.

## A. The 15 documented workflows

| WF | Workflow | Reads (shape) | Write/transition truth | Rules/params | Notes |
|---|---|---|---|---|---|
| WF-01 | Academic-year setup & rollover | STATIC + RUNTIME-RO (admin) | WRITE-REQ / TEST-ENV | DOCS (rollover policy) | Admin role needed even to see the screens |
| WF-02 | Academic calendar | RUNTIME-RO ✓ (teacher saw reads) | WRITE-REQ (publish) | DOCS (event types G-33, recurrence G-34) | Publish state machine is WRITE-REQ |
| WF-03 | Timetable & substitution | RUNTIME-RO (grid seen empty) | WRITE-REQ (assign sub) | DOCS (substitution rules G-37, split-period G-36) | Coordinator role owns assignment |
| WF-04 | Daily diary & homework | RUNTIME-RO ✓ (reads seen) | WRITE-REQ (create/evaluate) | DOCS (late policy G-42, aggregate boundary G-41) | Editor UI never opened (G-39) |
| WF-05 | Student attendance | RUNTIME-RO (filters/empty roster) | WRITE-REQ (mark/edit) | DOCS (mode/status/edit-window Q01–Q03) | Daily vs period undecided |
| WF-06 | Assessment & report card | RUNTIME-RO ✓ (lists/dashboard) | WRITE-REQ (enter/publish marks) | DOCS (grading Q05, weighting Q06, template G-49) | Report-card layout needs a CUSTOMER sample |
| WF-07 | Admission & enrolment | STATIC only (route names) | WRITE-REQ | DOCS (form fields G-59, fee) | Admissions counselor role needed |
| WF-08 | Student lifecycle approvals | STATIC | WRITE-REQ (approve) | DOCS (promotion G-62, approval chain) | Principal/central roles |
| WF-09 | Fees collection | STATIC (menus) + finance API catalogue | WRITE-REQ (pay) | DOCS (fee computation G-64, gateway G-65) | Accountant role + Finance SPA; **no fee rules invented** |
| WF-10 | Concession / refund / wallet | STATIC + finance API (`sibling-concession` endpoints seen) | WRITE-REQ | DOCS (concession G-67, refund G-68) | Accountant role |
| WF-11 | Announcements | RUNTIME-RO ✓ (lists) | WRITE-REQ (publish) | DOCS (audience G-53, teacher-create Q11) | Compose not seen |
| WF-12 | Parent ticket | STATIC (teacher denied tickets) | WRITE-REQ | DOCS (statuses/SLA Q09) | Parent + support roles |
| WF-13 | Staff leave | RUNTIME-RO (HR self-service seen) | WRITE-REQ (apply/approve) | DOCS (leave types G-74) | HR scope |
| WF-14 | Profile-change approval | STATIC/RUNTIME-RO | WRITE-REQ (approve) | DOCS | Approval chain |
| WF-15 | Online class | RUNTIME-RO (lists) | WRITE-REQ (schedule/join) | DOCS (provider Q18) | e-book listing returned 404 |

## B. What read-only runtime observation *can* still add (given roles)
- Render-vs-redirect outcome and **value-free response-field names** for each role's reachable screens.
- Which catalogued endpoints actually fire, their HTTP status codes, and query-parameter names.
- UX patterns (filters, tables, drawers) for screens never seen (admin, finance, parent, student).
It cannot add: the request bodies, the server's acceptance/rejection rules, or the resulting state.

## C. Fundamentally inaccessible from client inspection (SERVER-ONLY)
These are **not gaps to be closed by more browser work** — they are design decisions for the replacement (per `DOCUMENTATION_GAPS.md` §2):
- Every **write payload** schema (create/update/delete/approve) — G-14.
- **Server-side authorization** enforcement behaviour — SEC-05 / G-15.
- **Database schema**, indexes, constraints, triggers, and the server framework internals — only INFERRED (Django/DRF).
- **Validation rules, idempotency, and error semantics** behind each endpoint — G-04.
- **Enum values** and business-rule parameters held server-side — G-05 / G-17.
- The **access-matrix response** contents per level and the meaning of non-teacher levels — G-20.
- Background jobs, schedulers, outbox/notification dispatch internals.

## D. Business rules that need customer/owner input (not discovery)
Fee computation, gateway, receipts/tax, concessions, refunds, offline modes, accounting export (G-64…G-69); grading/weighting and report-card layout (G-48/G-49); attendance mode/status/edit-window (Q01–Q03); ticket statuses/SLA (Q09); admission forms & TC (G-59/G-61); promotion rules (G-62); retention/consent (G-12/G-86/G-87). See `OPEN_QUESTIONS.md` and `DOCUMENTATION_GAPS.md`.

**Bottom line:** even with every role observed read-only, categories **C** (server-only) and **D** (business rules) remain. Eduvate can be understood as a *frontend contract and UX surface* comprehensively; it cannot be understood as a *complete backend* from the client. The replacement supplies C and D by design, not by inference.
