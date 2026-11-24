# Information Architecture

Evidence: E005 (static unified navigation config), E007 (HRMS routes), E012 (ERP routes), E013–E016 (runtime shell observations). The full raw menu tree is in `10_REA_EVIDENCE/bundle_analysis/E005_unified_navigation_megamenu.md`.

## 1. Shell layout (OBSERVED, HIGH)

```
┌──────┬──────────────────────────────────────────────────────────────────────────┐
│ Logo │ Network-quality badge   [Search type ▾|Search box] [Branch ▾] [Year ▾] 📅 🗓 🔔(n) (Avatar) │  ← top bar
│ 🔍   ├──────────────────────────────────────────────────────────────────────────┤
│ 📖   │ Breadcrumb: Home · Module · Screen                                       │
│ 💬   │ ┌──────────────────────────────────────────────────────────────────────┐ │
│ ▦    │ │ Page header card (title, context chips: year / branch / grade, CTA) │ │
│ 💼   │ │ Filters row → content (table / calendar / cards / form)             │ │
│ $    │ └──────────────────────────────────────────────────────────────────────┘ │
│ 👤   │                                               [Help assistant FAB]     │
│ 🔧   │                                                                          │
│  >   │  (expand sidebar)                                                        │
└──────┴──────────────────────────────────────────────────────────────────────────┘
```

- **Left icon rail** (80 px): Search, then one icon per top-level category the user may access, then "Expand sidebar". Hovering or clicking a category opens a **mega-menu** of sub-sections and items. Clicking the category also navigates to its default route (for this teacher, Academics → `/erp/acms/calendar`).
- **Top bar**: network-quality indicator ("Excellent (10.0 Mbps)"), a global search with a type selector (for example `CAND` = candidate, by ID), **Branch selector**, **Academic-year selector** (`2026-27`), timetable and calendar shortcuts, **notification bell with unread count**, and a profile menu (avatar initial).
- **Floating "Help — Ask our Assistant"** button (AI help bot, dismissible, "NEW" badge).
- **Breadcrumbs** on ERP pages (`Home · Acms · Academic Calendar`).

## 2. Top-level categories (STATIC, HIGH)

The category list is shared by all three apps. Each category routes to one app; visibility is decided by the access matrix.

| id | Label | Target app | Default route | Access gate |
|---|---|---|---|---|
| dashboard | Dashboard | current | — | always |
| academics | Academics | erp | /erp/ | always (filtered by matrix) |
| students | Students | erp | /erp/ | always |
| admissions | Marketing/CRM | crm | /crm/ | `crm_enabled` |
| hr-recruitment | HR & Hiring | recruitment | /recruitment/ | `recruitment_enabled` |
| people | People | recruitment | /recruitment/ | `recruitment_enabled` |
| payroll | Payroll | recruitment | /recruitment/ | `recruitment_enabled`, hidden when impersonating |
| finance | Finance | erp | /erp/ | always |
| transport | Transport | erp | /erp/ | always |
| operations | Operations | erp | /erp/ | always |
| communication | Comms | erp | /erp/ | always |
| approvals | Approvals | erp | /erp/ | always |
| admin | Admin | erp | /erp/ | always |
| student-dashboard | Dashboard (student/parent) | erp | /erp/dashboard | always |
| student-academics | Academics (student/parent) | erp | /erp/lesson-plan | always |
| student-fees | Fees & Wallet (student/parent) | erp | /erp/student-fee/student-fee-payment | always |
| student-communication | Communication (student/parent) | erp | /erp/ticket | always |
| student-activities | Activities (student/parent) | erp | /erp/summer-camp-registration | always |
| tools | Tools | recruitment | /recruitment/ | always |

**Observed for the teacher account:** the rail shows Search, Academics, Comms, Dashboard, HR & Hiring, Payroll, People, Tools. Students, Marketing/CRM, Finance, Transport, Operations, Approvals and Admin are **not shown** (OBSERVED, HIGH).

## 3. Mega-menu sub-sections (STATIC, HIGH)

| Top-level category | Access flag | Sub-section | App | Items | Example items |
|---|---|---|---|---|---|
| Dashboard | always | ERP Dashboards | erp | 23 | Main Dashboard, Academic Dashboard, Operations Dashboard, Branch Analysis, Finance Dashboard V1, Finance Dashboard V2 … |
| Dashboard | always | CRM Dashboards | crm | 2 | CRM Dashboard, School Partner Payment Dashboard |
| Dashboard | always | HRMS Dashboards | recruitment | 5 | HRMS Dashboard, Attrition, Teacher Recruitment, KP Dashboard, Recruiter Performance |
| Academics | always | Curriculum Planning | erp | 8 | Version Mapping, Yearly Curriculum Plan, Clickers Disparity (by Content), Lesson Plan Upload, Ebook Upload, QR Code … |
| Academics | always | Events | erp | 2 | Event Manager, Event Reports |
| Academics | always | Academic Operations | erp | 12 | Dashboard, Academic Calendar, Boards and Curriculum, Academic Team, Teacher Allocation Requests, LP Config … |
| Academics | always | Settings | erp | 1 | Grade-Subject Mappings |
| Academics | always | Video Monitoring | erp | 8 | Dashboard, Due for Observation, My Observations, Pending Approvals, Improvement Plans, Teacher Obs. Report … |
| Academics | always | Academic Improvement | erp | 3 | Academic Improvement, Improvement Plans, Academic Daily Log |
| Academics | always | House System | erp | 6 | House Directory, Allocations, Leadership, Events, Leaderboard, Branch Setup |
| Academics | always | Audio Analysis | erp | 4 | My Teaching Insights, School Dashboard, Network View, Rubric Editor |
| Academics | always | Teaching & Classroom | erp | 9 | Lesson Plan, School Clicker, Online Books, Online Class, Homework, Homework Dashboard … |
| Academics | always | Assessments & Evaluation | erp | 16 | Blueprint, Assessment, Performance Analytics, Question Bank, SDP, Print Vendors … |
| Academics | always | Observation & Quality | erp | 6 | Assign Video, Observation, Observation Evaluation, Teacher Observation, Observation Report, Daily Class Observations |
| Academics | always | Board Prep | erp | 7 | Dashboard, Goal Setup, Central Dashboard, Inbox, Access Allowlist, Board Content … |
| Students | always | Admission | erp | 2 | Admission, Referral Report |
| Students | always | Student Management | erp | 8 | Student Shuffle, Section Shuffle, Student Type, Student Promotion, Temporary Status, Permanent Status … |
| Students | always | Attendance | erp | 1 | Student Attendance |
| Students | always | Reports | erp | 8 | Student Contact Report, Temporary Status Report, TPND Reports, Total Form Report, SEN Report, User Status Report … |
| Students | always | User Management | erp | 6 | View Users, User Level, Access Blocker, Virtual School, TC Request, OCAP Students |
| Marketing/CRM | crm_enabled | Lead Management | crm | 8 | Dashboard, All Leads, AI Calling, Bulk Upload Lead, Child Details Approval, Daily Followup … |
| Marketing/CRM | crm_enabled | Lead Assigning | crm | 6 | Assign to PRM, Reassign to PRM, Unassign from PRM, Regen & Assign to PRM, Assign to CCA, Reassign to CCA |
| Marketing/CRM | crm_enabled | Enquiry Forms | crm | 3 | Digital Enquiry Form, Physical Enquiry Form, Application Form |
| Marketing/CRM | crm_enabled | Event Management | crm | 3 | Hotspots, Events, Central Events |
| Marketing/CRM | crm_enabled | Targets & Budget | crm | 10 | Assign Targets, Budget Type, Assign Budget, Expense, Budget Summary, Incentive Rules … |
| Marketing/CRM | crm_enabled | Budget Planning | crm | 9 | Budget Model, Admissions Projections, Zonal Allocation, Actuals & BvA, Reforecast, Zone Dashboard … |
| Marketing/CRM | crm_enabled | SAP | crm | 4 | Approval Matrix, Purchase Requisition, SAP Settings, Vendors |
| Marketing/CRM | crm_enabled | Reports | crm | 25 | Management Report, Analysis Report, Monitoring Report, Additional Report, Ageing Report, Affiliate Report … |
| Marketing/CRM | crm_enabled | Audit Management | crm | 7 | Call Auditing, PRM Voice Recording, Quality Checker, Admission Verification, Image Gallery, Video Observation … |
| Marketing/CRM | crm_enabled | Forms | crm | 4 | Newspaper Insert, Pre School Tie Up, RWA Event, Visibility Marketing |
| Marketing/CRM | crm_enabled | Master Data | crm | 7 | Activity Status, Source Type, Source, Checkout Question, Whatsapp Agent Config, ChatGPT Config … |
| Marketing/CRM | crm_enabled | Google Ranking | crm | 2 | Ranking Master Data, SERP Report |
| Marketing/CRM | crm_enabled | User Management | crm | 2 | User Dashboard, User Management |
| Marketing/CRM | crm_enabled | CRM Misc | crm | 10 | My Referrals, Payment Requests, Document Verification, Partner Documents Verification, Payment Details, Admissions … |
| HR & Hiring | recruitment_enabled | MRF Management | recruitment | 8 | MRF Management, MRF Recruiter, Candidates, My Interviews, Assessment, Batch Interview … |
| HR & Hiring | recruitment_enabled | Approval | recruitment | 3 | Salary Fitment, Budget Approvals, Promotions/Transfers |
| HR & Hiring | recruitment_enabled | Onboarding | recruitment | 1 | Onboarding |
| HR & Hiring | recruitment_enabled | BG Verification | recruitment | 2 | OnGrid BGV, Trace BGV |
| HR & Hiring | recruitment_enabled | Performance Management | recruitment | 6 | PMS Config, Evaluation Workflow, Performance Evaluation, Designation Templates, My Performance, Growth Action Plan |
| HR & Hiring | recruitment_enabled | Separations | recruitment | 6 | Resignation, Candidate Separation, All Separations, Employee Exit, Exit Management, Clearance |
| HR & Hiring | recruitment_enabled | Employee Mobility | recruitment | 8 | Apply Transfer, Direct Transfer, Transfer Approval, All Transfers, Assign MRF to Transfer, Observation Transfers … |
| HR & Hiring | recruitment_enabled | Star Teacher Program | recruitment | 7 | Dashboard, Shortlisting, Candidates, My Interviews, Interview Schedule, Question Banks … |
| HR & Hiring | recruitment_enabled | SJT Assessment | recruitment | 2 | Templates, Sessions |
| HR & Hiring | recruitment_enabled | Vacancy Grid | recruitment | 1 | Vacancy Grid |
| HR & Hiring | recruitment_enabled | Recruiter Salary Dashboard | recruitment | 1 | Recruiter Salary Dashboard |
| HR & Hiring | recruitment_enabled | Audit | recruitment | 4 | Campus Hiring, Lateral Hiring, My Audit Report, Auditor Activity |
| HR & Hiring | recruitment_enabled | AI Interview | recruitment | 5 | Question Bank, Dashboard, Templates, Sessions, Settings |
| HR & Hiring | recruitment_enabled | Recruitment Metrics | recruitment | 1 | Metrics Dashboard |
| HR & Hiring | recruitment_enabled | Career Applications | recruitment | 1 | Applications |
| HR & Hiring | recruitment_enabled | Zonal Recruitment | recruitment | 1 | Zonal Recruitment |
| HR & Hiring | recruitment_enabled | School Planning | recruitment | 16 | Pipeline Overview, Settings, Student Planning, Teacher Planning, Salary Planning, Projected vs Actual … |
| HR & Hiring | recruitment_enabled | Corporate Planning | recruitment | 1 | Corporate Planning |
| HR & Hiring | recruitment_enabled | Referral | recruitment | 4 | Employee Referral, Referral Bonus Slab, My Referral, Staff Referral |
| HR & Hiring | recruitment_enabled | Master Data | recruitment | 11 | Applicant Stage, Source Type, Source, Department, Designation, Entity Master … |
| HR & Hiring | recruitment_enabled | WhatsApp Connect | recruitment | 3 | Inbox, Message Template, Workflow |
| People | recruitment_enabled | People Management | recruitment | 5 | User Management, SAP Business Partner, Approval, Manager Update Approval, My Growth Plan |
| People | recruitment_enabled | Presence Management | recruitment | 13 | Attendance, Leave Approval, Leave Configuration, Employee Attendance, Employee Leave, Regularization … |
| People | recruitment_enabled | Training & Courses | recruitment | 7 | Category, Course Master, My Courses, Training Request, Training Calendar, Course Analytics … |
| People | recruitment_enabled | Assets | recruitment | 9 | Asset List, Asset Management Dashboard, Your Assets, Request Asset, Asset Dashboard, All Asset Requests … |
| People | recruitment_enabled | Employee Care | recruitment | 3 | My Care, Config, Dashboard |
| People | recruitment_enabled | Travel | recruitment | 4 | My Travel Request, Travel Approval, All Travel Requests, Travel Config |
| People | recruitment_enabled | Salary Advance | recruitment | 3 | Advance Request, Advance Approval, Loan Statement |
| People | recruitment_enabled | Staff Model | recruitment | 4 | Dashboard, Runs, Norms, Settings |
| People | recruitment_enabled | Analytics | recruitment | 1 | Attrition Dashboard |
| People | recruitment_enabled | Document Center | recruitment | 5 | Document Center, Documents, Payslips, Company Policies, Form 16 |
| People | recruitment_enabled | WhatsApp Connect | recruitment | 3 | Inbox, Message Template, Workflow |
| Payroll | recruitment_enabled | Configuration | recruitment | 4 | Component Management, One Time Inputs, Session Inputs, Attendance Arrears |
| Payroll | recruitment_enabled | Salary | recruitment | 20 | Employee Salary, Travel Allowance, Salary Approval, Generate Payroll, KP Generate Payroll, KP Employee Salary … |
| Payroll | recruitment_enabled | Tax & Declarations | recruitment | 4 | IT Declaration, IT Declaration Window, IT Declaration Approval, Income Tax Forms (Form 16) |
| Payroll | recruitment_enabled | Reports | recruitment | 6 | Reports Gallery, Weekly Report, Attendance Report, Missing Info Report, Advance Recovery, Leave Encashment |
| Payroll | recruitment_enabled | Reimbursement | recruitment | 4 | Claim Request, Claim History, Claim Approval, Claim Draft |
| Finance | always | Fee Management | erp | 15 | Fee Term, Fee Plan, Misc Fee, Cheque Payment, Fine Config, Fine Request … |
| Finance | always | Kiosk & Cash Deposit | erp | 4 | Kiosk Dashboard, Cash Deposit Machine, Cheque Verification, Kiosk Cash Deposit |
| Finance | always | Fee Collection & Follow-Up | erp | 5 | Calling vs Due, Follow Up, Fee Reminder, Assigned Follow-ups, Fee Follow Up Dashboard |
| Finance | always | Concession & Approvals | erp | 17 | Master Concession, Concession Upload, Approve Concession, Sibling concession approvals, Approve SEN Opt Out, Approve Pay Request … |
| Finance | always | Imprest & Store Wallet | erp | 7 | Imprest Wallet, Imprest Wallet Report, Imprest Pay Request, Imprest Refund Request, Store Wallet, Store Pay Request … |
| Finance | always | Expense Management | erp | 17 | Expense Settings, Account Head-Subhead, TDS Configuration, TDS Report, Cash In Hand, Party List … |
| Finance | always | Reimbursement | erp | 5 | Team, Config, Reimbursement List, SAP Config, Claude Subscription |
| Finance | always | Branch P&L | erp | 7 | Dashboard, Compare, Finance Dashboard, Fee Collection Dashboard, Late Fine Dashboard, Books and Uniform Dashboard … |
| Finance | always | Reports | erp | 17 | Cheque Bounce Report, Fee Concession Report, Wallet Report, Transaction Report, Deleted Fee Log, Tally Report … |
| Finance | always | Rent Management | erp | 6 | Dashboard, Agreements, Monthly Dues, Payments, TDS Tracker, Reports |
| Finance | always | JODO Integration | erp | 3 | JODO Cred Report, JODO Disbursement, JODO Flex Report |
| Finance | always | Avanse Integration | erp | 1 | Admin Config |
| Transport | always | Transport Hub | erp | 10 | Dashboard, Operations, Bus Cleanliness, Services, Fees, Analytics & P&L … |
| Transport | always | Modules | erp | 2 | Bus Module, DG Module |
| Transport | always | Transport Management | erp | 5 | Transport Fee Plan, Transport Fee Term, Transport Report, Transport Audit, Bus Transfer Request |
| Operations | always | Store & Inventory | erp | 23 | Store Dashboard, Item Master, Book List, Store Config, Store Order Report, Ekart Bulk Order … |
| Operations | always | Co Scholastic Inventory Management | erp | 2 | Sports Inventory, Arts Inventory |
| Operations | always | Outlet Management | erp | 4 | Outlet Category, Outlet Item, Outlet List, Outlet Report |
| Operations | always | Procurement | erp | 7 | Dashboard, Purchase Requisitions, Purchase Orders, Goods Receipt (GRN), Invoices, Materials … |
| Operations | always | Infrastructure | erp | 7 | Infradesk, Electricity Meter, School Cost Calculator, Infra Blueprint, DG Generator, School Infra Details … |
| Operations | always | Expansion & Growth | erp | 22 | Dashboard, Finance Dashboard, Legal Dashboard, Management Dashboard, Maps View, Properties … |
| Operations | always | Contracts (CLM) | erp | 6 | Dashboard, All Contracts, Pending Approvals, Counterparties, Masters, Contract Detail |
| Operations | always | Summer Camp | erp | 2 | Summer Camp (Staff), Summer Camp (Students) |
| Comms | always | Central Team Communication | erp | 2 | Communication Admin, Communication Inbox |
| Comms | always | Meetings | erp | 2 | Calendar Hub, Meeting Scheduler |
| Comms | always | Messaging | erp | 2 | Chat, Announcements |
| Comms | always | Parent Communication | erp | 5 | Ticket, Ticket Config, Ticket Dashboard, Ticket Report, Refund Ticket |
| Comms | always | Visitors & Social | erp | 4 | MySchoolGate, Offline Gate Checkin, Gate Entry Approval, School Wall |
| Comms | always | Feedback | erp | 5 | Feedback, Feedback Report, ZFM Observations, Accountant Observations, TV Media |
| Comms | always | WhatsApp Connect | crm | 4 | Inbox, Agent Reports, Message Template, Workflow |
| Comms | always | Digital Engagement (CRM) | crm | 3 | Variables, Campaign, Templates |
| Comms | always | Digital Engagement (HRMS) | recruitment | 2 | Variables, Templates |
| Approvals | always | Student Approvals | erp | 4 | Temporary Status Request, Permanent Status Request, Permanent Status (Principal), Student Shuffle Request |
| Approvals | always | Fee Approvals | erp | 8 | Physical Cash Position, Fee Plan Change, Student Type Change, Transport Fee Plan Change, Wallet Request, Store Wallet Request … |
| Admin | always | Staff | erp | 4 | Staff Management, Staff Distribution, Staff ID Card, Play Store Reviews |
| Admin | always | Master Data | erp | 9 | Academic Blueprint, Mappings, Signature Upload, Language, Language Selection Config, System Config … |
| Admin | always | Migration | erp | 1 | Store Item Migration |
| Admin | always | Access Control | erp | 1 | Manage Access |
| Admin | always | WhatsApp | erp | 2 | WhatsApp, WhatsApp Notifications |
| Admin | always | Bulk Operations | erp | 4 | Student Type Upload, Temporary Status Upload, Collection Upload, Concession Upload |
| Admin | always | Onboarding Kit | erp | 7 | Onboarding Kit, Admin, Branch Content, Branch-Grade Content, Grade-Wise Quiz, Leadership … |
| Dashboard | always | Dashboard | erp | 2 | My Dashboard, Academic Calendar |
| Academics | always | Academics | erp | 7 | Lesson Plan, Online Books, Online Class, Homework, Homework Dashboard, Assessment … |
| Fees & Wallet | always | Fees & Wallet | erp | 4 | Fee Payment, Wallet Transactions, Imprest Wallet, Refunds |
| Communication | always | Communication | erp | 3 | Parent Communication, Announcements, Feedback |
| Activities | always | Activities | erp | 1 | Summer Camp |
| Tools | always | Project Management | recruitment | 3 | Project Management, Scrum Board, Scrum Projects |
| Tools | always | Bug Tracker | erp | 7 | Dashboard, Bug List, Kanban Board, Submit Bug, Test Scenarios, RCA Dashboard … |
| Tools | always | Feature Requests | erp | 4 | Dashboard, All Requests, Submit Request, Roadmap |
| Tools | always | Goal Tracker | erp | 3 | Goals, Activities, Hierarchy |
| Tools | always | Work Tracker | erp | 11 | Dashboard, Roles, Activities, Goals, Campaigns, Reports … |
| Tools | always | Chat Monitoring | erp | 1 | Chat Monitoring |
| Tools | always | Tech Training | erp | 4 | Django Training, React Training, Flutter Training, Training Dashboard |
| Tools | always | Leave (CRM) | crm | 2 | Leave Applications, Own Application Status |
| Tools | always | Testing | erp | 1 | Testing Figma |
| Tools | always | Creative | erp | 3 | Creative Cap, Campaign Studio, Marketing Ad Banners |
| Tools | always | ByteLearn | erp | 10 | Dashboard, Videos, Assignments, Leaderboard, Catalog, My Learning … |
| Tools | always | L&D Programs | erp | 8 | Command Centre, Programs, Score Entry, CXO Dashboard, Leaderboard, Reflection Reviews … |
| Tools | always | Lead Principal | erp | 4 | LP Dashboard, Batches, My LP Program, Evaluations |
| Tools | always | L&D Travel Request | erp | 3 | My Requests, My Approvals, Approval Configuration |
| Tools | always | L&D LMS | erp | 4 | LMS Home, Programs (Admin), My Batches (Trainer), My Training (Trainee) |

Total menu items: 772

## 4. Route volume
| App | Client routes (static) | Notes |
|---|---|---|
| ERP | 688 path→component pairs | many parameterised detail/receipt/form routes |
| HRMS | 82 top-level route entries plus nested children (about 250 lines) | each with a permission declaration |
| CRM | not captured (bundle not loaded) | menu shows about 90 items |

## 5. Scope note for the replacement
The original is a **group-scale school-chain operating system** (admissions marketing, HR, payroll, procurement, expansion/real estate, store/e-commerce, transport, L&D). A replacement **school ERP** should treat the core (org structure, users, students, academics, timetable, attendance, homework/diary, assessments and report cards, fees, communication, transport basics) as the product. HR/payroll, CRM, procurement, store, expansion and internal tools are separate, optional bounded contexts. See `01_PRODUCT_OVERVIEW/product_scope.md`.
