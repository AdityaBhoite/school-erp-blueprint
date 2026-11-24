# Remaining Unknowns

What Phase 3 did **not** resolve, and why. Phase 3 closed the "no HTTP methods" gap and mapped the whole API/route surface statically; the items below need a different evidence type.

## 1. The live crawl — EXECUTED (2026-11-23), these are now resolved or newly bounded
| Was unknown | Outcome |
|---|---|
| Which of the 426 safe screens render vs redirect for a teacher | **Resolved for 408**: 42 render, 359 role-redirect to profile, 7 other. See `ROUTE_CRAWL_RESULTS.csv`. |
| Runtime response **shapes** for teacher-reachable endpoints | **72 new schemas** captured value-free (`E019_phase3_crawl_api_shapes.json`) |
| Which STATIC endpoints are actually fired by a page | **114 confirmed fired** (39 STATIC→RUNTIME promotions + 38 runtime-only + overlaps) |
| HTTP method agreement static vs runtime | **104/107 direct, 0 contradictions** |

### Still open after the crawl
| Unknown | Why it stayed open |
|---|---|
| **18 queued routes never visited** | The crawl stopped on the server's 429 rate limit at route 408/426. Re-running was declined to respect the rate limit. Several are teacher-facing and would likely have rendered — their non-coverage is the rate-limit stop, not role restriction. Full list below. |

**The 18 unreached ERP routes (all would need a fresh, rate-limited-respecting re-run):** `/communication-inbox`, `/dashboard`, `/event-manager`, `/feature-request/dashboard`, `/feedback`, `/goal-tracker`, `/homework`, `/homework-dashboard`, `/meeting-scheduler`, `/myschoolgate`, `/observation`, `/online-books`, `/school-clicker`, `/school-wall`, `/teacher-observation`, `/test-module`, `/test-result`, `/ticket-dashboard`.
| The 359 role-gated ERP screens' own data/shapes | They redirect a teacher to the profile page; only a higher-privilege account could render them (out of scope — one account) |
| STATIC_PATH_ONLY endpoints a teacher never triggers | A teacher cannot reach most admin/finance screens, so their catalog paths remain path-only; this needs another role, not another crawl |

## 2. Needs another role (cannot be obtained safely or at all)
| Unknown | Why |
|---|---|
| Admin, accountant, principal, front-office runtime behaviour | Only a Teacher account exists; impersonation/privilege escalation is out of bounds |
| Parent and student experience | No such account; the whole parent/student UI is static-only |
| HRMS/CRM beyond self-service | Teacher has limited HR access and no CRM flag |
| The server-side access-matrix contents per level | It is server data (`/erp_user/access-control/matrix/`); the client only caches what the server sends for the current user |

## 3. Needs a write action (deliberately never performed)
| Unknown | Why |
|---|---|
| Request bodies for create/update/approve/pay | The crawl is navigate-only; no form is ever submitted. Static body **key names** (1,720 endpoints) are the ceiling of what passive analysis can give. |
| State machines' real transitions (calendar publish, marks publish, approvals) | Observing them requires performing them |
| Idempotency, validation and error responses | Require sending requests |

## 4. Cannot be recovered from the shipped artifact
| Unknown | Why |
|---|---|
| Original source names / comments | No source maps are shipped (0 of 1,954 files) |
| Server framework internals, DB schema | Not in the client; Django/DRF remains INFERRED |
| 1,158 endpoints' HTTP methods | Their only evidence is a catalog path with no resolved call site or a non-literal method; recorded UNKNOWN, not guessed |
| Exact meaning of some minified data flows | Single-letter bindings; surfaced as UNKNOWN |
| Finance **SPA** internal screens | `orchids.finance.letseduvate.com` is a separate app with its own bundle that was not fetched; only the finance endpoints the ERP app calls (141) are recovered |

## 5. Business decisions (unchanged)
All open questions in `OPEN_QUESTIONS.md` and the decision gaps in `DOCUMENTATION_GAPS.md` remain business/owner/customer decisions. Phase 3 is technical discovery; it does not resolve product choices (fee rules, grading, retention, product name, etc.).

## 6. Explicitly not attempted (by rule)
Privilege escalation, authentication bypass, ID enumeration, direct API invocation, request replay, fuzzing, and any write. None was performed; none is a gap to be "closed" by doing it.
"""