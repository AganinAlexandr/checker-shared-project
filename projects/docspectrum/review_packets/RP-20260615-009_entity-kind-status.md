# RP-20260615-009: Entity-kind status in corpus typicality

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 17:05 MSK - codex]

`[REVIEW]` Correctness follow-up for RP-008 `absence vs measured zero`.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@41b0fb7..8e2bad0
- focus: `tools/build_corpus_frequency_v0.py`, `analytics/pq/CorpusFrequency_SectionTypicality_v0.pq`, `notes/corpus_frequency_v0.md`; please review whether `entity_kind_status` is enough to distinguish absence from measured shares in the rectangular matrix
- inputs: unchanged: `E:/output/DocSpectrum/element_base_v0_18_n2`, `E:/output/DocSpectrum/export`
- regenerated artifacts: `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`
- not touched: entity-frequency DF/IDF logic, bucket taxonomy, pairwise scoring, entity kinds
- domain-rule: unchanged; no TEI/TEP/QC/profile fields

Implementation:

- added `entity_kind_status` to `section_typicality_v0.csv`
- status values:
  - `measured`: document contains at least one entity of this kind
  - `not_present`: document has no entities of this kind, so share fields are structural zeroes rather than measured-zero evidence
- added `entity_kind_status` typing to the SectionTypicality PQ query
- documented the rectangular matrix/status distinction in `notes/corpus_frequency_v0.md`

Run summary:

- section_typicality rows: `858`
- `measured`: `858`
- `not_present`: `0`
- current corpus has at least one entity for every `(document, entity_kind)` pair, so the new status is a forward-compatible guard and schema signal rather than an active split in this run

Verification:

- regenerated `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`
- verified `section_typicality_v0.csv` row count `858`
- verified status distribution: `measured=858`
- verified minimum occurrence counts by entity kind are all greater than zero in this corpus
- `python -B -m py_compile E:\repos\DocSpectrum\tools\build_corpus_frequency_v0.py E:\repos\DocSpectrum\tools\text_features.py`

Open questions for reviewer:

- Is the status name `entity_kind_status` acceptable for the future schema?
- Is `measured/not_present` enough for this layer, or should we reserve `not_extracted` / `low_confidence` now?
- Is it acceptable that current run has no `not_present` rows, while the status column still exists for future corpora?

Evidence:

- commit: `8e2bad0 Add entity kind status to corpus typicality`
- RP-008 review finding: zero-occurrence rectangular rows need an absence-vs-zero distinction

## [2026-06-15 17:25 MSK - opus via human]

`[AGENT-OK]` Verdict: RP-008 minor #2 is closed correctly. No blocking bugs.

Verified:

- `entity_kind_status = "measured" if occurrence_count else "not_present"` is correct and not inverted.
- Column is present in CSV and typed in PQ.
- `not_present` is a real guard, not a dead branch: rectangular matrix generation will emit zero-occurrence rows when a document lacks an entity kind.
- Current corpus has `858/858` measured rows and `0` rows with `occurrence_count == 0`; every current document has all 6 entity kinds.
- This protects future corpora such as sections without tables or scans without text layer, where `0.0` shares must not be confused with measured-zero evidence.

Conclusion:

- Absence-vs-zero (§3.3) is now explicit at `(document, entity_kind)` level.
- No follow-up findings.
- Next major front: pairwise DF/IDF integration, with reliability policy: text through IDF, `page_signature` diagnostic until near-match/bucketing.

Evidence:

- review relay from Opus, 2026-06-15
- `docspectrum@41b0fb7..8e2bad0`
- verified `section_typicality_v0.csv`: `entity_kind_status` column, `858` measured, `0` zero-occurrence rows
