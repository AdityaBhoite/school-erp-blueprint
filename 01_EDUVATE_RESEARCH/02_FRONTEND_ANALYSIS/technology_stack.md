# Frontend Technology Stack

> **Scope: the ORIGINAL system**, except §4 (PROPOSED implications). The replacement stack is fixed in `CLAUDE_CODE_MASTER_PROMPT.md` §5.2.

Investigation date: 2026-11-22/23. Evidence: E002, E004, E004x, E010, E010x (script exports), plus local static reading. Classification is given per row.

## 1. Application topology (OBSERVED + STATIC, HIGH)

The product served at `https://orchids.letseduvate.com` is **not one SPA**. It is three independently built single-page applications under one origin, sharing a common "unified shell" (navigation rail, mega-menu, global search, help assistant, notifications, profile menu).

| App | Base path | Page title observed | Build | Purpose |
|---|---|---|---|---|
| ERP | `/erp/` | "Orchids - The International School \| Eduvate ERP Portal" | Vite, chunks `assets/*.[hash].js` (dot-separated hashes), manual vendor chunks (`vendor-antd`, `vendor-redux`, `vendor-react`, `vendor-date`, `vendor-utils`) | School operations: academics, students, attendance, assessments, fees, transport, store, communication, admin |
| CRM / Marketing | `/crm/` | not visited | (not captured) | Admissions funnel: leads, enquiries, events, budgets, incentives |
| HRMS / Recruitment / Payroll | `/recruitment/` | "HRMS" | Vite, chunks `assets/*-[hash].js` (dash-separated), about 84 module chunks loaded on the profile route | Hiring, onboarding, employee lifecycle, attendance and leave, payroll, training, assets |

Navigating between apps is a full document load (OBSERVED: target document changed when going from `/recruitment/` to `/erp/`). Navigating within an app is client-side routing.

`unified-mat.js` (an un-minified, non-module script shipped in both apps) is a **"Module Adoption Tracker" SDK**. It detects the current app from the path prefix (`/erp`, `/crm`, `/recruitment`), reads the app-specific login record from localStorage, and batches usage events to an analytics service (`analytics.letseduvate.com/qbox/api/v1/track/session` and `/track/batch`, OBSERVED at runtime). It embeds a client API key (value not recorded).

## 2. Libraries and versions

| Concern | ERP app | HRMS app | Evidence | Class |
|---|---|---|---|---|
| UI framework | React (separate `vendor-react` chunk) | React **18.3.1** (version string in bundle) | E004x, E010x | STATIC, HIGH |
| Component library | Ant Design (1.16 MB `vendor-antd` chunk) | Ant Design **5.29.3**, `@ant-design/icons` (each icon is its own lazy chunk) | bundle strings | STATIC, HIGH |
| CSS-in-JS | Ant Design token system; Emotion present | Emotion (`@emotion`) present | strings | STATIC, MEDIUM |
| Routing | React Router (`{path, Component: lazy(() => import(...)), exact}` route objects; 688 routes) | React Router (`useNavigate`); route table with permission metadata (82 top-level entries) | E012, E007 | STATIC, HIGH |
| State management | **Redux + redux-saga** (`vendor-redux`; `yield put({type:"user/SET_STATE"})`) | React Context (about 70 `createContext` calls); no Redux observed | strings | STATIC, HIGH |
| HTTP client | axios-style client with request interceptors | axios (`He.create()`, `interceptors.request.use`) | strings | STATIC, HIGH |
| Dates | `vendor-date` chunk (moment and/or dayjs) | moment | strings | STATIC, MEDIUM |
| Charts | Chart.js and c3/Chartist references | Highcharts (`highcharts-react`, `highcharts-more`) | chunk names | STATIC, MEDIUM |
| Spreadsheets | — | `xlsx` (SheetJS) for import/export | strings | STATIC, MEDIUM |
| PDF | — | pdf.js, plus a generated statutory form (`form12B` chunk) | chunk names | STATIC, MEDIUM |
| Validation | — | `yup` | strings | STATIC, MEDIUM |
| Animation | — | framer-motion | strings | STATIC, LOW |
| Computer vision | — | Google **MediaPipe** (face, hand and object detection models from storage.googleapis.com) for **proctored online assessments** (`ProctoringReport` chunk) | URLs | STATIC, HIGH |
| Document editing | OnlyOffice Docs integration (`office.letseduvate.com`, JWT) | — | config object | STATIC, HIGH |
| Session analytics | Microsoft Clarity (`s.clarity.ms/collect`) | Clarity | runtime network | OBSERVED, HIGH |
| Usage analytics | Unified MAT SDK → analytics service | same | runtime network | OBSERVED, HIGH |
| Push | `POST /qbox/erp_user/fcm-register/` on page load (Firebase Cloud Messaging token registration, INFERRED from the name) | — | runtime network | OBSERVED, HIGH |
| Telephony | Vonage click-to-call endpoints; "Setup Airtel Telephony" button in HRMS profile | Airtel telephony | E011, E002 | STATIC/OBSERVED |
| Source maps | none declared on any captured script | none | E002, E010 | OBSERVED |

## 3. Build characteristics
- Vite production builds with aggressive code-splitting: the ERP route table maps 688 paths to about 1,000 lazy chunks (`__vite__mapDeps` table of 1,003 files). The HRMS app lazy-loads even individual icons.
- The ERP build keeps readable internal identifiers (`reactExports`, `__vitePreload`, `config$3`, `endpoints`), which made static recovery of the endpoint catalog straightforward. The HRMS build is fully minified.
- Runtime config is **compiled into the bundle** (`VITE_*` variables): API bases for each brand host, CDN buckets and third-party service URLs. The host is selected at runtime from `window.location`.

## 4. Implications for the replacement (PROPOSED)
- One SPA per bounded context is not required. A single modular frontend (one router, lazy feature modules) with a shared shell gives the same UX at lower operational cost. See `11_IMPLEMENTATION_PLAN/recommended_architecture.md`.
- Ant Design 5 is a reasonable, legally reusable choice that reproduces the observed interaction patterns (tables, drawers, steps, date pickers). Any equivalent library is acceptable.
- Do **not** ship secrets in the client bundle (see `07_NON_FUNCTIONAL_REQUIREMENTS/security.md`, finding SEC-01).
