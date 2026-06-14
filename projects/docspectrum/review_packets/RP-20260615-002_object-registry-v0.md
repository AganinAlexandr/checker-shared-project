# RP-20260615-002: Object registry v0 and TEI layer

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 01:40 MSK - codex]

`[REVIEW]` Object-level registry builder for the Podolsk UUiR validation corpus.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@d64cc89..6986e9b
- focus: `tools/build_object_registry_v0.py`, object registry artifact contract, TEI extraction, OKEI unit decoding, same-address TEI consistency check, separation between object-level registry and section-level passport
- inputs: `E:/commons/DocSpectrum/Капремонт_дома_УУиР_Подольск.xlsx`, source folders under `E:/MSE_арх`, Minstroy `explanatorynote-01-06.xsd`
- metrics: `35` objects in Excel, `35` source folders found, `34` objects with TEI, `1683_25` missing required explanatory note XML, OKEI dictionary parsed with `588` units, `5` same-address groups, `1` inconsistent same-address group
- generated artifacts: `E:/output/DocSpectrum/object_registry_v0/object_registry_v0.csv`, `object_tei_overview_v0.csv`, `address_tei_consistency_v0.csv`, `object_tei_long_v0.csv`, `object_registry_v0.jsonl`, `summary.json`
- not touched: no PDF/explorer reprocessing, no N-object pair comparison, no section-passport schema changes, no UI/API changes
- risks/regressions: TEI canonical-name mapping is v0 heuristic; some TEI names vary by wording/case; `1683_25` remains incomplete until the proper explanatory note XML is restored; `1680_25` / `1684_25` same-address inconsistency is treated as source-PD designer error before expertise, not legitimate house difference; full generated CSV/JSON are intentionally outside git
- domain-rule: object registry is library-independent object context; `section_passport` must remain specific to a concrete section; OKEI codes must be decoded from Minstroy schema, not hard-coded as unexplained numbers

Open questions for reviewer:

- Is the artifact split acceptable: script and PQ in repo, generated registry under `E:/output/DocSpectrum`?
- Should normalized TEI fields stay in `object_registry_v0.csv`, or should all canonicalization live only in `object_tei_long_v0.csv`?
- Is the v0 canonical field set sufficient for the first architectural-nearness vector: `entrances_count`, `floors_count`, `apartments_count`, `length_m`, `width_m`, `height_m`, `footprint_area_m2`, `building_volume_m3`, `total_area_m2`, `build_year`?
- Is `address_tei_consistency_v0.csv` sufficient as the first quality-control artifact for same-address TEI mismatches?
