# WF-13 Staff leave application and approval (HRMS)

Evidence: STATIC (HRMS routes `/leave-apply`, `/leave-approval`, `/leave-configuration`, `/employee-leave`, `/regularization`, `/attendance-muster`, `/holiday-calendar`, `/shift-management`, `/rest-day-roster`, `/absence-notifications`; route permissions show leave apply/approval visible to almost all HRMS levels) + OBSERVED (profile "Leaves" button; shift `08:15-15:00` field; runtime shapes in `api_contracts.md#hrms` if captured). Confidence MEDIUM.

1. Leave types and policies configured (Leave Configuration): entitlement per year, accrual, carry-forward, encashment (Leave Encashment report exists). (S)
2. Employee applies: type, from/to dates, half-day, reason, attachment; the balance is shown. (S)
3. Reporting manager approves or rejects (Employee Leaves Approval). (S)
4. Approved leave reflects in the attendance muster and payroll (arrears/LOP). (S)
5. Attendance regularisation requests for missed punches go through the same approval path. (S)
6. Teacher absence feeds timetable substitution (WF-03). (I)
