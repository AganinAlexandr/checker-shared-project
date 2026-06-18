# RP-20260618-023: Page near-match triage v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 17:22 MSK - codex]

`[REVIEW]` Stable human-ground-truth queue for the 240 body-page candidates
accepted for calibration in RP-022.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@7c6a0f1..ca37f35
- focus: review label taxonomy, preservation of human input across rebuilds, body-only scope, PDF/page linkage, review ordering and Power Query transport
- inputs: RP-022 review shortlist, combined documents index, explorer object-view exports
- metrics: `240/240` candidates joined; `240/240` pairs have existing PDF paths; `30` high-overlap first-batch candidates (`17` ИОС5.4.1, `13` СМ); `210` partial-overlap candidates; all labels initially pending
- risks/regressions: labels are still empty; category boundaries require human calibration; CSV is authoritative while PQ is a read-only view; source paths are local-machine paths
- not touched: near-match scoring/thresholds, typical-element promotion, UC3 verdict logic, title-page near-matches, foreign-to-high triage, graphical signatures
- domain-rule: human labels are external ground truth and are never inferred by the generator; `borrowing_candidate` is a research routing label, not a legal conclusion

Purpose:

- Turn RP-022's research shortlist into a reproducible expert-review process.
- Keep human decisions outside generated artifacts.
- Preserve completed labels when the candidate queue is rebuilt.
- Prevent title-page normative convergence from contaminating body UC3
  calibration.

Canonical editable input:

`E:/commons/DocSpectrum/page_near_match_triage_labels_v0.csv`

Controlled labels:

- `borrowing_candidate`;
- `normative_form`;
- `estimate_boilerplate`;
- `shared_technical_content`;
- `false_positive`;
- `uncertain`.

Confidence:

- `high`;
- `medium`;
- `low`.

Generated queue:

`E:/output/DocSpectrum/page_near_match_triage_v0/page_near_match_triage_queue_v0.csv`

Each row contains both PDF paths/page numbers, object/cohort/section metadata,
structural and hash-only content evidence, plus the joined human-review fields.

Results:

```text
candidate rows:                         240
PDF path coverage:                      240
pending labels:                         240

rare-text high overlap:                  30
  ИОС5.4.1:                              17
  СМ:                                    13

rare-text partial overlap:              210
  ИОС5.4.1:                             143
  СМ:                                    67
```

Review ordering:

1. high-overlap before partial-overlap;
2. higher text-segment Jaccard;
3. higher structural near-match similarity.

Code / note:

- `tools/build_page_near_match_triage_v0.py`;
- `tests/test_page_near_match_triage.py`;
- `analytics/pq/PageNearMatchTriage_v0.pq`;
- `notes/page_near_match_triage_v0.md`.

Verification:

- `python -m unittest discover -s tests -p "test_*.py"`: `5/5 OK`;
- regression test proves an existing human label survives rebuild;
- invalid label values are rejected;
- generated queue checked: `240` rows, `240` pending, `240` valid PDF pairs;
- Cyrillic section/file names verified in the generated UTF-8 CSV;
- `git diff --check`: clean.

Open questions:

- Is the six-label taxonomy sufficient for the first 30-candidate calibration
  batch?
- Should `shared_technical_content` remain separate from `normative_form`?
- Is CSV-as-canonical plus PQ-as-view the right ownership boundary?
- After reviewing the first 30, should we freeze label guidance before
  processing the remaining 210?

Evidence:

- commit: `docspectrum@ca37f35`;
- range: `docspectrum@7c6a0f1..ca37f35`;
- labels: `E:/commons/DocSpectrum/page_near_match_triage_labels_v0.csv`;
- artifacts: `E:/output/DocSpectrum/page_near_match_triage_v0`;
- preceding review: `RP-20260618-022`.
