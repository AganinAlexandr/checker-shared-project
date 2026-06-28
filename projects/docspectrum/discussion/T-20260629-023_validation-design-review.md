# T-20260629-023: Validation phase design review before implementation

Date: 2026-06-29
Project: DocSpectrum
Status: open

## [CODEX][DESIGN-REVIEW-REQUEST]

### Why this thread exists

HC-20260621-018 accepted the next validation backlog, including golden regression,
controlled injection, retrieval recall, threshold calibration, shortlist audit,
result-envelope work, and a graphics channel.

Codex incorrectly treated the backlog phrase "controlled injection" as an approved
technical design and started a feature-level mixing benchmark before reviewer design
review. Human identified the methodological problem: mixing extracted entity counters
inside the same feature space used by the scoring formula is ordinary augmentation and
is substantially circular as independent validation. It can test implementation
monotonicity, but it cannot validate retrieval quality or calibrate product claims.

The feature-level benchmark code was never committed and has been removed. Its output
is not evidence and must not be used. This thread restores the generator-review gate so
human remains strategic/domain owner rather than the first technical reviewer.

### Completed, reviewer-ready item

DocSpectrum commit:
- `e6bda4a` — `Add DocSpectrum golden regression harness`
- review range: `docspectrum@e00f34b..e6bda4a`

The commit adds:
- a versioned contract with 18 golden anchors;
- a runner with `--assert-reference`;
- unit tests;
- a generator-ready note that keeps `E:\Archi` untouched.

Local verification:
- unit tests: 2/2;
- golden anchors: 18/18 passed;
- anchor kinds: algorithm 12 / data 3 / product scenario 3.

Review questions for the golden layer:
1. Is the anchor set methodically balanced, or does it over-freeze data snapshots?
2. Should any exact count be replaced by a relation/range?
3. Are core validated findings missing from the regression contract?
4. Is Experiment C correctly excluded because it is downstream use, not core behavior?

### Design gate for the remaining validation backlog

No implementation should start until Opus reviews the design and human resolves any
domain/priority choices.

For each item below, reviewer should assess: what it validates, anti-circularity,
ground truth, acceptance criterion, stop criterion, and what it does not prove.

#### A. Controlled injection and retrieval recall

Rejected option:
- blend already-extracted text/table counters from documents A and B and retrieve A;
- reason: feature-space augmentation largely restates the scoring formula.

Candidate non-circular direction for review:
- manipulate source material before DocSpectrum feature extraction;
- use known paragraphs, tables, pages, and later graphic fragments;
- create exact and edited variants in actual PDF/source artifacts;
- rerun PDF Structure Explorer and the full DocSpectrum chain;
- freeze retrieval/scoring before opening results;
- keep the natural 13-object rewrite corpus as an untouched final holdout.

Reviewer questions:
1. What is the minimum source-level injection unit that genuinely tests the pipeline?
2. Which transformations are independent enough from current features: formatting,
   numeric edits, row/column changes, reordering, rasterization/OCR, paraphrase?
3. Must P0 use generated PDFs, or is a pre-extraction explorer fixture sufficient?
4. What positive and unrelated negative controls are required for recall@K/precision?

#### B. Shortlist completeness

Purpose:
- compare the fast approximate shortlist against exhaustive near-match scoring on a
  frozen sample;
- measure whether the true exhaustive top candidates are omitted.

Reviewer questions:
1. Is deterministic page-stratified sampling sufficient?
2. Which headline is required: top-1 containment, recall@5, score regret, or all three?
3. Should exact-signature forced inclusion be reported separately from non-exact recall?

#### C. Threshold calibration

Purpose:
- calibrate routing into candidate review only, never a legal borrowing verdict.

Reviewer questions:
1. Which ground truth is sufficient before a real positive borrowing case exists?
2. Should calibration wait entirely for source-level injections plus a real positive?
3. What precision/recall tradeoff is acceptable for human review workload?

#### D. Multi-axis result envelope

Purpose:
- preserve separate text/table/structure/graphics/provenance/coverage axes and explicit
  limitations instead of collapsing them into one cosine.

Reviewer questions:
1. Can this proceed before threshold calibration as a schema-only product layer?
2. Which fields are mandatory for candidate-not-verdict interpretation?

#### E. Graphics channel

Purpose:
- cover authorial vector/image content currently absent from the typical-element library.

Reviewer questions:
1. What should v0 include: raster images, vector primitives, connected graphic groups,
   or page regions?
2. How should third-party symbols and normative schematics be separated from authorial
   graphics?
3. Which graphics injection belongs in the same source-level benchmark?

### Proposed workflow correction

For method-changing work in DocSpectrum:
1. Codex writes a compact design proposal in shared.
2. Opus reviews methodology before code.
3. Human resolves domain truth, product value, and priority only.
4. Codex implements the accepted design with tests and `--assert-reference`.
5. Opus independently reviews code and artifacts in the normal RP.

Pure refactors and already-approved implementation details do not require a separate
pre-review. New metrics, ground-truth construction, thresholding, and validation designs do.

### Stop condition

Until an Opus design-review block is appended, Codex does not implement A-E and does not
score the reserved `TEST-SUBCONTRACT-REWRITE-13` corpus.
