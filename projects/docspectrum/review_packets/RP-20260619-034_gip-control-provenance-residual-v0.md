# RP-20260619-034: GIP-control provenance residual v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 22:10 MSK - codex]

`[REVIEW]` This packet adds the agreed `provenance-residual` layer on top of the
clean `571`-pair near-match corpus (`RP-033`). The goal is narrow and explicit:
subtract page-match rows that look like already expert-confirmed
**third-party-authored** material, then re-read the H1/H2 signals on the
residual.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@d393c4c..ac14432
- focus: review only the new third-party subtraction layer for GIP-control:
  calibrated provenance bands, page-match labeling, residual recomputation, and
  the resulting H1/H2 shifts
- inputs:
  - `E:/output/DocSpectrum/gip_control_near_match_v0/*`
  - `E:/output/DocSpectrum/provenance_assessment_v0/page_provenance_assessment_v0.csv`
  - export roots used to resolve reviewed prototype pages:
    - `E:/output/pdf-structure-explorer/exports`
    - `E:/output/DocSpectrum/export`
    - `E:/output/DocSpectrum/export_nk_34`
    - `E:/output/DocSpectrum/export_nk_34_object_view`
    - `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`
- metrics:
  - residual corpus:
    - pair rows: `571`
    - page-match rows: `29651`
    - reviewed provenance calibration pairs: `30`
    - third-party matched page-match rows: `99`
    - affected pairs: `14`
    - all-excluded pairs: `0`
  - calibrated third-party bands:
    - `estimate_boilerplate` (`СМ`, n=`13`)
      - structural min `0.8649`
      - text-segment min `0.5023`
      - shingle min `0.48`
    - `normative_form` (`ИОС5.4.1`, n=`13`)
      - structural min `0.9330`
      - text-segment min `0.5000`
      - shingle min `0.9459`
    - `shared_technical_content` (`ИОС5.4.1`, n=`4`)
      - structural min `0.8911`
      - text-segment min `0.5000`
      - shingle min `1.0000`
  - matched labels in the wide GIP corpus:
    - `estimate_boilerplate 99`
    - `normative_form 0`
    - `shared_technical_content 0`
  - H1 overall before -> residual:
    - `same_gip`
      - shingle mean median `0.1155 -> 0.0727`
      - strong-share median `0.3196 -> 0.3077`
    - `diff_gip`
      - shingle mean median `0.0025 -> 0.0025`
      - strong-share median `0.0 -> 0.0`
  - H2 overall before -> residual:
    - `same_org`
      - shingle mean median `0.0668 -> 0.0668`
      - strong-share median `0.3333 -> 0.3333`
    - `cross_org`
      - shingle mean median `0.1230 -> 0.1230`
      - strong-share median `0.1639 -> 0.1639`
  - section-local effect:
    - all matched third-party rows fall in `СМ`
    - strongest local H1 attenuation is also in `СМ`
    - example: H1 / same_gip / `СМ` / `чердак`
      - shingle `0.4167 -> 0.2114`
      - strong-share `0.5000 -> 0.1905`
- risks/regressions:
  - current subtraction is intentionally conservative and only uses
    **expert-confirmed third-party labels** as calibrated minimum bands
  - no new organization-authored / eligible borrowing cases are inferred here
  - `ИОС5.4.1` prototype classes did not match the current GIP corpus under
    these strict bands; this may be true absence or a sign that a looser
    second-stage band / near family is still needed
  - the layer is page-match based, not document-semantic; it validates the
    near-signal source before any stronger borrowing interpretation
- not touched:
  - title/GIP registry
  - alias canon
  - baseline or near scorer logic
  - section-family selection
  - borrowing eligibility / legal framing
- domain rule:
  - HC-012 provenance-first: third-party authored material must be removed
    before reading organization/GIP handwriting residuals

Implementation:

```text
ac14432  add GIP provenance residual layer
```

What changed:

- new tool: `tools/build_gip_control_provenance_residual_v0.py`
  - loads the clean `571`-pair near corpus
  - re-materializes the `30` reviewed provenance cases as actual page metrics
  - calibrates conservative minimum bands per reviewed third-party label
  - labels page-match rows in the GIP corpus if they satisfy a reviewed band
  - recomputes per-pair residual near metrics after excluding those rows
- new tests: `tests/test_build_gip_control_provenance_residual_v0.py`
  - band calibration
  - section-aware page-match classification
  - all-excluded vs measured residual summary behavior

Artifacts:

- `E:/output/DocSpectrum/gip_control_provenance_residual_v0`
  - `gip_control_page_match_provenance_v0.csv`
  - `gip_control_pairwise_provenance_residual_v0.csv`
  - `gip_control_provenance_residual_summary_v0.csv`
  - `gip_control_provenance_bands_v0.csv`
  - `gip_control_provenance_residual_v0.json`

Verification:

- local tests:
  - `test_build_gip_control_provenance_residual_v0.py` -> `3/3 OK`
- builder run:
  - `python E:\repos\DocSpectrum\tools\build_gip_control_provenance_residual_v0.py`
- artifact sanity:
  - `pair_count = 571`
  - `third_party_matched_page_count = 99`
  - `all_excluded_pair_count_v0_3 = 0`
  - matched labels are only `estimate_boilerplate`

Primary reading after subtraction:

- the H1 signal **survives** third-party subtraction:
  - same-GIP remains clearly above diff-GIP on shingle-near and strong-share
- the subtraction is **not cosmetic**:
  - it removes a real third-party component from `СМ`
  - the biggest local attenuation is in `СМ`, exactly where smeta boilerplate
    was expected
- the wide H2 picture is largely unchanged because the current third-party hits
  are sparse and mostly local to the H1 `СМ` cells

Open questions:

- Does Opus agree that the current residual layer is methodically correct as a
  first conservative provenance subtraction, even though only
  `estimate_boilerplate` fired on the wide GIP corpus?
- Should the next refinement stay on the provenance side
  (`normative/vendor` family widening for `ИОС5.4.1`), or is the cleaner next
  front now H2 alias-canon / cross-org interpretation?
