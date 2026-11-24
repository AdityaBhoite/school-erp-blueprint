# M15 — Finance

**Access:** Finance is not visible to the observed teacher; only a shell call to the finance service was observed (`GET orchids.finance…/qbox/apiV1/expense-delegation-context/?erp_id` → `{is_delegate, delegators[]}`). STATIC evidence only.

## Menu structure (11 sub-sections, about 110 items)
| Sub-section | Items (abridged) |
|---|---|
| Fee Management | Fee Term, Fee Plan, Misc Fee, Cheque Payment, Fine Config, Fine Request, Reconciliation, Upload Collection, Student/Transport Fee Plan Change, Fee/Last-date/Receipt/Fee-popup Settings, Banks & Fee Accounts |
| Kiosk & Cash Deposit | Kiosk Dashboard, Cash Deposit Machine, Cheque Verification, Kiosk Cash Deposit |
| Fee Collection & Follow-up | Calling vs Due, Follow Up, Fee Reminder, Assigned Follow-ups, Fee Follow Up Dashboard |
| Concession & Approvals | Master Concession, Concession Upload, Approve Concession, Sibling concession approvals, Approve SEN Opt Out, Approve Pay Request, Wallet, Wallet Refund(s), Approve (Curriculum/Transport) Fee Cancel, Wallet Transactions, Student Fee Group, Student Fee Payment, Offline Receipt Request |
| Imprest & Store Wallet | Imprest Wallet (+report, pay/refund requests), Store Wallet (+pay/refund requests) |
| Expense Management | Expense Settings, Account Head-Subhead, TDS Config/Report, Cash In Hand, Party List/Change Request, Budget List, On-Behalf Payee/Payment, Expense Entry/Booking, Approve Expense, Account Payable, Batch Payment, Employee Advance (+dashboard) |
| Reimbursement | Team, Config, Reimbursement List, SAP Config |
| Branch P&L | Dashboard, Compare, Finance/Fee Collection/Late Fine/Books & Uniform/Net Paying dashboards |
| Reports | Cheque Bounce, Fee Concession, Wallet, Transaction, Deleted Fee Log, Tally, Curriculum Fee, Historic (fees/concession/store/tally), SSPL, Delivery (+payment), Total Paid Due, Reconciliation Dashboard |
| Rent Management | Dashboard, Agreements, Monthly Dues, Payments, TDS Tracker, Reports |
| JODO / Avanse | Education-loan/EMI partner reports and config |

Parent/student nav: Fees & Wallet → Fee Payment, Wallet Transactions, Imprest Wallet, Refunds.

## Notable concepts
- Multiple **wallet** types (academic, store, transport, imprest) with pay requests and refunds requiring approval.
- **Imprest events**: paid school events with registration, price, refundability, participation (OBSERVED in `/academic/imprest-event/`).
- **Fee popup / defaulters banner** shown to parents (`/apiV1/fee-defaulters-banner/`, S).
- **Expense delegation** (staff may act on behalf of others) — OBSERVED context call.
- **Sibling concession** with Aadhaar matching (S).

Workflows: `wf09_fees_collection.md`, `wf10_concession_refund_wallet.md`. ACs: FEE-*, PAY-*.
Replacement scope: fee core (terms, plans, dues, payments, receipts, concessions, fines, refunds, basic wallet, reconciliation export). Expense management, rent, P&L, loan partners = optional.
