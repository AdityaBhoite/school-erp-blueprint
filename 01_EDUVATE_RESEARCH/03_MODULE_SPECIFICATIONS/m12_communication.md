# M12 — Communication

## Announcements (`/erp/announcement`, OBSERVED). Screenshot `announcements-inbox.png` (descriptions redacted).
- Tabs: **INBOX (n)** | **DRAFTS** | **SENT**.
- Category chips: All, Circular, Exam, Event, General, TimeTable, **Show Other Categories**; date filter.
- Table header (brand-coloured bar): Type, Title, Description, Created at (relative: "1w ago"); grouped by **This Month** / **Earlier**; coloured left border per type (Circular dark, Exam red, Event green).
- APIs: `GET /qbox/announcement/announcement-category/` → `{data:[{id, category_name}]}`; `GET /qbox/announcement/v2/announcement/?acad_session&page&page_size` → paginated `[{id, title, content, category, category_name, created_at, erp_name, created_by, is_flash_event, flash_img[], event_link, is_draft}]`.
- STATIC write: create/update/publish-draft, members count, students, upload file.
- "Flash event" announcements show an image and link (home banner) (O fields).

## Communication Inbox (`/erp/communication-inbox`, OBSERVED). Screenshot `communication-inbox.png`.
Central-team → school communication with required actions (task-like circulars).
- Tabs: **Inbox** | **My Chats**; filters All (n) / Unread / Action Pending / Completed; search subject/message; Category, Grade, Priority, Sent from/to dates; pagination.
- `GET /qbox/academic-communication/meta/` → `{roles[{key, label, level, assign_to_levels[]}], user_levels[{level, name}], priorities[{key, label}], required_actions[{key, label}], reminder_offsets[], categories[{id, name}], branches[{branch_id, branch_name, zone_id, zone_name, boards[]…}]}`.
- `GET /qbox/academic-communication/communications/inbox/?bucket&page&page_size&session_year` → `{results[], count, page, page_size, summary:{all, new, action, overdue}, can_oversee}`.
- `GET /qbox/academic-communication/communications/chat/unread-count/` → `{count}`.
- Concepts: communications with **priority**, **required action** (acknowledge/respond/upload…), **reminders** at offsets, **overdue**, threaded chat, oversight for managers.

## Chat (`/erp/chat`, OBSERVED): embedded **Mattermost** (`chat.letseduvate.com/api/v4/...`). One call returned 401 (custom profile attributes).

## School Wall (`/erp/school-wall`, OBSERVED). Screenshot `school-wall-moderation.png`.
Social feed of student/school posts with **moderation**: tab Post Moderation; view toggle; filters; bulk action; status buttons Pending / Approved / Rejected / Deleted; Approve Comments; grade/section filters. APIs: `/school-feed/feed-draft/?acad_session&page&page_size&post_status`, `/school-feed/comments-draft/?comments_status&page&page_size`; profanity list (`/erp_user/profanity-txt/`, 404).

## Notifications & banners (OBSERVED, shell)
See M01. Notification item shape: `{id(string), title, message, module:{name…}, …}`; counts `{total_unread, approval_unread}`.

## Other (STATIC)
Communication (SMS/email) module: user lists, groups, message types, send messages, SMS credits, email/SMS logs. WhatsApp notifications config. Meeting scheduler (`/erp/meeting-scheduler`: `GET /qbox/acms/meetings/` returned **404**). Calendar hub (denied).

Workflow: `wf11_announcements.md`. ACs: COM-01..03.

## Runtime evidence (Phase 3 crawl, 2026-11-23)
`/erp/announcement`, `/erp/chat` and `/erp/ptm-feedback` **rendered** for a teacher.
- **Announcements:** `GET ERP /announcement/v2/announcement` (the feed, a v2 endpoint), `GET ERP /announcement/announcement-category`
- **Chat is a Mattermost deployment** (`chat.letseduvate.com`), runtime-confirmed by its API surface (29 endpoints on load): `GET CHAT /api/v4/users/me`, `/api/v4/users/me/teams`, `/api/v4/users/me/channels`, `/api/v4/teams`, `/api/v4/channels/{id}/stats`, `/api/v4/config/client`, `/api/v4/license/client`, `POST /api/v4/users/login`, `POST /api/v4/users/status/ids`, etc. Field names only (e.g. `email` captured as the `string(email)` descriptor, no value).

Build implications: (1) the in-app chat is a **third-party Mattermost**, not a bespoke service — the replacement decides whether to keep, wrap, or replace it; (2) Mattermost's on-load polling is heavy (29 calls) and is what tripped the server's 429 that stopped the crawl. Status: RUNTIME_OBSERVED. See `RESPONSE_SCHEMA_CATALOG.md` §5.
