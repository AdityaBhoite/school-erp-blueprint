# WF-07 Admission and enrolment

Evidence: STATIC (ERP routes `/student-registration`, `/student-registration-form/:id`, `/student-registration-edit/:regid/:appId`, `/admission`, `/admission-form/:regId/:appId`, `/admission-form-edit/:regId`, `/admission-payment/:id`, `/application-input-form/*`, reports "Single Day Admission Report", "Application Registration Report", "Total Form Report"; CRM enquiry forms, application form and lead stages; user-management `create-student`; finance `apiV2/student/`, sibling search/map). Confidence MEDIUM.

## Steps
1. **Enquiry/lead** captured in CRM (digital/physical enquiry form, walk-in, events). (S)
2. **Registration**: the registration form creates a registration ID (`regId`) with an application (`appId`). A registration fee may be charged. (S)
3. **Application/admission form**: student details, parents/guardians, previous school, documents. Edits allowed before confirmation. (S)
4. **Admission payment** (admission fee) → admission confirmed. (S `admission-payment/:id`)
5. **Student creation** in ERP (`/erp_user/user-mgmt/student/`) and Finance (`/apiV2/student/`): ERP ID generated; parent accounts created/linked; siblings mapped. (S)
6. **Enrolment**: assign to grade and section for the session (student session record), student type (new/existing/staff ward…), fee plan. (S)
7. Credentials issued to parent/student; ID card generated. (S)

## Rules (PROPOSED/INFERRED)
- Unique ERP ID per student; parent identity is de-duplicated by phone/email; sibling linkage enables sibling concessions.
- Seat availability per grade/section (capacity) checked before enrolment (PROPOSED).
- Admission is confirmed only after admission-fee payment (I from route ordering).

```mermaid
flowchart LR
  L[Lead/Enquiry] --> R[Registration (regId)]
  R --> AF[Admission form (appId)]
  AF --> AP[Admission fee payment]
  AP --> ST[Create student + parents (ERP ID)]
  ST --> EN[Enrol: session, grade, section, type]
  EN --> FP[Assign fee plan]
  FP --> CR[Credentials + ID card]
```
