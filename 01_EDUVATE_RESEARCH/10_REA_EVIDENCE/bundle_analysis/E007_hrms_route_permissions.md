# HRMS (/recruitment) route table with role-level permissions — static extraction

Evidence: E007. Source: /recruitment/ main bundle route config (82 top-level entries). Classification: STATIC. Numbers are HRMS user-level IDs (names not resolved statically — UNKNOWN). Specific employee-ID allow-lists were REDACTED.

Format: `Label | route | perm={is_view/is_add/is_modify:[user levels], flags} | is_route | is_sidebar`

```text
Dashboard | / | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
Teacher Recruitment Dashboard | /teacher-recruitment-dashboard | perm={"is_view": [1, 2, 5, 6]} | route=True side=True
KP Dashboard | /kp-dashboard | perm={"is_view": [1, 2, 5], "require_keyperson": true} | route=True side=True
Recruiter Performance | /recruiter-performance-dashboard | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
Vacancy Grid | /vacancy-grid | perm={"is_view": [1, 2, 5, 6, 8, 9, 13, 33]} | route=True side=True
Recruiter Salary Dashboard | /recruiter-salary-dashboard | perm={"is_view": [1, 2, 5, 6, 8, 9, 33]} | route=True side=True
Audit — Campus Hiring | /audit/campus-hiring | perm={"is_view": [1, 2, 5, 16, 34], "erpIds": [REDACTED]} | route=True side=True
Audit — Lateral Hiring | /audit/lateral-hiring | perm={"is_view": [1, 2, 5, 16, 34], "erpIds": [REDACTED]} | route=True side=True
My Audit Report | /audit/my-report | perm={"is_view": [1, 2, 5, 6], "erpIds": [REDACTED]} | route=True side=True
Auditor Activity | /audit/auditor-activity | perm={"is_view": [1, 2, 5], "erpIds": [REDACTED]} | route=True side=True
Audit Detail | /audit/detail/:id | perm={"is_view": [1, 2, 5, 6, 16, 34], "erpIds": [REDACTED]} | route=True side=False
Recruitment Metrics | /recruitment-metrics | perm={"is_view": [1, 2, 5, 8, 9, 33]} | route=True side=True
Career Applications | /career-applications | perm={"is_view": [1, 2, 5, 6]} | route=True side=True
None | /resignation | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
None | /profile | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=False
None | /edit-profile | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
None | /employee-profile/:id | perm={"is_view": [1, 19, 26, 28]} | route=True side=False
Master Data | None | perm={"is_view": [1, 2, 5, 6, 10, 11, 12, 13, 14, 16, 17, 20, 21, 22, 26, 28, 29, 36]} | route=True side=True
  Referral Bonus Slab | /master-data/referral-bonus-slab | perm={"is_view": [1, 2, 5], "is_add": [1, 2, 5], "is_modify": [1, 2, 5]} | route=True side=True
  Applicant Stage | /master-data/applicant-stage | perm={"is_view": [1, 2, 5, 11], "is_add": [1], "is_modify": [1]} | route=True side=True
  Source Type | /master-data/source-type | perm={"is_view": [1, 2, 5, 11], "is_add": [1, 2, 5, 11], "is_modify": [1, 2, 5, 11]} | route=True side=True
  Source | /master-data/source | perm={"is_view": [1, 2, 5, 11], "is_add": [1, 2, 5, 11], "is_modify": [1, 2, 5, 11]} | route=True side=True
  Department | /master-data/department | perm={"is_view": [1, 13], "is_add": [1], "is_modify": [1]} | route=True side=True
  Designation | /master-data/designation | perm={"is_view": [1, 5, 13, 16, 26, 28], "is_add": [1], "is_modify": [1, 5, 13, 16, 26, 28]} | route=True side=True
  Entity Master | /master-data/entity-master | perm={"is_view": [1, 2, 12, 26, 28], "is_add": [1, 12, 26, 28], "is_modify": [1, 12, 26, 28]} | route=True side=True
  Resignation Master | /master-data/resignation-master | perm={"is_view": [1, 5, 16, 26, 28], "is_add": [1, 5, 16, 26, 28], "is_modify": [1, 5, 16, 26, 28]} | route=True side=True
  Partner Hiring | /master-data/campus-drives | perm={"is_view": [1, 2, 12, 26, 28, 36], "is_add": [1, 2, 12, 26, 28], "is_modify": [1, 2, 12, 26, 28]} | route=True side=True
  User Configuration | /master-data/user-configuration | perm={"is_view": [1, 2, 5, 11, 26, 28], "is_add": [1, 2, 5, 11, 26, 28], "is_modify": [1, 2, 5, 11, 26, 28]} | route=True side=True
  Asset Types | /master-data/asset-types | perm={"is_view": [1, 2, 5, 11, 17, 21, 22], "is_add": [1, 2, 5, 11, 17, 21, 22], "is_modify": [1, 2, 5, 11, 17, 21, 22]} | route=True side=True
  Test Banks | /master-data/test-banks | perm={"is_view": [1, 2, 5, 6, 10, 14, 20, 29], "is_add": [1, 2, 5, 10, 14, 20], "is_modify": [1, 2, 5, 10, 14, 20]} | route=True side=True
User Management | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 35, 33]} | route=True side=True
  People | /user-management | perm={"is_add": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 35, 33], "is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 35, 33], "is_modify": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 35, 33]} | route=True side=True
  Profile Completion | /user-management/profile-completion | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33]} | route=True side=False
  SAP Business Partner | /sap-integration | perm={"is_add": [1, 2, 3], "is_view": [1, 2, 3], "is_modify": [1, 2, 3]} | route=True side=True
  Manager Update Approval | /report-to-approval | perm={"is_add": [1, 5, 14, 16, 17, 26, 28], "is_view": [1, 5, 14, 16, 17, 26, 28], "is_modify": [1, 5, 14, 16, 17, 26, 28]} | route=True side=True
Approval | /approval | perm={"is_add": [1, 2, 5, 7], "is_view": [1, 2, 5, 7], "is_modify": [1, 2, 5, 7]} | route=True side=True
MRF Management | /mrf-management | perm={"is_add": [1, 2, 5, 8, 9, 10, 16, 21], "is_view": [1, 2, 5, 7, 8, 9, 10, 11, 13, 15, 16, 21, 28, 33, 35], "is_modify": [1, 2, 5, 7, 8, 9, 10, 11, 13, 16, 21, 33]} | route=True side=True
MRF Management | /mrf-recruiter | perm={"is_add": [6, 8, 9, 33], "is_view": [6, 8, 9, 33], "is_modify": [6, 8, 9, 33]} | route=True side=True
Assessment | /assessment | perm={"is_add": [1, 2, 5, 7, 8, 9, 10, 11, 16, 33], "is_view": [1, 2, 5, 7, 8, 9, 10, 11, 16, 33], "is_modify": [1, 2, 5, 7, 8, 9, 10, 11, 16, 33]} | route=True side=True
Batch Interview | /batch-interview | perm={"is_add": [1, 2, 5, 6, 8, 9, 33], "is_view": [1, 2, 5, 6, 8, 9, 33], "is_modify": [1, 2, 5, 6, 8, 9, 33]} | route=True side=True
Scoring Criteria | /scoring-criteria | perm={"is_add": [1, 2, 5], "is_view": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 15, 16, 21, 35, 33], "is_modify": [1, 2, 5, 6, 8, 9, 10, 11, 13, 16, 21, 35, 33]} | route=True side=True
Candidates | None | perm={"is_view": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 15, 35, 33]} | route=True side=True
  All Candidates | /candidates | perm={"is_add": [1, 2, 5, 6, 7, 8, 9, 10, 11, 35, 33], "is_view": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 15, 35, 33], "is_modify": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 35, 33]} | route=True side=True
  Resume Finder | /resume-finder | perm={"is_view": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 35, 33], "is_add": [1, 2, 5, 6, 7, 8, 9, 10, 11, 35, 33], "is_modify": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 35, 33]} | route=True side=True
  WhatsApp Connect | /whatsapp-connect | perm={"is_view": [1, 2, 5, 6]} | route=True side=False
  Unassigned MRF | /candidates/unassigned-mrf | perm={"is_view": [1, 2, 5, 6, 8, 9, 33]} | route=True side=True
  Talent Pool | /candidates/talent-pool | perm={"is_view": [1, 2, 5, 6, 7, 8, 9, 10, 11, 35, 33], "is_add": [1, 2, 5, 6, 7, 8, 9, 10, 11, 35, 33]} | route=True side=True
None | /candidate/:id | perm={"is_add": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 33], "is_view": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 33], "is_modify": [1, 2, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 33]} | route=True side=False
My Interviews | /my-interviews | perm={"is_add": [1, 2, 3, 5, 7, 8, 9, 10, 11, 12, 15, 16, 21, 26, 29, 30, 31, 35, 33], "is_view": [1, 2, 3, 5, 7, 8, 9, 10, 11, 12, 15, 16, 21, 26, 29, 30, 31, 35, 33], "is_modify": [1, 2, 3, 5, 7, 8, 9, 10, 11, 12, 15, 16, 21, 26, 29, 30, 31, 35, 33]} | route=True side=True
Onboarding | /onboarding | perm={"is_add": [1, 2, 5, 12, 14, 16, 17, 19, 26, 28], "is_view": [1, 2, 5, 12, 14, 16, 17, 19, 26, 28], "is_modify": [1, 2, 5, 12, 14, 16, 17, 19, 26, 28]} | route=True side=True
Budget Approvals | None | perm={"is_view": [1, 2, 15]} | route=True side=True
  Salary Fitment | /salary-fitment | perm={"is_add": [1, 15], "is_view": [1, 15], "is_modify": [1, 15]} | route=True side=True
  MRF Budget Approvals | /budget-approvals | perm={"is_add": [1, 2], "is_view": [1, 2], "is_modify": [1, 2]} | route=True side=True
  Promotions / Transfers | /promotions-transfers | perm={"is_add": [1, 7, 15, 35, 33], "is_view": [1, 7, 15, 35, 33], "is_modify": [1, 7, 15, 35, 33]} | route=True side=True
Performance Management | None | perm={"is_view": [1, 2, 3, 5, 7, 10, 11, 12, 13, 14, 15, 16, 17, 21, 22, 29, 30, 31, 35, 33], "erpIds": [REDACTED]} | route=True side=True
  PMS Config | /performance-management/pms-config | perm={"is_view": [1, 16], "erpIds": [REDACTED]} | route=True side=True
  Evaluation Workflow | /performance-management/evaluation-workflow-config | perm={"is_view": [1, 16], "erpIds": [REDACTED]} | route=True side=True
  Performance Evaluation | /performance-management/performance-evaluation | perm={"is_view": [1, 2, 5, 7, 11, 12, 13, 16], "erpIds": [REDACTED]} | route=True side=True
  Designation Templates | /performance-management/designation-templates | perm={"is_view": [1, 16], "erpIds": [REDACTED]} | route=True side=True
  My Performance | /performance-management/my-performance | perm={"is_view": [3, 7, 11]} | route=True side=True
  Growth Action Plan | /growth-action-plan | perm={"is_view": [1, 2, 3, 5, 7, 10, 11, 13, 16, 20, 33], "require_reporting_manager": true} | route=True side=True
  None | /growth-action-plan/initiate | perm={"is_view": [1, 2, 3, 5, 7, 10, 11, 13, 16, 20, 33], "require_reporting_manager": true} | route=True side=False
  None | /growth-action-plan/case/:id | perm={"is_view": [1, 2, 3, 5, 7, 10, 11, 13, 16, 20, 29, 30, 31, 33], "require_reporting_manager": true} | route=True side=False
  My Growth Plan | /growth-action-plan/my-plan | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 29, 30, 31, 35, 33]} | route=True side=True
  None | /growth-action-plan/previous-plans | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 29, 30, 31, 35, 33]} | route=True side=False
  None | /growth-action-plan/my-plan/:id | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 29, 30, 31, 35, 33]} | route=True side=False
  None | /growth-action-plan/meeting-room/:caseId/:meetingId | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 29, 30, 31, 35, 33]} | route=True side=False
Employee Mobility | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 35, 33]} | route=True side=True
  Apply Transfer | /transfers/apply-transfer | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 35, 33]} | route=True side=True
  Direct Transfer | /transfers/direct-transfer | perm={"is_view": [1, 2, 5, 16, 26, 28, 35]} | route=True side=True
  Transfer Approval | /transfers/transfer-approval | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 35]} | route=True side=True
  Transfer Letter Release | /transfers/transfer-letter-release | perm={"is_view": [1, 2, 16, 28, 35]} | route=True side=True
  Assign MRF To Transfer | /transfers/assign-mrfs-to-transfer | perm={"is_view": [1, 5, 16, 28]} | route=True side=True
  All Transfers | /transfers/all-transfer | perm={"is_view": [1, 5, 16, 19, 26, 28, 35]} | route=True side=True
  Observation Transfers | /transfers/observation-transfer | perm={"is_view": [1, 5, 16, 28, 35]} | route=True side=True
  Transfer Flow Config | /transfers/transfer-flow-config | perm={"is_view": [1, 2, 16, 28]} | route=True side=True
Separations | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
  Candidate Separation | /resignation/candidate-resignation | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
  All Separations | /resignation/all-resignation | perm={"is_view": [1, 5, 14, 16, 17, 18, 19, 21, 22, 26, 28]} | route=True side=True
  Employee Exit | /resignation/resign-candidate | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Exit Management | /exit-management | perm={"is_add": [1, 5, 14, 16, 17, 26, 28], "is_view": [1, 5, 14, 16, 17, 26, 28], "is_modify": [1, 5, 14, 16, 17, 26, 28]} | route=True side=True
Clearance | /clearance | perm={"is_view": [12, 18, 19, 26, 28]} | route=True side=True
Course | None | perm={"is_view": [1, 2, 5, 10, 14, 20]} | route=True side=True
  Category | /course/category | perm={"is_view": [1, 2, 5, 10, 20], "is_add": [1, 2, 5, 10, 20], "is_modify": [1, 2, 5, 10, 20]} | route=True side=True
  Course Master | /course/course-master | perm={"is_view": [1, 2, 5, 10, 14, 20], "is_add": [1, 2, 5, 10, 14, 20], "is_modify": [1, 2, 5, 10, 14, 20]} | route=True side=True
  My Courses | /course/my-courses | perm={"is_view": [1, 2, 5, 10, 14, 20]} | route=True side=True
  Training Calendar | /course/training-calendar | perm={"is_view": [1, 2, 5, 10, 11, 14, 20], "is_add": [1, 2, 5, 10, 11, 20], "is_modify": [1, 2, 5, 10, 11, 20]} | route=True side=True
  Training Request | /course/training-request | perm={"is_view": [1, 2, 5, 10, 14, 20], "is_add": [1, 2, 5, 10, 14, 20], "is_modify": [1, 2, 5, 10, 14, 20]} | route=True side=True
  Course Analytics | /course/analytics | perm={"is_view": [1, 2, 5, 10, 11, 14, 20]} | route=True side=True
My Courses | /course/my-courses | perm={"is_view": [3, 6, 7, 8, 9, 11, 12, 13, 15, 16, 17, 18, 19, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
Training Request | /course/training-request | perm={"is_view": [3, 6, 7, 8, 9, 11, 12, 13, 15, 16, 17, 18, 19, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35, 33], "is_add": [3, 6, 7, 8, 9, 11, 12, 13, 15, 16, 17, 18, 19, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35, 33], "is_modify": [3, 6, 7, 8, 9, 11, 12, 13, 15, 16, 17, 18, 19, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
Trainer Dashboard | /course/trainer-dashboard | perm={"is_view": [1, 5, 6, 10, 20, 31]} | route=True side=True
Employee Referral | /employee-referral | perm={"is_view": [1, 2, 5, 6, 14]} | route=True side=True
My Referral | /my-referral | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35], "is_add": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35], "is_modify": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
Staff Referral | /staff-referral | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35], "is_add": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35], "is_modify": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
Travel | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  My Travel Request | /travel/my-travel-request | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Travel Approval | /travel/travel-approval | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  All Travels Request | /travel/all-travels-request | perm={"is_view": [1, 5, 16, 23, 26, 28]} | route=True side=True
  Travel Config | /travel/travel-config | perm={"is_view": [1, 5, 16]} | route=True side=True
None | /course/my-course/:courseId | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=False
None | /course/posh-training | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 29, 30, 31, 35, 33]} | route=True side=False
Digital Engagement | None | perm={"is_view": [1, 2]} | route=True side=True
  Variables | /digital-engagement/variables | perm={"is_view": [1, 2]} | route=True side=True
  Templates | /digital-engagement/templates | perm={"is_view": [1, 2], "is_add": [1, 2], "is_modify": [1, 2]} | route=True side=True
Reimbursement | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Claim Request | /reimbursement/claim-request | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Claim History | /reimbursement/claim-history | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Claim Approvement | /reimbursement/claim-approvement | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Claim Draft | /reimbursement/claim-draft | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  My Team Outings | /reimbursement/team-outing | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31]} | route=True side=True
  None | /reimbursement/team-outing/:id | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31]} | route=True side=False
  Team Outing (CHRO) | /reimbursement/team-outing/chro-queue | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31]} | route=True side=False
  Team Outing (KP) | /reimbursement/team-outing/kp-queue | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 27, 28, 29, 30, 31]} | route=True side=False
Presence Management | None | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
  Attendance and Leave | /attendance | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
  Employee Leaves Approval | /leave-approval | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Leave Apply | /leave-apply | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=False
  Arrears Input | /arrears-input | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=True
  None | /regularization | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=False
  Leave Configuration | /leave-configuration | perm={"is_view": [1, 12, 19, 26, 28]} | route=True side=True
  Employee Attendance | /employee-attendance | perm={"is_view": [1, 12, 19, 26, 28]} | route=True side=True
  Employee Leave | /employee-leave | perm={"is_view": [1, 12, 19, 26, 28]} | route=True side=False
  Shift Management | /shift-management | perm={"is_view": [1, 12, 19, 26, 28]} | route=True side=True
  Holiday Management | /holiday-management | perm={"is_view": [1, 12, 19, 26, 28]} | route=True side=True
  Leave Balance | /leave-balance | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=False
  Holiday Calendar | /holiday-calendar | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Attendance Muster | /attendance-muster | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]} | route=True side=True
  Absence Notifications | /absence-notifications | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]} | route=True side=True
  Rest Day Roster | /rest-day-roster | perm={"is_view": [1, 5, 7]} | route=True side=True
  Arrears Report | /arrears-report | perm={"is_view": [1, 19, 26, 28]} | route=True side=True
  NJ Arrears Report | /nj-arrears-report | perm={"is_view": [1, 19, 26, 28]} | route=True side=True
Asset | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Asset List | /asset/asset-listing | perm={"is_view": [1, 2, 18, 21, 22], "is_add": [1, 2, 21, 22], "is_edit": [1, 21]} | route=True side=True
  Asset Management Dashboard | /asset/asset-assigning | perm={"is_view": [1, 2, 21, 17, 18, 19, 22, 26, 28]} | route=True side=True
  Your Assets | /asset/your-assets | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Request Asset | /asset/request-asset | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=True
  Asset Dashboard | /asset/asset-manager | perm={"is_view": [1, 2, 18, 19, 21, 22, 23, 26, 28]} | route=True side=True
  All Asset Requests | /asset/asset-requests | perm={"is_view": [1, 2, 18, 21, 22]} | route=True side=True
  Manage Approvers | /asset/approvers-config | perm={"is_view": [1, 2, 18, 21, 22]} | route=True side=True
  Subscriptions | /asset/subscriptions | perm={"is_view": [1, 2, 18, 21, 22]} | route=True side=True
  Asset Approval | /asset/approval | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 35, 33], "require_asset_approver": true} | route=True side=True
Salary Advance Management | None | perm={"is_view": [1, 19, 26, 28]} | route=True side=True
  Salary Advance Request | /salary-advance/request | perm={"is_view": [1, 19, 26, 28]} | route=True side=True
  Salary Advance Approval | /salary-advance/approval | perm={"is_view": [1, 19, 26, 28]} | route=True side=True
  Loan Statement | /salary-advance/statement | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=True
Payroll Management | None | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Component Management | /payroll/component-management | perm={"is_view": [1, 19, 26, 28], "is_add": [1, 19, 26, 28], "is_modify": [1, 19, 26, 28]} | route=True side=True
  One Time Inputs | /payroll/one-time-input-component | perm={"is_view": [1, 2, 19, 26, 28], "is_add": [1, 2, 19, 26, 28], "is_modify": [1, 2, 19, 26, 28], "is_delete": [1, 2, 19, 26, 28]} | route=True side=True
  Session Inputs | /payroll/session-input | perm={"is_view": [1, 2, 19, 26, 28], "is_add": [1, 2, 19, 26, 28], "is_modify": [1, 2, 19, 26, 28], "is_delete": [1, 2, 19, 26, 28]} | route=True side=True
  None | /payroll/arrears-input-component | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
  Employee Salary | /payroll/employee-salary | perm={"is_view": [1, 19, 26, 28], "is_add": [1, 19, 26, 28], "is_modify": [1, 19, 26, 28]} | route=True side=True
  KP Employee Salary | /payroll/kp/employee-salary | perm={"is_view": [1, 19, 26, 28]} | route=True side=False
  Travel Allowance | /payroll/travel-allowance | perm={"is_view": [1, 19, 26, 28], "is_add": [1, 19, 26, 28], "is_modify": [1, 19, 26, 28]} | route=True side=True
  TA Group Configuration | /payroll/travel-allowance/group/:id | perm={"is_view": [1, 19, 26, 28], "is_add": [1, 19, 26, 28], "is_modify": [1, 19, 26, 28]} | route=True side=False
  Salary Approval | /payroll/salary-approval | perm={"is_view": [1, 26], "is_add": [1, 26], "is_modify": [1, 26]} | route=True side=True
  Attendance Report | /payroll/reports/attendance | perm={"is_view": [1, 12, 15, 26]} | route=True side=True
  Missing Info Report | /payroll/reports/missing-information | perm={"is_view": [1, 7, 12, 15, 19, 26, 28]} | route=True side=True
  Advance Recovery | /payroll/reports/advance-recovery | perm={"is_view": [1, 7, 12, 15, 19, 26, 28]} | route=True side=True
  Leave Encashment | /payroll/reports/leave-encashment | perm={"is_view": [1, 16, 19, 26]} | route=True side=True
  Generate Payroll | /payroll/generate-payroll | perm={"is_view": [1, 26]} | route=True side=True
  KP Generate Payroll | /payroll/kp/generate-payroll | perm={"is_view": [1, 19, 26, 28]} | route=True side=False
  My Payslip | /payroll/my-payslip | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  My Salary | /payroll/my-salary | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=True
  YTD Statement | /payroll/ytd-statement | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=True
  IT Declaration | /payroll/it-declaration | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  IT Declaration Window | /payroll/it-declaration-window | perm={"is_view": [1, 26], "is_add": [1, 26], "is_modify": [1, 26]} | route=True side=True
  IT Declaration Approval | /payroll/it-declaration-approval | perm={"is_view": [1, 26], "is_add": [1, 26], "is_modify": [1, 26]} | route=True side=True
  Income Tax Forms (Form 16) | /payroll/forms16 | perm={"is_view": [1, 19, 26, 28], "is_add": [1, 19, 26, 28], "is_modify": [1, 19, 26, 28]} | route=True side=True
  IT Statement | /payroll/it-statement | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35], "is_add": [1], "is_modify": [1]} | route=True side=True
  Salary Revision | /payroll/salary-revision | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35], "is_add": [1], "is_modify": [1]} | route=True side=True
  IT Declaration Edit | /payroll/it-declaration/edit | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=None
  IT Declaration View | /payroll/it-declaration/view | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=None
  tax-planner | /payroll/it-declaration/tax-planner | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=None
  IT Declaration View | /payroll/it-declaration/tax-planner/edit | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=None
  Employee Detail Update Approval | /payroll/employee-details-update-approval | perm={"is_view": [1, 14, 26, 28]} | route=True side=True
  Internal Transfers | /payroll/internal-transfers | perm={"is_view": [1, 2, 5, 16, 26, 28, 35]} | route=True side=True
  Flexi Benefits (FBP) | /payroll/fbp | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
  FBP Approvals | /payroll/fbp/approvals | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35, 33]} | route=True side=True
  FBP Config | /payroll/fbp/config | perm={"is_view": [1, 2, 5]} | route=True side=True
  Attendance Bonus | /payroll/attendance-bonus-config | perm={"is_view": [1, 19, 26]} | route=True side=True
  Cult Membership | /payroll/cult | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33]} | route=True side=True
  Cult Membership Queue | /payroll/cult/approvals | perm={"is_view": [1, 2, 5, 19, 26]} | route=True side=True
  Cult Membership Config | /payroll/cult/config | perm={"is_view": [1, 2, 5, 26]} | route=True side=True
  Cult Membership Reports | /payroll/cult/reports | perm={"is_view": [1, 2, 5, 19, 26]} | route=True side=True
Project Management | /project-management | perm={"is_view": [1, 3, 29, 30, 31]} | route=True side=True
Scrum Management | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Scrum Board | /scrum-management/board | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
  Scrum Project | /scrum-management/projects | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35], "is_add": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35], "is_modify": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 35]} | route=True side=True
Star Teacher Program | None | perm={"is_view": [1, 5, 6, 29, 30]} | route=True side=True
None | None | perm=null | route=None side=None
SJT Assessment | None | perm={"is_view": [1, 2, 5, 11, 12, 14]} | route=True side=True
None | None | perm=null | route=None side=None
AI Interview | None | perm={"is_view": [1, 2, 5, 6, 11, 12]} | route=True side=True
None | None | perm=null | route=None side=None
School Planning | None | perm={"is_view": [1, 2, 5, 6, 7, 10, 11, 13, 33]} | route=True side=True
  {'__expr__': '(()=>{var e;try{const t=JSON.parse(localStorage.getItem("recruitmentLoginDetails")||"{}");return Number((e=t==null?void 0:t.user_level)==null?void 0:e.id)===7?"'} | /school-planning | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Settings | /school-planning/settings | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Student Planning | /school-planning/students | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Teacher Planning | /school-planning/teachers | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Salary Planning | /school-planning/salary | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Projected vs Actual | /school-planning/projected-vs-actual | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Planning Inputs | /school-planning/inputs | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=False
  P&L Config | /school-planning/pnl | perm={"is_view": [1, 5, 7, 10, 11, 33]} | route=True side=True
  Position Management | /school-planning/positions | perm={"is_view": [1, 2, 5, 6, 7, 10, 11, 13, 33]} | route=True side=True
  Deputy Coord Approvals | /school-planning/deputy-coordinator-approvals | perm={"is_view": [1, 5]} | route=True side=True
  Multi-Subject Approvals | /school-planning/multi-subject-approvals | perm={"is_view": [1, 5]} | route=True side=True
  Position Dashboard | /school-planning/positions/dashboard | perm={"is_view": [1, 2, 5, 6, 7, 10, 11, 13, 33]} | route=True side=True
  Position Approvals | /school-planning/positions/approvals | perm={"is_view": [1, 2, 3, 5, 6, 7, 10, 11, 33]} | route=True side=True
  Role Revamp | /school-planning/role-revamp | perm={"is_view": [1, 2, 5, 6, 7, 11]} | route=True side=True
  To-Be-Vacant | /school-planning/to-be-vacant | perm={"is_view": [1, 2, 5]} | route=True side=True
  Books Planning | /school-planning/books | perm={"is_view": [1, 5, 7, 10, 11]} | route=True side=True
  Run Compute | /school-planning/books/compute | perm={"is_view": [1, 5, 10]} | route=True side=False
  Branch Detail | /school-planning/books/branch/:branchCode | perm={"is_view": [1, 5, 7, 10, 11]} | route=True side=False
  All Branches (Books) | /school-planning/books/all-branches | perm={"is_view": [1, 5, 10, 11]} | route=True side=True
  Books History | /school-planning/books/history/:branchCode/:grade | perm={"is_view": [1, 5, 10, 11]} | route=True side=False
  Books Settings | /school-planning/books/settings | perm={"is_view": [1, 5, 10]} | route=True side=True
HR & Hiring | None | perm={"is_view": [1, 2, 5, 6, 7, 10, 11]} | route=True side=True
  Zonal Recruitment | /hr-hiring/zonal-recruitment | perm={"is_view": [1, 2, 5, 6, 7, 10, 11]} | route=True side=True
Attrition Dashboard | /attrition | perm={"is_view": [1, 5, 13, 14, 16]} | route=True side=True
Corporate Planning | /corporate-planning | perm={"is_view": [1, 5, 10, 14, 16]} | route=True side=True
Reports Gallery | /reports-gallery | perm={"is_view": [1, 2, 5, 16, 19, 26, 28]} | route=True side=True
Leave Transaction Report | /reports-gallery/leave-transaction | perm={"is_view": [1, 5, 16, 19, 26, 28]} | route=True side=False
Leave Balance Report | /reports-gallery/leave-balance | perm={"is_view": [1, 5, 16, 19, 26, 28]} | route=True side=False
Loan Report | /reports-gallery/loan | perm={"is_view": [1, 5, 19, 26, 28]} | route=True side=False
Probation Report | /reports-gallery/probation | perm={"is_view": [1, 5, 16, 19, 26, 28]} | route=True side=False
Notice Period Report | /reports-gallery/notice-period | perm={"is_view": [1, 5, 16, 19, 26, 28]} | route=True side=False
Weekly Report — Schools & Colleges | /weekly-report | perm={"is_view": [1, 5, 16, 19, 26, 28]} | route=True side=True
None | /reports-gallery/weekly-report | perm={"is_view": []} | route=True side=False
CEO Report | /reports-gallery/ceo-report | perm={"is_view": [1, 2]} | route=True side=False
Document Center | /document-center | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=True
My Care | /employee-grievance/my-tickets | perm={"is_view": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 33]} | route=True side=True
Employee Care | None | perm={"is_view": [1, 5, 16]} | route=True side=True
None | None | perm=null | route=None side=None
None | /document-center/documents | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
None | /document-center/appointment-letter | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
None | /document-center/payslips | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
None | /document-center/policies | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
WhatsApp Connect | None | perm={"is_view": [1, 2, 5], "is_add": [1, 2, 5], "is_modify": [1, 2, 5]} | route=True side=True
  Inbox | /whatsapp-connect | perm={"is_view": [1, 2, 5], "is_add": [1, 2, 5], "is_modify": [1, 2, 5]} | route=True side=True
  Message Template | /whatsapp-template | perm={"is_view": [1, 2, 5], "is_add": [1], "is_modify": [1]} | route=True side=True
  Template Create | /whatsapp-template/create | perm={"is_view": [1], "is_add": [1], "is_modify": [1]} | route=True side=False
  Workflow | /whatsapp-workflow | perm={"is_view": [1, 2, 5], "is_add": [1, 2, 5], "is_modify": [1, 2, 5]} | route=True side=True
BG Verification | None | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33]} | route=True side=True
  OnGrid BGV | /bgv-status | perm={"is_add": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33], "is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33], "is_modify": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33]} | route=True side=True
  Trace BGV | /trace-bgv | perm={"is_add": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33], "is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33], "is_modify": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 26, 28, 29, 30, 31, 32, 33]} | route=True side=True
None | /document-center/form-16 | perm={"is_view": [1, 2, 3, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31]} | route=True side=False
```
