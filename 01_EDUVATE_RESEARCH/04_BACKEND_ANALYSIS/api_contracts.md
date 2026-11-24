# API Contracts (observed shapes)

Each entry gives the **value-free response shape** observed at runtime (E016). Types: `integer`, `number`, `boolean`, `null`, `string` with format hints `string(date|datetime|time|url|email|numeric|uuid|empty)`; `a|b` = union seen across items; `["empty"]` = empty array observed; `{"<dynamic-key>": …}` = map keyed by ids/dates. Request bodies: four request shapes were observed, all infrastructure (token refresh, FCM token registration, two analytics calls). No domain write body was observed (read-only tour), so write contracts are in module specs as STATIC/PROPOSED (audit ER-04).

Authentication (conceptual): Bearer access token (JWT) in `Authorization`; brand header `X-DTS-BRAND` on shared services; no header values recorded.

## Response envelopes observed

| Variant | Shape | Seen on |
|---|---|---|
| A (most ERP/HRMS) | `{"status_code": integer, "message": string, "result": <payload>}` | erp_user, academic, timetable, announcement categories, payroll… |
| A2 | `{"status_code", "status": string, "message", "data": <payload>}` | `/erp_user/grade/`, `/announcement/announcement-category/` (no `status`) |
| A3 | `{"status_code", "status": string, "message", "result": <payload>}` | `/test-module/my-results/` |
| A4 | `{"status": string(numeric), "message", "data": string, "force_update": boolean}` | `POST /erp_user/access-token/` (refresh, HTTP 200) |
| B (DRF raw pagination) | `{"count", "next", "previous", "results": [...]}` (+ `limit, current_page, total_pages`) | `/school-feed/my-diary-given/`, `/announcement/v2/announcement/`, `/acms/assessments/`, `/school-clicker/clickers/` |
| C (bare object/array) | resource or list without envelope | `/acms/cities/`, `/acms/calendar-events/`, finance delegation, grievance module |
| D | `{"success": boolean, "message", "data"}` | `/payroll/employee-payslip/` |
| Error (observed) | `{"status": integer, "data": string}` (HTTP 500); Mattermost `{id, message, detailed_error, request_id, status_code}` (401) | |

**Replacement:** use one envelope convention (see `12_CLAUDE_CODE_HANDOFF/ARCHITECTURE_BLUEPRINT.md` §4).

## Academics

### `GET /qbox/academic/ebook-dropdown/` (ERP)
- Query params: `acad_session`, `book_type` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"grade_id":"integer","grade__grade_name":"string","grade__eduvate_grade_id":"integer"}],"is_acms":"boolean"}
```

### `GET /qbox/academic/event-coscholastic-subjects/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","subject_name":"string"}]}
```

### `GET /qbox/academic/imprest-event/` (ERP)
- Query params: `acad_session`, `end_date`, `page`, `start_date` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":[{"id":"integer","title":"string","highlight":"null","description":"string","event_date":"string(date)","reg_start":"null","reg_end":"null","is_subscription_need":"boolean","remarks":"null","event_price":"integer","refundable":"boolean","acad_session":["integer"],"sec_map":["empty"],"grades":[{"id":"integer","grade_name":"string"}],"subjects":["empty"],"attachments":["empty"],"policy":{},"approval_status":"integer","students_count":"integer","participation_count":"integer","policy_dates":{},"subscription":"string(empty)","branch_name":"string","event_type":"string","scores_published":"boolean","my_score":"null","has_media":"boolean","is_managed":"boolean","change_status":"string","proposed_event_date":"null","proposed_event_price":"null","proposed_event_name":"null","proposed_description":"null","proposed_attachments":"null","change_requested_at":"null"}],"counts":{"total":"integer","live":"integer","rejected":"integer","pending":"integer","cancelled":"integer","approved":"integer"}}}
```

### `GET /qbox/academic/lp-grade-subject-list/` (ERP)
- Query params: `acad_session` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":[{"grade_id":"integer","grade__grade_name":"string","grade__eduvate_grade_id":"integer"}],"status_code":"integer","is_acms":"boolean"}
```

### `GET /qbox/academic/v1/ebook_school_wise_filter/` (ERP)
- Query params: `acad_session_id`, `academic_year`, `book_type`, `branch`, `domain_name`, `page_number`, `page_size`, `session_year` · Status: 404 · Evidence: E016 (OBSERVED)
- Response shape: not captured (non-JSON, empty or error)

## Academics/ACMS <a id="calendar"></a>

### `GET /qbox/acms/assessments/` (ERP)
- Query params: `branch_id`, `date_filter`, `page`, `page_size`, `session_year`, `sort_dir` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"count":"integer","next":"string(url)","previous":"null","results":[{"id":"integer","code":"string","name":"string","test_type":"string","test_type_display":"string","section_access":"string","grade":"integer","grade_name":"string","subject":"integer","subject_name":"string","test_date":"string(date)","test_time":"string(time)","scheduled_via_calendar":"boolean","calendar_event_id":"integer","effective_test_date":"string(date)","effective_test_time":"null","total_marks":"string(numeric)","duration_minutes":"integer","status":"string","status_display":"string","practice_paper_count":"integer","sample_paper_count":"integer","has_final_paper":"boolean","final_paper_locked":"boolean","has_blueprint":"boolean","blueprint_status":"string","blueprint_status_display":"string","is_blueprint_approved":"boolean","branch_assignments":[{"id":"integer","assessment":"integer","branch":"integer","branch_name":"string","acad_session":"integer","session_year":"integer","session_year_name":"string","assigned_by":"integer|null","assigned_by_name":"string","assigned_at":"string(datetime)","marks_published":"boolean","marks_published_at":"null","marks_published_by_name":"null"}],"calendar_event_detail":{"id":"integer","title":"string","event_date":"string(date)","event_time":"null","city_names":["string"],"branch_names":["string"],"is_city_wide":"boolean"},"result_date":"string(date)","test_id":"null|string","term":"null|string","paper_set":"null|string","paper_name":"null","school_type":"string","assessment_mode":"string","is_manual":"boolean","online_test":"null","marks_published":"boolean","marks_published_at":"null","parent_assessment_id":"null","parent_assessment_code":"null","parent_assessment_name":"null","parent_assessment_test_date":"null","parent_assessment_test_time":"null","is_rescheduled":"boolean","rescheduled_children":["empty|mixed"],"assigned_to":"integer|null","assigned_to_name":"null|string","assignment_deadline":"null","can_edit":"boolean","practice_paper_deadline":"null","sample_paper_deadline":"null","final_paper_deadline":"null|string(date)","send_to_qc_deadline":"null|string(date)","qc_to_planner_deadline":"null|string(date)","practice_papers_overdue_count":"integer","sample_papers_overdue_count":"integer","final_paper_overdue":"boolean","created_by_name":"string","created_at":"string(datetime)"}]}
```

### `GET /qbox/acms/assessments/marks_upload_dashboard/` (ERP)
- Query params: `branch_id`, `page`, `page_size`, `paginate_by`, `session_year`, `tab` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"count":"integer","page":"integer","page_size":"integer","paginate_by":"string","total_rows":"integer","results":[{"id":"integer","name":"string","code":"string","grade_id":"integer","grade_name":"string","subject_id":"integer","subject_name":"string","test_date":"string(date)","result_date":"string(date)","is_overdue":"boolean","is_urgent":"boolean","days_remaining":"integer","marks_published":"boolean","assessment_mode":"string","teacher_pdf_url":"string(url)","branch_assignment_id":"integer","branch_id":"integer","branch_name":"string","branch_marks_published":"boolean","acad_session_id":"integer","online_pending_sync":"integer","online_pending_grading":"integer","section_mapping_id":"integer","section_name":"string","total_attempts":"integer","total_students":"integer","absent_with_marks":"integer","absent_without_marks":"integer","has_marks":"boolean","omr_uploaded":"integer","omr_scanned":"integer","omr_evaluated":"integer","omr_errors":"integer"}]}
```

### `GET /qbox/acms/assessments/my-access/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"scope":"string","user_level":"integer","grades":[{"id":"integer","grade_name":"string"}],"subjects":[{"id":"integer","subject_name":"string"}]}
```

### `GET /qbox/acms/calendar-events/` (ERP)
- Query params: `branch`, `end_date`, `session_year`, `start_date` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
[{"id":"integer","title":"string","description":"string(empty)","event_type":"string","event_type_display":"string","event_date":"string(date)","event_time":"null","end_date":"null","city_names":["empty|string"],"city_ids":["empty|integer"],"session_year":"integer","session_year_name":"string","grade_names":["empty|string"],"grade_ids":["empty|integer"],"subject_names":["empty|string"],"subject_ids_list":["empty|integer"],"branches_detail":["mixed"],"resolved_branches":["mixed"],"branch_exclusions":["empty|mixed"],"test_type":"null|string","test_type_display":"null|string","assessment":"null","assessment_code":"null","assessment_detail":"null","recurrence_pattern":"string","is_recurring_parent":"boolean","notes":"string(empty)","color":"string","is_published":"boolean","is_auto_holiday":"boolean","created_at":"string(datetime)","legacy_event_id":"null","legacy_event_detail":"null","is_cancelled":"boolean"}]
```

### `GET /qbox/acms/calendar-events/calendar_status/` (ERP)
- Query params: `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"session_year_id":"string(numeric)","is_published":"boolean","is_generated":"boolean","draft_events_count":"integer","total_events_count":"integer"}
```

### `GET /qbox/acms/calendar-events/holiday_range/` (ERP)
- Query params: `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"has_holidays":"boolean","start_date":"string(date)","end_date":"string(date)","count":"integer","generated_at":"string(datetime)"}
```

### `GET /qbox/acms/cities/` (ERP)
- Query params: `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
[{"id":"integer","city_name":"string","description":"string(empty)","is_active":"boolean","branch_count":"integer","created_at":"string(datetime)"}]
```

### `GET /qbox/acms/curriculum/grades/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
[{"id":"integer","grade_name":"string|string(empty)"}]
```

### `GET /qbox/acms/curriculum/volumes/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","result":[{"id":"integer","name":"string","is_current":"boolean"}]}
```

### `GET /qbox/acms/meetings/` (ERP)
- Query params: `page`, `page_size` · Status: 404 · Evidence: E016 (OBSERVED)
- Response shape: not captured (non-JSON, empty or error)

### `GET /qbox/acms/team-roles/my_roles/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
["empty"]
```

## Announcements <a id="announcements"></a>

### `GET /qbox/announcement/announcement-category/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","data":[{"id":"integer","category_name":"string"}]}
```

### `GET /qbox/announcement/v2/announcement/` (ERP)
- Query params: `acad_session`, `page`, `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"next":"string(url)","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":[{"id":"integer","title":"string","content":"string","category":"integer","category_name":"string","created_at":"string(datetime)","erp_name":"string","created_by":"integer","is_flash_event":"boolean","flash_img":["empty"],"event_link":"null","is_draft":"boolean"}]}
```

## Assessment <a id="assessment"></a>

### `GET /qbox/assessment/exam-type-list/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","exam_name":"string","is_delete":"boolean","created_at":"string(datetime)","updated_at":"null|string(datetime)","created_by":"null","updated_by":"null"}]}
```

### `GET /qbox/assessment/get-start-quiz-user-levels-access/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["integer"]}
```

### `GET /qbox/assessment/get-student-grades/` (ERP)
- Query params: none · Status: 500 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status":"integer","data":"string"}
```

### `GET /qbox/assessment/report-card-question-category-mapping/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","central_category_id":"integer","central_category_name":"string","erp_category_id":"integer","is_delete":"boolean","is_draft":"boolean","is_review":"boolean","created_at":"string(datetime)","is_subject_dependent":"boolean","erp_category_name":"string"}]}
```

## Board prep

### `GET /qbox/board-prep/central/rollup/` (ERP)
- Query params: `branch_id`, `grade`, `include_no_marks` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"grade":"string","kpis":{"total_students":"integer","on_track":"integer","behind_total":"integer","behind_mild":"integer","behind_concerning":"integer","behind_critical":"integer","ahead":"integer","no_marks":"integer","no_goal":"integer"},"students":[{"student_session_id":"integer","student_name":"string","erp_id":"string","branch_id":"integer","branch_name":"string","section_name":"string","cohort_id":"integer","bucket":"string","bucket_lower":"integer","bucket_upper":"integer","rolling_avg_pct":"integer|number","gap":"integer|number","status":"string","status_tier":"null|string","slots":[{"slot":"string","subject_id":"integer|null","subject_name":"null|string","avg_pct":"integer|null|number","ct_avg":"integer|null|number","pa_avg":"integer|null|number","absent":"boolean"}],"subjects":"null"}],"branches":[{"branch_id":"integer","branch_name":"string","branch_code":"string(numeric)"}]}
```

### `GET /qbox/board-prep/cohorts/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
[{"id":"integer","branch":"integer","branch_name":"string","branch_code":"string(numeric)","grade":"integer","grade_name":"string","session_year":"integer","session_year_label":"string","board":"string","is_active":"boolean","is_bucket_locked":"boolean","bucket_locked_at":"null","created_at":"string(datetime)","updated_at":"string(datetime)"}]
```

## Central admin

### `GET /qbox/central-admin/b2b-report-config/` (MGMT)
- Query params: `user_level` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"access":"boolean"}}
```

## Chat (Mattermost)

### `GET /api/v4/config/client` (CHAT)
- Query params: `format` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"AboutLink":"string(url)","AllowDownloadLogs":"string","AndroidAppDownloadLink":"string(url)","AndroidLatestVersion":"string(empty)","AndroidMinVersion":"string(empty)","AppDownloadLink":"string(url)","AppsPluginEnabled":"string","AsymmetricSigningPublicKey":"string","BuildDate":"string(empty)","BuildEnterpriseReady":"string(empty)","BuildHash":"string(empty)","BuildHashEnterprise":"string(empty)","BuildNumber":"string(empty)","CWSURL":"string(empty)","CustomBrandText":"string(empty)","CustomDescriptionText":"string(empty)","DefaultClientLocale":"string","DiagnosticId":"string","DiagnosticsEnabled":"string","EmailLoginButtonBorderColor":"string","EmailLoginButtonColor":"string","EmailLoginButtonTextColor":"string","EnableAskCommunityLink":"string","EnableBotAccountCreation":"string","EnableClientMetrics":"string","EnableComplianceExport":"string","EnableCustomBrand":"string","EnableCustomEmoji":"string","EnableDesktopLandingPage":"string","EnableDiagnostics":"string","EnableFile":"string","EnableGuestAccounts":"string","EnableJoinLeaveMessageByDefault":"string","EnableLdap":"string","EnableMultifactorAuthentication":"string","EnableOpenServer":"string","EnableSaml":"string","EnableSignInWithEmail":"string","EnableSignInWithUsername":"string","EnableSignUpWithEmail":"string","EnableSignUpWithGitLab":"string","EnableSignUpWithGoogle":"string","EnableSignUpWithOffice365":"string","EnableSignUpWithOpenId":"string","EnableUserCreation":"string","EnableUserStatuses":"string","EnforceMultifactorAuthentication":"string","FeatureFlagAppsEnabled":"string","FeatureFlagAttributeBasedAccessControl":"string","FeatureFlagChannelBookmarks":"string","FeatureFlagCloudAnnualRenewals":"string","FeatureFlagCloudDedicatedExportUI":"string","FeatureFlagCloudIPFiltering":"string","FeatureFlagConsumePostHook":"string","FeatureFlagCustomProfileAttributes":"string","FeatureFlagDeprecateCloudFree":"string","FeatureFlagEnableExportDirectDownload":"string","FeatureFlagEnableRemoteClusterService":"string","FeatureFlagEnableSharedChannelsDMs":"string","FeatureFlagEnableSharedChannelsMemberSync":"string","FeatureFlagEnableSharedChannelsPlugins":"string","FeatureFlagEnableSyncAllUsersForRemoteCluster":"string","FeatureFlagExperimentalAuditSettingsSystemConsoleUI":"string","FeatureFlagMoveThreadsEnabled":"string","FeatureFlagNormalizeLdapDNs":"string","FeatureFlagNotificationMonitoring":"string","FeatureFlagOnboardingTourTips":"string","FeatureFlagPermalinkPreviews":"string","FeatureFlagSt…
```

### `GET /api/v4/custom_profile_attributes/fields` (CHAT)
- Query params: none · Status: 401 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"id":"string","message":"string","detailed_error":"string(empty)","request_id":"string","status_code":"integer"}
```

### `GET /api/v4/license/client` (CHAT)
- Query params: `format` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"IsLicensed":"string"}
```

### `GET /static/files/7654b55b2f3442e91404.css` (CHAT)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape: not captured (non-JSON, empty or error)

## Clicker

### `GET /qbox/school-clicker/clickers/` (ERP)
- Query params: `acad_session`, `page`, `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"count":"integer","next":"null","previous":"null","results":["empty"]}
```

### `GET /qbox/school-clicker/config/check/` (ERP)
- Query params: `acad_session` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","result":{"enabled":"boolean","branch_id":"integer"}}
```

## Comms inbox <a id="comms"></a>

### `GET /qbox/academic-communication/communications/chat/unread-count/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":{"count":"integer"},"status_code":"integer"}
```

### `GET /qbox/academic-communication/communications/inbox/` (ERP)
- Query params: `bucket`, `page`, `page_size`, `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":{"results":["empty"],"count":"integer","page":"integer","page_size":"integer","summary":{"all":"integer","new":"integer","action":"integer","overdue":"integer"},"can_oversee":"boolean"},"status_code":"integer"}
```

### `GET /qbox/academic-communication/meta/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":{"roles":[{"key":"string(numeric)","label":"string","level":"integer","assign_to_levels":["empty|integer"]}],"user_levels":[{"level":"integer","name":"string"}],"priorities":[{"key":"string","label":"string"}],"required_actions":[{"key":"string","label":"string"}],"reminder_offsets":["string"],"categories":[{"id":"integer","name":"string"}],"branches":[{"branch_id":"integer","branch_name":"string","zone_id":"integer","zone_name":"string","boards":["empty|string"],"branch_type":"string"}],"zones":[{"zone_id":"integer","zone_name":"string"}],"branch_types":[{"value":"string","label":"string"}],"acms_cities":[{"city_id":"integer","city_name":"string","branch_ids":["integer"]}],"completion_modes":[{"key":"string","label":"string"}],"grades":[{"grade_id":"integer","grade_name":"string"}],"is_super_admin":"boolean","is_approver":"boolean","is_second_level_approver":"boolean","is_central":"boolean","has_admin_access":"boolean","crm_roles":["empty"],"hrms_roles":["empty"],"branch_bases":[{"key":"string","label":"string"}],"is_recipient":"boolean","can_create":"boolean","creatable_category_ids":["empty"],"can_manage_chats":"boolean","mandatory_cc":{"overall":["empty"],"by_category":{"<dynamic-key>":["integer"]},"people":{"<dynamic-key>":{"name":"string","erp_id":"string"}}}},"status_code":"integer"}
```

## Curriculum (central)

### `GET /qbox/lesson_plan/list-volume/` (MGMT)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"results":[{"id":"integer","volume_name":"string","is_delete":"boolean","created_by":"integer|null"}]}}
```

## Diary/Feed

### `GET /qbox/school-feed/comments-draft/` (ERP)
- Query params: `comments_status`, `page`, `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":["empty"]},"status_code":"integer"}
```

### `GET /qbox/school-feed/feed-draft/` (ERP)
- Query params: `acad_session`, `page`, `page_size`, `post_status` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":["empty"]},"status_code":"integer"}
```

### `GET /qbox/school-feed/my-diary-given/` (ERP)
- Query params: `acad_session`, `end_date`, `page_num`, `page_size`, `start_date` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"next":"string(url)","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":[{"id":"integer","title":"null|string|string(empty)","classwork":"string","homework":"string","has_homework":"boolean","media_files":["empty"],"is_online":"boolean","is_substitute":"boolean","last_submission_dt":"string(datetime)","created_at":"string(datetime)","sections":["string"],"subjects":["string"]}]}
```

## Finance

### `GET /qbox/apiV1/v2/reimbursement-form/` (FIN)
- Query params: `erp_id`, `include_withdrawn`, `is_draft`, `is_local_conveyance`, `page`, `page_size`, `request_kind` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"total":"integer","page":"integer","page_size":"integer","data":["empty"]}}
```

## Goal tracker

### `GET /qbox/goal-tracker/goals/` (ERP)
- Query params: `page_size`, `period_id` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"total":"integer","page":"integer","page_size":"integer","data":["empty"]}}
```

### `GET /qbox/goal-tracker/hierarchy/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"me":{"id":"integer","user_id":"integer","auth_user_id":"integer","name":"string","erp_id":"string","designation":"string","department":"string","is_head":"boolean","report_to":{"id":"integer","name":"string","erp_id":"string"}},"reportees":["empty"],"tree":{"id":"integer","user_id":"integer","auth_user_id":"integer","name":"string","erp_id":"string","designation":"string","department":"string","children":["empty"]},"can_self_complete":"boolean"}}
```

### `GET /qbox/goal-tracker/periods/` (ERP)
- Query params: `period_type` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","year":"integer","period_type":"string","period_number":"integer","start_date":"string(date)","end_date":"string(date)","label":"string","is_active":"boolean"}]}
```

## HRMS (lead-mgmt app)

### `GET /qbox/lead-mgmt/employee-refferal-history/` (HRMS)
- Query params: `page`, `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":["empty"]}}
```

### `GET /qbox/lead-mgmt/internal-transfer-request/` (HRMS)
- Query params: `page`, `page_size`, `view_type` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

## HRMS LMS

### `GET /qbox/lms/my-courses/` (HRMS)
- Query params: `course_type`, `is_active`, `page`, `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":["empty"]}}
```

### `GET /qbox/lms/teacher-cpd-hours/` (HRMS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"user_id":"integer","user_name":"string","erp_id":"string","total_duration":"integer","completed_courses_count":"integer","certificate_count":"integer","courses":["empty"]}}
```

## HRMS assets

### `GET /qbox/asset/asset-items/` (HRMS)
- Query params: `employee_id` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"results":[{"id":"string(uuid)","asset_code":"string","make":"string","model":"string","serial_number":"string","type":"integer","issue_type":"string","other":"null","procurement_date":"string(date)","asset_cost":"string(numeric)","warranty_expiry_date":"null","image":"string(empty)","description":"null","asset_config":"null","branch_id":"integer","branch_name":"string","status":"string","ownership":"null","condition":"null","remarks":"null","created_by":"integer","updated_by":"null","created_at":"string(datetime)","updated_at":"string(datetime)","active_assignment_id":"integer","assignment_type":"string","employee_id":"integer","employee_name":"string","employee_erp_id":"string","assigned_to_display":"string","location_name":"null"}],"status_counts":{"available":"integer","assigned":"integer","maintenance":"integer","lost":"integer","disposed":"integer","damaged":"integer","permanently_deactivated":"integer","total":"integer"}}}
```

### `GET /qbox/asset/asset-requests/` (HRMS)
- Query params: `employee_id` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

### `GET /qbox/asset/asset-types/` (HRMS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","name":"string","asset_type_code_prefix":"string","asset_counter":"integer","created_at":"string(datetime)","updated_at":"string(datetime)"}]}
```

### `GET /qbox/asset/subscription-assignments/` (HRMS)
- Query params: `employee_id`, `status` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

## HRMS central DB

### `GET /qbox/central-db/branch-view/` (HRMS)
- Query params: `is_active` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","branch_name":"string","is_delete":"boolean","zone":{"id":"integer","zone_name":"string","is_delete":"boolean"},"city_fk":{"id":"integer","city_name":"string","is_delete":"boolean"},"school_type":"string(numeric)","school_type_name":"string","address":"string","latitude":"integer|number","longitude":"integer|number","brand":{"id":"integer","code":"string","name":"string","short_name":"string","erp_id_suffix":"null|string"},"branch_code":"string(numeric)"}]}
```

## HRMS documents

### `GET /qbox/document-center/summary/` (HRMS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"viewing":{"is_self":"boolean","erp_id":"string","employee_name":"string","can_view_others":"boolean"},"documents":"integer","payslips":"integer","company_policies":"integer"}}
```

## HRMS exit

### `GET /qbox/exit-mgmt/resignation-request/` (HRMS)
- Query params: `view_type` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

## HRMS grievances

### `GET /qbox/grievance-module/employee-detail/` (HRMS)
- Query params: `erp_id` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"name":"string","erp_id":"string","contact":"string","gender":"string","email":"string(email)","branch_id":"integer","branch_name":"string","department":"integer","department_name":"string","designation":"integer","designation_name":"string","attendance_branch_id":"integer","attendance_branch_name":"string"}
```

### `GET /qbox/grievance-module/employee-team-member-check/` (HRMS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"is_team_member":"boolean","is_sme":"boolean","is_super_poc":"boolean","super_poc_groups":["empty"],"handler_groups":["empty"]}
```

### `GET /qbox/grievance-module/employee-ticket-category-group/` (HRMS)
- Query params: `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"count":"integer","current_page":"integer","next":"null","previous":"null","results":[{"id":"integer","name":"string","group_name":"string","allow_multiple_pocs":"boolean","default_poc":"string","super_pocs":[{"id":"integer","erp_id":"string","name":"string"}],"is_deleted":"boolean","created_by":"string","updated_by":"null","created_at":"string(datetime)","updated_at":"string(datetime)"}]}
```

### `GET /qbox/grievance-module/employee-ticket/` (HRMS)
- Query params: `page`, `page_size`, `scope` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"count":"integer","current_page":"integer","next":"null","previous":"null","results":[{"id":"integer","employee_ticket_response":[{"id":"integer","replied_by_name":"string|string(empty)","reply":"string","replied_by":"string","replied_at":"string(datetime)","file":"null","is_deleted":"boolean","is_read":"boolean","reply_type":"string","window":"string","visible_to_other":"boolean","ticket":"integer","created_by__name":"string|string(empty)","created_by__erp_id":"string","attachments":["empty"],"media":["empty"]}],"ticket":["mixed"],"assigned_to_name":"string","assign_to_second_level_name":"null|string","sme_name":"null|string","substitute_cover":"null","new_response":"boolean","tat_time":"string","ticket_priority_name":"string","is_unresponse":"boolean","first_response":"string(empty)","branch_name":"string","merged_tickets":["empty"],"department_name":"string","designation_name":"string","is_special_category":"boolean","viewer_role":"null","viewer_can_view_all_details":"null","viewer_is_group_super_poc":"null","subject":"string","description":"string","file":"string(url)","ticket_status":"string","ticket_priority":"integer","created_at":"string(datetime)","updated_at":"string(datetime)","branch_id":"string(numeric)","is_deleted":"boolean","due_date":"string(datetime)","resolved_date":"string(datetime)","re_opened_date":"null","ticket_number":"string(numeric)","feature_request_number":"null","tat_start_at":"string(datetime)","sensitivity":"string","gmail_uid":"null","mailed_at":"null","remarks":"null","internal_resolved_date":"null","closure_token":"null","closure_token_expiry":"null","last_reminder_sent_at":"null","attendance_branch_id":"integer","cc_users":["empty|string"],"reopen_count":"integer","internal_visible_to_employee":"boolean","employee_visible_to_members":"boolean","should_take_feedback":"boolean","closed_at":"string(datetime)","auto_resolved":"boolean","raised_against":["empty"],"routed_to_super_poc":"boolean","info_request_ask_questions":"boolean","raised_by":"string","assigned_to":"string","assign_to_second_level":"null|string","sme_erp_id":"null|string","updated_by":"string","created_by":"string","deleted_by":"null","ref_ticket":"null","parent_ticket":"null","second_point_of_contact":"null","designation":"integer","department":"integer","raised_by_staff_id":"integer","raised_by__name":"string","raised_by__erp_id":"string","email":"string(email)","contact":"string","employee_name":"string","assigned_to__name":"string","assigned_to__erp_id…
```

## HRMS growth plan

### `GET /qbox/pip/my-plan/` (HRMS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":"null"}
```

## HRMS payroll/attendance <a id="hrms"></a>

### `GET /qbox/payroll/employee-attendance-details/` (HRMS)
- Query params: `current_date`, `end_date`, `erp_id`, `start_date` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"erp_id":"string","employee_name":"string","first_erp":"string","joining_date":"string(date)","leaving_date":"null","period":{"start_date":"string(date)","end_date":"string(date)","total_days":"integer"},"shift":{"name":"string","start_time":"string(time)","end_time":"string(time)","in_margin":"string","out_margin":"string","window_start":"string(datetime)","window_end":"string(datetime)","swipe_type":"string","max_no_of_regularisation":"integer"},"summary":{"present":"integer","absent":"integer","halfday":"integer","leaves":"integer","rest_days":"integer","holidays":"integer","grace_days_used":"integer","regularizations":"integer"},"daily_records":[{"date":"string(date)","day":"string","attendance_status":"string","is_grace_day":"boolean","is_activity_forgiven":"boolean","activity_remarks":"null","check_in_time":"string(empty)|string(time)","check_out_time":"string(empty)|string(time)","is_override":"boolean","is_rest_day":"boolean","is_holiday":"boolean","is_regularized":"boolean","regularization_status":"null","leaves":["empty"],"is_half_day_leave":"boolean","is_any_leave_request_pending":"boolean","has_approved_comp_off":"boolean"}]}}
```

### `GET /qbox/payroll/employee-payslip/` (HRMS)
- Query params: `employee_id`, `month-year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"success":"boolean","message":"string","data":{"employee_name":"string","employee_code":"string","bank_name":"null","account_number":"string(numeric)","cycle_name":"string","payroll_month":"string(numeric)","payroll_year":"string(numeric)","cycle_start_date":"string(date)","cycle_end_date":"string(date)","gross_salary":"integer","total_deductions":"integer","net_salary":"integer","it_gross":"integer","basic_salary":"integer","hra":"integer","pf_deduction":"integer","income_tax":"integer","professional_tax":"integer","esic_deduction":"integer","employer_pf":"integer","employer_esic":"integer","gratuity":"integer","max_no_of_days":"integer","paid_days":"integer","paid_sessions":"integer","lop_days":"integer","reversal_arrears_days":"integer","nj_arrears_days":"integer","arrear_amount":"integer","status":"string","calculated_at":"string(datetime)","approved_at":"null","payslip_generated":"boolean","payslip_url":"string(url)","employee_details":{"id":"integer","pt_disability_disabled_by_name":"null","pt_disability_exemption_logs":["empty"],"gender":"string","pay_type":"string","business_category":"string","collar":"string","cost_center":"string","sub_level":"string","job_type":"string","joining_date":"string(date)","leaving_date":"null","date_of_birth":"string(date)","pan_number":"string","aadhar_number":"string(numeric)","aadhar_name":"null","account_no":"string(numeric)","ifsc_code":"string","pf_opted":"boolean","voluntary_pf":"null","epf_excess_contribution":"boolean","esic_opted":"boolean","nps_opted":"boolean","pension_opt":"boolean","nps_rate":"integer","pran_number":"null","pf_account_number":"null","uan_number":"null","pf_dop":"null","esic_number":"null","first_erp":"string","fhd":"string(date)","pt_disability_exemption":"boolean","pt_disability_remarks":"null","pt_disability_document":"null","pt_disability_disabled_at":"null","confirmation_date":"null","international_employee":"boolean","pan_operative_status":"boolean","first_hire_date":"string(date)","probation_period":"string(numeric)","notice_period":"integer","is_settlement":"boolean","is_backout":"boolean","backout_reson":"null","relieving_experience_letter":"null","entity":"integer","designation":"integer","employee":"integer","pt_disability_disabled_by":"null","shift":"string(uuid)","leave_scheme":"integer","email":"string(email)"},"components":[{"component_name":"string","component_type":"string","amount":"integer","description":"null"}],"created_at":"string(datetime)","updated_at":"string(da…
```

### `GET /qbox/payroll/leave-applications/` (HRMS)
- Query params: `application_type`, `page`, `page_size`, `status` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":["empty"]}}
```

### `GET /qbox/payroll/leave-types/` (HRMS)
- Query params: `attendance`, `is_active` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","name":"string","code":"string","is_paid":"boolean","encashment_allowed":"boolean","description":"null|string","default_balance":"integer","carry_forward":"boolean","max_carry_forward_days":"integer|null","requires_document":"boolean","haf_day_allowed":"boolean","is_multiple_day_allowed":"boolean","is_active":"boolean","created_at":"string(datetime)","updated_at":"string(datetime)","is_apply_leave_without_balance":"boolean","allow_negative_balance":"boolean","max_negative_balance":"string(numeric)","auto_approve_after_days":"integer","approver_type":["integer"]}]}
```

### `GET /qbox/payroll/payroll-cycle-lock/` (HRMS)
- Query params: `payroll_month`, `payroll_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"id":"integer","cycle_name":"string","payroll_month":"string(numeric)","payroll_year":"string(numeric)","is_locked":"boolean","locked_at":"null","locked_by":"null","locked_by_name":"null","locked_by_erp_id":"null","status":"string"}}
```

### `GET /qbox/payroll/reportees-dropdown/` (HRMS)
- Query params: `include_reportees`, `page_size` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","name":"string","erp_id":"string","designation":"string","is_self":"boolean"}]}
```

### `GET /qbox/payroll/tax-declaration-summary/` (HRMS)
- Query params: `financial_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"financial_year":"string","tax_regime":"null","status":"string","window_status":"string","is_window_open":"boolean","last_updated":"null","sections":[{"section":"string","max_limit":"integer|null","declared_amount":"integer","eligible_amount":"integer","has_declarations":"boolean","declaration_count":"integer"}],"totals":{"total_declared":"integer","total_eligible":"integer"}}}
```

### `GET /qbox/payroll/user-holiday/` (HRMS)
- Query params: `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"Jan":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Feb":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Mar":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Apr":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"May":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Jun":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Jul":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Aug":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Sep":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Oct":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Nov":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}],"Dec":[{"name":"string","holiday_date":"string(date)","session_year":"string(numeric)","description":"null"}]}}
```

## HRMS travel

### `GET /qbox/travel/travel-request/` (HRMS)
- Query params: `page`, `page_size`, `view_type` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":["empty"],"progress_counts":{"approval":"integer","booking":"integer","partly":"integer"},"tab_counts":{"ongoing":"integer","upcoming":"integer","history":"integer"}}}
```

## HRMS users

### `GET /qbox/user-mgmt/department-list/` (HRMS)
- Query params: `access`, `is_active` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","department_name":"string","department_code":"string","category":"string","description":"string(empty)","sub_level":"null|string","is_active":"boolean","created_at":"string(datetime)","updated_at":"string(datetime)","super_department":"integer"}]}
```

### `GET /qbox/user-mgmt/designation-list/` (HRMS)
- Query params: `access`, `is_active` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","department":{"id":"integer","department_name":"string"},"role_preference":["empty|mixed"],"name":"string","mail_id_policy":"string","job_description":"null","greythr_id":"null","is_active":"boolean","is_delete":"boolean","collar_type":"string","is_recruiter_screening_required":"boolean","created_at":"string(datetime)","updated_at":"null","created_by":"integer"}]}
```

## Identity/Users

### `GET /qbox/erp_user/access-control/matrix/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape: not captured (non-JSON, empty or error)

### `POST /qbox/erp_user/access-token/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Request shape: `{"refresh":"string"}`
- Response shape:
```json
{"status":"string(numeric)","message":"string","data":"string","force_update":"boolean"}
```

### `GET /qbox/erp_user/grade/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","status":"string","message":"string","data":[{"id":"integer","grade_id":"integer","grade_name":"string","branch_id":"integer","branch_name":"string"}]}
```

### `GET /qbox/erp_user/principle-signature/` (ERP)
- Query params: `branch_id`, `session_year`, `user_level` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"next":"null","previous":"null","count":"integer","limit":"integer","current_page":"integer","total_pages":"integer","results":[{"id":"integer","signature":"string","branch_id":"integer","is_delete":"boolean","author_id__erp_id":"string","author_id__name":"string","author_id__branch_id__branch_name":"string","author_id__roles_id__role_name":"null","author__user__level__user_level":"integer"}]}}
```

### `GET /qbox/erp_user/profanity-txt/` (ERP)
- Query params: none · Status: 404 · Evidence: E016 (OBSERVED)
- Response shape: not captured (non-JSON, empty or error)

### `GET /qbox/erp_user/students-online-class/` (ERP)
- Query params: `end_date`, `page`, `page_size`, `start_date`, `status` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"total":"integer","page":"string(numeric)","page_size":"string(numeric)","data":[{"id":"integer","title":"string","description":"string","sectionmapping__grade":"integer","sectionmapping__grade__grade_name":"string","sectionmapping__section":"integer","sectionmapping__section__section_name":"string","subject__subject_name":"string","subject":"integer","start_time":"string(datetime)","end_time":"string(datetime)","is_delete":"boolean","class_link":"string(url)","duration":"integer","host__name":"string","host__erp_id":"string","provider":"string","status":"string","zoom_join_url":"null","zoom_start_url":"null","zoom_meeting_id":"null","recording_status":"string","recording_play_url":"null","recording_password":"null","recording_bucket_status":"string","recording_bucket_url":"null"}]}}
```

### `GET /qbox/erp_user/user-mgmt/user-branches/` (ERP)
- Query params: `all_branches`, `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","branch_id":"integer","branch__branch_name":"string","branch__branch_code":"string(numeric)","branch__branch_type":"string","session_year_id":"integer","branch__brand_id":"integer","branch__brand__code":"string","branch__brand__name":"string"}]}
```

### `GET /qbox/erp_user/user-mgmt/user-grades/` (ERP)
- Query params: `acad_session`, `access_level`, `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"grade_id":"integer","grade__grade_name":"string","grade__eduvate_grade_id":"integer"}]}
```

### `GET /qbox/erp_user/user-mgmt/user-info/` (ERP)
- Query params: `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"message":"string","result":{"id":"integer","erp_id":"string","name":"string","email":"string(email)","contact":"string","profile":"string(url)","address":"string","pin_code":"string(numeric)","date_of_birth":"string(date)","session_details":{}},"status_code":"integer"}
```

## Shell

### `POST /collect` (CLARITY(3p))
- Query params: none · Status: 200, 204, None · Evidence: E016 (OBSERVED)
- Request shape: `"non-json"`
- Response shape: not captured (non-JSON, empty or error)

### `POST /qbox/api/v1/track/batch` (ANALYTICS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Request shape: `{"events":[{"module":"string","action":"string","detail":"string","meta":{"search":"string(empty)","hash":"string(empty)","fullPath":"string"},"event_id":"string(uuid)","session_id":"string(uuid)","platform":"string","app":"string","user_id":"string(numeric)","user_role":"string(numeric)","school_id":"string(numeric)","timestamp":"string(datetime)","url":"string(url)","pathname":"string"}]}`
- Response shape:
```json
{"success":"boolean","accepted":"integer","rejected":"integer","errors":["empty"]}
```

### `POST /qbox/api/v1/track/session` (ANALYTICS)
- Query params: none · Status: 201, None · Evidence: E016 (OBSERVED)
- Request shape: `{"type":"string","session_id":"string(uuid)","platform":"string","app":"string","user_id":"string(numeric)","user_role":"string(numeric)","school_id":"string(numeric)","started_at":"string(datetime)","user_agent":"string","screen_width":"integer","screen_height":"integer","url":"string(url)"}`
- Response shape:
```json
{"success":"boolean","session_id":"string"}
```

### `GET /qbox/apiV1/expense-delegation-context/` (FIN)
- Query params: `erp_id` · Status: 200, None · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"is_delegate":"boolean","delegators":["empty"]}
```

### `POST /qbox/erp_user/fcm-register/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Request shape: `{"fcm_token":"string"}`
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

### `GET /qbox/event-feedback/staff/my/pending/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

### `GET /qbox/grievance-module/employee-ticket-summary/` (HRMS)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"has_active_request":"boolean","unread_count":"integer","pending_reply_count":"integer","pending_feedback_count":"integer","info_requested_count":"integer","has_pending_action":"boolean"}
```

### `GET /qbox/home-banner/announcement/active/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","title":"string","web_image_url":"string(url)","app_image_url":"string(empty)","redirect_url":"string(empty)|string(url)","announcement_text":"string(empty)","show_image":"boolean","show_text":"boolean"}]}
```

### `GET /qbox/home-banner/promotional/active/` (ERP)
- Query params: none · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":[{"id":"integer","title":"string","web_image_url":"string(url)","app_image_url":"string(empty)","redirect_url":"string(empty)"}]}
```

### `GET /qbox/lms/training-reminders/` (HRMS)
- Query params: none · Status: 200, None · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":["empty"]}
```

### `GET /qbox/mongoapp/notifications/` (ERP)
- Query params: `page`, `page_size` · Status: 200, 403, None · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"results":[{"id":"string","title":"string","message":"string","module":{"name":"string"},"action_url":"string","category":"string","source":"string","is_read":"boolean","created_at":"string(datetime)"}],"total":"integer","page":"integer","page_size":"integer"}}
```

### `GET /qbox/mongoapp/notifications/counts/` (ERP)
- Query params: none · Status: 200, 403, None · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"total_unread":"integer","approval_unread":"integer"}}
```

### `GET /qbox/payroll/kp/access/` (HRMS)
- Query params: none · Status: 200, None · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"is_kp_payroll_admin":"boolean"}}
```

## Staff tests

### `GET /qbox/test-module/my-results/` (ERP)
- Query params: `session_year` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","status":"string","message":"string","result":[{"id":"integer","paper":"string","test_type":"string","test_type_id":"integer","is_tutor":"boolean","sections":[{"name":"string","obtained":"integer","max":"integer","count":"integer","cut_off":"null","passed":"null"}],"total_obtained":"integer","total_possible":"integer","percentage":"integer","result":"string(empty)","submitted_at":"string(datetime)","rank":"null"}]}
```

## Timetable <a id="timetable"></a>

### `GET /qbox/timetable/branch-staff-timetable/` (ERP)
- Query params: `section_mapping` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"period_system":[{"id":"integer","period_name":"string","position":"integer","start_time":"string(time)","end_time":"string(time)","is_break":"boolean"}],"week_days":{"0":"string","1":"string","2":"string","3":"string","4":"string","5":"string","6":"string"},"week_holidays":{"0":"null","1":"null","2":"null","3":"null","4":"null","5":"null","6":"null"},"teacher_periods":[{"id":"integer","period_id":"integer","period__period_name":"string","period__position":"integer","period__start_time":"string(time)","period__end_time":"string(time)","day":"integer","teacher_id":"integer","teacher__name":"string","teacher__erp_id":"string","teacher__teacher_type":"string","mapping_subject_id":"integer|null","mapping_subject__subject__subject_name":"null|string","is_draft":"boolean","is_delete":"boolean","dummy_teacher_id":"null","dummy_teacher__name":"null","is_sen":"boolean","student_id":"null","student__name":"null","student__erp_id":"null","common_group_id":"null","substitute_teacher_id":"null","substitute_teacher_erp_id":"null","substitute_teacher_name":"null","substitute_teacher_type":"null","sub_mapping_subject_id":"null","sub_subject_id":"null","sub_subject_subject_name":"null","common_section_names":["empty"],"is_planner":"boolean"}],"sen_periods_overlay":["empty"],"sen_student_ids":["empty"]}}
```

### `GET /qbox/timetable/teacher-timetable/` (ERP)
- Query params: `acad_session`, `teacher_id` · Status: 200 · Evidence: E016 (OBSERVED)
- Response shape:
```json
{"status_code":"integer","message":"string","result":{"week_days":{"0":"string","1":"string","2":"string","3":"string","4":"string","5":"string","6":"string"},"week_holidays":{"0":"null","1":"null","2":"null","3":"null","4":"null","5":"null","6":"null"},"teacher_periods":[{"id":"integer","period_id":"integer","period__period_name":"string","period__position":"integer","period__is_break":"boolean","section_mapping__grade_id":"integer","section_mapping__section_id":"integer","section_mapping_id":"integer","section_mapping__grade__grade_name":"string","section_mapping__section__section_name":"string","period__start_time":"string(time)","period__end_time":"string(time)","day":"integer","teacher_id":"integer","teacher__name":"string","teacher__erp_id":"string","mapping_subject_id":"integer|null","mapping_subject__subject__subject_name":"null|string","is_draft":"boolean","dummy_teacher_id":"null","dummy_teacher__name":"null","teacher__teacher_type":"string","is_sen":"boolean","student_id":"null","student__name":"null","student__erp_id":"null","common_group_id":"null","substitute_period_id":"null","substitute_teacher_id":"null","substitute_teacher_erp_id":"null","substitute_teacher_name":"null","substitute_teacher_type":"null","sub_mapping_subject_id":"null","sub_subject_id":"null","sub_subject_subject_name":"null","homework_assigned":"boolean","subject_id":"integer|null","eduvate_grade_id":"integer","eduvate_subject_id":"integer|null","central_gsmap":"integer|null","type":"null|string","student_count":"integer","common_section_names":["empty"]}],"substitute_periods":["empty"]}}
```

## Tickets <a id="tickets"></a>

Not observed (access denied for the teacher account). Static candidates: finance `/apiV1/ticket/`; ERP routes `/ticket*`. See `03_MODULE_SPECIFICATIONS/m13_tickets_feedback.md`.