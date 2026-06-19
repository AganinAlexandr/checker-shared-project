# RP-20260619-032: GIP-control near-match layer

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 16:30 MSK - codex]

`[REVIEW]` Follow-up to RP-031. This packet adds the first document-level
near-match layer on top of the existing `605` GIP pairwise rows, without
changing the registry or the baseline H1/H2 cell design.

Goal:

- relax the structural/style side beyond exact page signatures;
- measure whether that relaxation strengthens the already observed H1 signal;
- inspect whether H2 becomes clearer or more plausibly interpretable.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@78a0579..577a419
- focus: review the near-match augmentation method itself
  (`best bidirectional page matches -> document-level summary`) and the H1/H2
  interpretation of the first results
- inputs:
  - `E:/output/DocSpectrum/gip_control_baseline_v0_1/gip_control_pairwise_v0.csv`
  - `E:/output/pdf-structure-explorer/exports/doc_*`
- metrics:
  - pairwise input rows: `605`
  - profiled documents: `192`
  - enriched pair rows: `605`
  - best-page-match rows: `36447`
  - summary rows: `52`
  - H1 overall raw-pair medians:
    - page near structural mean: `same 0.6760` vs `diff 0.5777`
    - page near shingle mean: `same 0.1268` vs `diff 0.0054`
    - page near strong-share: `same 0.3704` vs `diff 0.0000`
    - exact page share: `same 0.0` vs `diff 0.0`
  - H2 overall raw-pair medians:
    - page near structural mean: `same_org 0.7049` vs `cross_org 0.7020`
    - page near shingle mean: `same_org 0.1114` vs `cross_org 0.1280`
    - page near strong-share: `same_org 0.5271` vs `cross_org 0.2118`
- risks/regressions:
  - this is still a document-level near layer, not yet explicit provenance
    subtraction
  - page matching is greedy best-per-page in both directions, not global optimal
    assignment; chosen for explainability and speed
  - no title/body separation yet in this layer
  - H2 remains sensitive to small same-org baselines and subcontractor transfer
  - page exact share is effectively zero across the board, so all useful signal
    here comes from relaxed matching rather than exact signature recurrence
- not touched:
  - registry/source-of-truth joins
  - baseline v0.1 headlines
  - provenance queue/labels
  - page-level borrowing triage
- domain rule:
  - direct implementation of RP-030 review conclusion:
    `near-match first, provenance-residual second`

Implementation:

```text
577a419  add GIP control near match layer
```

What changed:

- added `tools/build_gip_control_near_match_v0.py`
  - reads the already-built GIP pairwise baseline rows
  - profiles every page directly from flat explorer exports
  - computes all cross-document page-pair structural near scores
  - picks best page matches bidirectionally (`left->right`, `right->left`)
  - aggregates them into document-level near metrics
- added `tests/test_build_gip_control_near_match_v0.py`
  - tie-break preference toward stronger shingle overlap
  - bidirectional summary aggregation smoke test

Artifacts:

- `E:/output/DocSpectrum/gip_control_near_match_v0`

Verification:

- `tests/test_build_gip_control_near_match_v0.py` -> `2/2 OK`
- import smoke for `build_gip_control_near_match_v0.py` -> OK
- `build_gip_control_near_match_v0.py` completed on real data

Primary findings:

- H1 becomes materially stronger on the relaxed page/document layer:
  - structural near: `0.6760 > 0.5777`
  - shingle-near: `0.1268 > 0.0054`
  - strong-share: `0.3704 > 0.0000`
- this supports the RP-030 review argument that exact matching was too strict:
  page exact share stays `0.0` even where near-match now separates same-GIP
  from diff-GIP clearly.
- strongest positive H1 cells on the near layer include:
  - `ПОС/плоская`
  - `ПОС/скатная`
  - `КР/плоская`
  - `ПЗ/скатная`
  - `СМ/скатная`
- H2 stays mixed but becomes more structured:
  - cross-org remains extremely close to same-org on structural near overall
  - same-org retains a much higher strong-share overall
  - cross-org can still beat same-org on shingle-near in some cells, which is
    compatible with the subcontractor-transfer hypothesis rather than a simple
    failure mode

Open questions:

- Does Opus accept the bidirectional-best-page aggregation as an adequate v0
  near layer before any heavier optimal matching / residual subtraction?
- Is the next step now clearly:
  1. provenance-residual subtraction on top of this near layer, or
  2. title/body separation first, then residual subtraction?

