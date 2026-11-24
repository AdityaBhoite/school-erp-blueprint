# M09 — Lesson Plans and Curriculum (ACMS)

**Routes:** `/erp/lesson-plan` (teacher, OBSERVED), ACMS routes `/erp/acms/*` (version mapping, YCP, chapters and key concepts, LP upload, e-book upload, QR codes, LP config, LP assignment dashboard, teacher allocation requests, grade-subject mappings, my lesson plans — several denied for the teacher). Screenshot `lesson-plan-filters.png`.

## Screen (OBSERVED)
- Toggle **Period View** / **Annual Plan View** (checkbox).
- Required filters: **\* Grade**, **\* Subject**; **Filter** button; empty state "Please apply filter to view data"; help link "Can't find a grade or subject?".
- Grade list: `GET /qbox/academic/lp-grade-subject-list/?acad_session` → `[{grade_id, grade__grade_name, grade__eduvate_grade_id}]`.

## STATIC endpoints
`lessonPlanRevamp`: grade dropdown, volume/module/chapter/topic dropdowns (central management service), `/academic/lp-view/` (periods), `/lesson_plan/lp-files/` (period resources), `/academic/lp-completion-view/` (completion), `/lesson_plan/lp-ycp-pd/` (overview files), ACMS equivalents (`/academic/acms-lp-volumes|chapters|topics|files|overview/`), `/acms/authored-lps-teacher/`, `/acms/feature-config/check/`. `lessonPlan`: subjects, volumes, sessions, central chapters, key concepts. Document editing via OnlyOffice (config `onlyOffice`, callback `/acms/onlyoffice/callback/`).

## Concepts
- **Volume** = curriculum edition/version (`list-volume`, `curriculum/volumes` with `is_current`); **Version mapping** maps branches to volumes.
- **YCP** (Yearly Curriculum Plan): chapter sequence across the year by week.
- **LP period**: per chapter/topic, a numbered period with objectives and resources (files).
- **Completion**: a teacher marks LP periods completed per section (feeds curriculum-completion dashboards).
- **Clickers disparity**: compares classroom-clicker usage to content (S).

Replacement priority: Should (basic library + completion), Could (central authoring, OnlyOffice, QR codes).

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/lesson-plan` **rendered** for a teacher (17 calls on load). Route-specific endpoints observed:
- `GET ERP /academic/lp-view` — the lesson-plan view (envelope `{message, result, status_code}`)
- `GET ERP /academic/lp-grade-subject-list` — grade/subject picker
- `GET ERP /academic/v1/ebook_ibook_count` — ebook/ibook counts
- **Lesson-plan content is served by the MGMT service** (`mgmt.letseduvate.com`), runtime-confirmed: `GET MGMT /lesson_plan/lp-ycp-pd`, `/lesson_plan/module-dropdown`, `/lesson_plan/topic-dropdown`, `/lesson_plan/chapter-dropdown`, `/lesson_plan/volume-dropdown`

Architecture note for the build: the curriculum/lesson-plan catalog lives behind a **separate service** from the ERP app — a boundary the replacement should preserve or deliberately merge. Status: RUNTIME_OBSERVED.
