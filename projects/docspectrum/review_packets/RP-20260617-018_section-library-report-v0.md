# RP-20260617-018: Section library report v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-17 17:38 MSK - codex]

`[REVIEW]` Combined consumer-facing UC1 report over table-form and text
coverage layers.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@532061b..675c3a4
- focus: review `section_library_report_v0` as the combined table+text section-level report: join logic, headline coverage/residual, organization conformance, review-priority triage, and product-safe interpretation
- inputs: `E:/output/DocSpectrum/typical_element_coverage_v0/typical_element_section_coverage_v0.csv`, `E:/output/DocSpectrum/text_element_library_v0/text_element_section_coverage_v0.csv`, shared context `HC-20260617-010`, `RP-20260617-016`, `RP-20260617-017`
- metrics: `474` documents; `0` missing table rows; `0` missing text rows; review priority `297` clear / `148` watch / `29` high; coverage bands `26` high / `331` medium / `117` low; cohort median headline coverage `NK=0.6415`, `RSPK=0.4569`
- risks/regressions: headline score is an unweighted v0 mean, not calibrated; residual means library-unexplained, not proven original authorship; review priority is triage, not a legal/authorship conclusion; positive foreign-template/copy cases are still needed; graphics/vector/near-match are not included
- not touched: table/text candidate generation, text rarity guard, pairwise scoring, near-match, graphics/vector candidates, embeddings, executor-level handwriting, raw-text artifacts
- domain-rule: follows HC-010 UC1 priority; combines RP-016 table forms and RP-017 text segments into one consumer-facing view; text shingles remain diagnostic and are not included in headline coverage

Purpose:

- Give a single section-level report instead of separate table and text views.
- Preserve the separate source metrics while providing a simple headline layer.
- Support the practical UC1 questions: known library coverage, residual,
  organization conformance, foreign-pattern/copy-review triage.

Code / notes added in DocSpectrum:

- `tools/build_section_library_report_v0.py`
- `notes/section_library_report_v0.md`

Headline metric:

```text
headline_library_coverage_ratio =
  mean(table_form_coverage_ratio, text_segment_coverage_ratio)
```

Only measured available layers are included. `text_word_shingle` coverage is
kept as `text_shingle_coverage_ratio_diagnostic` and excluded from headline
coverage because the shingle layer is saturated.

Main output fields:

- `headline_library_coverage_ratio`
- `headline_library_residual_ratio`
- `headline_coverage_band`
- `headline_org_conformance_ratio`
- `review_priority`
- table source metrics
- text source metrics

Run summary:

```text
document_count: 474
missing_table_coverage_rows: 0
missing_text_coverage_rows: 0
```

Review priority:

```text
review_clear 297
review_watch 148
review_high   29
```

Coverage bands:

```text
high    26
medium 331
low    117
```

Median headline coverage by cohort:

```text
NK    0.6415
RSPK  0.4569
```

Section medians:

```text
section   coverage  residual  org_conformance
АР        0.4905    0.5094    1.0000
ИД        0.6472    0.3528    1.0000
ИОС5.1    0.4832    0.5168    1.0000
ИОС5.4.1  0.5234    0.4766    0.9904
ИОС5.5.1  0.3356    0.6644    1.0000
КР        0.5376    0.4624    1.0000
ПОКР      0.7853    0.2147    1.0000
ПОС       0.4545    0.5455    0.9990
СМ        0.2343    0.7657    0.9947
```

Interpretation:

- The combined report confirms the earlier pattern: NK is more covered by the
  current library than RSPK.
- `ПОКР` is strongly library-covered; `СМ` and `ИОС5.5.1` retain high residual.
- Organization conformance remains near-perfect in this negative-control corpus.
- Review priority is a triage field for inspection, not a proof of copying.

Generated artifact paths:

- `E:/output/DocSpectrum/section_library_report_v0/section_library_report_v0.csv`
- `E:/output/DocSpectrum/section_library_report_v0/section_library_report_v0.json`

Verification:

- `python -m py_compile E:\repos\DocSpectrum\tools\build_section_library_report_v0.py`
- Generated `section_library_report_v0` on current table/text coverage artifacts.
- Checked JSON summary for document count, missing rows, review priority counts, coverage bands and section medians.
- DocSpectrum current commit: `675c3a4 Add section library report v0`.

Known limitations / reviewer attention:

- Headline metric is an unweighted v0 mean; weights are not calibrated.
- Positive foreign-template/copy cases are still required to validate catch ability.
- Graphics, vector forms, image candidates and near-match are absent.
- The report is product-facing in shape, but still research-v0 in calibration.

Open questions for reviewer:

- Is the unweighted mean of table-form coverage and text-segment coverage acceptable for v0?
- Is `review_priority` taxonomy (`clear/watch/high`) safe as triage wording?
- Should the next step be Excel/PQ export for human inspection, or near-match for UC3/Axis C?
- Should the combined report include candidate ID lists, or stay compact and link to source coverage artifacts?
- Does this close HC-010 Build 1 at the consumer-report layer?

Evidence:

- commits: `docspectrum@532061b..675c3a4`
- notes: `notes/section_library_report_v0.md`
- artifacts: paths listed above
- shared context: `HC-20260617-010`, `RP-20260617-016`, `RP-20260617-017`
