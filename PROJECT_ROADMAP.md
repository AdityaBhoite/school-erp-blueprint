# Project Roadmap

The agreed sequence for the school-erp-blueprint project. Each stage completes and is reviewed before the next begins.

**Strategy (5 stages):**
1. **Stage 1 — Fully understand Eduvate.**
2. **Stage 2 — Fully understand the existing ERP implementation.**
3. **Stage 3 — Compare both systems; identify the strongest features and architectural decisions.**
4. **Stage 4 — Produce the final unified product blueprint.**
5. **Stage 5 — Begin implementation.**

> **No ERP implementation starts until the Stage 4 unified blueprint is approved.** Stage 4 itself is gated behind an approved Stage 3 comparison.

See [`CONTINUATION_ASSESSMENT.md`](CONTINUATION_ASSESSMENT.md) for the current status, Eduvate gap register, and access requirements.

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

Still open **within** Stage 1 (requires a different evidence type, not more of the same crawling) — full register and classification in [`CONTINUATION_ASSESSMENT.md §B`](CONTINUATION_ASSESSMENT.md):
- Higher-privilege role experiences inaccessible to the teacher account: **admin, accountant/finance, parent, student, transport administration** — need legitimately-provisioned accounts (owner-authorised).
- The **18 routes** not reached because of the rate limit — recoverable with the *existing* teacher account via a targeted, rate-limit-respecting re-crawl.
- The **finance** (`orchids.finance.letseduvate.com`) and **CRM** bundles, never fetched — a distinct static gap.
- Write flows and server-side enforcement (SEC-05) — out of scope for passive, read-only analysis.

None of these blocks Stage 3; they would raise fidelity on the surfaces the teacher account could not reach.

---

## Stage 2 — Existing build analysis  ·  *status: framework ready, pending location*

Independently inspect the existing in-house ERP project **when its location is provided**. Framework and output structure: [`02_EXISTING_BUILD/README.md`](02_EXISTING_BUILD/README.md).

Twelve dimensions: frontend architecture · backend architecture · database schema · authentication & authorization · implemented modules · APIs · UI/UX · business workflows · data models · technical debt · performance & security · functionality status.

This analysis is **read-only** (the existing source is never modified) and **independent** — it stands on the existing build's own code and does not assume parity with Eduvate.

---

## Stage 3 — Comparative analysis  ·  *status: framework ready, pending Stage 2*

Build a **feature-by-feature comparison** of Eduvate and the existing build. Framework and per-feature template: [`03_COMPARATIVE_ANALYSIS/README.md`](03_COMPARATIVE_ANALYSIS/README.md).

Ten dimensions:
1. Eduvate functionality
2. Existing-implementation functionality
3. UI / UX
4. Technical architecture (incl. maintainability & performance)
5. APIs
6. Database / data model
7. Roles & permissions
8. Business workflows
9. Security
10. Missing & additional capabilities

Classify every feature as exactly one of:
- **RETAIN FROM EDUVATE**
- **RETAIN FROM EXISTING BUILD**
- **IMPROVE**
- **COMBINE**
- **REDESIGN**
- **NEW FEATURE**
- **DISCARD**

The output is an **approved comparative architecture** — the gate to Stage 4.

---

## Stage 4 — Final unified product blueprint  ·  *status: pending*

Produce the **clean-room, multi-tenant school SaaS ERP blueprint** from the strongest findings of both systems and the approved comparison.

- Clean-room: designed from the comparison and requirements, **not** copied from any proprietary source.
- Multi-tenant: tenant → branch (campus) → academic year → class-section scoping, with enforced server-side authorization.
- Produces: product scope, domain model, API design, UX direction, multi-tenant architecture, and the Architecture Decision Records in [`05_ARCHITECTURE_DECISIONS/`](05_ARCHITECTURE_DECISIONS/).
- **No implementation begins until this blueprint is approved.**

---

## Stage 5 — Implementation  ·  *status: pending (not started)*

Build the product against the approved Stage 4 blueprint. Begins **only** after Stage 4 approval. The new product's work lives in [`04_NEW_PRODUCT/`](04_NEW_PRODUCT/).
