# 03 — Comparative Analysis (Stage 3)

*Status: **framework ready — awaiting Stage 2.** No verdicts recorded.*

This folder compares **Eduvate** (Stage 1, in hand) against your **existing build** (Stage 2, pending) feature by feature, and classifies every finding. Its approved output is the **gate to Stage 4** (the unified product blueprint). Nothing is pre-decided; this file defines only the *structure* the comparison will fill.

## The ten comparison dimensions

| # | Dimension | Output file |
|---|---|---|
| 1 | Feature functionality — Eduvate | `01_functionality.md` |
| 2 | Feature functionality — existing build | `01_functionality.md` |
| 3 | UI / UX | `02_ui_ux.md` |
| 4 | Technical architecture | `03_architecture.md` |
| 5 | APIs | `04_apis.md` |
| 6 | Database / data model | `05_database.md` |
| 7 | Roles & permissions | `06_roles_permissions.md` |
| 8 | Business workflows | `07_workflows.md` |
| 9 | Security | `08_security.md` |
| 10 | Missing & additional capabilities (gaps on each side) | `09_gaps.md` |

A `00_COMPARISON_SUMMARY.md` will roll these up into the recommended comparative architecture put forward for approval.

## Classification taxonomy

Every feature/decision is tagged with exactly one verdict:

| Verdict | Meaning |
|---|---|
| **RETAIN FROM EDUVATE** | Eduvate's version is stronger; adopt its approach (clean-room, not copied). |
| **RETAIN FROM EXISTING BUILD** | The existing build's version is stronger; keep it. |
| **IMPROVE** | The concept is right in one system but needs enhancement before use. |
| **COMBINE** | Take the best of both into a single design. |
| **REDESIGN** | Both are inadequate; rethink the feature from first principles. |
| **NEW FEATURE** | Neither has it; the commercial product needs it. |
| **DISCARD** | Present in one/both but not worth carrying forward. |

## Per-feature comparison template

Each feature row in the dimension files follows this shape:

| Field | Notes |
|---|---|
| Feature / capability | The unit being compared |
| Eduvate | State + evidence label (OBSERVED / STATIC / INFERRED / UNKNOWN) + source citation |
| Existing build | State + evidence label + source citation |
| **Verdict** | One of the seven above |
| Rationale | Why — tied to evidence, not preference |
| Target behaviour | One-line statement of what the new product should do (feeds Stage 4) |
| Open decision | Any owner/customer input still required (links to `OPEN_QUESTIONS.md`) |

Example (illustrative only — **not** a real verdict):

| Feature | Eduvate | Existing build | Verdict | Rationale | Target | Open decision |
|---|---|---|---|---|---|---|
| Attendance capture | OBSERVED (teacher) — period/day modes | *(pending Stage 2)* | *(pending)* | *(pending evidence)* | *(pending)* | Q01–Q03 |

## Evidence-discipline note

- Eduvate facts are quoted from `../01_EDUVATE_RESEARCH/`, carrying their original labels. Where Stage 1 only had STATIC or INFERRED evidence (admin, finance, parent, student — see `../CONTINUATION_ASSESSMENT.md §B`), the comparison says so and does not inflate it to OBSERVED.
- A verdict is only as strong as the weaker side's evidence; low-confidence comparisons are flagged, not hidden.

## What this stage does **not** do

- No verdict is recorded until both Stage 1 and Stage 2 evidence exist.
- No product design or code — that is Stage 4 / Stage 5.

See [`../PROJECT_ROADMAP.md`](../PROJECT_ROADMAP.md) and [`../CONTINUATION_ASSESSMENT.md`](../CONTINUATION_ASSESSMENT.md).
