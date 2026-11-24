# GitHub Upload Audit

**Date:** 2026-11-24
**Repository (intended):** `school-erp-blueprint` · **visibility:** PRIVATE
**Staging directory:** `D:\REA Claude\school-erp-blueprint` (separate from, and leaving untouched, the canonical research directory `D:\REA Claude\orchids-erp-research`)
**Purpose:** Record the privacy/secret review performed before the first commit and push, and exactly what was included and excluded.

---

## 1. What was staged

The sanitised Eduvate research was mirrored into `01_EDUVATE_RESEARCH/`, preserving the original documentation hierarchy. The repository root adds project scaffolding and this audit.

| Metric | Value |
|---|---|
| Tracked files at commit | **135** (133 content + this audit + the SHA-256 manifest) |
| of which under `01_EDUVATE_RESEARCH/` | 126 |
| Directories (excl. `.git`) | 22 |
| Total working-tree size (excl. `.git`) | ~5.29 MB |
| Largest file | `01_EDUVATE_RESEARCH/13_AUTONOMOUS_DEEP_ANALYSIS/API_CALLSITE_INDEX.csv` (~1.57 MB) |
| Files over 25 MB | 0 |
| File types | 121 `.md`, 10 `.csv`, 10 `.png`, 4 `.json`, 2 `.mmd`, 1 `.zip`, 1 `.txt`, plus repo scaffolding |

The SHA-256 manifest of every committed content file is `FILE_MANIFEST_SHA256.txt` (repo root).

## 2. Security & privacy review (performed BEFORE staging)

The complete source tree (149 files) was inspected before anything was copied.

### 2.1 Secret / credential / PII scan — **CLEAN**
A value-level scan across every text file (`.md/.csv/.json/.mmd/.txt`) found **zero** of:
- Email addresses (real) · JWTs (`eyJ…`) · bearer-token values · AWS keys (`AKIA…`)
- Cookies / session tokens / CSRF tokens (as values)
- Chrome DevTools WebSocket debugger URLs or browser identifiers
- 9+ digit runs (no phone numbers, account numbers, Aadhaar, or ID values)

Terms such as `aadhaar`, `ifsc`, `account number`, `refresh_token`, `Authorization:` **do** appear — but only as **data-model field names, auth-scheme descriptions, and security requirements**, never as real values (e.g. the auth header is documented as `Authorization: Bearer <access>` with a placeholder; session fields are listed as schema keys `token, refresh_token`). These are legitimate, value-free research content.

### 2.2 Screenshots — 10 reviewed, all **redacted**, included
Each of the 10 PNGs under `01_EDUVATE_RESEARCH/09_VISUAL_EVIDENCE/screenshots/` was opened and visually inspected:
- Profile photos, teacher/host names, and message descriptions are blacked out with explicit **REDACTED** boxes.
- Data tables shown are empty states (attendance, communication inbox, school-wall, timetable, homework/lesson-plan filters) — **no student rosters, names, IDs, or contact details**.
- Remaining content is institutional only (school/branch name in the header, class/section structure, academic-calendar events, circular titles).
- **No** student personal information, employee personal information, bank data, payslips, faces, or contact details appear.

They therefore qualify as *sanitised* screenshots and are included.

### 2.3 Proprietary source / raw captures — **none present**
- No `.js/.ts/.map` application bundles (the original 1,954-file raw corpus was already deleted; only value-free derived metadata remains).
- No `.har/.pcap` network captures, no raw browser/session captures.
- No `.env/.pem/.key` or other secret files.
- The research package stores only derived metadata (paths, methods, field names, structure) and the project's own written analysis.

## 3. Excluded from the repository (and why)

| Excluded | Reason |
|---|---|
| `orchids-erp-research/PHASE3_PROOF_PACKAGE/` (unzipped, 23 files) | Redundant — it duplicates content already mirrored under `13_AUTONOMOUS_DEEP_ANALYSIS/`, `10_REA_EVIDENCE/`, and the root reports. The packaged deliverable is kept as `01_EDUVATE_RESEARCH/PHASE3_PROOF_PACKAGE.zip` (with its own `SHA256SUMS.txt` inside). |
| Chrome debugging endpoints / browser identifiers | Not present; blocked by `.gitignore` as defence-in-depth. |
| Cookies, auth tokens, passwords, API keys, secrets | Not present; blocked by `.gitignore`. |
| Personal student / employee / bank data, payslips | Not present; screenshots redacted; blocked by `.gitignore` patterns. |
| Unredacted screenshots, raw personal browser captures | None exist; any future image must be confirmed redacted before adding. |
| Session transcripts, temporary scratchpads, app JS/executable exports | Not present; the previous session's scratch tooling and raw corpus lived in ephemeral temp and are gone; blocked by `.gitignore`. |

A strict [`.gitignore`](.gitignore) enforces these exclusions going forward.

## 4. Corrected call-site reconciliation — reflected

The copied documentation includes the final documentation erratum and all corrected figures:
- `01_EDUVATE_RESEARCH/13_AUTONOMOUS_DEEP_ANALYSIS/CALLSITE_COUNT_ERRATUM.md` is present.
- `COVERAGE_REPORT.md`, `API_METHOD_RESOLUTION.md`, `APPLICATION_CHUNK_GRAPH.md`, and `04_BACKEND_ANALYSIS/api_inventory.md` carry the reproducible **8,530** exported call-site records / **10,006** catalog `callsite_count` figures (same 5,364 endpoints); the non-reproducible "11,146" is no longer presented as a verified total. The HTTP-method resolution figure (**5,427** / 82.5%) is preserved.

## 5. Git & GitHub — **completed**

- **git:** initialised in the staging directory (branch `main`); all files staged; local commit `802afa259c05300009d9af1959d72d222802b9ad` created.
- **Author:** the repository's configured git identity (`git config user.name` / `user.email`).
- **No force push; no overwrite of any existing remote** (the target repository did not previously exist — verified via `gh repo view`, which returned "Could not resolve to a Repository").
- **GitHub CLI (`gh`):** installed (2.105.0). Authenticated interactively by the user via the web browser device-code flow as **AdityaBhoite** (token scopes: `repo, read:org, workflow, gist`). No password or token was requested or stored by the assistant.
- **Remote created and pushed:** `gh repo create school-erp-blueprint --private --source . --remote origin --push`.
  - **Repository:** <https://github.com/AdityaBhoite/school-erp-blueprint>
  - **Visibility:** PRIVATE (confirmed by `gh repo view`: `isPrivate: true`).
  - **Owner:** `AdityaBhoite`.
  - **Pushed commit:** `802afa259c05300009d9af1959d72d222802b9ad` — verified identical on `origin/main` (GitHub API `.sha`).
  - **Files on remote tree:** 135 (matches local).
- This audit was updated **after** a successful push to record the outcome; that update is a second commit on top of the pushed `802afa2`. The SHA-256 manifest is unaffected (this file is excluded from its own hashing).

## 6. Verification performed
- Canonical research directory file count unchanged (149) — original left untouched.
- Staged tree re-scanned at the value level: clean.
- Tracked set reviewed: no sensitive file tracked; nothing of ours accidentally ignored.
- No file exceeds size limits (largest ~1.57 MB; GitHub's warning threshold is 50 MB).
