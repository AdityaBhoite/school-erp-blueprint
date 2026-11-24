# WF-15 Online class scheduling and attendance

Evidence: OBSERVED (`GET /erp_user/students-online-class/?start_date&end_date&status&page&page_size` → `{total, page, page_size, data:[{id, title, description, sectionmapping__grade, sectionmapping__grade__grade_name, sectionmapping__section, sectionmapping__section__section_name, subject__subject_name, subject, start_time, end_time, is_delete, class_link, duration, host__name, host__erp_id, provider, status, zoom_join_url, zoom_start_url, zoom_meeting_id, recording_status, recording_play_url, recording_password, recording_bucket_status, recording_bucket_url}]}` (full key list, audit ER-38; note the host-only `zoom_start_url` and `recording_password` in a list response, SEC-10)) + STATIC (online class service on `api-classes`: create, recurring, cancel, attendee list, mark attendance, Google Meet OAuth/config, Zoom redirect, tutor availability, resources, save recording to bucket). Confidence MEDIUM-HIGH.

1. Teacher creates a class: title, description, grade/section(s), subject, start/end time, duration, provider (Zoom or Google Meet), optionally recurring. Tutor availability is checked. (O/S)
2. The meeting is created via the provider API; the join/start links are stored. (S)
3. Students see today's classes; blocked students cannot join (access-blocker check). (S)
4. Host starts the class (start URL); attendance is captured from the attendee list or marked manually; recordings can be saved to storage. (S)
5. Reports: online class report and date-wise Excel. (S)
6. Classes can be cancelled (soft delete `is_delete`). (O/S)

Statuses: `status` field (values UNKNOWN; PROPOSED: scheduled, live, completed, cancelled).
