# T-20260629-024: DocSpectrum CODE-3 pilot — golden regression

Date: 2026-06-29
Project: DocSpectrum
Status: focused_review_packages_ready
Profile: CODE-3 pilot under proposed DEC-20260629-008

## [2026-06-29 · codex]

`[CODE3-CYCLE-OPEN]` First progressive dual-review cycle for the golden regression layer.

### Frozen snapshot

- generator: Codex
- repo: `AganinAlexandr/DocSpectrum`
- range: `docspectrum@e00f34b..6285ddd`
- package_id: `CODE3_DS_GOLDEN_20260629_001`
- work type: validation infrastructure
- claim: golden anchors protect previously validated behavior; they do not establish new model accuracy

### Focused packages

Local common path:

- `E:\commons\DocSpectrum\review_packages\CODE3_DS_GOLDEN_20260629_001\CODE3_DS_GOLDEN_20260629_001_A_FOCUSED.zip`
- SHA256: `D766FBEF2AAE627A84D92CC7ACD080D78A72DBAAB0F58B136063E5AC85E8F1D2`
- `E:\commons\DocSpectrum\review_packages\CODE3_DS_GOLDEN_20260629_001\CODE3_DS_GOLDEN_20260629_001_B_FOCUSED.zip`
- SHA256: `18DF21C1B43C0041D9A752247D238FAAB994A3F2EBC3B2A9B9CB2521F2D73B13`

Evidence files inside A/B are byte-identical. Only scope/instruction/response files differ:

- A: mandatory unguided sweep, then full guided question overlay;
- B: mandatory unguided, less-guided risk review.

### Included

- complete range diff and commit stat;
- four changed files;
- generated 18-anchor summary and per-anchor results;
- package-local SHA256 manifest;
- response template.

### Physically withheld from focused stage

- repository access and browsing;
- HC/RP history behind anchor values;
- underlying source JSON/CSV artifacts;
- full corpora and 13-object holdout;
- every other reviewer result.

### Generator verification (not a review verdict)

- unit tests: 2/2 passed;
- golden assertions: 18/18 passed;
- DocSpectrum worktree clean; `origin/main=6285ddd`.

### Next sequence

1. Human provides A and B ZIPs to separate fresh chat sessions.
2. A/B do not see each other's outputs.
3. `FOCUSED-REVIEW-A` is frozen before A receives full context.
4. A then performs progressive full-context reasoning audit in the same session.
5. T independently reruns tests/artifacts for `reproduction-OK`.
6. Reconciliation occurs only after both focused reviews are frozen.

No full-context material or review result is appended here until human confirms both focused passes are frozen.
