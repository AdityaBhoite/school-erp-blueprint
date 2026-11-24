# M18 — HRMS Self-Service (optional bounded context)

**App:** `/recruitment/` (title "HRMS"). **Evidence:** E002 (profile, OBSERVED), E016 (HRMS self-service tour, OBSERVED shapes), E006/E007 (STATIC).
Every observed HRMS screen (15) also called `GET /qbox/payroll/kp/access/` → `{is_kp_payroll_admin}` and `GET /qbox/lms/training-reminders/` (OBSERVED, E016). `GET /qbox/user-mgmt/current-user/` is a static catalog entry (E006 `login.tokenLoginApi`) and was **not** observed (audit ER-47).

## Employee profile (`/recruitment/` → Profile, OBSERVED)
Left card: avatar, name, role badge (EMPLOYEE), `@erp_id`, official email, phone; buttons **Change Password**, **Setup Airtel Telephony**, **Assigned assets**, **Leaves**, link **Resignation**. Info alert about sensitive-data change approval (WF-14).
Sections (label/value grids with icons): **Personal Information** (DOB, gender, marital status, nationality, official/personal email, contact); **Address** (permanent, current); **Current Company Information** (joining date, company experience, branch, compliance branch, department, designation, company code, company/legal entity, reporting manager, HR SPOC, city, cadre, shift); **Family** (father, mother, spouse, marriage date, number of children; children cards); **Bank & ID** (Aadhaar, PAN, account number masked with reveal; IFSC; account holder); **Emergency Contact**; **Education Details** (Class 10, Class 12, graduation… with "+N More"); **Previous Company** (employment history with approval status); **Statutory Forms** (PF/UAN form, Gratuity Form F, Form 12B income details, nominees with share %); **Platform Access** (platform and hiring access levels); embedded **Recruiter Performance** widget (date range, Performance / Scheduled by Type / Assessments Taken tabs, recruiter table).

## Self-service screens (OBSERVED APIs)
| Screen | Route | Key API (shape fields) |
|---|---|---|
| Attendance & leave | `/attendance` | `/payroll/leave-types/?attendance&is_active` → `{name, code, is_paid, encashment_allowed, default_balance, carry_forward, max_carry_forward_days, requires_document, haf_day_allowed, is_multiple_day_allowed, is_active}`; `/payroll/employee-attendance-details/?erp_id&start_date&end_date` (period, shift `{name, start_time, end_time, in_margin, out_margin}`, day records); `?current_date` (today: shift window, `swipe_type`, `max_no_of_regularisation`); `/payroll/payroll-cycle-lock/?payroll_month&payroll_year` → `{cycle_name, is_locked, status…}`; `/payroll/reportees-dropdown/` |
| Holiday calendar | `/holiday-calendar` | `/payroll/user-holiday/?session_year` → `{Jan:[{name, holiday_date, session_year, description}], …}` |
| Leave apply | `/leave-apply` | `/payroll/leave-applications/?application_type&status&page&page_size` |
| Document centre | `/document-center` | `/document-center/summary/` → `{viewing{is_self, erp_id, employee_name, can_view_others}, documents, payslips, company_policies}` |
| My courses | `/course/my-courses` | `/lms/my-courses/?course_type&is_active`; `/lms/teacher-cpd-hours/` → `{total_duration, completed_courses_count, certificate_count, courses[]}` (CPD hours for teachers) |
| My care (grievances) | `/employee-grievance/my-tickets` | `/grievance-module/employee-ticket/?scope` (tickets with `employee_ticket_response[]` replies: reply, replied_by, replied_at, file, is_read, reply_type, window, visible_to_other…); `/employee-ticket-category-group/` (category groups with default POC and super POCs); `/employee-team-member-check/` → `{is_team_member, is_sme, is_super_poc…}`; `/employee-detail/?erp_id` |
| Your assets | `/asset/your-assets` | `/asset/asset-items/?employee_id` (asset_code, make, model, serial_number, type, issue_type, procurement_date, asset_cost, warranty_expiry_date…); `/asset/asset-types/`; `/asset/asset-requests/`; `/asset/subscription-assignments/` |
| Claim history | `/reimbursement/claim-history` | finance `/apiV1/v2/reimbursement-form/?erp_id&is_draft&request_kind&is_local_conveyance&include_withdrawn` |
| Travel | `/travel/my-travel-request` | `/travel/travel-request/?view_type` (+`progress_counts{approval, booking, partly}`, `tab_counts{ongoing, upcoming, history}`); `/central-db/branch-view/` (branches with zone, city, school type, address, lat/long) |
| Apply transfer | `/transfers/apply-transfer` | `/user-mgmt/department-list/`, `/user-mgmt/designation-list/` (department, collar_type, mail policy…), `/lead-mgmt/internal-transfer-request/` |
| Growth plan | `/growth-action-plan/my-plan` | `/pip/my-plan/` (performance improvement plan) |
| My referral | `/my-referral` | `/lead-mgmt/employee-refferal-history/` |
| My payslip | `/payroll/my-payslip` | `/payroll/employee-payslip/?employee_id&month-year` → `{employee_name, employee_code, bank_name, account_number, cycle_name, payroll_month/year, cycle dates, gross_salary, total_deductions, net_salary, …}` (envelope `{success, message, data}`) |
| IT declaration | `/payroll/it-declaration` | `/payroll/tax-declaration-summary/?financial_year` → `{tax_regime, status, window_status, is_window_open, sections[{section, max_limit, declared_amount, eligible_amount, has_declarations, declaration_count}], totals}` |
| Resignation | `/resignation` | `/exit-mgmt/resignation-request/?view_type` |
| Denied for this account | `/performance-management/my-performance`, `/salary-advance/request` | redirected to `/erp/user-profile` |

Replacement: optional HR context. For a school ERP MVP, implement only **staff leave + holidays + basic staff attendance** (feeds timetable substitution) and the staff profile (non-sensitive). Payroll, tax, assets, travel and grievances are separate products.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
In the HRMS app (`recruitment.letseduvate.com`), a teacher could reach self-service routes: `/employee-grievance/action`, `/njwelcomeform-sample`, `/payroll/set-pin` **rendered**; most other HRMS routes role-redirected. This matches the static finding that HRMS carries per-route `permission` arrays (m02/E007). Teacher HRMS access is limited to self-service, as expected. Status: routes RUNTIME_OBSERVED (teacher scope). See `ROLE_ROUTE_API_MATRIX.md` §6.
