# T-20260629-025: Source-lineage retrieval benchmark design

Date: 2026-06-29
Project: DocSpectrum
Status: accepted_fixture_group_built
Profile: Phase-0 design by codex; Opus reserved for final CODE-3 reasoning review

## [2026-06-29 · codex] DESIGN

### Decision being proposed

Milestone 2 is narrowed from a generic “injection benchmark” to a **source-lineage component benchmark**.

V0 does **not** mix extracted feature counters and does **not** paste fragments into unrelated full sections.
It asks a simpler question first:

> Given a source text or table and a source-level edited variant, does DocSpectrum retrieve the known source
> family ahead of unrelated and structurally similar hard negatives?

Only after component retrieval is measured may a separate document-dilution benchmark combine components with
hosts. Such mixing is a stress test of dilution, not evidence that the synthetic document is realistic.

This incorporates the human objection to feature-space augmentation and avoids manufacturing hundreds of full
documents before the primitive retrieval claim is known.

## What this validates

1. End-to-end extraction and retrieval of a known text/table lineage.
2. Retrieval rank degradation under pre-registered source edits.
3. Separation of table-form similarity from table-content similarity.
4. Retrieval recall (`@1/@5/@10`), reciprocal rank, source margin, and paired wins against hard negatives.
5. Later, with the same frozen candidates, shortlist completeness against exhaustive scoring.

## What this does not validate

- legal or factual “borrowing”;
- realism of a synthetic project section;
- semantic paraphrase detection beyond the current channels;
- organization/GIP/performer identity;
- graphics;
- the untouched 13-object natural holdout;
- thresholds for a product verdict.

## Benchmark unit

One **lineage family** contains:

- one canonical source component;
- deterministic source-level variants derived before PDF extraction;
- matched hard negatives of similar size/form but different meaning or values;
- immutable `family_id`, `source_id`, `variant_id`, transformation metadata, and expected source family.

Each component is rendered as a one-page PDF fixture and passed through PDF Structure Explorer and the normal
DocSpectrum extraction/retrieval chain. Text and table fixtures use the same neutral page shell so page decoration
cannot identify the family.

Primary retrieval is component-to-component. Page structure is diagnostic, not part of the text/table success
claim, because a common renderer would otherwise leak fixture identity.

## Frozen factorial v0

Small enough to audit fully, broad enough to expose failure modes:

- text families: `8`;
- table families: `8`;
- split by family, not variant: `10` calibration families + `6` sealed evaluation families;
- hard negatives: at least `3` per family, including one length/form-matched negative;
- every accepted transformation is crossed with every family of that component kind;
- no result-dependent resampling or removal.

### Text variants

1. `exact` — identical text, independently rendered.
2. `format_only` — font/line-wrap/spacing changes; words unchanged.
3. `numeric_edit` — deterministic replacement of quantities/designations; syntax retained.
4. `sentence_reorder` — sentence order changed; sentences retained.
5. `partial_25` — deterministic removal of 25% of sentences.
6. `partial_50` — deterministic removal of 50% of sentences.

No LLM paraphrases in v0: they add an uncontrolled author/model and test a semantic channel that is not yet
implemented. Paraphrase becomes a separate future benchmark.

### Table variants

1. `exact` — same cells and geometry, independently rendered.
2. `format_only` — typography/line styling changes.
3. `values_edit` — values/designations changed, headers and geometry retained.
4. `row_reorder` — rows permuted, cells retained.
5. `row_drop_25` — deterministic removal of 25% of body rows.
6. `column_change` — one non-key column added or removed.
7. `form_same_content_new` — geometry retained, all non-header content replaced.

The last variant is essential: it distinguishes a typical form from copied content.

## Ground truth and negatives

- Ground truth is lineage-by-construction, fixed before extraction.
- Canonical sources and hard negatives are selected without looking at retrieval scores.
- Negatives include:
  - unrelated content with similar length;
  - same broad engineering vocabulary but a different technical subject;
  - for tables, similar dimensions with unrelated headers/content.
- Third-party/normative material is labeled separately and cannot become an organization-authored borrowing
  positive.

## Anti-circularity controls

1. All changes occur in source fixture content before PDF creation and feature extraction.
2. No extracted counter, hash, score, shortlist, or threshold is used to generate a variant.
3. Family selection, transformations, split, and expected relations are frozen before the first scoring run.
4. Calibration/evaluation split is disjoint by lineage family.
5. The 13-object natural holdout remains unopened for scoring.
6. The benchmark reports all pre-registered variants, including failures.

## Metrics

Per component kind and transformation:

- `recall@1`, `recall@5`, `recall@10`;
- mean reciprocal rank;
- source rank distribution;
- source score and margin over best non-family candidate;
- paired family-win rate;
- extraction success/absence status;
- exhaustive-vs-shortlist containment and score regret in the later shortlist pass.

Library coverage is not called recall and is not a headline here.

## Pre-registered gates

### Benchmark validity

- 100% fixture and manifest generation;
- 100% stable source/variant hashes;
- every PDF is processed or explicitly classified as extraction failure;
- no family crosses calibration/evaluation split;
- exhaustive candidate universe is identical for all variants of a component kind.

### Algorithm hypotheses (measured, not fitted)

- exact variants: `recall@1 = 1.00`;
- format-only variants: `recall@5 >= 0.95`;
- light content edits (`numeric_edit`, `values_edit`, `row_reorder`): `recall@5 >= 0.90`;
- partial/structural edits: report the curve; no threshold tuning on sealed evaluation;
- median source margin remains positive for each non-heavy transformation;
- table `form_same_content_new` should retrieve the source on layout but not be reported as same-content.

These are validation hypotheses, not promised product accuracy. Failure is a benchmark result, not a reason to
delete or regenerate a family.

## Claim → artifact → source map (generator-ready requirement)

| Claim | Generated artifact | Ground-truth/source |
|---|---|---|
| Fixture lineage is intact | `lineage_manifest_v0.csv` | frozen source/variant hashes and transformation plan |
| Exact/light retrieval works | `component_retrieval_metrics_v0.csv` | expected `family_id` vs ranked candidates |
| Rank degrades with edits | `retrieval_sensitivity_curve_v0.csv` | pre-registered transformation severity |
| Table form differs from content | `table_axis_retrieval_v0.csv` | layout-preserved/content-replaced variants |
| Fast shortlist is complete | `shortlist_exhaustive_audit_v0.csv` | exhaustive ranking on the same frozen candidate set |
| Interpretation stays bounded | `source_lineage_benchmark_v0.json` | this design + HC-018/HC-012/HC-016 |

## Implementation boundary

After human acceptance, Codex may implement only:

1. fixture source manifest and deterministic renderer;
2. fixture generation and hash audit;
3. explorer batch orchestration with bounded batches;
4. exhaustive component retrieval and metrics;
5. tests and `--assert-reference` for benchmark integrity.

Threshold calibration, host mixing, graphics, and the 13-object holdout remain outside this build.

## Review/resource plan

- Phase-0: this document + human design decision; no Opus spend.
- Implementation: Codex.
- Technical reproduction: independent CI on clean checkout with machine report.
- Methodological checkpoint: one compact CODE-3 reasoning package for Opus after artifacts exist, with the
  claim map above and delta-only full context.

## Human gate

Implementation begins when the owner confirms that v0 should start with isolated text/table lineage families
and defer full-document mixing. Any change to the unit, transformations, split, or hypotheses reopens Phase-0.

## [2026-06-29 · human]

`[DESIGN-ACCEPTED]` Isolated component-lineage benchmark first; full-document mixing deferred.

## [2026-06-29 · codex]

`[IMPLEMENTATION-CHECKPOINT]` Fixture group built without scoring.

- DocSpectrum commit: `bc041bd`
- output: `E:\output\DocSpectrum\source_lineage_fixtures_v0`
- fixtures: `120` unique PDFs (`16` canonical sources + `104` queries)
- kinds: text `56`, table `64`
- family split: calibration `10`, sealed evaluation `6`
- manifest PDF hash audit: `120/120`, mismatches `0`
- repeated-build hash comparison: differences `0`
- exact queries byte-distinct from sources: `16/16` (opaque metadata instance token prevents CRC deduplication; not used by scoring)
- tests: fixture suite `5/5` in the PyMuPDF environment; combined fixture+golden-v1 `13/13`
- claim boundary: synthetic component lineage only; no borrowing/authorship/holdout claim

No Opus resource used. No explorer bundle, retrieval score, threshold, or natural holdout result exists yet.

Next implementation slice: three terminating PSE batches of at most 40 one-page fixtures, then exhaustive component retrieval in a dedicated benchmark namespace.
