# Finance Application Analysis (Priority 2)

**Date:** 2026-11-24 · Part of Stage 1B.
**Two parts:** (A) the finance **service API surface** recovered from **existing** evidence (no re-download), and (B) the separate **Finance SPA frontend** (`orchids.finance.letseduvate.com`) — the legitimate-access determination and a ready-to-run mapping plan.

> **Discipline:** **No fee policy or payment behaviour is invented.** Field and endpoint *names* are evidence; fee *rules*, amounts, tax, and gateway behaviour are **CUSTOMER/OWNER decisions** (`DOCUMENTATION_GAPS.md` G-64…G-69) and are not inferred from names.

---

## Part A — Finance service API surface (from existing metadata)

`service=FIN` has **151 catalogued endpoints**; **16 are runtime-observed** (the transport endpoints the teacher crawl hit), **41 carry value-free response-field names**. Methods: 49 GET, 10 POST, 8 PUT, 6 GET|POST, DELETE present, **74 UNKNOWN** (path-only, no resolved call site). This is the finance **service** as called cross-service by the ERP — **not** the finance SPA's own frontend.

### A.1 Functional groupings (from path names)
| Group | Representative endpoints (value-free) | Note |
|---|---|---|
| **Student fees / siblings** | `apiV2/student`, `apiV2/parent`, `apiV2/search-siblings`, `apiV2/map-sibling`, `apiV1/sibling-concession` (11), `apiV1/crm-child-fee-link`, `apiV2/finance-session-year-list` | Links students/parents/siblings to fee accounts; **concession rules not present** |
| **Transport (runtime-observed)** | `api/v2/transport/transport-stop-point`, `api/v2/transport/get-inquiry-count` | GET, 200; query keys like `branch_id`, `route_assigned`, `page_size` — the only finance endpoints the teacher actually hit |
| **Quotations / approvals** | `apiV1/quotation`, `apiV1/quotation-approver` | Approval surface |
| **Staff finance / reimbursements** | `apiV1/v2/team-outing/{id}/finance-action`, `apiV1/v2/staff-uniform/{id}/manager-action`, `apiV1/wallet-list`, reimbursement endpoints | FIN also covers **staff** finance (reimbursements, uniforms, team-outings), not only student fees |
| **Ops / checklists / dashboards** | `apiV1/dashboard`, `apiV1/checklist-config`, `apiV1/campus-checklist-video`, `apiV1/bus-cleanliness` (10), `apiV1/monthly-security-summary` | Branch-ops finance/operations |
| **Integrations** | `ekart/orders-performance-dashboard`, `mb_droid/apiV1/student-trip-detail`, `apiV1/whatsup-log` | Third-party/logistics and messaging hooks |

### A.2 What this establishes (and its ceiling)
- The finance service spans **student fees, transport, staff reimbursements, quotations/approvals, and branch operations** — broader than "fee collection."
- Methods are resolved for ~77 of 151; **74 remain UNKNOWN** (path-only) — honestly unresolved, not guessed.
- Value-free response-field names exist for 41 endpoints (in the response-field catalogue). **No request bodies, no amounts, no rules.**
- This is the same cross-service surface noted as "141 recovered" earlier; the final catalogue holds **151** after the runtime crawl added transport endpoints.

---

## Part B — Finance SPA frontend (`orchids.finance.letseduvate.com`)

### B.1 Legitimate-access determination (performed 2026-11-24, read-only)
A single read-only GET of the site root `https://orchids.finance.letseduvate.com/` returned **HTTP 404, with no redirect** (no login/SSO redirect, no served SPA shell at the root).

**Conclusion:** the finance SPA's frontend is **not anonymously served at its root**, and there is **no public entry document** to enumerate. I did **not** path-fish for other entry points on this third-party host (that would be enumeration/probing, which is out of bounds). Legitimate access therefore requires the **authenticated handoff from the ERP** — the ERP reaches finance via `apiGateway.baseFinanceURL`, and the ERP teacher surface includes a `/finance-partner` route and an `/sso/:token_data/login`-style token handoff. In other words: the finance frontend is served **to an authenticated session that arrives via the ERP**, not to an anonymous visitor.

### B.2 Status: **PENDING an authenticated browser session**
The Claude browser extension is **not currently connected**, and the finance SPA frontend cannot be reached anonymously. So mapping the finance SPA's **own routes/components/chunks** is deferred until the owner drives an authenticated browser to it. **This is not a failure of analysis — it is a legitimate access boundary**, exactly the kind Priority 2 asked me to determine.

### B.3 Ready-to-run mapping plan (execute when the authenticated browser is available)
With the extension connected and the owner logged into the ERP, then navigating into the finance app (so the SPA loads under the authenticated session), a **read-only** pass will capture — **without accessing private records or calling undocumented APIs**:
1. **Routes** — the finance SPA's client routes (from its loaded router).
2. **Components / lazy-loaded chunks** — the asset graph of the loaded bundle (via the browser's loaded scripts / `export_web_scripts`-style capture).
3. **API service functions & endpoint definitions** — the fetch/XHR targets the finance app defines (read from the loaded client code and the network list, value-free).
4. **HTTP methods** — resolved from the finance client's own call sites.
5. **Response-field structures** — **names only**, captured value-free from responses the owner's own screens already load (no record contents retained).
6. **Finance workflows** — the screen→action→endpoint flow for fee setup, collection, receipts, concessions, refunds (observed, read-only; **no payment performed**).
7. **Role requirements** — which finance level (E017 L32 Branch Accountant, L29 Central Finance, etc.) renders which screens.

**Guardrails for that pass:** navigate-only; no form submission; **no payment, no refund, no receipt generation**; stop on 429 with cooldown; retain only value-free structure; never store a real financial value, account number, or personal record.

### B.4 What will remain out of reach even after B.3
Fee computation, gateway, receipt/tax content, concession eligibility, refund policy, offline modes, accounting export (G-64…G-69) are **CUSTOMER/OWNER/LEGAL decisions**, and all **write payloads + server rules** are SERVER-ONLY. The finance SPA frontend can show the *shape* of these screens; it cannot supply the *rules*.

See `ROLE_ACCESS_REQUIREMENTS.md` (accountant role) and `COMPLETENESS_ASSESSMENT.md` for how to enable B.3.
