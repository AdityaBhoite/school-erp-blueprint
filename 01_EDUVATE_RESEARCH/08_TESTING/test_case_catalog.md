# Test Case Catalog

Each case references acceptance criteria (AC). Types: U = unit, I = integration (API+DB), E = e2e (Playwright), A = authz matrix, P = performance. All data synthetic.

| TC | AC | Type | Module | Title | Steps (abridged) | Expected |
|---|---|---|---|---|---|---|
| TC-AUTH-001 | AUTH-01 | E | Auth | Teacher login lands on calendar | login as `teacher1` | Calendar visible ≤ 2 s; cookie httpOnly |
| TC-AUTH-002 | AUTH-02 | I | Auth | Lockout after 5 failures | 6 bad logins | 423/429; generic message |
| TC-AUTH-003 | AUTH-03 | E | Auth | Forced password change | login `newuser` | Redirected to change password on every route |
| TC-AUTH-004 | AUTH-04 | I | Auth | Concurrent refresh | expire access; fire 5 calls | 1 refresh call; 5 × 200 |
| TC-AUTH-005 | AUTH-05 | I | Auth | Refresh reuse detection | use old refresh after rotation | all sessions revoked |
| TC-AUTH-006 | AUTH-06 | E | Auth | TOTP login | 2FA user login | OTP step required |
| TC-TEN-001 | TEN-01 | A | Tenancy | Cross-tenant ID access | for each GET/PUT/DELETE with B's IDs as A | 404 everywhere |
| TC-TEN-002 | TEN-02 | I | Tenancy | RLS without context | raw query | 0 rows |
| TC-RBAC-001 | RBAC-01 | E | Shell | Menu per role snapshot | login each seeded role | menu equals expected snapshot |
| TC-RBAC-002 | RBAC-02 | A | All | Permission matrix | every endpoint × role lacking perm | 403 |
| TC-RBAC-003 | RBAC-03 | A | Attendance/Diary | Out-of-scope section | teacher posts attendance for unassigned section | 403 |
| TC-RBAC-004 | RBAC-04 | E | Parent | Sibling switch | parent with 2 children switches child | all widgets change |
| TC-RBAC-005 | RBAC-05 | E | Shell | Unauthorized URL | teacher opens /finance/fee-plans | "No access" page |
| TC-RBAC-006 | RBAC-07 | I | People | PII reveal audit | reveal guardian phone | audit row created |
| TC-ORG-001 | ORG-01 | I | Org | Single current year | set two current | second → validation error |
| TC-ORG-002 | ORG-02 | I | Org | Soft delete/restore section mapping | delete then restore | identical relations |
| TC-ORG-003 | ORG-03 | I | Org | Rollover idempotent | apply twice | no duplicates |
| TC-PPL-001 | PPL-01 | U/I | People | Student validation | missing DOB | 422 with field error |
| TC-PPL-002 | PPL-02 | P/I | People | Bulk import 500 | upload XLSX with 10 bad rows | 490 imported, report of 10 |
| TC-PPL-003 | PPL-03 | I | People | Guardian dedupe | two students same phone | one guardian, siblings |
| TC-ENR-001 | ENR-01 | I | Enrolment | Single active enrolment | enrol twice same year | conflict error |
| TC-ENR-002 | ENR-02 | I | Enrolment | Promotion | promote grade 9A → 10A | new enrolment; history intact |
| TC-CAL-001 | CAL-01 | P | Calendar | Month load | 200 events | ≤ 1 s |
| TC-CAL-002 | CAL-02 | E | Calendar | Draft visibility | teacher views month with drafts | drafts hidden |
| TC-CAL-003 | CAL-03 | I | Calendar | Holiday blocks attendance | mark on holiday | 422 |
| TC-CAL-004 | CAL-04 | U | Calendar | Recurrence expansion | weekly rule with exception | correct dates |
| TC-TT-001 | TT-01 | U | Timetable | Period overlap | overlapping times | validation error lists both |
| TC-TT-002 | TT-02 | I | Timetable | Teacher clash | allocate same slot twice | 409 |
| TC-TT-003 | TT-03 | E | Timetable | Draft invisible | publish vs draft | views match published only |
| TC-TT-004 | TT-04 | E | Timetable | Substitution in diary | absent T1, sub T2 | T2 diary shows period flagged |
| TC-DIA-001 | DIA-01 | E | Diary | Today's periods | teacher opens diary | periods sorted by time |
| TC-DIA-002 | DIA-02 | I | Diary | Attachment rules | upload .exe / 25 MB | rejected |
| TC-DIA-003 | DIA-03 | A | Diary | Multi-section scope | include foreign section | 403 |
| TC-HW-001 | HW-01 | E | Homework | Student sees homework | after teacher saves | visible ≤ 1 min, guardian notified |
| TC-HW-002 | HW-02 | I | Homework | Late submission | submit after due | status Late / blocked per config |
| TC-HW-003 | HW-03 | E | Homework | Evaluation | teacher evaluates | status Evaluated; notification |
| TC-ATT-001 | ATT-01 | E | Attendance | Required filters | click Search without grade | inline validation |
| TC-ATT-002 | ATT-02 | P/I | Attendance | Bulk idempotent save | save twice | 45 rows, not 90; ≤ 1 s |
| TC-ATT-003 | ATT-03 | I | Attendance | Future date | mark tomorrow | 422 |
| TC-ATT-004 | ATT-04 | A | Attendance | Edit past | teacher edits yesterday | 403; coordinator OK + audit |
| TC-ATT-005 | ATT-05 | I | Attendance | Notify once | save absent twice | one SMS/push |
| TC-ATT-006 | ATT-06 | U | Attendance | Percentage | fixture month | expected % |
| TC-COM-001 | COM-01 | E | Announcements | Audience count | select grade 10 parents | count shown |
| TC-COM-002 | COM-02 | P | Announcements | Fan-out 10k | publish | in-app ≤ 5 min |
| TC-COM-003 | COM-03 | E | Notifications | Unread count | read item | count decrements ≤ 5 s |
| TC-TKT-001 | TKT-01 | E | Tickets | Raise and route | parent raises | assignee sees ticket |
| TC-TKT-002 | TKT-02 | U | Tickets | State machine | invalid transition | rejected |
| TC-TKT-003 | TKT-03 | E | Tickets | Dashboard filters | filter CSAT ≤ 2 | correct subset |
| TC-ASM-001 | ASM-01 | I | Assessment | Validation | total marks 0 | 422 |
| TC-ASM-002 | ASM-02 | U | Marks | Range check | 105/100 | rejected |
| TC-ASM-003 | ASM-03 | I | Marks | Excel upload | unknown ERP ID | mismatch report; no commit |
| TC-ASM-004 | ASM-04 | E | Marks | Publish visibility | publish | parent sees marks |
| TC-ASM-005 | ASM-05 | U | Marks | Overdue flag | result date past, unpublished | is_overdue true |
| TC-RC-001 | RC-01 | U | Report card | Weighted grade | fixture weights | expected grades |
| TC-RC-002 | RC-02 | E | Report card | PDF after publish | guardian before/after publish | 403 then PDF |
| TC-RC-003 | RC-03 | I | Report card | Revert | revert run | hidden; audit row |
| TC-FEE-001 | FEE-01 | U | Fees | Plan totals | components | sum check |
| TC-FEE-002 | FEE-02 | I | Fees | Dues generation | assign plan with concession | dues correct |
| TC-FEE-003 | FEE-03 | A | Fees | Maker-checker | same user approves | 403 |
| TC-FEE-004 | FEE-04 | U | Fees | Fine cap | 90 days late | fine = cap |
| TC-PAY-001 | PAY-01 | I | Payments | Webhook dedupe | send webhook twice | 1 payment |
| TC-PAY-002 | PAY-02 | I | Payments | Receipt numbering | 100 parallel payments | unique sequential numbers |
| TC-PAY-003 | PAY-03 | A | Payments | Immutable payments | PUT payment | 405/403 |
| TC-PAY-004 | PAY-04 | I | Payments | Reconciliation export | fixture settlements | mismatches flagged |
| TC-LIFE-001 | LIFE-01 | E | Lifecycle | Blocked student login | approve temp block | login denied with message |
| TC-LIFE-002 | LIFE-02 | I | Lifecycle | Shuffle history | approve shuffle | past attendance still on old section |
| TC-NFR-001 | NFR-02 | E | All | Accessibility | axe on all routes | 0 serious |
| TC-NFR-002 | NFR-03 | E | All | Mobile layout | 390 px | no page-level horizontal scroll |
| TC-NFR-003 | NFR-04 | static | Build | No secrets in bundle | scan dist | no matches |
| TC-NFR-004 | NFR-05 | I | Audit | Audit coverage | each write endpoint | audit row with before/after |
