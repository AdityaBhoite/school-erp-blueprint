# school-erp-blueprint

A permanent, version-controlled **source of truth** for a commercial multi-school SaaS ERP project.

This repository is **private**. It holds research, analysis, and (later) design work. **No ERP implementation has begun** — this stage is about understanding, not building.

## What this project is

The goal is to design and eventually build a clean-room, multi-tenant school SaaS ERP that combines the strongest characteristics of two systems:

1. **Eduvate** — a commercial school-chain ERP studied here through **read-only, legitimate-access reverse engineering** (one teacher account). The research captures the system's *structure* — routes, API surface, HTTP methods, response-field names, roles — as value-free, derived metadata.
2. **An existing in-house ERP build** — to be analysed in a later stage, once its location is provided.

A feature-by-feature comparison of the two will then inform a new product design.

> **Provenance & IP discipline.** This repository contains only **derived, value-free metadata** (paths, methods, field names, structure) and the project's own written analysis. It contains **no** proprietary application source code, **no** credentials or tokens, and **no** personal data. Screenshots are manually redacted. The eventual product is to be designed clean-room; nothing here is to be copied as source. See `01_EDUVATE_RESEARCH/` for the evidence-labelling discipline (OBSERVED / STATIC / INFERRED / PROPOSED / UNKNOWN) used throughout.

## Repository layout

| Path | Purpose |
|---|---|
| [`PROJECT_ROADMAP.md`](PROJECT_ROADMAP.md) | The agreed five-stage project sequence |
| [`CONTINUATION_ASSESSMENT.md`](CONTINUATION_ASSESSMENT.md) | Current status, the Eduvate gap register, access requirements, and next milestones |
| [`GITHUB_UPLOAD_AUDIT.md`](GITHUB_UPLOAD_AUDIT.md) | The privacy/secret audit performed before the first upload |
| [`FILE_MANIFEST_SHA256.txt`](FILE_MANIFEST_SHA256.txt) | SHA-256 manifest of every committed file |
| `01_EDUVATE_RESEARCH/` | **Stage 1 (done):** the complete, sanitised Eduvate reverse-engineering package |
| `02_EXISTING_BUILD/` | **Stage 2 (pending):** analysis of the existing in-house ERP — framework ready |
| `03_COMPARATIVE_ANALYSIS/` | **Stage 3 (pending):** feature-by-feature comparison of both systems — framework ready |
| `04_NEW_PRODUCT/` | **Stage 4 (blueprint) & Stage 5 (implementation), pending:** the new commercial product |
| `05_ARCHITECTURE_DECISIONS/` | Architecture Decision Records (ADRs) accumulated across all stages |

## Status

- **Stage 1 — Eduvate reverse engineering:** complete and verified. The package in `01_EDUVATE_RESEARCH/` includes the investigation, a documentation audit with errata, a full static analysis of 1,954 JavaScript chunks (original raw corpus deleted), a consolidated API catalogue (6,577 rows), route/component maps, call-site analysis, response-field catalogues, role/permission findings, 19 module specs, 15 workflow specs, the data model + ER diagrams, acceptance criteria/test cases, the read-only route-crawl results, the Phase 3 forensic audit, and the proof package with checksums. The call-site counts were reconciled by a final documentation erratum (`01_EDUVATE_RESEARCH/13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md`).
- **Stages 2–5:** not started. Stage 2 and Stage 3 now have ready frameworks (`02_EXISTING_BUILD/README.md`, `03_COMPARATIVE_ANALYSIS/README.md`); Stage 4 (blueprint) and Stage 5 (implementation) remain gated. The Eduvate gap register and access requirements are in `CONTINUATION_ASSESSMENT.md`.

## Ground rules

- No implementation begins until the **Stage 4 unified blueprint** is approved (itself gated behind the approved Stage 3 comparison).
- Keep the five evidence labels strict; never present code-derived or inferred behaviour as observed.
- Do not reconnect to or re-probe the original Eduvate system **without the owner's explicit, case-by-case authorisation** (see `CONTINUATION_ASSESSMENT.md` §B/§E). No privilege escalation or write actions, ever.
- Never commit secrets, tokens, personal data, or proprietary source (see `.gitignore`).
