# API / Route / Feature Trace

For selected features, the chain **route → page component → lazy chunk → service modules → API endpoints (method) → response fields**, built from the static graph. This is static reachability; it shows the API surface a screen draws from, not a runtime call log. Endpoints a screen *could* call via an imported service module are listed as the module's surface, not asserted as used.


## Attendance — `/attendance`
- **Component / chunk:** `(inline)` → `index.D2BrJoK4.js`
- **Imported service modules (API-bearing):** api.CFQDbzOL.js
- **APIs called directly in the chunk:** 9 (services: ERP:9)
- **Sample direct APIs:** GET ERP/erp_user/student-annual-attendance;GET ERP/erp_user/student-monthly-attendance;GET ERP/erp_user/student-upcoming-holidays;GET ERP/erp_user/user-mgmt/monthly-attendance-report;GET ERP/erp_user/user-mgmt/student-attendance;GET ERP/erp_user/user-mgmt/user-grades;GET ERP/erp_user/user-mgmt/user-sections;POST ERP/erp_user/sms-notifying-students
- **Runtime cross-check (E016):** observed accessible for the teacher


## Daily diary — `/diary`
- **Component / chunk:** `(inline)` → `index.BkKnnET3.js`
- **Imported service modules (API-bearing):** (none)
- **APIs called directly in the chunk:** 9 (services: ERP:9)
- **Sample direct APIs:** GET ERP/homework/student-homework;GET ERP/school-feed/my-diary-given;GET ERP/timetable/branch-grades;GET ERP/timetable/branch-staff-timetable;GET ERP/timetable/grade-sections;GET ERP/timetable/teacher-timetable;GET ERP/timetable/tt-grade-subjects;POST ERP/academic/upload-question-file
- **Runtime cross-check (E016):** observed accessible for the teacher


## Student fee payment — `/student-fee/student-fee-payment`
- **Component / chunk:** `(inline)` → `index.Bq7deGEq.js`
- **Imported service modules (API-bearing):** api.CFQDbzOL.js;deliveryApplicabilityTag.DlMXt39F.js;index.Bvseyjdu.js;index.CTsxigB0.js;paymentMethods.BRioK12g.js;trackDeliveryHistory.D04b_Tcx.js
- **APIs called directly in the chunk:** 37 (services: ERP:37)
- **Sample direct APIs:** GET ERP/api/v2/student_fee/student-config;GET ERP/apiV1/application-form-list;GET ERP/apiV1/assigned-kit-item;GET ERP/apiV1/backdue-collected;GET ERP/apiV1/check-is-blocked;GET ERP/apiV1/fee-collected-list;GET ERP/apiV1/finance-system-config;GET ERP/apiV1/flat-registration-data
- **Runtime cross-check (E016):** not accessible / not visited for the teacher


## Academic calendar — `/acms/calendar`
Route not found in the static router table.


## Timetable — `/timetable`
- **Component / chunk:** `(inline)` → `index.Bo_LY7d7.js`
- **Imported service modules (API-bearing):** api.CFQDbzOL.js
- **APIs called directly in the chunk:** 30 (services: ERP:30)
- **Sample direct APIs:** DELETE ERP/timetable/teacher-period-common/{id};GET ERP/erp_user/subject-all;GET ERP/erp_user/user-mgmt/student-information;GET ERP/erp_user/user-mgmt/user-grades;GET ERP/erp_user/user-mgmt/user-sections;GET ERP/erp_user/user-mgmt/user-subjects;GET ERP/school-gate/subject-prompt;GET ERP/timetable/period-system-section-options
- **Runtime cross-check (E016):** observed accessible for the teacher


## Report card — `/assessment/report-card`
- **Component / chunk:** `(inline)` → `index.CQPETK78.js`
- **Imported service modules (API-bearing):** api.CFQDbzOL.js;index.C7fWd6Gd.js;index.CE3_qYP9.js;index.CjxsNzxn.js;useFeeBlocked._DDlWQ0a.js
- **APIs called directly in the chunk:** 11 (services: ERP:11)
- **Sample direct APIs:** GET ERP/academic/ps-rc-template;GET ERP/academic/ps-report-card;GET ERP/academic/sports-report-card;GET ERP/academic/vlc-report-card;GET ERP/assessment/eyp-report-card-generation;GET ERP/assessment/get-student-grades;GET ERP/assessment/senior-report-card/{id};GET ERP/assessment/teacher-remarks
- **Runtime cross-check (E016):** observed accessible for the teacher


## Reading the full graph
`ROUTE_COMPONENT_MATRIX.csv` has one row per route with its chunk, own-API count, services and imported service modules. `API_CALLSITE_INDEX.csv` has one row per resolved call site (file, line, method, client, catalog key, field names), so any endpoint can be traced back to its exact call sites.
