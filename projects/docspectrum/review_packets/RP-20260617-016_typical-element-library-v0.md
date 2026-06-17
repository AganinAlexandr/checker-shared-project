# RP-20260617-016: Typical element library v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-17 16:29 MSK - codex]

`[REVIEW]` First consumer-facing typical-element library layer: table-centric
candidates, organization distinctiveness, and section-level coverage/residual.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@6a365d2..9753855
- focus: review the first `typical_element_candidates_v0` library layer end-to-end: candidate promotion rules, hash-only evidence, P1 coverage, P2 organization distinctiveness, derived `candidate_class`, trivial micro-form handling, and the new section coverage output for UC1a/UC1v table-layer checks
- inputs: `E:/output/DocSpectrum/element_base_v0_rpsk35_nk34`, `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`, `E:/output/DocSpectrum/export` (RSPK35 cohort root), `E:/output/DocSpectrum/export_nk_34_object_view` (NK34 cohort root), shared context `T-20260617-016`, `HC-20260617-009`, `HC-20260617-010`
- metrics: `531` table-form candidates; `7485` evidence rows; `426` meaningful forms; `105` diagnostic trivial micro-forms; `522` org-specific, `7` cross-org common, `2` org-distinctive; `0` borrowing candidates in the RSPK-vs-NK negative-control corpus; coverage built for `474/474` documents
- risks/regressions: v0 covers table forms only, not full section originality; exact `table_layout_signature` remains too strict until near-match; `residual` means unexplained by the v0 table-form library, not proven original authorship; org-conformance has only a negative-control test so far and still needs positive foreign-form/borrowing cases; current org-distinctiveness thresholds are experimental for two cohorts
- not touched: pairwise scoring weights, v0.3 similarity metrics, page/graphics/vector candidates, text-only candidate promotion, embeddings, near-match/bucketing, executor-level handwriting, raw-text artifacts
- domain-rule: follows HC-010 consumer-use-case priority and T-016 library design; `form coverage != copied content != original residual`; using organization templates is expected, while suspicious copying requires content-level or residual-level evidence beyond the typical form layer; cohort labels are applied after candidate extraction and do not affect core extraction

Purpose:

- Start the library as a product-facing layer, not a decorative catalog of entities.
- Convert recurring table layout signatures into candidate typical forms.
- Preserve per-occurrence evidence separately from the thin candidate row.
- Add P1 coverage and P2 organization distinctiveness immediately, per HC-010/T-016.
- Provide the first section-level answer to: what part of the table layer is explained by known typical forms, what remains residual, and whether the matched forms look like the document organization's own handwriting.

Code / notes added in DocSpectrum:

- `tools/build_typical_element_candidates_v0.py`
- `tools/build_typical_element_coverage_v0.py`
- `notes/typical_element_candidates_v0.md`

Candidate model:

- v0 starts table-centric.
- One candidate is one `(section_code, table_layout_signature)` group.
- Promotion threshold: at least `3` distinct objects.
- `candidate_id` is hash-derived from section, candidate kind and signature group.
- Candidate row stays thin; occurrence-level locations and table-cell hashes are stored in a separate evidence table.
- Coverage is counted by distinct objects/documents, not raw occurrences.
- Organization distinctiveness is computed after extraction.
- Raw text is not written; table-cell evidence stores hashes only.

Candidate classes and statuses:

- `candidate_class` is derived:
  - `cross_org + same_content/mostly_same_content` -> `borrowing_candidate`;
  - otherwise `typical_form`.
- In this corpus, `borrowing_candidate = 0`, so the RSPK-vs-NK contrast remains a clean negative borrowing control.
- Small stamp-like forms are not dropped silently.
- They are retained as `candidate_status = diagnostic_trivial` with `triviality_status = trivial_micro_form`.
- Main coverage excludes diagnostic trivial forms by default.

Candidate results:

```text
candidate_count: 531
evidence_row_count: 7485
table_cell_hash_cache_document_count: 473
skipped_layout_groups_below_min_objects: 1830
```

Candidate counts by section:

```text
АР        21
ИД        28
ИОС5.1    86
ИОС5.4.1 183
ИОС5.5.1  74
КР        24
ПОКР      22
ПОС       22
СМ        71
```

Organization distinctiveness:

```text
cross_org_common   7
org_distinctive    2
org_specific     522
```

Candidate status:

```text
meaningful_form       426
trivial_micro_form    105
```

IOS5.4.1 seed interpretation:

- The expected bridge was promoted as cross-organization table-form candidates.
- Main seed shape: `8 rows x 4 columns / 22 cells`.
- Object distribution: `NK:10 | RSPK:3`.
- Content signatures vary.
- Correct interpretation: stable form, variable content.
- Therefore it is a normative/typical form candidate, not borrowing by itself.

Section coverage model:

- New tool: `tools/build_typical_element_coverage_v0.py`.
- Output: `typical_element_section_coverage_v0.csv`.
- Coverage is table-form-only in v0.
- `table_form_coverage_ratio` counts meaningful matched table-form occurrences over all table occurrences.
- `table_form_residual_ratio` is unexplained by the v0 table-form library, not proven original authorship.
- `org_form_conformance_ratio` counts matched forms that are either cross-org common or dominant for the document cohort.
- `foreign_org_form_ratio` detects non-common forms whose dominant organization differs from the document cohort.

Coverage result:

- documents measured: `474/474`;
- median org conformance ratio: `1.0` across all sections;
- foreign organization form ratio: `0.0` in the current negative-control corpus;
- borrowing candidate occurrences: `0`.

Median table-form coverage by section:

```text
АР        0.5000
ИД        0.8966
ИОС5.1    0.6818
ИОС5.4.1  0.7143
ИОС5.5.1  0.4615
КР        0.6000
ПОКР      0.9667
ПОС       0.5903
СМ        0.2222
```

Interpretation:

- P2 works: the library separates a large organization-specific layer from a small cross-organization normative layer.
- The RSPK-vs-NK corpus remains a negative borrowing control: no cross-org same/mostly-same content table-form candidates.
- Section coverage gives the first practical UC1a/UC1v table-layer report.
- The high `ПОКР`/`ИД` coverage and low `СМ` coverage look plausible and should be treated as a first sanity signal, not a final product metric.
- `org_form_conformance_ratio = 1.0` is expected here because this corpus does not include positive foreign-template cases.

Generated artifact paths:

- `E:/output/DocSpectrum/typical_element_candidates_v0/typical_element_candidates_v0.csv`
- `E:/output/DocSpectrum/typical_element_candidates_v0/typical_element_candidate_evidence_v0.csv`
- `E:/output/DocSpectrum/typical_element_candidates_v0/typical_element_candidates_v0.json`
- `E:/output/DocSpectrum/typical_element_coverage_v0/typical_element_section_coverage_v0.csv`
- `E:/output/DocSpectrum/typical_element_coverage_v0/typical_element_section_coverage_v0.json`

Verification:

- `python -m py_compile` on `build_typical_element_candidates_v0.py` and `build_typical_element_coverage_v0.py`.
- Regenerated candidate artifacts on RSPK35+NK34.
- Generated coverage artifacts on RSPK35+NK34.
- Checked JSON summaries for candidate counts, class counts, triviality counts, org-distinctiveness counts, and section median coverage.
- DocSpectrum current commit: `9753855 Add typical element coverage v0`.

Known limitations / reviewer attention:

- Coverage is only table-form coverage.
- Full UC1v "share created by performer" still needs text, graphics, near-match and executor labels.
- Positive copy-catching cases are not present in RSPK-vs-NK, so `foreign_org_form_ratio` and `borrowing_candidate` are hooks validated only as negative controls.
- Exact layout signatures miss near-identical forms and should not be the final mechanism for UC3.
- Micro-forms are flagged but still present in the candidate catalog for diagnostics.

Open questions for reviewer:

- Does the candidate-class derivation close the UC3 hook sufficiently for v0?
- Is `diagnostic_trivial` the right handling for small stamp-like forms, or should they be split into a separate artifact later?
- Is the section coverage report a sufficient close of HC-010 "Build 1" for table-layer UC1a/UC1v?
- Should the next step prioritize near-match for table/page forms, or expand coverage to text candidates first?
- Should `org_form_conformance_ratio` remain a simple ratio, or should it be weighted by candidate IDF / distinctiveness in v0.1?

Evidence:

- commits: `docspectrum@6a365d2..9753855`
- notes: `notes/typical_element_candidates_v0.md`
- artifacts: paths listed above
- shared context: `T-20260617-016`, `HC-20260617-009`, `HC-20260617-010`
