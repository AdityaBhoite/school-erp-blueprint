# Open Questions

Each question has a **proposed default** so implementation is not blocked. Record the final decision as an ADR.

| # | Question | Why unknown | Proposed default |
|---|---|---|---|
| Q01 | Attendance granularity: daily or period-wise? | Only the filter screen was observed | Daily by default; period-wise as a tenant setting |
| Q02 | Attendance status values | Roster not loaded (no section selected) | Present, Absent, Late, Half-day, Leave (configurable) |
| Q03 | Attendance edit window and who can backdate | Not observable | Same day for teachers; 7 days for coordinators; audit all |
| Q04 | Late homework submission policy | Not observable | Allowed and flagged "Late" (tenant setting) |
| Q05 | Grading scheme(s) used (CBSE 9-point, percentages, custom) | Report-card data not accessible | Configurable grading systems; seed CBSE-style bands |
| Q06 | Report-card weighting rules across terms | Not observable | Template-defined weights per component |
| Q07 | Fee computation details (installments, rounding, fine formula, partial payments) | Finance not visible | See WF-09 BR-FEE-1..5 |
| Q08 | Payment gateway(s) | Not found in loaded code | Gateway abstraction; one Indian gateway adapter first |
| Q09 | Ticket statuses and SLA rules | Tickets denied | Open → In Progress → Awaiting Parent → Resolved → Closed; SLA per category |
| Q10 | Calendar review/approval roles | Only the "Reviews" button observed | Academic Coordinator reviews, Principal publishes |
| Q11 | Do teachers create announcements? | Inbox seen; create not tested | Yes, for their own classes (scoped), with coordinator approval optional |
| Q12 | Parent account model (one login per family vs per guardian) | No parent account | One user per guardian; children linked; sibling switcher |
| Q13 | Student login (own credentials or via parent only) | No student account | Own credentials for grade ≥ configurable threshold |
| Q14 | Data retention and deletion policies | Not observable | Configurable per data class; DPDP-compliant defaults |
| Q15 | Multi-board support within one branch | Board lists exist | Board per grade offering |
| Q16 | Language/i18n requirements | Language config exists | English first; i18n framework from day one |
| Q17 | Mobile app parity | Mobile app inferred only | PWA for v1; native later |
| Q18 | Which optional contexts (transport, online classes, HR) the first customer needs | Business decision | Decide before Phase 7 |
| Q19 | Product name and branding | Must be original | To be chosen by the owner |
| Q20 | Hosting region and compliance (DPDP) | Business decision | India region; DPDP controls |
| Q21 | HRMS user-level names | Not captured (separate namespace) | Not needed for MVP |
| Q22 | Exact semantics of "acad_session" vs "session_year" in every API | Both used | Model as BranchAcademicYear vs AcademicYear (entity dictionary) |
