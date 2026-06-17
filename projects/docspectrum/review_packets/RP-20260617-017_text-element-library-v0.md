# RP-20260617-017: Text element library v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-17 17:19 MSK - codex]

`[REVIEW]` Text-layer extension of the consumer-facing library: exact-hash
text candidates, text coverage/residual, and rarity-guarded cross-org
copy-review signals.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@9753855..532061b
- focus: review `text_element_library_v0` as the next HC-010 Build 1 layer after table forms: text candidate promotion, hash-only evidence, text coverage/residual, org text conformance, shingle saturation handling, and the rarity guard that prevents normative text from flooding UC3 copy-review signals
- inputs: `E:/output/DocSpectrum/element_base_v0_rpsk35_nk34`, `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`, `E:/output/DocSpectrum/export` (RSPK35), `E:/output/DocSpectrum/export_nk_34_object_view` (NK34), shared context `HC-20260617-010`, `RP-20260617-016`
- metrics: `43,740` text candidates; `27,865` `text_segment`, `15,875` `text_word_shingle`; `36,355` org text patterns, `7,135` normative text, `250` rare cross-org text bridges after rarity guard; `26,707` candidate rows and `17,033` diagnostic trivial rows; `598,437` sampled evidence rows; coverage built for `474/474` documents
- risks/regressions: exact text hashes are brittle to minor edits; shingle coverage is saturated and must not be used as the headline originality metric; cross-org text bridges are review signals, not proof of copying; evidence is sampled; source encoding defects can affect text hashes; no semantic embeddings or near-match are used
- not touched: table candidate/coverage outputs, pairwise scoring, page/graphics/vector candidates, embeddings, near-match, executor-level handwriting, raw-text artifacts
- domain-rule: follows HC-010 priority UC1 before UC3; text coverage extends UC1a/UC1v beyond tables, but `residual` still means "unexplained by the v0 exact-hash text library," not proven original authorship; cross-org exact text is classified conservatively as `cross_org_text_bridge` / `copy_review_needed`, not `borrowing_candidate`

Purpose:

- Add a text layer to the typical-element library after RP-016's table-form layer.
- Produce text candidates and section-level coverage without writing raw text.
- Keep `text_segment` as the primary interpretable coverage signal.
- Keep `text_word_shingle` as phrase-reuse diagnostics, not the headline originality metric.
- Add a UC3 hook for rare cross-org exact text while suppressing common boilerplate.

Code / notes added in DocSpectrum:

- `tools/build_text_element_library_v0.py`
- `notes/text_element_library_v0.md`

Candidate model:

- `text_segment`: normalized text segment hash, primary interpretable signal.
- `text_word_shingle`: 5-token shingle hash, phrase-reuse signal.
- Promotion threshold: at least `3` distinct objects.
- Short segments are retained as `diagnostic_trivial`.
- Candidate rows store full occurrence counts.
- Evidence rows are sampled per candidate with `max_evidence_per_candidate = 20`.
- Evidence sampling is cohort-aware, so cross-org candidates expose both sides when available.
- Raw text is not written.

Candidate classes:

```text
org_text_pattern       - exact text appears only inside one cohort
normative_text         - exact text is common across cohorts or too frequent for copy-review
cross_org_text_bridge  - rare cross-org exact text that needs copy/normative review
```

Rarity guard:

- Initial no-rarity classification over-flagged common normative text.
- Fix: cross-org exact text gets `copy_review_needed` only if its same-section document-frequency ratio is `<= 0.25`.
- Frequent cross-org text is overridden to `normative_text`, even if organization ratios are slightly unbalanced.

Run summary:

```text
document_count: 474
candidate_count: 43,740
evidence_row_count: 598,437
coverage_row_count: 474
skipped_entity_groups_below_min_objects: 46,636
copy_review_max_section_df_ratio: 0.25
```

Candidate kind counts:

```text
text_segment        27,865
text_word_shingle   15,875
```

Candidate class counts after rarity guard:

```text
org_text_pattern        36,355
normative_text           7,135
cross_org_text_bridge      250
```

Candidate status counts:

```text
candidate           26,707
diagnostic_trivial  17,033
```

Coverage results:

Median `text_segment` coverage by section:

```text
АР        0.4464
ИД        0.3986
ИОС5.1    0.2902
ИОС5.4.1  0.3723
ИОС5.5.1  0.1779
КР        0.4517
ПОКР      0.6039
ПОС       0.3101
СМ        0.2457
```

Median `text_word_shingle` coverage by section is very high (`~0.97-0.997`),
so it is kept as phrase-reuse diagnostics. Headline originality/residual should
use `text_segment` first.

Median `text_segment` coverage by cohort:

```text
NK    0.3612
RSPK  0.3174
```

Copy-review / foreign text control after rarity guard:

```text
cross_org_text_bridge candidates: 250
documents with copy_review_needed occurrences: 123 / 474
max copy_review_needed occurrences in one document: 52
documents with foreign_org_text_ratio > 0.05: 68 / 474
median segment_org_text_conformance_ratio: 1.0
```

Interpretation:

- The text layer is much denser than table forms.
- Exact text bridges are common in design documentation and often normative.
- Rarity guard reduces copy-review overload from `3,246` bridge candidates to `250`.
- `text_segment` coverage is the meaningful v0 text residual proxy.
- `text_word_shingle` coverage is saturated and should not be used as a headline originality metric.
- UC1a/UC1v now has both table and text coverage layers, with conservative wording.

Generated artifact paths:

- `E:/output/DocSpectrum/text_element_library_v0/text_element_candidates_v0.csv`
- `E:/output/DocSpectrum/text_element_library_v0/text_element_candidate_evidence_v0.csv`
- `E:/output/DocSpectrum/text_element_library_v0/text_element_section_coverage_v0.csv`
- `E:/output/DocSpectrum/text_element_library_v0/text_element_library_v0.json`

Verification:

- `python -m py_compile E:\repos\DocSpectrum\tools\build_text_element_library_v0.py`
- Generated `text_element_library_v0` on RSPK35+NK34.
- Checked JSON summary for candidate counts, class counts, status counts, coverage medians and rarity guard effect.
- Checked coverage CSV aggregate: `copy_review_needed` appears in `123/474` documents after guard; max `52` occurrences.
- DocSpectrum current commit: `532061b Refine text copy-review signals`.

Known limitations / reviewer attention:

- Exact text hashes miss paraphrases and small edits.
- Encoding defects in source PDFs can create false uniqueness or false mismatch.
- `cross_org_text_bridge` still needs normative filtering and near-match context before UC3 conclusions.
- Evidence sampling is cohort-aware but not exhaustive.
- No combined table+text product report exists yet; layers are still separate.

Open questions for reviewer:

- Does the rarity guard (`section_df_ratio <= 0.25`) sufficiently address boilerplate over-flagging for v0?
- Should the next product-facing report combine table and text coverage into one section-level view?
- Is `text_segment` the right headline residual proxy, with shingle coverage kept diagnostic?
- Should `foreign_org_text_ratio` also be reported per kind (`segment` vs `shingle`) before product surfacing?
- Should the next milestone be near-match for UC3, or combined table+text coverage for UC1 reporting?

Evidence:

- commits: `docspectrum@9753855..532061b`
- notes: `notes/text_element_library_v0.md`
- artifacts: paths listed above
- shared context: `HC-20260617-010`, `RP-20260617-016`
