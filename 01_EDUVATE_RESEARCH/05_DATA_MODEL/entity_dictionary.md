# Entity Dictionary (proposed schema with provenance)

Legend for the **Src** column: **O** = field observed in a runtime API shape (E016), **S** = implied by static endpoint/route names, **I** = inferred, **P** = proposed for the replacement (no evidence in the original).
All tenant-scoped tables carry `tenant_id` (P) and standard columns `id` (UUIDv7, per `12_CLAUDE_CODE_HANDOFF/ARCHITECTURE_BLUEPRINT.md` §5; audit ER-42), `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at` (soft delete; the original uses `is_delete` O). These standard columns are not repeated below.

---
## Tenancy & organisation

### Tenant (original: Brand)
| Field | Type | Req | Src | Notes |
|---|---|---|---|---|
| code | varchar(16) unique | Y | O (`branch__brand__code`) | e.g. short brand code |
| name | varchar(200) | Y | O (`branch__brand__name`) | |
| short_name | varchar(50) | N | S | |
| logo_url, branding_photo_url | text | N | S | |
| primary_color | char(7) | N | S | theme colour |
| terms_url, privacy_url | text | N | S | |
| custom_domain | varchar | N | S (host per brand) | |

### Zone / City
| Entity | Fields | Src |
|---|---|---|
| Zone | name, description, is_active | S (`zone-view`) |
| City | city_name, description, is_active, (branch_count derived) | O (`/acms/cities/`). No `zone_id` on City is evidenced; Branch references Zone and City independently (audit ER-36). |

### Branch (school campus)
| Field | Type | Req | Src |
|---|---|---|---|
| branch_name | varchar(200) | Y | O |
| branch_code | varchar(20) unique per tenant | Y | O (numeric string) |
| branch_type | enum (e.g. school, preschool, …) | Y | O (values UNKNOWN) |
| zone_id | fk Zone | N | O (`zone{}` on `/central-db/branch-view/`) |
| city_id | fk City | N | O (`city_fk{}` on `/central-db/branch-view/`) |
| address, phone, email, affiliation_board, affiliation_no | | N | P |
| logo_url, principal_signature_url | | N | O (principal signature entity) |
| is_active | bool | Y | I |

### AcademicYear (original: SessionYear)
| Field | Type | Req | Src |
|---|---|---|---|
| name | varchar(20) e.g. "2026-27" | Y | O |
| start_date, end_date | date | Y | P |
| is_current | bool (one per tenant) | Y | O (`session_year__is_current_session`) |

### BranchAcademicYear (original: branch-acad map, "acad_session")
| Field | Type | Req | Src |
|---|---|---|---|
| branch_id, academic_year_id | fk | Y | O (`acad_session`, `session_year_id`) |
| status | enum(planning, active, closed) | Y | P |
| week_holidays | smallint[] (0..6) | N | O (`week_holidays`) |

---
## Academic structure

### Grade (class level)
| Field | Type | Req | Src |
|---|---|---|---|
| grade_name | varchar(50) | Y | O |
| global_grade_id / catalogue key | int | N | O (`eduvate_grade_id`) |
| sort_order | int | Y | P |
| board | enum/fk (CBSE, ICSE, State, IB…) | N | S (`get-board-list`) |

### GradeOffering (original: GradeMapping) — grade offered in a branch for a year
Fields: branch_academic_year_id, grade_id, capacity (P), is_active. Src: S/O (`/erp_user/grademapping/` returns grade_id, grade_name, branch_id, branch_name).

### Section
Fields: section_name (e.g. "A"), sort_order. Src: O.

### ClassSection (original: SectionMapping, "section_mapping_id")
| Field | Type | Req | Src |
|---|---|---|---|
| grade_offering_id | fk | Y | O (`section_mapping__grade_id`) |
| section_id | fk | Y | O |
| class_teacher_id | fk Staff | N | I |
| room | varchar | N | P |
| period_system_id | fk | N | O/S (period-system-section-options) |
| capacity | int | N | P |

### Subject
Fields: subject_name, subject_code (P), type enum(scholastic, co_scholastic) (O `event-coscholastic-subjects`), is_optional (P), global_subject_id (S central subjects).

### ClassSubject (original: SubjectMapping, "mapping_subject_id")
Fields: class_section_id, subject_id, weekly_periods (P), is_elective (P).

### Curriculum: Volume → Chapter → Topic → KeyConcept
| Entity | Fields | Src |
|---|---|---|
| Volume (curriculum version/edition) | volume_name / name, is_current | O (`list-volume`, `curriculum/volumes`) |
| Chapter | grade_id, subject_id, volume_id, name, sequence | S |
| Topic | chapter_id, name, sequence | S |
| KeyConcept | chapter_id/topic_id, text | S |

---
## Identity, people and access

### User
| Field | Type | Req | Src |
|---|---|---|---|
| erp_id (username) | varchar(40) unique per tenant | Y | O |
| name / first_name / last_name | varchar | Y | O |
| email | varchar | N | O |
| contact (phone) | varchar | N | O |
| profile (photo URL) | text | N | O |
| address, pin_code | text | N | O |
| date_of_birth | date | N | O |
| gender | enum | N | O (session) |
| user_type | enum(staff, student, parent) | Y | I |
| password_hash, must_change_password, two_factor_enabled | | Y | O (`force_update`, `is_2fa_enabled`) |
| is_superuser, is_active | bool | Y | O |
| last_login_at | ts | N | P |

### Role / Permission / RoleAssignment (replaces UserLevel + access matrix)
| Entity | Fields | Src |
|---|---|---|
| Role | name, code, description, is_system | S (`roles_all`, `user-level`) |
| Permission | code (`module.resource.action`), description | P (original: is_view/is_add/is_modify/is_delete per nav item) |
| RolePermission | role_id, permission_id | P |
| RoleAssignment | user_id, role_id, scope_type (tenant/branch/grade/class/subject), scope_id, academic_year_id | P (original: user-branches/grades/sections/subjects scopes O) |
| UserGrant (override) | user_id, permission_id, scope, reason, expires_at | P (replaces hard-coded `erpIds`) |

### Staff (employee profile, ERP-side)
| Field | Src | Notes |
|---|---|---|
| user_id | O | |
| employee_code | O (Company Code) | |
| designation, department, cadre | O (profile labels) | |
| joining_date | O | |
| branch_id, compliance_branch_id | O | |
| reporting_manager_id, hr_spoc_id | O | |
| shift (start–end) | O ("08:15-15:00") | |
| teacher_type | O (`teacher__teacher_type`) | e.g. regular/visiting |
| status (active, on_notice, exited) | S | |
Sensitive HR data (bank, Aadhaar, PAN, family, education, previous employment, statutory forms, nominees) belongs to the optional **HR context** (O labels in profile).

### Student
| Field | Src |
|---|---|
| user_id (login), erp_id | O/S |
| admission_no, admission_date | S |
| first_name, last_name, dob, gender, blood_group (P), photo | S/P |
| student_type (e.g. regular, staff ward, scholarship) | S (`student-type`) |
| is_virtual (online-only student) | O (`is_virtual_student`) |
| sen_flag / sen_details (restricted) | S (SEN module) |
| status enum(active, temp_blocked, left, alumni) | S |
| previous_school | P |

### StudentEnrolment (original: student session)
Fields: student_id, academic_year_id, class_section_id, roll_no (P), enrolled_on, status (active/promoted/left), house_id (S house system). One active per student per year.

### Guardian & StudentGuardian
Guardian: user_id, name, relation, phone, email, occupation (P). StudentGuardian: student_id, guardian_id, relation, is_primary, can_pickup (P). Siblings = students sharing a guardian (S `siblingsData`, sibling mapping).

---
## Scheduling

### CalendarEvent (all fields O unless noted)
title, description, event_type (enum: holiday, assessment, other_event, competition, ptm, …), event_date, event_time, end_date, academic_year_id, cities[], branches[] (+ exclusions), grades[], subjects[], test_type, assessment_id, recurrence_pattern, is_recurring_parent, parent_event_id (I), notes, color, is_published, is_auto_holiday, legacy_event_id.

### CalendarPublication (per academic year)
is_generated, is_published, published_at (P), draft_events_count (derived). Src: O (`calendar_status`).

### PeriodSystem / Period
PeriodSystem: name, branch_academic_year_id. Period: period_system_id, period_name ("P1"), position, start_time, end_time, is_break. Src: O.

### TimetableSlot (original: teacher period)
| Field | Src |
|---|---|
| period_id, day (0–6) | O |
| class_section_id | O |
| class_subject_id | O (`mapping_subject_id`) |
| teacher_id | O |
| is_draft | O |
| is_sen + sen_student_id | O |
| common_group_id | O |
| version/publish_id | P |

### Substitution / TeacherAbsence
TeacherAbsence: teacher_id, date, reason (S). Substitution: timetable_slot_id, date, substitute_teacher_id, substitute_subject_id (O `sub_mapping_subject_id`), status.

---
## Teaching & learning

### LessonPlan
LessonPlanPeriod: volume_id, grade_id, subject_id, chapter_id, topic_id, period_no, objectives, resources[] (files). LessonPlanCompletion: lesson_plan_period_id, class_section_id, teacher_id, completed_on. Src: S (`lp-view`, `lp-completion-view`, `lp-files`).

### DiaryEntry (O)
teacher_id, date, period_id (I), title, classwork (text), homework (text), has_homework, media_files[], is_online, is_substitute, last_submission_dt, class_sections[], subjects[], created_at.

### HomeworkSubmission (S)
diary_entry_id/homework_id, student_id, submitted_at, files[], text, status (assigned/submitted/evaluated/overdue), marks/remarks, evaluated_by, evaluated_at.

### OnlineClass (O)
title, description, class_section_id, subject_id, start_time, end_time, duration, host_id, provider (zoom/gmeet), class_link, meeting_id, join_url, start_url, status, recording_url, is_recurring (S), is_delete.

### OnlineClassAttendance (S): online_class_id, student_id, joined_at, left_at, status.

---
## Attendance

### StudentAttendance (S)
student_id, class_section_id, date, period_id (nullable; P for period-wise), status enum(present, absent, late, half_day, leave) (P: values UNKNOWN), marked_by, marked_at, remarks, notified_at.
Unique (student_id, date, period_id).

### StaffAttendance (S, HR)
staff_id, date, in_time, out_time, status, source (biometric/app/manual), regularisation_id.

---
## Assessment

| Entity | Fields | Src |
|---|---|---|
| ExamType | exam_name, is_delete | O |
| GradingSystem | name, bands[{min, max, grade, grade_point, remark}] | S |
| Assessment | code, name, test_type, section_access (all/selected), grade_id, subject_id, academic_year_id, test_date, test_time, effective_test_date, scheduled_via_calendar, calendar_event_id, total_marks (decimal), duration_minutes, assessment_mode (online/offline), status, result_date | O |
| AssessmentBranchAssignment | assessment_id, branch_id, marks_published, teacher_pdf_url | O |
| Blueprint | assessment_id, rows[{chapter, topic, question_type, difficulty, marks, count}] | S |
| QuestionPaper | assessment_id, type (practice/sample/final), is_locked, status (draft/published), questions[] | O/S |
| Question | text, type, options, answer, marks, difficulty, chapter_id, topic_id, media, source (central/school) | S |
| MarksEntry | assessment_id, student_id, class_section_id, marks_obtained, status (present/absent/exempt), entered_by, entered_at | S |
| ReportCardTemplate | grade scope, components[], sub_components[], weights, grading_system_id | S |
| ReportCardRun (pipeline) | template_id, academic_year_id, branch_id, status (generated/reviewed/published/reverted), published_at | S |
| StudentReportCard | run_id, student_id, pdf_url, data jsonb | S |
| PrincipalSignature | branch_id, academic_year_id, image_url, author_id | O |

---
## Communication

| Entity | Fields | Src |
|---|---|---|
| Announcement | category_id, title, body, attachments[], audience_spec jsonb, status (draft/published), published_at, author_id | S |
| AnnouncementRecipient | announcement_id, user_id, read_at | I |
| Notification | user_id, title, message, module, ref_type/ref_id, is_read, created_at | O |
| Banner | title, web_image_url, app_image_url, type (announcement/promotional), active_from/to | O |
| Ticket | ticket_no, category_id, sub_category_id, raised_by (guardian), student_id, branch_id, subject, description, status, assignee_id, csat_score, closed_at | S |
| TicketMessage | ticket_id, author_id, body, attachments[], internal_note | S/P |
| MessageLog | channel (sms/email/whatsapp/push), template, recipient, status, provider_ref | S (`email-sms-logs`) |

---
## Finance (core+, from static evidence)
Provenance (audit ER-37): "S" here means the **entity** is implied by routes or menu labels. Individual **fields** are INFERRED or PROPOSED unless a route or endpoint names them; the core finance API was never recovered.

| Entity | Key fields | Src |
|---|---|---|
| FeeAccount (bank) | name, bank, account_no (masked), ifsc, gateway_merchant_id | S |
| FeeTerm | name, academic_year_id, due_date, last_date | S |
| FeeHead (component/group type) | name, type (tuition, admission, curriculum, transport, misc, SEN), is_refundable | S |
| FeePlan | name, academic_year_id, branch_id, grade_ids[], student_type, components[{fee_head, term, amount}] | S |
| StudentFeeAssignment | student_id, fee_plan_id, academic_year_id, effective_from | S |
| Concession | student_id, type (master/sibling/staff), fee_head/term, amount or %, status, approved_by | S |
| FineRule / Fine | rule: per_day/flat, cap; fine: student, term, amount, waived | S |
| Invoice/Due (computed or materialised) | student, term, fee_head, amount, paid, balance | I |
| Payment | student_id, amount, mode (online/cheque/cash/kiosk/wallet), gateway_order_id, status, paid_at | S |
| Receipt | receipt_no, payment_id, allocations[{due, amount}], pdf_url, cancelled | S |
| Wallet / WalletTxn | wallet type (academic/store/transport/imprest), balance; txn type, amount, ref | S |
| Refund / ApprovalRequest | type, amount, reason, status, maker/checker | S |

---
## Transport (extended)
Route(name, branch_id), Stop(name, lat, lng, sequence, route_id, pickup_time, drop_time), Vehicle(registration_no, capacity, rc/insurance/fitness expiry), Driver/Attendant(staff_id, licence), TripSchedule(route, vehicle, driver, shift), StudentTransport(student, stop, route, from_date, to_date), TransportFeePlan(slab by distance/stop). Src: S for entity existence (route names); fields INFERRED/PROPOSED (audit ER-37).
