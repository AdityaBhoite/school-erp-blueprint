# Module Dependencies

```mermaid
flowchart LR
  IAM[Identity & Access] --> ORG[Org & Academic Structure]
  ORG --> PPL[People: Staff/Students/Guardians]
  IAM --> PPL
  PPL --> ENR[Enrolment]
  ORG --> ENR
  ORG --> CAL[Academic Calendar]
  ORG --> TT[Timetable]
  PPL --> TT
  CAL --> TT
  TT --> DIA[Diary & Homework]
  ENR --> ATT[Student Attendance]
  CAL --> ATT
  TT --> ATT
  ORG --> CUR[Curriculum & Lesson Plans]
  CUR --> DIA
  CUR --> ASM[Assessments]
  CAL --> ASM
  ENR --> ASM
  ASM --> RC[Report Cards]
  PPL --> COM[Announcements & Notifications]
  ENR --> COM
  COM --> TKT[Parent Tickets]
  ENR --> FIN[Fees]
  FIN --> PAY[Payments & Receipts]
  FIN --> CON[Concessions/Refunds/Wallets]
  ENR --> TRN[Transport]
  TRN --> FIN
  ATT --> RPT[Reports & Dashboards]
  ASM --> RPT
  FIN --> RPT
  DIA --> RPT
  NOTIF[Notification infrastructure] --> COM
  NOTIF --> ATT
  NOTIF --> DIA
  NOTIF --> FIN
  AUD[Audit log] -.-> ATT & ASM & FIN & PPL & IAM
```

| Module | Hard dependencies | Soft dependencies |
|---|---|---|
| Identity & Access | — | Audit |
| Org & Academic Structure | IAM | — |
| People | IAM, Org | — |
| Enrolment | People, Org | Fees (plan assignment) |
| Academic Calendar | Org | Assessments (link) |
| Timetable | Org, People (staff), Calendar (holidays) | Substitution ← Staff leave |
| Curriculum & Lesson Plans | Org | Timetable (period mapping) |
| Diary & Homework | Timetable, People, Enrolment | Lesson plans, Notifications |
| Student Attendance | Enrolment, Calendar | Timetable (period-wise), Notifications |
| Assessments | Org, Enrolment, Curriculum | Calendar, Question bank |
| Report Cards | Assessments, Grading | Principal signature (Org) |
| Announcements/Notifications | People, Enrolment | Channels (email/SMS/push/WhatsApp) |
| Parent Tickets | People, Enrolment | Notifications |
| Fees | Enrolment, Org | Transport, Concessions |
| Payments | Fees | Gateway |
| Transport | Enrolment, Org | Fees, GPS |
| Reports | all producers | — |
