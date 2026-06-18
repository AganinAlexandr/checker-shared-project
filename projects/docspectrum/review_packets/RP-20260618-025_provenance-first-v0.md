# RP-20260618-025: Provenance-first authorship layer v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 20:44 MSK - codex]

`[REVIEW]` Explicit provenance/authorship gate before organization handwriting
and borrowing interpretation, implementing HC-012.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@6552e7e..3b7b678
- focus: review provenance schema/taxonomy, conservative borrowing gate, mapping of 30 expert labels, organization-authored residual rule and stratified selection of the next 16 cases
- inputs: HC-012; RP-024 canonical labels; `page_near_match_triage_queue_v0.csv`
- metrics: `240` assessed rows; `30 third_party/ineligible/confirmed_non_copy`; `210 unknown/blocked_unassessed/not_assessable`; `0 eligible_for_review`; stratified sample `16` (`8 ИОС5.4.1 + 8 СМ`)
- risks/regressions: provenance inference is not automated; no positive borrowing control; historical v0 library classes remain unchanged and may still contain pre-HC-012 names; sample strata are corpus-relative
- not touched: near-match scoring, borrowing thresholds, typical-element historical artifacts, remaining human labels, positive-control corpus
- domain-rule: provenance precedes borrowing; unassessed never defaults to organization-authored; third-party material is removed from organization/GIP/executor handwriting residuals

Purpose:

- Make the RP-024/HC-012 conclusion machine-readable.
- Prevent rare cross-organization overlap from being interpreted as copying
  before authorship/source is established.
- Define the organization-authored residual for Axis C.
- Select the next review batch by provenance novelty rather than top score.

Provenance output fields:

- `provenance_status`;
- `authorship_scope`;
- `source_class` / `source_subclass`;
- `distinctiveness_class`;
- `borrowing_eligibility`;
- `borrowing_signal_status`;
- `reason_code`;
- `provenance_confidence` and evidence reference;
- `org_authored_residual_status`.

Current mapping:

```text
normative_form
  -> third_party / external_form
  -> ineligible_third_party / confirmed_non_copy

estimate_boilerplate
  -> third_party / software_generated
  -> ineligible_third_party / confirmed_non_copy

shared_technical_content
  -> third_party / vendor_technical_material
  -> ineligible_third_party / confirmed_non_copy

pending
  -> unknown / unassessed
  -> blocked_unassessed / not_assessable
```

Borrowing eligibility:

- only expert-supported `organization_authored + distinctive` becomes
  `eligible_for_review`;
- eligibility is still not a borrowing verdict;
- no current row is eligible.

Result:

```text
candidate_count:                                   240
expert_assessed / third_party:                      30
unassessed / unknown:                              210
ineligible_third_party / confirmed_non_copy:        30
blocked_unassessed / not_assessable:               210
eligible_for_review:                                 0
```

Next review sample:

- selected from the 210 pending cases;
- stratified by `section × text-overlap-band × structure/text-ratio-band`;
- two cases per populated stratum;
- result: `16` cases, balanced `8 ИОС5.4.1 / 8 СМ`;
- selection is not descending score.

Current populated strata:

```text
ИОС5.4.1: high/text_stronger, low/structure_dominant,
          middle/structure_dominant, middle/text_stronger
СМ:       high/text_stronger, low/structure_dominant,
          middle/structure_dominant, middle/text_stronger
```

Code / schemas:

- `tools/build_provenance_assessment_v0.py`;
- `schemas/provenance_assessment_model.md`;
- `schemas/comparison_result_model.md` (`provenance_assessment` block 16);
- `tests/test_provenance_assessment.py`;
- `notes/provenance_assessment_v0.md`.

Analytics / human interface:

- `analytics/pq/ProvenanceAssessment_v0.pq`;
- `analytics/pq/ProvenanceReviewSample_v0.pq`;
- `E:/commons/DocSpectrum/page_provenance_sample16_review_v0.html`;
- `E:/commons/DocSpectrum/page_provenance_sample16_compact_v0.csv`.

Artifacts:

- `E:/output/DocSpectrum/provenance_assessment_v0/page_provenance_assessment_v0.csv`;
- `E:/output/DocSpectrum/provenance_assessment_v0/page_provenance_review_sample_v0.csv`;
- `E:/output/DocSpectrum/provenance_assessment_v0/provenance_assessment_v0.json`.

Verification:

- `python -m unittest discover -s tests -p "test_*.py"`: `14/14 OK`;
- third-party label blocks borrowing: regression-tested;
- unassessed never defaults to org-authored: regression-tested;
- sample IDs unique: `16/16`;
- sample section balance: `8/8`;
- compact sample CSV: `16` rows / `0` multiline cells;
- review HTML: `16` cards with isolated local storage;
- `git diff --check`: clean.

Open questions:

- Is the provenance taxonomy sufficient for v0, especially
  `external_form` vs `regulatory_material`?
- Is `blocked_unassessed` the correct conservative default?
- Is the 16-case stratification suitable for the next provenance calibration?
- Should the legacy table/text library classes be migrated immediately, or
  remain versioned historical outputs until provenance has broader labels?

Evidence:

- commit/range: `docspectrum@6552e7e..3b7b678`;
- shared context: `HC-20260618-012`;
- preceding review: `RP-20260618-024`.
