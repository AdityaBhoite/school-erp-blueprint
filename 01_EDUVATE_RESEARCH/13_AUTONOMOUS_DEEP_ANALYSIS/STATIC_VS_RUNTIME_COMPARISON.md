# Static vs Runtime Comparison

Static discovery is **not** runtime verification. This document keeps the two apart and states exactly what the runtime evidence confirms about the static surface. Runtime evidence now includes the Phase 3 read-only route crawl (2026-11-23, one teacher account) in addition to E016 and the Phase 1/2 teacher tour.

## 1. The two populations
| Population | Count | Nature |
|---|---:|---|
| Distinct endpoints found **statically** | 6,539 | Referenced in shipped code; reachability unproven |
| Runtime-only endpoints (observed, no static call site) | 38 | Added to the catalog from the crawl; mostly third-party (Mattermost chat) and profile-page calls not built in the ERP's own bundles |
| **Total catalog rows** | **6,577** | Static ∪ runtime-only |
| Endpoints observed on the wire (E016 + Phase 3 crawl) | **171** | Actually issued by the app for the teacher account |

Of the 114 endpoints the crawl observed, 76 matched an existing static catalog row (39 of which were newly flipped to RUNTIME_OBSERVED) and 38 were runtime-only.

## 2. What the overlap proves
- **Method validation:** of **107** endpoints with both a static call-site method and a runtime method, **104 agree directly**. The 3 remainder are the benign multi-method REST pattern — static resolved a write method (POST/PUT) on a collection path while the crawl observed GET/list on that same path (`/travel/travel-request`, `/academic/imprest-event`, `/school-feed/feed-draft`). **Zero true contradictions** (no static-GET-vs-runtime-POST case). The static method resolution is therefore trustworthy for the 5,256 STATIC_CALLSITE_CONFIRMED endpoints never hit at runtime.
- Where both exist, static response-field names are a **subset** of the runtime shape's keys — the frontend reads fewer fields than the server returns. No contradictions.

## 3. What static discovery does NOT establish
- **Reachability.** 6,406 catalog endpoints were never observed at runtime. Absence means the teacher crawl/tour did not trigger them — not that they are unreachable, unused or hidden. Most require another role, a write action, or a screen that did not render for a teacher (see §5).
- **Server acceptance.** Endpoints were never probed directly; only whatever a *navigated* screen chose to fetch was seen. A resolved `POST /x/` means the client *would* POST there, not that the server accepts it.
- **Currency.** A catalog path may be legacy. 1,033 STATIC_PATH_ONLY endpoints have no resolved call site at all — some may be dead catalog entries.

## 4. Method coverage by classification (post-crawl)
| Classification | Count | Method known? |
|---|---:|---|
| RUNTIME_OBSERVED | 171 | Yes (observed on the wire) |
| STATIC_CALLSITE_CONFIRMED | 5,256 | Yes (from code call site) |
| STATIC_PATH_ONLY | 1,033 | **No** (catalog path only) |
| DYNAMIC_ENDPOINT_TEMPLATE | 117 | Template; method where a call site resolved it |
| **Total** | **6,577** | 5,427 rows (82.5%) carry a resolved method |

## 5. What the crawl changed, and its limits
- **Runtime coverage rose 94 → 171 endpoints** (+77) and added 72 new response schemas. It changed **nothing** about the static population; it only *promoted* confidence where a screen rendered.
- **Reachability is role-scoped.** 359 of 408 attempted ERP routes redirected a teacher to `/erp/user-profile` — runtime proof of role-based route guarding, but it means the teacher account could *not* exercise most endpoints. Promotions concentrate in teacher-facing areas (attendance, timetable, calendar, assessment, diary, student information, chat, transport-stop views) and the universally-loaded profile/bootstrap calls.
- **Not full coverage.** 18 queued routes were not reached (429 stop). Everything still unobserved stays explicitly STATIC.

_Counts reconcile with `API_ENDPOINT_CATALOG.csv`, `crawl_summary.json` and `method_compare.json`._
