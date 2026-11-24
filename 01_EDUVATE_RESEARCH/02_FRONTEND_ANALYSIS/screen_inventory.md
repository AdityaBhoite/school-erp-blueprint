# Screen Inventory

Source: read-only CDP tour (E016) with the teacher account, plus HRMS profile (E002). "Denied" = the app redirected to `/erp/user-profile`, so the screen exists (STATIC route) but this role cannot open it. Tabs/buttons are taken from the accessibility tree of the final page state.

| # | Requested route | Result | Tabs observed | Actions observed | Spec | Screenshot |
|---|---|---|---|---|---|---|
| 1 | `/erp/academic-communication` | DENIED → profile | — | — | — | — |
| 2 | `/erp/academic-improvement/daily-log` | DENIED → profile | — | — | — | — |
| 3 | `/erp/acms/assessments` | OK | upload Marks Upload, file-text Exam, message Discussion Requests, calendar Assessment Calendar | filter, edit Enter Marks, download Template, cloud-upload Upload, cloud-upload Upload, delete Delete | m10 | — |
| 4 | `/erp/acms/blueprints` | DENIED → profile | — | — | — | — |
| 5 | `/erp/acms/calendar` | OK | — | audit Reviews, Today, fullscreen, November 1, 2026, November 2, 2026, November 3, 2026 Space Camp Other Event | m04 | academic-calendar-month-view.png |
| 6 | `/erp/acms/dashboard` | DENIED → profile | — | — | — | — |
| 7 | `/erp/acms/my-lesson-plans` | DENIED → profile | — | — | — | — |
| 8 | `/erp/acms/performance-analytics` | DENIED → profile | — | — | — | — |
| 9 | `/erp/acms/questions` | DENIED → profile | — | — | — | — |
| 10 | `/erp/announcement` | OK | INBOX (30), DRAFTS, SENT | All, Circular, Exam, Event, General, TimeTable | m12 | announcements-inbox.png |
| 11 | `/erp/assessment` | DENIED → profile | — | — | — | — |
| 12 | `/erp/assessment-reports` | OK | — | Student Comparison, Individual Test Report, Gradewise Assessment Summary, Gradewise Assessment Completion, Consolidated Assessment Report, Report Card | m10 | — |
| 13 | `/erp/assessment/clicker` | DENIED → profile | — | — | — | — |
| 14 | `/erp/assessment/report-card` | OK | Report Card | arrow-left Go Back | m10 | — |
| 15 | `/erp/assessment/test` | OK | Test | All, Online, Offline, plus-circle Create Test | m10 | — |
| 16 | `/erp/attendance` | OK | Mark Attendance, View Attendance | search Search | m08 | attendance-mark-empty.png |
| 17 | `/erp/audio-analysis/my-insights` | DENIED → profile | — | — | — | — |
| 18 | `/erp/board-prep` | OK | — | All, Not Applicable | m19 | — |
| 19 | `/erp/bug-tracker/dashboard` | DENIED → profile | — | — | — | — |
| 20 | `/erp/bytelearn` | DENIED → profile | — | — | — | — |
| 21 | `/erp/bytelearn/lnd/my-learning` | DENIED → profile | — | — | — | — |
| 22 | `/erp/calendar` | OK | — | clock-circle 7 Days, clock-circle 15 Days, clock-circle 30 Days, clear Clear All, download Download, bar-chart Report | m04 | — |
| 23 | `/erp/calendar-hub` | DENIED → profile | — | — | — | — |
| 24 | `/erp/chat` | OK | — | — | m12 | — |
| 25 | `/erp/communication-inbox` | OK | inbox Inbox, message My Chats | — | m12 | communication-inbox.png |
| 26 | `/erp/dashboard` | DENIED → profile | — | — | m17 | — |
| 27 | `/erp/diary` | OK | — | plus Homework for another class, read Annual Lesson Plan, sync Refresh | m06 | diary-teacher-week.png |
| 28 | `/erp/event-manager` | DENIED → profile | — | — | — | — |
| 29 | `/erp/feature-request/dashboard` | DENIED → profile | — | — | — | — |
| 30 | `/erp/feedback` | DENIED → profile | — | — | — | — |
| 31 | `/erp/goal-tracker` | OK | aim Goals, calendar Activities, team Hierarchy | download Download Report, plus Create Goal | m19 | — |
| 32 | `/erp/homework` | OK | Homework, Upload, Worksheet | — | m07 | homework-filters.png |
| 33 | `/erp/homework-dashboard` | DENIED → profile | — | — | — | — |
| 34 | `/erp/house-system/directory` | DENIED → profile | — | — | — | — |
| 35 | `/erp/house-system/leaderboard` | DENIED → profile | — | — | — | — |
| 36 | `/erp/lesson-plan` | OK | — | filter Filter, question-circle Can't find a grade or sub | m09 | lesson-plan-filters.png |
| 37 | `/erp/meeting-scheduler` | OK | — | reload, info-circle How to use | m12 | — |
| 38 | `/erp/myschoolgate` | DENIED → profile | — | — | — | — |
| 39 | `/erp/observation` | DENIED → profile | — | — | — | — |
| 40 | `/erp/online-books` | OK | EBOOK | filter Filter | m11 | — |
| 41 | `/erp/online-class` | OK | Regular Online Classes, OCAP Zoom Classes, Today, Upcoming, Completed, Cancelled | Clear All, Get Classes, plus-circle Create Class, download Download, Host, Host | m11 | online-class-list.png |
| 42 | `/erp/school-clicker` | OK | — | — | m11 | — |
| 43 | `/erp/school-wall` | OK | Post Moderation | eye Change View - 4, filter Show Filters, Enable Bulk Action, reload Pending, check-circle Approved, close-circle Rejected | m12 | school-wall-moderation.png |
| 44 | `/erp/teacher-observation` | DENIED → profile | — | — | — | — |
| 45 | `/erp/test-module` | DENIED → profile | — | — | — | — |
| 46 | `/erp/test-result` | OK | My Results, My Tutor Tests | eye View Details | m10 | — |
| 47 | `/erp/ticket` | DENIED → profile | — | — | — | — |
| 48 | `/erp/ticket-dashboard` | DENIED → profile | — | — | — | — |
| 49 | `/erp/timetable` | OK | Class Timetable, Teacher Timetable, Teacher Audit | Module Demo | m05 | timetable-class-tab.png |
| 50 | `/erp/upload-marks` | OK | — | arrow-left Back, filter Filter | m10 | — |
| 51 | `/erp/user-profile` | OK | — | Previous, Next, Previous, Next, lock Change Password, mail Reset Email Password | m02 | — |
| 52 | `/erp/video-monitoring/my-observations` | DENIED → profile | — | — | — | — |
| 53 | `/erp/work-tracker/dashboard` | DENIED → profile | — | — | — | — |
| 54 | `/recruitment/asset/your-assets` | OK (HRMS) | — | — | m18 | — |
| 55 | `/recruitment/attendance` | OK (HRMS) | Attendance, Regularisation, Leave Apply, Comp Off Grant, Leave Cancel, Leave Balance | left Prev, Next right | m18 | — |
| 56 | `/recruitment/course/my-courses` | OK (HRMS) | book Training, play-circle Self Paced, calendar Training Schedule | All, Upcoming, Live, Completed | m18 | — |
| 57 | `/recruitment/document-center` | OK (HRMS) | — | file-text My Documents 1 item Offer lette, solution Appointment Letter Preview, sign, dollar Payslips 7 items Monthly salary pa, safety-certificate Company Policies 19 it, audit Form 16 TDS certificates (Form 16 / | m18 | — |
| 58 | `/recruitment/employee-grievance/my-tickets` | OK (HRMS) | — | Refresh, plus Raise Request, My Requests (3), Assigned to Me (0), message, eye | m18 | — |
| 59 | `/recruitment/growth-action-plan/my-plan` | OK (HRMS) | — | history Previous Growth Plans | m18 | — |
| 60 | `/recruitment/holiday-calendar` | OK (HRMS) | — | — | m18 | — |
| 61 | `/recruitment/leave-apply` | OK (HRMS) | Leave, Comp Off Grant, Leave Cancel, Arrears Input | plus Apply Leave, user Pending, history History, user Pending, history History, plus Apply Leave | m18 | — |
| 62 | `/recruitment/my-referral` | OK (HRMS) | — | — | m18 | — |
| 63 | `/recruitment/payroll/it-declaration` | OK (HRMS) | — | Edit, Edit, Edit, Edit, Edit, Edit | m18 | — |
| 64 | `/recruitment/payroll/my-payslip` | OK (HRMS) | — | download Download, eye Preview, mail Send Email, eye, eye, eye | m18 | — |
| 65 | `/recruitment/performance-management/my-performance` | DENIED → profile | — | — | m18 | — |
| 66 | `/recruitment/reimbursement/claim-history` | OK (HRMS) | Claim History, Team Outing, Marketing Team Outing, Staff Uniform, Local, Outstation | — | m18 | — |
| 67 | `/recruitment/resignation` | OK (HRMS) | — | My Request, History, plus Apply for Resignation | m18 | — |
| 68 | `/recruitment/salary-advance/request` | DENIED → profile | — | — | m18 | — |
| 69 | `/recruitment/transfers/apply-transfer` | OK (HRMS) | — | plus Apply Transfer, clock-circle Ongoing, history History, Filter | m18 | — |
| 70 | `/recruitment/travel/my-travel-request` | OK (HRMS) | — | In progress 0, Booked · upcoming 0, Past & closed 0, plus Create Request, Open travel request guide, All 0 | m18 | — |

Additional observed screen: `/recruitment/` HRMS Profile (E002; see m18). Screenshots exist only where no personal data remained after redaction; see `09_VISUAL_EVIDENCE/screen_reference_index.md`.