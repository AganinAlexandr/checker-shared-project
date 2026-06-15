# RP-20260616-014: Axis B page-count control

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-16 01:49 MSK - codex]

`[REVIEW]` Page-count control layer for the RP-013 Axis B shortlist.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@2341727..bd63045
- focus: verify partial Spearman implementation, page-count control semantics, and interpretation of the 28-row strict Axis B shortlist after control
- inputs: `E:/output/DocSpectrum/object_registry_v0/object_registry_v0.csv`, `E:/output/DocSpectrum/element_base_v0_35_n2/documents_index.csv`, `E:/output/DocSpectrum/corpus_frequency_v0_35_n2/section_typicality_v0.csv`, first subset anchor `E:/output/DocSpectrum/element_base_v0_18_n2/documents_index.csv`
- generated artifacts: updated `E:/output/DocSpectrum/axis_b_correlations_v0_35_n2`
- not touched: scoring, Axis A eval, Axis C, pairwise comparison, corpus-frequency generation, near-match/bucketing, graphics, extractor/encoding repair
- domain-rule: TEI/domain fields remain eval/profile context and do not enter core scoring; this is validation/control, not fitting

Purpose:

- RP-013 found the strict Axis B shortlist: `persisted ∩ all35 FDR(q<=0.10) = 28`.
- RP-014 checks whether those 28 candidates are explained away by document length measured as `page_count`.
- This is a size-confound control layer, not a new scoring metric.

Code changes:

- `tools/build_axis_b_correlations_v0.py`
  - adds `partial_spearman_page_count` and related columns to `axis_b_correlations_v0.csv`;
  - computes partial Spearman as Pearson correlation of rank residuals after regressing TEI ranks and metric ranks on `page_count` ranks;
  - carries all35 partial fields into `axis_b_replication_v0.csv`;
  - emits `axis_b_shortlist_page_control_v0.csv` for `persisted ∩ all35 FDR` rows with `page_control_class`.
- `analytics/pq/AxisB_Correlations_v0.pq` and `AxisB_Replication_v0.pq` type the new partial fields.
- `analytics/pq/AxisB_ShortlistPageControl_v0.pq` loads the new shortlist table.
- `notes/axis_b_page_control_v0.md` summarizes the result.

Generated files:

- `axis_b_correlations_v0.csv`
- `axis_b_replication_v0.csv`
- `axis_b_shortlist_page_control_v0.csv`
- `axis_b_correlations_v0.json`

Run summary:

- correlation rows: `8125`
- all35 correlation rows: `3096`
- first18 correlation rows: `2400`
- added17 correlation rows: `2629`
- replication rows: `279`
- strict shortlist rows: `28`
- page-control metric: `page_count`
- page-control threshold: `abs(partial_spearman_page_count) >= 0.45`

Shortlist result:

| Page-control class | Count |
|---|---:|
| survives_page_control | 28 |
| page_size_confounded | 0 |
| not_controlled | 0 |

Family split:

| Family | Count |
|---|---:|
| entity | 15 |
| document_count | 7 |
| document_ratio | 6 |

Minimum absolute partial Spearman among the 28 rows:

- `0.5718`

Top surviving rows by absolute partial Spearman:

| Section | TEI feature | Metric | Family | Spearman | Partial |
|---|---|---|---|---:|---:|
| РРћРЎ5.5.1 | `tei_norm_floors_count` | `entity_text_segment_typical_occurrences` | entity | 0.7911 | 0.8474 |
| РРћРЎ5.5.1 | `tei_norm_height_m` | `entity_text_segment_typical_occurrences` | entity | 0.7301 | 0.7848 |
| РРћРЎ5.5.1 | `tei_norm_floors_count` | `text_segment_count` | document_count | 0.6525 | 0.7447 |
| РРћРЎ5.5.1 | `tei_norm_floors_count` | `entity_text_segment_shared_rare_occurrences` | entity | -0.7211 | -0.7432 |
| РРћРЎ5.5.1 | `tei_norm_building_volume_m3` | `entity_text_segment_typical_occurrences` | entity | 0.6474 | 0.7055 |

Interpretation:

- The 28 high-confidence Axis B candidates are not explained away by page count alone.
- The 7 `document_count` rows still need cautious interpretation: they survive page-count control, but can still be proxies for other size variables.
- Entity and ratio rows remain the cleaner Axis B candidates.
- This strengthens the HC-006 reading that height/floors/volume leave measurable traces in engineering-section document/entity counts.

Verification:

- `python -m py_compile E:\repos\DocSpectrum\tools\build_axis_b_correlations_v0.py`
- regenerated `E:/output/DocSpectrum/axis_b_correlations_v0_35_n2`
- verified `axis_b_shortlist_page_control_v0.csv`: 28 rows, all `survives_page_control`, min abs partial `0.5718`
- pushed DocSpectrum commit `bd63045`

Known limitations / reviewer attention:

- Partial Spearman controls only `page_count`, not all size proxies.
- This does not replace future partial models that may control total area, building volume, or other TEI fields.
- The all35 FDR layer still includes a few low-population TEI fields elsewhere; the strict shortlist should be read row by row.
- Cross-organization validation remains necessary before treating Axis B as stable across project organizations.

Open questions for reviewer:

- Is the rank-residual implementation of partial Spearman acceptable for v0?
- Should `abs(partial) >= 0.45` be the right provisional survival threshold, matching replication threshold?
- Does "28/28 survive page-count control" justify keeping the 7 `document_count` rows in the shortlist, or should they stay marked as caution-only?
- Should RP-015 next control for additional size proxies, or move to near-match page bucketing?

Evidence:

- commit: `bd63045 Add Axis B page-count control`
- note: `notes/axis_b_page_control_v0.md`
- generated summary: `E:/output/DocSpectrum/axis_b_correlations_v0_35_n2/axis_b_correlations_v0.json`
- generated shortlist: `E:/output/DocSpectrum/axis_b_correlations_v0_35_n2/axis_b_shortlist_page_control_v0.csv`
- shared context: `RP-20260616-013`, `HC-20260615-006`, `HC-20260615-007`, `T-20260615-013`
