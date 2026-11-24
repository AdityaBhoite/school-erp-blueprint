# Security Requirements: Independent Replacement School ERP

| | |
|---|---|
| Status | **PROPOSED, normative for the build.** MUST and SHOULD follow RFC 2119. |
| Created | 2026-11-23, as task F of the documentation audit (`AUDIT_REPORT.md` §F) |
| Relationship | Extends `07_NON_FUNCTIONAL_REQUIREMENTS/security.md` §2 (SR-AUTH-*, SR-AZ-*, SR-DP-*, SR-AS-*). Those stay valid; where both cover the same point, the more specific requirement here wins. |
| Scope | Every module of the replacement, including workers, file storage and integrations |
| Evidence note | The observations below were made **passively**, from shipped frontend code and value-free response shapes. Nothing in the original was probed or exploited, and no secret value was recorded. The requirements describe the new system, not the original. |

Some numeric defaults below (timeouts, expiries) are PROPOSED values. Each one is tenant-configurable where marked and needs owner confirmation; record each confirmation as an ADR.

---

## 1. From observed weaknesses to requirements

| Obs. | What was seen in the original | Class | Addressed by |
|---|---|---|---|
| SEC-01 | Client bundle contains config keys `xAPIKey`, `edunavKey` and `onlyOffice.jwtSecret` (a document-server signing secret) | STATIC, HIGH | SR-SEC-1…4, SR-SEC-8 |
| SEC-02 | The analytics SDK embeds a live API key | STATIC, HIGH | SR-SEC-4, SR-STU-5 |
| SEC-03 | Access and refresh tokens and the user profile are stored in `localStorage`, one record per app | STATIC, HIGH | SR-SES-1, SR-SES-2, SR-EXT-2 |
| SEC-04 | Route permissions contain hard-coded employee-ID allow-lists; the access matrix is cached in `localStorage` for 1 h | STATIC, HIGH | SR-ID-1…3, SR-AUTHZ-5, SR-AUTHZ-6 |
| SEC-05 | Authorization is visibly enforced client-side (redirect to profile). Server-side enforcement could not be verified. | OBSERVED / UNKNOWN | SR-AUTHZ-1…9 |
| SEC-06 | HR identity and bank fields are masked with a reveal button; sensitive changes need approval (a good pattern) | OBSERVED, HIGH | SR-STU-1…3 (keep and extend) |
| SEC-07 | Third-party session replay (Microsoft Clarity) runs on authenticated pages that show personal data | OBSERVED, HIGH | SR-STU-5, SR-EXT-2 |
| SEC-08 | Impersonation ("switch user") via an `X-Is-Switch` header and a client flag | STATIC, HIGH | SR-EXT-1, SR-AUD-2 |
| SEC-09 | Data requests fired before the token was ready got 403 and were retried | OBSERVED | SR-SES-10 |
| SEC-10 | The online-class list response (teacher account) includes `zoom_start_url` (a host link), `recording_password`, `recording_play_url` and `class_link`. What students receive is UNKNOWN. | OBSERVED (shape) | SR-STU-6 |
| SEC-11 | Self-service endpoints take the subject's identifier as a query parameter (`erp_id`, `employee_id` on payslip, attendance, assets, reimbursements and delegation). Routes embed `:erpId` in receipt URLs. Ownership checks could not be verified. | OBSERVED (pattern); risk INFERRED | SR-AUTHZ-2, SR-AUTHZ-3, SR-ID-4, SR-STU-10 |
| SEC-12 | Shared services select the tenant from a client-supplied `X-DTS-BRAND` header | STATIC, HIGH | SR-TEN-1, SR-TEN-7 |
| SEC-13 | Teacher-scoped responses return per-student names with ERP IDs (board-prep rollup, SEN timetable slots) and staff names with ERP IDs (`mandatory_cc.people` in the communication metadata) | OBSERVED (shape) | SR-STU-1, SR-STU-10, SR-AUTHZ-4 |

---

## 2. Requirements

### 2.1 Secrets stay on the server

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-SEC-1 | No secret may appear in frontend source, build output, HTML, source maps or any runtime configuration served to a browser. Secrets include privileged API keys, signing keys, JWT secrets, OAuth client secrets, and database, SMTP, SMS, WhatsApp, payment-gateway and webhook credentials. | MUST | CI scans the built `dist/` for high-entropy strings and key-name patterns (`secret`, `jwtSecret`, `privateKey`, `apiKey`, `password`); NFR-04 |
| SR-SEC-2 | Frontend build variables are limited to an explicit allow-list of public values (prefix `VITE_PUBLIC_`). Referencing any other `VITE_*` variable fails the build. | MUST | Build-script check |
| SR-SEC-3 | Tokens for embedded third-party services (document editor, video meetings, maps) are minted server-side per request. They are short-lived (≤ 5 min where the provider allows) and scoped to one user and one resource. The signing key never leaves the server. | MUST | Integration test: no signing material in any response; token expiry test |
| SR-SEC-4 | A key that must be public (push registration, analytics ingestion) is a publishable key with no read capability, restricted by origin or app ID, and rate-limited server-side. | MUST | Config review; abuse test against the ingestion endpoint |
| SR-SEC-5 | Secrets live in a secret manager and are injected at runtime. They are never committed. Secret scanning (for example gitleaks) runs on every pull request and over the full history. | MUST | CI |
| SR-SEC-6 | Every secret has an owner and a documented rotation procedure. Rotation never requires a frontend release. Rotate immediately on suspected exposure. | MUST | Runbook review |
| SR-SEC-7 | Secrets, tokens, passwords, OTPs and full government-ID numbers never appear in logs, error messages, analytics events or audit payloads. | MUST | Unit tests on the log redactor; periodic log sampling |
| SR-SEC-8 | Production source maps are not publicly served; upload them privately to the error tracker. | SHOULD | Deployment check |

### 2.2 Authentication and session handling

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-SES-1 | Session credentials are held only in `httpOnly`, `Secure`, `SameSite=Lax` (or `Strict`) cookies. They are never kept in `localStorage`, `sessionStorage`, IndexedDB, JS-readable cookies or URLs. | MUST | E2E test asserts no token in browser storage; AUTH-01 |
| SR-SES-2 | Access tokens or session lifetime ≤ 15 min. Refresh tokens rotate on every use and are stored server-side as hashes. Reusing a rotated refresh token revokes that token's whole family. | MUST | AUTH-04, AUTH-05 |
| SR-SES-3 | Idle timeout and absolute session lifetime are enforced server-side. Proposed defaults: 30 min idle and 12 h absolute for staff; longer for parents only with an explicit "remember me". Tenant-configurable. | MUST | Clock-injected integration test |
| SR-SES-4 | Every state-changing request is CSRF-protected: SameSite cookies plus a synchronizer or double-submit token, or strict `Origin` checking. | MUST | Integration test: a cross-origin POST is rejected |
| SR-SES-5 | Passwords are hashed with Argon2id using current OWASP parameters, and checked against a breached-password list (k-anonymity API or local list). | MUST (hash), SHOULD (breach check) | Unit test on hash parameters |
| SR-SES-6 | Login, OTP send/verify and password reset are throttled. Lockout does not reveal whether an account exists: an unknown user and a wrong password get identical responses and timing. | MUST | AUTH-02; timing-variance test |
| SR-SES-7 | MFA (TOTP, or OTP by SMS/email) is **enforced** for any role holding admin, finance, PII-reveal, impersonation or permission-management permissions, and optional for everyone else. | MUST | AUTH-06; role-based enforcement test |
| SR-SES-8 | Password reset uses single-use, hashed, out-of-band tokens that expire in ≤ 30 min. A completed reset revokes all existing sessions. | MUST | Integration test |
| SR-SES-9 | Logout revokes the server-side session and refresh token. Protected responses carry `Cache-Control: no-store`. | MUST | AUTH-07 |
| SR-SES-10 | The client completes session bootstrap (one `GET /api/v1/me/bootstrap`) before issuing any data request. | MUST | E2E network assertion: no 401/403 noise on first load |
| SR-SES-11 | Users can list and revoke their active sessions. Administrators can revoke all sessions of a user. | SHOULD | E2E |

### 2.3 Permissions enforced on the server

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-AUTHZ-1 | Deny by default. Every API route declares the permission it requires, or is explicitly marked public. An architecture test fails the build if a route has no declaration. | MUST | RBAC-02; route-metadata test |
| SR-AUTHZ-2 | **Object-level authorization.** Whenever a request references a resource ID (in the path, query or body), the server loads the object and checks tenant, branch/year scope and relationship (own record, linked child, allocated section) *before* reading or changing it. | MUST | Generated IDOR matrix per endpoint: IDs of another user, another section and another tenant |
| SR-AUTHZ-3 | Self-service endpoints take the subject from the session (`/me/...`). They never take the subject from a client-supplied identifier, so there is no `?erp_id=` or `?employee_id=` for one's own data. | MUST | Contract review; IDOR matrix |
| SR-AUTHZ-4 | Data scope comes from data the server controls. Teachers: TeachingAssignment, plus published substitutions for that date. Guardians: StudentGuardian links. Students: their own record. | MUST | RBAC-03, RBAC-04 |
| SR-AUTHZ-5 | Frontend permission checks are for display only. Navigation and route guards read the same permission catalogue the server enforces (`packages/permissions`). | MUST | RBAC-01, RBAC-05 |
| SR-AUTHZ-6 | Role, permission and grant changes take effect within 60 s without re-login. No client-side permission cache outlives the access token. | MUST | RBAC-06 |
| SR-AUTHZ-7 | An object that is cross-tenant or out of scope returns **404**, so its existence is not disclosed. An in-scope object with a forbidden action returns **403**. | MUST | TEN-01, RBAC-02 |
| SR-AUTHZ-8 | Approvals enforce maker ≠ checker on the server; self-approval is rejected even for super administrators. | MUST | FEE-03; approval unit tests |
| SR-AUTHZ-9 | Bulk, search and export endpoints apply exactly the same row-level scope as single-record endpoints, and are rate-limited. | MUST | Scope tests on bulk and export endpoints |

### 2.4 Tenant isolation

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-TEN-1 | The tenant is resolved server-side from the request host (custom domain or subdomain) and checked against the authenticated user's membership. Any tenant header or parameter sent by the client is ignored. | MUST | Integration test: a forged tenant header has no effect |
| SR-TEN-2 | Every tenant-owned table has `tenant_id uuid NOT NULL` and row-level security that is enabled **and forced**, with policy `tenant_id = current_setting('app.tenant_id')::uuid`. | MUST | TEN-02; migration lint that lists tables without a policy |
| SR-TEN-3 | The application connects with a database role that does not own the tables, is not a superuser and lacks `BYPASSRLS`. Migrations use a separate role. | MUST | Startup self-check that queries `pg_roles` |
| SR-TEN-4 | `app.tenant_id` is set with `SET LOCAL` inside the transaction of every unit of work, request or job, so pooled connections cannot carry tenant context from one request to the next. | MUST | Concurrency test: interleaved requests from two tenants on one pool never cross |
| SR-TEN-5 | Jobs, outbox events, cache keys, rate-limit keys, search indexes and object-storage keys all carry and enforce the tenant (storage prefix `tenants/{tenantId}/…`). | MUST | Unit tests on key builders; TEN-03 |
| SR-TEN-6 | An automated cross-tenant matrix covers every endpoint and runs in CI. | MUST | TEN-01 |
| SR-TEN-7 | A person who belongs to several tenants has a separate membership in each. Switching tenant issues a new session and is audited. | MUST | Integration test; audit entry |
| SR-TEN-8 | Business-key uniqueness includes the tenant, for example `(tenant_id, erp_id)` and `(tenant_id, branch_id, receipt_series, number)`. | MUST | Schema review |

### 2.5 No hard-coded personal identifiers

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-ID-1 | No personal identifiers (user IDs, employee codes, ERP IDs, emails, phone numbers, names) appear in source code, configuration, the navigation registry, feature flags or permission definitions. | MUST | CI lint rule that rejects ID-, email- and phone-like literals in `packages/permissions`, config and the nav registry; review checklist |
| SR-ID-2 | Per-person exceptions are **UserGrant** records: permission, scope, reason, grantor and a mandatory expiry. Creating, using and revoking a grant is audited. | MUST | FR-IAM-06; audit test |
| SR-ID-3 | Feature flags target tenants, branches or roles, never named people. | MUST | Flag-schema validation |
| SR-ID-4 | Business identifiers (ERP ID, admission number, receipt number) never act as authorization tokens. Opaque UUIDs reference records in URLs and links. | MUST | Contract review |
| SR-ID-5 | Seed, fixture and test data come only from seeded generators. No imported or real data is used. | MUST | DEVELOPMENT_RULES 3; fixture review |

### 2.6 Audit logs

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-AUD-1 | The audit log is append-only and tenant-scoped. Each entry records: actor (and impersonator, if any), tenant, action, entity type and ID, UTC timestamp, request ID, IP address, user agent, outcome, and before/after values of changed fields. | MUST | NFR-05 |
| SR-AUD-2 | Events that must be audited, at minimum: login success, failure, lockout and MFA; session revocation; changes to roles, permissions and grants; impersonation start, stop and actions; PII reveal; exports and downloads of student or finance data; writes to students, guardians, enrolments, attendance, marks, report cards, fees, payments, refunds, concessions, settings and tenant configuration. | MUST | RBAC-07, NFR-05; event-coverage test generated from the list |
| SR-AUD-3 | Sensitive values inside before/after snapshots are masked (government IDs and bank accounts show only the last 4 digits). Secrets are never stored. | MUST | Unit test on the snapshot masker |
| SR-AUD-4 | The application database role holds only `INSERT` and `SELECT` on audit tables; no update or delete. Retention follows the policy decided for Q14. | MUST | Startup grant check |
| SR-AUD-5 | Tamper evidence: hash-chain entries per tenant per day and verify the chain with a scheduled job. | SHOULD | Job test |
| SR-AUD-6 | The audit viewer is limited to permitted roles and scoped by tenant and branch. Exporting from the viewer is itself audited. | MUST | FR-IAM-08 |

### 2.7 Protecting financial information

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-FIN-1 | The system never collects or stores card, UPI or net-banking credentials. Online payment uses the gateway's hosted checkout or SDK. | MUST | Architecture review |
| SR-FIN-2 | A payment counts as successful only after a verified gateway webhook or a server-to-server status check. The signature must verify, and order, amount and currency must match the server-side order. Processing is idempotent on the gateway event ID. | MUST | PAY-01; tampered-amount and replay tests |
| SR-FIN-3 | The amount payable is computed on the server from dues. Client-supplied amounts are never trusted. | MUST | Integration test with a modified amount |
| SR-FIN-4 | Payments, receipts, wallet transactions and ledger entries are immutable. Corrections are reversing entries created through an approved cancellation or refund. | MUST | PAY-03; database grants or triggers |
| SR-FIN-5 | Receipt numbers are allocated on the server, inside the payment transaction, and are unique and gap-checked per series. | MUST | PAY-02; concurrency test |
| SR-FIN-6 | Concessions, waivers, refunds and cancellations follow maker–checker with amount thresholds set per tenant. | MUST | FEE-03 |
| SR-FIN-7 | Finance permissions are separate from academic permissions, and finance roles require MFA (SR-SES-7). | MUST | Permission catalogue review |
| SR-FIN-8 | Bank account numbers (fee accounts, refund details) are encrypted at field level and masked to the last 4 digits in UI and API. | MUST | Unit and API tests |
| SR-FIN-9 | Gateway settlements are reconciled against receipts daily, and mismatches raise alerts. | MUST | PAY-04 |
| SR-FIN-10 | Guardians and students can access only their own children's (or their own) dues, payments and receipts. Receipt files are served through signed, expiring URLs. | MUST | RBAC-04, TEN-03 |

### 2.8 Protecting student and personal information

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-STU-1 | Each role's responses carry only the fields its screens need. List endpoints exclude guardian contact details, DOB, address, health/SEN and government IDs; detail views mask them by default. | MUST | Contract snapshot tests per role; SR-DP-3 |
| SR-STU-2 | Revealing a masked field requires a scoped `pii.reveal` permission and writes an audit entry. | MUST | RBAC-07 |
| SR-STU-3 | Special-category data (health, SEN, disciplinary, government IDs) is encrypted at field level with keys held in a KMS, and only explicit permissions can read it. | MUST | Schema review; key-access test |
| SR-STU-4 | Government-ID numbers (for example Aadhaar) are collected **only** with a documented legal basis and an owner decision. If stored, they are encrypted, only the last 4 digits can be displayed, and they are never used in clear as a lookup or matching key. | MUST | Owner ADR; schema review |
| SR-STU-5 | No third-party session replay, heat-mapping or behavioural tracking runs on authenticated pages. Product analytics are first-party and PII-free, with no student-level behavioural profiling. | MUST | CSP allow-list review; bundle check for known replay SDKs |
| SR-STU-6 | Online-class host start links, meeting passwords and recording passwords are returned only to the host and to explicitly permitted staff, generated per request where the provider allows. Students receive join links only during the class window. | MUST | Contract tests per role on online-class endpoints |
| SR-STU-7 | Homework files, report cards, receipts and photos are served only through signed, tenant-prefixed URLs valid ≤ 10 min. Authorization is checked when each URL is issued. | MUST | TEN-03 |
| SR-STU-8 | Processing of children's data follows the DPDP Act 2023: recorded verifiable parental consent per guardian, purpose limitation, no targeted advertising. Legal details to be confirmed by counsel (Q14, Q20). | MUST | Consent-record tests; legal sign-off |
| SR-STU-9 | Exports of student data run as asynchronous jobs that are permission-checked, audited and expiring, and SHOULD be watermarked with the requester and the time. | MUST | Export tests |
| SR-STU-10 | No personal data in URLs, query strings, logs or analytics events. Only opaque UUIDs identify records. | MUST | Log and URL lint; contract review |

### 2.9 Third parties, impersonation and platform hygiene

| ID | Requirement | Level | Verify by |
|---|---|---|---|
| SR-EXT-1 | Impersonation requires a dedicated permission and a stated reason. It is time-boxed (proposed default 30 min), read-only by default, shows a persistent banner, blocks finance and permission changes, and audits its start, its end and every action. | MUST | FR-IAM-07; E2E |
| SR-EXT-2 | Content Security Policy without `unsafe-inline` or `unsafe-eval`. Third-party scripts only from an allow-list, with Subresource Integrity. No third-party script on pages that show student data unless a privacy review approved it. | MUST | Header test; SR-AS-1 |
| SR-EXT-3 | Rate limiting covers authentication, OTP, search, export, upload and every public endpoint. | MUST | SR-AS-3 |
| SR-EXT-4 | Uploads are checked against MIME and extension allow-lists and size limits, virus-scanned before becoming available, and stored outside the web root. | MUST | SR-AS-2 |
| SR-EXT-5 | Error responses carry only a code, a message and a request ID: no stack traces, SQL or internal hostnames. | MUST | Error-filter tests |
| SR-EXT-6 | Dependency vulnerability scanning and static analysis run in CI. High or critical findings block a release. | MUST | CI |
| SR-EXT-7 | Outbound integrations (SMS, WhatsApp, email, payment, video) go through provider interfaces with server-side credentials. Inbound webhooks are signature-verified and protected against replay. | MUST | Adapter tests |
| SR-EXT-8 | Public share and tracking links (for example bus tracking) are signed, expiring and scoped to one resource. | SHOULD | Link tests |

---

## 3. Proposed acceptance criteria to add

These close gaps that the existing ACs (`08_TESTING/acceptance_criteria.md`) do not cover. They are PROPOSED; add them to the AC file when the relevant step starts.

| ID | Given / When / Then | Covers |
|---|---|---|
| SEC-AC-01 | Given a production build, when the bundle scan runs, then no secret pattern or non-public `VITE_*` variable is found, and the build fails otherwise. | SR-SEC-1, SR-SEC-2 |
| SEC-AC-02 | Given a logged-in user, when browser storage is inspected, then no access or refresh token exists outside httpOnly cookies. | SR-SES-1 |
| SEC-AC-03 | Given any endpoint that accepts a resource ID, when another user's, another section's or another tenant's ID is supplied, then the response is 404 (or 403 when in scope) and nothing leaks. | SR-AUTHZ-2, SR-AUTHZ-7 |
| SEC-AC-04 | Given a request carrying a forged tenant header or parameter, when it is processed, then the tenant is still the one resolved from host and session. | SR-TEN-1 |
| SEC-AC-05 | Given two tenants issuing interleaved requests on one connection pool, when 1,000 requests complete, then no row from either tenant is visible to the other. | SR-TEN-4 |
| SEC-AC-06 | Given the permissions package, nav registry and config, when CI lint runs, then any ID-, email- or phone-like literal fails the build. | SR-ID-1 |
| SEC-AC-07 | Given the application database role, when it attempts `UPDATE` or `DELETE` on the audit table, then the database refuses. | SR-AUD-4 |
| SEC-AC-08 | Given a student or guardian session, when online classes are listed, then no host start URL, meeting password or recording password is present. | SR-STU-6 |
| SEC-AC-09 | Given a gateway webhook whose amount differs from the server-side order, when it arrives, then the payment is not marked paid and an alert is raised. | SR-FIN-2, SR-FIN-3 |
| SEC-AC-10 | Given any authenticated page, when its scripts are listed, then no third-party session-replay or heat-map SDK is loaded. | SR-STU-5, SR-EXT-2 |

## 4. Count
75 requirements: SEC 8, SES 11, AUTHZ 9, TEN 8, ID 5, AUD 6, FIN 10, STU 10, EXT 8. They are mapped to 13 observations (SEC-01…SEC-13), and 10 new acceptance criteria are proposed.
