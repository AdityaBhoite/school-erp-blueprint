# Raw captures — withheld

Raw REA captures (inspect_web_page JSON with accessibility text, screenshots, exported evidence bundles) and the exported application JavaScript were **not** copied here because they contain:
- personal data of the logged-in employee and other people (profile, family, bank/ID fields, names in lists), and
- proprietary source code of the studied product.

They were stored only in the temporary session scratch directory of the investigating session and may be deleted. Sanitized derivatives are in:
- `../bundle_analysis/` — static extractions (navigation config, route tables, endpoint catalogs; employee IDs redacted)
- `../sanitized_metadata/` — value-free runtime API shapes (E016) and the role/level catalogue (E017)

REA evidence IDs for every capture are listed in `../../EVIDENCE_INDEX.md` for traceability.
