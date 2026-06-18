# RP-20260618-022: Page near-match v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 17:04 MSK - codex]

`[REVIEW]` Generic page-level near-match candidate generation, exact-baseline
guard and cross-organization title/body evaluation.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@b211535..7c6a0f1
- focus: review domain-neutral shortlist/scoring features, exact-signature recall guard, core/profile boundary, rare-text/table evidence, review-shortlist policy and interpretation limits
- inputs: `element_base_v0_rpsk35_nk34`, combined RSPK35+NK34 explorer exports, `corpus_frequency_v0_rpsk35_nk34`, shared RSPK title registry, DocSpectrum NK title input
- metrics: `10826` pages; `945` title pages; exact recall `5481/5481`; `367` cross-org title moderate near-matches unlocked; `240` unique review candidates (`30` high + `210` partial rare-text overlap)
- risks/regressions: experimental weights and thresholds; only two organizations; shortlist is not proof of copying; current graphical evidence is limited to element counts/group areas; `numpy` is a new declared dependency
- not touched: PDF extraction core, product scoring, typical-element promotion, UC3 verdicts, foreign-to-high triage, graphical/vector signatures, GIP/executor attribution
- domain-rule: page scorer uses no section/title/cohort labels and excludes page size; cohort/title metadata is joined only for evaluation/search mode; content hashes are diagnostic evidence after structural matching

Purpose:

- Replace exact-only page comparison with a near-match layer while preserving
  exact matches.
- Test the unlock predicted by RP-021, where cross-organization exact title
  matches were zero.
- Produce a narrow, hash-only research shortlist for later UC3 calibration.

Method:

- approximate shortlist: normalized count/composition/area vectors;
- final structural score: count ratio `0.30`, count composition `0.20`, area
  ratio `0.25`, area composition `0.15`, total element ratio `0.10`;
- same-document pages excluded;
- exact-signature candidates injected before reranking;
- content evidence: text-segment Jaccard, global DF/IDF rarity and table
  layout/content overlap;
- page size excluded from shortlist and final score.

Results:

```text
pages:                                      10826
neighbor rows:                             108260
pages with cross-document exact match:       5481
exact matches recovered at top rank:         5481
exact recall:                                  1.0

title pages:                                  945
cross-org moderate title near-match:          367
cross-org weak title match:                   578
cross-org title similarity p10/median/p90:
0.6417 / 0.6765 / 0.8110

review candidates:                            240
rare-text high overlap:                        30
rare-text partial overlap:                    210
ИОС5.4.1 <-> ИОС5.4.1:                        160
СМ <-> СМ:                                     80
```

Interpretation:

- Near-match opens a real structural bridge that exact page signatures could
  not represent.
- Exact recall does not regress.
- The review shortlist is deliberately framed as
  `research_shortlist_not_proof_of_copying`.
- Concentration in `ИОС5.4.1` and `СМ` is a hypothesis for evidence review:
  engineering reuse, normative forms and estimate boilerplate must still be
  separated.

Code / note:

- `tools/build_page_near_match_v0.py`;
- `notes/page_near_match_v0.md`;
- `requirements.txt`.

Outputs:

- `E:/output/DocSpectrum/page_near_match_v0/page_near_match_neighbors_v0.csv`;
- `E:/output/DocSpectrum/page_near_match_v0/page_near_match_review_candidates_v0.csv`;
- `E:/output/DocSpectrum/page_near_match_v0/title_page_near_match_summary_v0.csv`;
- `E:/output/DocSpectrum/page_near_match_v0/page_near_match_v0.json`.

Verification:

- `python -m unittest discover -s tests -p "test_*.py"`: `3/3 OK`;
- Python syntax compilation: OK;
- exact recall independently recomputed from the output CSV: `5481/5481`;
- `git diff --check`: clean.

Open questions:

- Are the structural weights acceptable as a v0 candidate generator?
- Does exact-candidate injection correctly protect the exact baseline?
- Is the rarity-gated review shortlist conservative enough for manual
  calibration?
- Should the next step inspect/calibrate the `240` candidates before feeding
  near-match into the typical-element library and UC3?

Evidence:

- commit: `docspectrum@7c6a0f1`;
- range: `docspectrum@b211535..7c6a0f1`;
- note: `notes/page_near_match_v0.md`;
- artifacts: `E:/output/DocSpectrum/page_near_match_v0`;
- preceding review: `RP-20260618-021`.
