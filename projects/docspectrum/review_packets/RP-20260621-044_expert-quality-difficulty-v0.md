# RP-20260621-044: Expert quality difficulty proxy v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Experiment C step 5 adds an expert-independent spectral difficulty
proxy and joins it to arena sessions for diagnostic stratification.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@32c8f26..7a82cdd
- focus:
  - raw complexity axes and section-relative normalization;
  - strict separation of difficulty calculation from expert/outcome data;
  - session join correctness;
  - difficulty-vs-clean diagnostic interpretation
- inputs:
  - RP-042 arena pairs (`260`);
  - RP-042 hash-only registry rows;
  - pre-expertise PSE exports
- metrics:
  - arena pairs: `260`;
  - unique arena documents: `199`;
  - joined review rows: `209`;
  - joined sessions: `130`;
  - anchor-role summaries: `4`;
  - experiment-C tests: `20/20`
- risks/regressions:
  - arena-relative sample, not the full project-document population;
  - composite uses equal axis weights, accepted-for-experiment;
  - percentile is relative within KR/POS, not an absolute engineering scale;
  - clean-share is still an outcome proxy, not remark recall;
  - correlation is diagnostic, not causal
- not touched:
  - remark content, recall, or typology;
  - pre/post-expertise delta;
  - arena threshold calibration;
  - owner-identity track
- domain rule:
  - complexity must be independent of expert and review result;
  - difficulty may normalize future recall but cannot define expert quality;
  - raw features must remain available beside the composite

## Difficulty construction

Raw axes:

1. page count;
2. total PDF element count;
3. elements per page;
4. non-text structural elements per page;
5. table cells per page.

For each axis:

- apply `log1p`;
- calculate midrank percentile within the same section code (`KR` or `POS`);
- preserve the raw value and percentile.

The headline `spectral_difficulty_percentile_v0` is the equal mean of the five
percentiles. This avoids comparing KR and POS on one absolute distribution.

Additional diagnostics include maximum page element load and component-mix
entropy.

## Diagnostic result

Session difficulty vs clean-share:

| Role | Sessions | Difficulty mean | Spearman |
|---|---:|---:|---:|
| ceiling-1a | 18 | 0.4404 | +0.1873 |
| ceiling-1b | 5 | 0.4945 | n/a (constant outcome) |
| floor-3 | 77 | 0.4687 | -0.1489 |
| holdout | 24 | 0.5288 | -0.2809 |

Holdout clean-share by difficulty band:

- low: `0.6667` (`n=3`);
- medium: `0.7692` (`n=13`);
- high: `0.5000` (`n=8`).

Difficulty may reduce clean-passes at the high end, but it does not explain
the holdout pattern as “only easy sections passed clean.” The sample is small
and arena-selected, so this is a diagnostic finding, not a causal conclusion.

## Outputs

`E:/output/DocSpectrum/expert_quality_difficulty_v0`

- `expert_quality_document_difficulty_v0.csv`;
- `expert_quality_review_difficulty_v0.csv`;
- `expert_quality_session_difficulty_v0.csv`;
- `expert_quality_difficulty_by_role_v0.csv`;
- `expert_quality_difficulty_v0.json`.

No raw expert names occur in the artifacts.

## Open reviewer questions

1. Are the five raw axes sufficient and non-duplicative for v0?
2. Is within-section percentile the right first normalization boundary?
3. Is equal weighting acceptable while all raw axes remain available?
4. Is the outcome interpretation sufficiently non-causal?
5. Does this complete the currently data-available C-track scaffold before
   remark-content survival work?
