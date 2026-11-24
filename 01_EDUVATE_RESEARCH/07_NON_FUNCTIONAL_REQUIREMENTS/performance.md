# Performance Requirements

## Observations (OBSERVED/STATIC)
- ERP initial JavaScript: about 2.17 MB main chunk + 1.16 MB Ant Design vendor chunk + other vendor chunks (about 3.7 MB uncompressed before route chunks). HRMS main chunk 2.86 MB. Heavy first load; routes are lazy-loaded.
- Shell calls repeat on every screen: notifications count and list on 38 screens, analytics on 39, FCM registration, event-feedback and finance delegation on 24 ERP screens (OBSERVED, E016). The earlier figures of "10–25 API calls per page" and "duplicated calls" cannot be reproduced from retained evidence, because E016 keeps no per-page call counts (UNVERIFIABLE, LOW; audit ER-12).
- The shell shows a **network-quality indicator** ("Excellent (10.0 Mbps)"), suggesting users on variable mobile networks.
- Filter-first screens (Attendance, Homework, Timetable) avoid loading large data until the grade and section are selected. This is a good pattern.

## Requirements (PROPOSED)
| ID | Requirement | Target |
|---|---|---|
| PR-1 | Initial JS for the authenticated shell (gzip) | ≤ 350 KB; route chunks ≤ 150 KB each |
| PR-2 | Largest Contentful Paint on a 10 Mbps connection, mid-range laptop | ≤ 2.5 s (p75) |
| PR-3 | API latency for list endpoints (≤ 50 rows) | p95 ≤ 300 ms server time |
| PR-4 | Attendance save for a 45-student section | ≤ 1 s, single bulk request |
| PR-5 | Shell bootstrap | one `GET /me/bootstrap` returning user, permissions, navigation, context (branches, years), unread counts |
| PR-6 | Report exports > 5k rows | asynchronous job + notification + download link |
| PR-7 | Pagination | server-side, default 20–50, max 200 |
| PR-8 | Caching | ETag/`Cache-Control` on master data (grades, subjects, periods); invalidate on change |
| PR-9 | Images and documents | CDN with resizing; lazy-loaded thumbnails |
| PR-10 | Mobile data | responses gzip/brotli; avoid duplicate calls (dedupe in the client query cache) |
