# API Method Resolution

How each endpoint's HTTP method was established, and how far that can be trusted.

## 1. Classification of 6539 distinct endpoints
| Status | Count | Meaning |
|---|---|---|
| RUNTIME_OBSERVED | 94 | Method seen on the wire during the Phase 1/2 teacher tour (E016). Highest confidence. |
| STATIC_CALLSITE_CONFIRMED | 5287 | At least one resolved call site fixes the method from code (e.g. `client.post(path)`, `{method:'put'}`). |
| STATIC_PATH_ONLY | 1041 | Path exists in a catalog or as a string, but no call site resolved the method. Method **UNKNOWN**. |
| DYNAMIC_ENDPOINT_TEMPLATE | 117 | Path is built at runtime (`/x/${id}/y/`); recorded as a template. |
| UNRESOLVED | 0 | Insufficient evidence. |

**Methods resolved for 5381 of 6539 endpoints (82%). 1158 remain method-unknown.** 1164 endpoints carry more than one method (the same REST path used for GET+POST+PUT+DELETE), which is why there are more call sites than endpoints: the exported `API_CALLSITE_INDEX.csv` holds **8,530** call-site records across **5,364** endpoints (the catalog attributes **10,006** occurrences via `callsite_count`). See `CALLSITE_COUNT_ERRATUM.md`.

## 2. How methods were read from code (call-site basis)
The basis breakdown below is from the static pass's own counters. **Erratum (2026-11-24):** the pass's "11,146 resolved / 11,120 with a method" totals came from in-memory counters that were **not retained** and are not reproducible from the shipped files; cite instead the exported index (8,530 records, 8,527 with a method, 8,513 to a known client) — see `CALLSITE_COUNT_ERRATUM.md`. The per-basis counts below derive from those same non-retained counters and are kept for provenance only.

| Basis | Call sites |
|---|---|
| direct method call on resolved HTTP client | 10912 |
| wrapper <fn> (direct method call on resolved HTTP client) | 56 |
| fetch default (no method option) | 46 |
| direct .get() call with path-like URL (receiver not resolved to a know | 32 |
| wrapper <fn> (fetch default (no method option)) | 27 |
| fetch method option literal | 25 |
| fetch options non-literal | 17 |
| config method literal | 7 |
| direct .delete() call with path-like URL (receiver not resolved to a k | 6 |
| direct .post() call with path-like URL (receiver not resolved to a kno | 4 |

Resolution order (per the task's rule — never infer from path shape):
1. **Direct method call** — `client.get/post/put/patch/delete(url)`. The receiver must resolve to a known HTTP client (axios instance/fetch), else it is marked "receiver not resolved".
2. **Config object** — `client({url, method})` or `client.request({...})`; method read from the `method` literal, or the axios default (GET) when the config omits `method`.
3. **Wrapper defaults** — a helper whose body is `client.<m>(itsUrlParam)`; the method comes from the wrapper body, applied at every caller. 90 call sites route through such wrappers.
4. **fetch** — `fetch(url, opts)`; method from `opts.method` literal, else the fetch default GET.
5. When the method came from a **non-literal** (a variable or parameter), it is recorded as **UNKNOWN**, never guessed.

## 3. Validation against runtime truth
The 94 endpoints observed on the wire (E016) are an independent check. Excluding third-party (Clarity) and Mattermost/static-asset calls, **89 of 89 runtime GET/POST endpoints have their method independently confirmed by at least one static call site, with zero disagreements.** This is the evidence that the call-site method resolution is sound.

## 4. What a resolved method does and does not prove
- It proves the shipped frontend, at that call site, issues that method to that path.
- It does **not** prove the endpoint is reachable for any given role, that the server accepts it, or that it is still in use. Server behaviour remains UNKNOWN (no endpoint was probed).
