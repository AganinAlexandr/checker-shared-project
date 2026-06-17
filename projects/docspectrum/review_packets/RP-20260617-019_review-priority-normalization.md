# RP-20260617-019: Review priority normalization

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-17 20:41 MSK - codex]

`[REVIEW]` Follow-up fix for the RP-018 must-fix: normalize combined-report
`review_priority` so large clean documents do not become false red alerts.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@675c3a4..44f01ef
- focus: review only the `review_priority` triage fix in `section_library_report_v0`: normalized copy-review ratio, high/watch taxonomy, updated report columns and note
- inputs: `E:/output/DocSpectrum/section_library_report_v0`, source coverage artifacts from RP-018
- metrics: `review_high` changed from `29` in RP-018 baseline to `0`; `review_watch` is `95`; `review_clear` is `379`; max `text_copy_review_needed_ratio` is `0.0202`
- risks/regressions: high-priority positive cases are not yet available, so thresholds remain provisional; foreign text ratio is downgraded to watch-only in v0; triage remains a product hint, not evidence
- not touched: combined report headline coverage, table/text coverage generation, text rarity guard, candidate libraries, pairwise scoring, near-match, graphics/vector layers
- domain-rule: clean RSPK/NK is a negative-control corpus; UC1 triage must not produce red alerts from document size alone

What changed:

- Added `text_copy_review_needed_ratio = copy_review_needed / text_segment_count`.
- `review_high` now requires:
  - table borrowing candidate occurrence, or
  - high normalized copy-review ratio (`>= 0.05`).
- `review_watch` now covers:
  - foreign organization ratio (`>= 0.05`), or
  - low normalized copy-review ratio (`>= 0.005`).
- Absolute `copy_review_needed_occurrence_count` remains as a source column but no longer drives red alerts.
- Foreign text ratio is watch-only in v0 because RP-018 showed it can be a clean-corpus artifact, especially in large text-heavy documents.

Before:

```text
review_clear 297
review_watch 148
review_high   29
```

After:

```text
review_clear 379
review_watch  95
review_high    0
```

Additional check:

```text
max text_copy_review_needed_ratio: 0.0202
```

Interpretation:

- The size artifact identified in RP-018 is removed.
- The clean negative-control corpus no longer produces red alerts.
- Yellow watch signals remain for later inspection/calibration.
- Positive foreign-template/copy cases are still required before product threshold calibration.

Generated artifact paths:

- `E:/output/DocSpectrum/section_library_report_v0/section_library_report_v0.csv`
- `E:/output/DocSpectrum/section_library_report_v0/section_library_report_v0.json`

Verification:

- `python -m py_compile E:\repos\DocSpectrum\tools\build_section_library_report_v0.py`
- Regenerated `section_library_report_v0`.
- Checked JSON `review_priority_counts`.
- Checked CSV aggregate: `high=0`, `watch=95`, `clear=379`, max ratio `0.0202`.
- DocSpectrum current commit: `44f01ef Normalize section report review priority`.

Open questions for reviewer:

- Does this close the RP-018 must-fix?
- Is foreign text ratio as watch-only acceptable until positive cases exist?
- Should thresholds stay as v0 constants, or move into a small contract/config before Excel/PQ export?

Evidence:

- commits: `docspectrum@675c3a4..44f01ef`
- notes: `notes/section_library_report_v0.md`
- shared context: `RP-20260617-018`
