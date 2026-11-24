# Component Inventory

Observed components (screens E016/E002) and static chunk names (E004x/E010x), mapped to reusable components for the replacement.

| Replacement component | Observed in original | Props / behaviour (PROPOSED) |
|---|---|---|
| `AppShell` | every page | rail items from permitted nav, top bar, breadcrumb slot, help FAB |
| `NavRail` + `MegaMenu` | rail + mega-menu config (E005) | categories → sections → items; keyboard navigable; search within the menu |
| `ContextSwitcher` | Branch + Academic Year selects | writes to URL/search params and the query-cache key |
| `GlobalSearch` | "CAND" type select + ID box | typed search providers (student, staff, guardian) |
| `NotificationBell` / `NotificationDrawer` | bell + count | counts (total, approvals), infinite list, mark read |
| `PageHeader` | Academic Calendar header card | title, icon, context chips, actions |
| `Tabs` (card) | Attendance, Homework, Timetable, Online Class, Announcements, Comms Inbox | URL-synced active tab |
| `FilterBar` | Attendance (Grade\*, Sections\*), Homework, Online Class, Assessment Test, Lesson Plan | required markers, Clear All, Apply/Search, "Show more filters" collapse |
| `DataTable` | Attendance roster, Online class list, Announcements, Recruiter table | server pagination, page size select, sticky header, empty state, horizontal scroll, column templates |
| `EmptyState` | "No data", "Period system has not been created", "Please apply filter to view data", "Nothing here yet" | icon + message + optional CTA/help |
| `StatusFilterChips` / `SegmentedRadio` | Comms Inbox (All/Unread/Action Pending/Completed), Assessment (Upcoming/Completed), mode (All/Online/Offline), Announcements categories | counts in labels |
| `MonthCalendar` | Academic Calendar | typed event chips, "+N more", day button a11y names, fullscreen |
| `WeekStrip` | Diary MON–SAT tiles | selected day, disabled holidays |
| `PeriodCard` | Diary | period code, times, class chip, subject, LP button, left accent |
| `TimetableGrid` | Timetable | periods × days, legend (Substitute, COMM, SPLIT, SEN), draft overlay |
| `ClassChip` / `Tag` | "Grade 10 Sec A", year/branch chips | colour variants |
| `CountdownStatus` | Online class status timer | live countdown to start |
| `ProfileSection` (definition grid) | HRMS profile | icon + label + value, 3 columns, responsive |
| `MaskedField` | Aadhaar/PAN/account with eye button | reveal with permission + audit |
| `InfoAlert` | HRMS profile banner | info/warning/success variants |
| `Drawer` (detail/history) | `TransferDetailsDrawer`, `ShiftHistoryDrawer`, `AnswerDetailDrawer`, `activityHistoryModal` chunks | right drawer with tabs, history timeline |
| `StepsForm` | `declarationStep`, onboarding chunks | multi-step forms with save-draft |
| `FileUpload` | homework upload, announcements, documents | drag-drop, type/size validation, progress |
| `PdfViewer` | pdf.js chunk | inline viewer for receipts/report cards/papers |
| `Chart` | Highcharts / Chart.js in dashboards | use ECharts/Recharts |
| `ApprovalTimeline` | approval requests (static) | maker/checker steps, comments |
| `HelpAssistant` | "Ask our Assistant" FAB | FAQ search; optional AI |
| `ModuleDemo` / `HowToUse` | Timetable side tab and button | contextual help video/tour |
