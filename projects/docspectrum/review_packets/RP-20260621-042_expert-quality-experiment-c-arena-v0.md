# RP-20260621-042: Expert quality experiment C arena v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Productized experiment C steps 1-3 from HC-017/T-020:
expert registry parsing, candidate/gold cells, pair-level near-identity gate,
and the cross-expert arena.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@4a0f0e0..8f70c56
- focus:
  - registry filters and clean/remark outcome semantics;
  - candidate/gold group construction;
  - pair-level similarity gate on original pre-expertise KR/POS;
  - holdout/ceiling/floor arena classification;
  - hash-only expert identity and reference-count reproducibility
- inputs:
  - `Объекты MSE_new 2602_24.xlsx`, sheet `данные`, header row 10;
  - `gip_control_registry_expanded_v0/gip_control_sections_v0.csv`;
  - PSE exports in `E:/output/pdf-structure-explorer/exports`
- metrics:
  - source rows: `11003`;
  - kept valid rows: `8605`;
  - candidate groups: `207`;
  - gold groups: `97`;
  - gold groups with at least two exported objects: `57`;
  - groups with at least one near-identical cross-expert pair: `33`;
  - near-identical cross-expert pairs: `260`;
  - holdout-to-floor: `149`;
  - ceiling-to-floor: `53`;
  - holdout-to-ceiling: `34` pairs / `6` cells;
  - unit tests: `9/9`
- risks/regressions:
  - `57` is export coverage, not proof that every pair in every group is near-identical;
  - similarity admission is pair-level (`shingle >= 0.70`);
  - registry outcomes are proxies until remark content is supplied;
  - objects inside one expert/date batch are correlated, not independent sessions;
  - group organization/work-type labels are registry context, not model inputs
- not touched:
  - session-level variance (step 4);
  - spectral difficulty proxy (step 5);
  - remark recall/typing (blocked on human source);
  - pre/post-expertise delta experiment B;
  - owner-identity track
- domain rule:
  - class 1 is the ceiling of the scale, not “easy section”;
  - holdout is conditional/high-variance and must later be evaluated across sessions;
  - arena is an admission set for quality research, not an expert verdict;
  - results remain informing-not-punishing and library-relative

## Reproduced acceptance contract

`--assert-reference` reproduces the probe counts exactly:

| Metric | Reference | Actual |
|---|---:|---:|
| candidate groups | 207 | 207 |
| gold groups | 97 | 97 |
| measured/exported gold groups | 57 | 57 |
| holdout-to-floor pairs | 149 | 149 |
| ceiling-to-floor pairs | 53 | 53 |
| holdout-to-ceiling pairs | 34 | 34 |
| holdout-to-ceiling cells | 6 | 6 |

The strict holdout contrast requires the holdout on exactly one pair side.
Two initially extra pairs had the holdout on both sides and therefore did not
isolate a cross-expert effect.

## New clarification from productionization

The exploratory phrase “57 groups pass near-identity” was too broad.

- `57` groups have at least two exported objects and can be measured;
- `33` contain at least one cross-expert pair with shingle Jaccard `>=0.70`;
- those `33` contribute `260` arena-admissible cross-expert pairs;
- median group-level shingle across all measured groups is `0.5612`, while
  specific admitted pairs form the near-identical substrate.

The artifact now exposes:

- `cross_expert_pair_count`;
- `cross_expert_near_pair_count`;
- `gate_status = passed_pair_level_near_identity` or
  `measured_no_near_identity_pair`.

This is a clarification of the gate denominator, not a change to the
reference arena counts.

## Prime arena

`АО ССУ № 3 / фундамент` remains the strongest holdout-to-ceiling arena:

- KR: `7` admitted pairs;
- POS: `20` admitted pairs;
- combined shingle range `0.7238..0.9247`, mean `0.8730`.

This is an arena substrate only. The February 2025 holdout objects belong to
one batch/session and must not be treated as independent quality observations.

## Privacy and outputs

Expert names are used only in memory. CSV/JSON artifacts contain SHA1 identity
hashes and generic roles:

- `ceiling_1_a`;
- `ceiling_1_b`;
- `floor_3`;
- `holdout`;
- `unlabeled`.

No raw anchor names occur in the generated output.

Output directory:
`E:/output/DocSpectrum/expert_quality_experiment_c_v0`.

## Open reviewer questions

1. Is the pair-level gate correction the right interpretation of the probe?
2. Is “holdout on exactly one side” the correct arena contrast rule?
3. Are candidate/gold filters faithful to HC-017/T-020?
4. Is hash-only expert identity sufficient for this research artifact?
5. Should step 4/5 be the next packet, or should session and difficulty outputs
   remain separate packets to keep the methodological review narrow?
