# Security Requirements

## 1. Observations of the original (for awareness, not exploitation)
These were noted passively in shipped frontend code or observed responses. No exploitation was attempted. Secret values were **not** recorded.

| ID | Observation | Evidence | Class | Lesson for the replacement |
|---|---|---|---|---|
| SEC-01 | The ERP client bundle contains configuration entries named `xAPIKey`, `edunavKey` and `onlyOffice.jwtSecret`. A JWT signing secret in a browser bundle lets anyone forge document-server tokens. | E010x config object (keys only) | STATIC, HIGH | Never ship secrets to the browser; sign server-side; rotate. |
| SEC-02 | The analytics SDK (`unified-mat.js`) embeds a live API key. | E004x | STATIC, HIGH | Use public, origin-restricted ingestion keys only. |
| SEC-03 | Access and refresh tokens are stored in **localStorage** (`user_details`, `recruitmentLoginDetails`, `crmLoginDetails`), which XSS can read. | E004x/E010x | STATIC, HIGH | httpOnly Secure SameSite cookies; CSP. |
| SEC-04 | Route-level permissions include **hard-coded employee IDs** (`erpIds` allow-lists) in frontend code. | E007 (redacted) | STATIC, HIGH | Grants must be server-side data with audit; never in code. |
| SEC-05 | Authorization is visibly enforced client-side (redirect to profile). Server-side enforcement for every endpoint could not be verified (no probing performed). | E016 | OBSERVED/UNKNOWN | Enforce authorization server-side on every endpoint; test with negative cases. |
| SEC-06 | Sensitive HR fields (Aadhaar, PAN, bank account) are masked in the UI with a reveal button. Changes go through an approval workflow. | E002 | OBSERVED, HIGH | Good pattern: field-level masking, reveal permission, audit of reveals, maker-checker for edits. |
| SEC-07 | Third-party session recording (Microsoft Clarity) runs on authenticated pages showing student and staff data. | E016 | OBSERVED, HIGH | Avoid session replay on PII screens, or mask all inputs and text; update the privacy notice. |
| SEC-08 | Impersonation ("switch user") exists with an `X-Is-Switch` header. | STATIC | HIGH | Super-admin only, audited, time-boxed, banner shown, sensitive actions blocked. |
| SEC-09 | Requests made before the token was ready received 403 and were retried. | E016 | OBSERVED | Bootstrap auth before firing data requests; avoid noisy 403s. |

## 2. Requirements for the replacement (PROPOSED)
### Authentication
- SR-AUTH-1: Password login with Argon2id hashing; lockout/backoff after repeated failures; optional OTP (SMS/email) and TOTP 2FA for staff; enforced for admins.
- SR-AUTH-2: Access tokens ≤ 15 min; rotating refresh tokens with reuse detection; httpOnly/Secure/SameSite=Lax cookies; CSRF protection for cookie auth.
- SR-AUTH-3: Forced password change on first login (`force_update` equivalent); password reset via signed single-use links.
- SR-AUTH-4: Parent accounts can be linked to several children (siblings) with explicit relationship records.

### Authorization and tenancy
- SR-AZ-1: Every request is resolved to `(tenant, user, roles, scopes)`; every query is tenant-filtered in a data-access layer (row-level security or equivalent) — no cross-tenant reads possible by construction.
- SR-AZ-2: Permissions are `module.resource.action` strings granted to roles; roles are assigned with scopes (tenant, branch (= school campus), academic year, grade offering / class section, class subject; audit ER-41). Teacher data scope comes from TeachingAssignment.
- SR-AZ-3: Teachers can only read/write data for sections/subjects they are allocated to (timetable or subject mapping); parents only for their linked children; students only for themselves.
- SR-AZ-4: Field-level protection for sensitive PII (government IDs, bank, health/SEN, contact details) with reveal permission and audit log.
- SR-AZ-5: Frontend navigation is derived from the same permission set (single source of truth) and is never relied on for enforcement.

### Data protection
- SR-DP-1: Compliance with India's DPDP Act 2023 (children's data: verifiable parental consent, no behavioural tracking or targeted ads to children), plus GDPR-style controls if serving other regions.
- SR-DP-2: Encryption in transit (TLS 1.2+) and at rest (database, backups, object storage); signed, expiring URLs for file downloads.
- SR-DP-3: Data minimisation in API responses (no full objects where IDs suffice); no PII in URLs or query strings; no PII in logs.
- SR-DP-4: Retention policies per data class; export and deletion workflows.

### Application security
- SR-AS-1: OWASP ASVS L2 baseline; CSP without `unsafe-inline`; dependency scanning; secret scanning in CI.
- SR-AS-2: File uploads: type and size allow-lists, antivirus scan, stored outside the web root, served via signed URLs.
- SR-AS-3: Rate limiting on auth, OTP, search and export endpoints.
- SR-AS-4: Audit log (who, what, when, before/after) for all writes to student, attendance, marks, fees and permission data; append-only and tenant-scoped.
- SR-AS-5: No secrets in frontend bundles (build-time check).
