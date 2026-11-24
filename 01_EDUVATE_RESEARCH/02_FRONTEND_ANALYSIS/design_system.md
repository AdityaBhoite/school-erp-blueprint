# Design System (observed) and Replacement Guidance

Evidence: screenshots S001–S003 and tour screenshots (E016), ERP bundle colour literals, stylesheet resource list (E010). Visual values are approximate (read from screenshots) unless taken from code literals.

> The replacement must use **original styling and assets**. This document records the observed *patterns* so the new product can be equally usable. Do not copy the logo, brand colour or iconography verbatim.

## 1. Colour (observed)
| Token (observed role) | Value | Source | Class |
|---|---|---|---|
| Brand primary (buttons, active rail item, tabs, badges) | `#BB2139` (crimson) | brand config `primary_color`, 11 literals in ERP bundle | STATIC, HIGH |
| Primary dark / hover | `#AD1F2E`, `#B32318` | bundle literals | STATIC, MEDIUM |
| HRMS accent (profile headings, Edit Profile) | magenta-crimson about `#C2185B` | screenshot | OBSERVED, MEDIUM |
| Page background | `#F7F8FC` / `#EEF0F5` (very light grey-blue) | literals + screenshots | STATIC/OBSERVED, HIGH |
| Surface | `#FFFFFF` cards with 1 px light border and very soft shadow | screenshots | OBSERVED |
| Text primary | `#1F2937` / `#16181D` | literals | STATIC, MEDIUM |
| Text secondary | `#8c8c8c`, `#9CA3AF` | literals | STATIC, MEDIUM |
| Link / breadcrumb | slate-blue about `#4A5A8A` | screenshot | OBSERVED, LOW |
| Status colours | Ant Design defaults: success `#52c41a`, warning `#faad14`, error `#ff4d4f`, info `#1677ff` | literals | STATIC, HIGH |
| Calendar event categories | Holiday → light red/pink fill; Assessment → light blue fill with blue/green progress bar; Other Event → light grey; Competition → light green; Parent-Teacher Meeting → light yellow | screenshot S002 | OBSERVED, HIGH |
| Network badge | green signal bars + green text | screenshot | OBSERVED |

## 2. Typography
- ERP: neutral sans-serif (Inter/Roboto-like), 14 px body, 16–18 px section titles, 24–26 px page titles in the brand-dark or navy colour. OBSERVED, MEDIUM.
- HRMS: geometric sans (Poppins-like). OBSERVED, LOW.
- Icon fonts loaded by ERP: Linearicons, Font Awesome 4, IcoMoon (legacy), plus Ant Design icons and lucide-style inline SVGs in the unified rail. STATIC, HIGH.

## 3. Layout and spacing
- Left **icon rail** 80 px wide, icons 24 px, active item has a rounded (12 px) light-pink tile and a red notification dot.
- **Top bar** 70 px, white, contains search, branch and year selects (rounded 8 px, 40 px high), icon buttons and avatar (48 px circle).
- Content padding about 16–24 px; cards have about 8–12 px radius.
- **Breadcrumb** row above the page card.
- Tables: header row on a light grey background, centred column titles, "No data" empty-state illustration (Ant Design `Empty`).
- **Primary action buttons**: solid crimson, white text, 8 px radius, 40 px high, often full-width in their grid cell (e.g. "Search" in Attendance).
- **Secondary buttons**: white with light border and icon prefix ("+ Homework for another class", "Annual Lesson Plan", "Refresh").
- **Tabs**: card-style tabs with the active tab in crimson text ("Mark Attendance" / "View Attendance").
- **Chips/tags**: rounded pill tags for context (year `2026-27`, branch in green outline, "All Grades" grey) and for class labels (light-pink background, crimson text "Grade 10 Sec A").
- **Day selector**: row of square day tiles (weekday plus date); active tile solid crimson.
- **Period cards** (Diary): white card with a 4 px crimson left border, period code (P1), start/end time, class chip, subject title, and an "LP" (lesson plan) badge button.
- **Calendar month grid**: 7 columns, each event as a coloured block with title plus category caption, "+N more" overflow link, today highlighted in blue, Sundays and holidays in red.
- **Floating help button**: bottom-right crimson pill with robot icon, "NEW" badge and a close (×) control.

## 4. Component inventory (patterns to implement)
| Component | Where observed | Notes |
|---|---|---|
| AppShell (rail + top bar + breadcrumb) | all pages | rail categories come from permission-filtered navigation |
| MegaMenu | rail hover/click | section → sub-section → item; search inside the menu (`searchIndex`) |
| ContextSelectors (Branch, Academic Year) | top bar | changing them re-scopes all data |
| GlobalSearch with type selector | top bar | e.g. CAND (candidate), student, staff |
| NotificationBell with count + drawer | top bar | `/mongoapp/notifications/` pagination |
| PageHeaderCard | Calendar, Diary | title, context chips, actions on the right |
| FilterBar (Select Grade*, Select Sections*, Search) | Attendance, Homework, Timetable | required filters marked `*` |
| DataTable with empty state | Attendance | columns: Sl No., ERP Id, Name, Grade, Section, Attendance |
| DayStrip / WeekPicker | Diary | |
| PeriodCard | Diary | |
| MonthCalendar with typed events | Academic Calendar | filters by branch and grade, fullscreen toggle, "Reviews" action |
| Tabs (card) | Attendance | |
| Drawers / Modals | HRMS (TransferDetailsDrawer, ShiftHistoryDrawer, activityHistoryModal) | right-side drawers for detail and history |
| Steps / multi-step forms | HRMS onboarding (`declarationStep`) | |
| Profile sections (label/value grid with icons) | HRMS profile | 3-column definition list |
| Masked sensitive values with reveal (eye) button | HRMS bank and ID fields | must be permission-checked and audited |
| Info alert banner | HRMS profile | blue info alert with bold inline text |
| Help assistant (chat) | all | AI FAQ assistant |

## 5. Responsive behaviour
- Captured only at desktop widths (1920 px and 1600 px). Mobile behaviour: **UNKNOWN**. A separate mobile app is likely (FCM registration, `app_image_url` in banners, Play Store reviews admin page). INFERRED, MEDIUM.

## 6. Replacement design guidance (PROPOSED)
- Build a token-based theme (CSS variables): `--color-primary`, `--color-primary-hover`, `--bg-page`, `--bg-surface`, `--text-primary`, `--text-secondary`, `--radius-sm 6`, `--radius-md 10`, `--space-1..8 (4px grid)`.
- Choose an **original** primary colour per tenant (tenant-configurable branding, as the original supports `primary_color` per brand).
- Use an open-licensed icon set (e.g. Lucide) and an open font (e.g. Inter).
- Accessibility: WCAG 2.1 AA contrast, labelled icon buttons (the original's rail buttons do have accessible names), keyboard-navigable mega-menu, focus rings.
- Responsive breakpoints: test at 390 / 768 / 1440 px (DoD, NFR-03); 1024 px may be used as an extra layout token (audit ER-43). Collapse the rail into a bottom bar or drawer on mobile.
