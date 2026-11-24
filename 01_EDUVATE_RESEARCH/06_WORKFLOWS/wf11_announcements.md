# WF-11 Announcements, notifications and banners

Evidence: STATIC (`createAnnouncement`/V2 endpoints: categories, members count, students, upload file, create, update, publish draft) + OBSERVED (`GET /home-banner/announcement/active/` and `/home-banner/promotional/active/` → `[{id, title, web_image_url, app_image_url, …}]`; `GET /mongoapp/notifications/counts/` → `{total_unread, approval_unread}`; `GET /mongoapp/notifications/?page&page_size` → `{results:[{id, title, message, module:{name…}, …}]}`). Confidence MEDIUM.

## Steps
1. Author selects a **category** (announcement categories), writes title/body, attaches files. (S)
2. Chooses an **audience**: branch(es), grade(s), section(s), roles (teachers/parents/students) or individual students; the system shows a **members count** before sending. (S)
3. Saves as **draft** or **publishes**; drafts can be published later (`publish-announcement`). (S)
4. On publish: in-app notification records are created per recipient, plus push (FCM) and optionally WhatsApp/SMS/email. (O for in-app/FCM presence; channels I)
5. **Banners**: active announcement and promotional banners are shown on the home page and apps (web and app image variants). (O)
6. Recipients read notifications; unread counters (total and approvals) are shown on the bell. (O)

Rules: only published announcements are visible; audience is resolved at publish time (snapshot) (PROPOSED); attachments are stored in object storage with signed URLs.
