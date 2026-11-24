# External Integrations

All entries are STATIC (referenced in shipped frontend code or navigation) unless marked OBSERVED. Presence in code does not prove the integration is active for the Orchids brand.

| Integration | Purpose (inferred from names and screens) | Evidence | Class / Confidence | Replacement priority |
|---|---|---|---|---|
| Google Cloud Storage + CDNs | File and media storage (lesson plans, e-books, documents, receipts) | config buckets | STATIC, HIGH | Required (S3-compatible storage) |
| Firebase Cloud Messaging | Web and mobile push notifications (`fcm-register` on every page load) | runtime | OBSERVED, HIGH | Required (push via FCM/APNs) |
| WhatsApp (bot service, templates, workflows, inbox, agent reports) | Parent/candidate messaging, notifications, CRM conversations | nav items, `whatsappbot` host | STATIC, HIGH | Phase 2 (WhatsApp Business API provider) |
| SMS | Attendance absence SMS (`/erp_user/sms-notifying-students/`) | endpoint | STATIC, MEDIUM | Phase 1 (SMS gateway abstraction) |
| Email | Official mail password reset, notifications | endpoints | STATIC, MEDIUM | Required (SMTP/transactional email) |
| Zoom | Online classes (`/online-class-attendance/:zoomid`) | route | STATIC, MEDIUM | Phase 2 (generic video-meeting link provider) |
| Mattermost | Staff chat (`matterMostChat` endpoints, chat host) | endpoints | STATIC, MEDIUM | Optional |
| OnlyOffice Docs | In-browser editing of lesson plans and documents | config | STATIC, HIGH | Optional (phase 3) |
| SAP | Business partner sync, purchase requisitions, invoicing, reimbursement posting, expansion | nav + endpoints (116 refs) | STATIC, HIGH | Out of scope for MVP; provide accounting export |
| Tally | Accounting export reports (Tally Report) | nav | STATIC, HIGH | Phase 2: export to accounting (CSV/Tally XML) |
| JODO, Avanse | Education-fee financing / EMI partners (JODO Cred/Flex/Disbursement reports, Avanse config) | nav | STATIC, HIGH | Optional |
| Payment gateway | Online fee payment (`/student-fee/student-fee-payment/:orderIdValue`, payment success pages) | routes | STATIC (provider UNKNOWN) | Required (Razorpay/Stripe-like abstraction) |
| Kiosk and cash deposit machines | On-campus fee kiosks, cash deposit and cheque verification | nav | STATIC, HIGH | Optional |
| EasyEcom, Ekart | Store (books/uniform kits) order fulfilment and delivery | nav | STATIC, HIGH | Optional (store module phase 3) |
| OnGrid, Trace | Employee background verification (BGV) | nav, endpoints | STATIC, HIGH | Out of scope (HR) |
| DigiLocker / Aadhaar / Income-tax portal links | HR identity and tax declarations (links to UIDAI, income-tax e-portal) | URLs | STATIC, HIGH | Out of scope (HR) |
| Vonage, Airtel | Click-to-call telephony for staff (parents, candidates) | endpoints, profile button | STATIC/OBSERVED | Optional |
| Microsoft Clarity | Session recording and heat-maps | runtime | OBSERVED, HIGH | Optional (privacy review required) |
| Module Adoption Tracker (in-house) | Feature-usage analytics | runtime | OBSERVED, HIGH | Optional (product analytics) |
| Google MediaPipe | Webcam proctoring for online tests (face, hand and object detection) | URLs | STATIC, HIGH | Optional |
| LLM features | "Ask our Assistant" help bot, AI calling, AI interview, AI screening, ChatGPT config, audio analysis of teaching, AI SEO | nav, endpoints | STATIC, HIGH | Optional (phase 3+) |
| Google Maps | Transport live tracking, expansion maps | routes | STATIC, MEDIUM | Phase 2 with transport |
| GPS devices | Bus live tracking (`/transport-gps`, `/transport-live-tracking`, `/iframe/track/:busNo`) | routes | STATIC, HIGH | Phase 2 |
