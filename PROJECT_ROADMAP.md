# Project Roadmap

The agreed sequence for the school-erp-blueprint project. Each stage completes and is reviewed before the next begins. **No ERP implementation starts until the comparative architecture is approved (end of Stage 3 → Stage 4 design).**

---

## Stage 1 — Eduvate reverse engineering  ·  *status: complete & verified*

Understand all three discovered application surfaces:
- **ERP** (`/erp`)
- **Recruitment / HRMS** (`/recruitment`)
- **CRM** (`/crm`)

Work done (in `01_EDUVATE_RESEARCH/`):
- Read-only, legitimate-access investigation (one teacher account), with strict evidence labelling (OBSERVED / STATIC / INFERRED / PROPOSED / UNKNOWN).
- Full static analysis of 1,954 JavaScript chunks (0 parse errors); the original raw corpus has been deleted — only value-free derived metadata is retained.
- Consolidated API catalogue (6,577 rows), route→component→service maps, call-site analysis, response-field catalogues, role/permission findings.
- 19 module specifications, 15 workflow specifications, data model + ER diagrams, acceptance criteria and test cases.
- Automated read-only route crawl (408 of 426 safe routes; stopped on the server's rate limit), Phase 3 forensic audit, and a proof package with SHA-256 checksums.

Still open within Stage 1 (requires a different evidence type, not more crawling):
- **Deepen the investigation of modules and roles inaccessible to the Teacher account** — admin, accountant, principal, front-office, parent, student. The teacher crawl showed ~88% of ERP routes are role-gated; those screens' own data needs a higher-privilege account.
- Continue to **distinguish observed functionality from code-derived functionality** — runtime-observed endpoints (171) stay separate from statically-discovered candidates.
- Server-side enforcement behaviour and write/interaction flows remain out of scope for passive, read-only analysis.

---

## Stage 2 — Existing build analysis  ·  *status: pending (awaiting location)*

Independently inspect the existing in-house ERP project **when its location is provided**.

Document:
- Architecture (services, boundaries, deployment).
- Functionality and feature coverage.
- Database and data model.
- Frontend (framework, structure, state, UX).
- Backend (framework, APIs, auth, jobs).
- UX and usability.
- Known limitations and technical debt.

This analysis is independent — it stands on the existing build's own code and does not assume parity with Eduvate.

---

## Stage 3 — Comparative analysis  ·  *status: pending*

Build a **feature-by-feature comparison** across these dimensions:
- Eduvate functionality
- Existing-build functionality
- Technical architecture
- UX and usability
- Business workflows
- Permissions / roles
- APIs
- Data model
- Security
- Performance
- Maintainability

Classify every feature as one of:
- **Keep from Eduvate**
- **Keep from existing build**
- **Improve**
- **Combine**
- **Discard**
- **New feature**

The output is an approved comparative architecture — the gate to Stage 4.

---

## Stage 4 — New commercial product  ·  *status: pending*

Design a **clean-room, multi-tenant school SaaS ERP** using the strongest findings from both systems.

- Clean-room: designed from the comparison and requirements, not copied from any proprietary source.
- Multi-tenant: tenant → branch (campus) → academic year → class-section scoping, with enforced server-side authorization.
- **No implementation begins until the comparative architecture is approved.**

Architecture Decision Records produced along the way live in `05_ARCHITECTURE_DECISIONS/`.
