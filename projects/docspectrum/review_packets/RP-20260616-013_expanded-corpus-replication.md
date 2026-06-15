# RP-20260616-013: expanded RSPK corpus replication

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-16 01:26 MSK - codex]

`[REVIEW]` RP-013 expanded-corpus replication checkpoint.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@c8762e2..2341727
- focus: replication correctness for 35-object RSPK corpus; specifically disjoint Axis B replication, FDR layer, v0.3 IDF pre-indexing, Axis A same-address guard, and coverage/UNKNOWN control
- inputs: `E:/output/DocSpectrum/export`, `E:/output/DocSpectrum/object_registry_v0/object_registry_v0.csv`, `E:/output/DocSpectrum/object_registry_v0/address_tei_consistency_v0.csv`, first subset anchor `E:/output/DocSpectrum/element_base_v0_18_n2/documents_index.csv`
- generated artifacts: `E:/output/DocSpectrum/element_base_v0_35_n2`, `E:/output/DocSpectrum/corpus_frequency_v0_35_n2`, `E:/output/DocSpectrum/comparison_results_v0_2_35_n2`, `E:/output/DocSpectrum/comparison_results_v0_3_35_n2`, `E:/output/DocSpectrum/eval_set_v0_35_n2`, `E:/output/DocSpectrum/axis_b_correlations_v0_35_n2`
- not touched: scoring weights, Axis C, near-match/bucketing, graphics, extractor/encoding repair, v0.1 artifacts
- domain-rule: this is HC-007 validation/replication, not metric fitting; TEI/domain fields remain eval/profile context and do not enter core scoring

Purpose:

- Run the existing chain on the expanded RSPK corpus of 35 objects.
- Treat the run as replication, not as "bigger n means true".
- Check whether first18 Axis B candidate correlations survive on the disjoint added17 subset.
- Keep Axis A content/system and Axis B size/TEI separated.

Code changes:

- `tools/compare_pairs_v0_3.py`: pre-indexes IDF as `(section_code, entity_kind) -> {entity_hash: idf}` instead of scanning the entity library per pair and per axis.
- `tools/build_axis_b_correlations_v0.py`: adds subset labels (`all`, `first18`, `added17`), approximate p-value, Benjamini-Hochberg q-value, FDR flag, and `axis_b_replication_v0.csv`.
- `analytics/pq/*`: current v0.2/v0.3/corpus/eval/AxisB queries now point to `35_n2`; new `AxisB_Replication_v0.pq` loads the replication table.
- `notes/expanded_corpus_35_replication_v0.md`: compact run summary.

Coverage:

- documents: `279`
- objects: `35`
- UNKNOWN sections: `0`
- same-section pairs: `4450`
- matrix statuses: `ok=279`, `missing=36`

Documents by section:

| Section | Documents | Pairs |
|---|---:|---:|
| РђР  | 35 | 595 |
| РљР  | 35 | 595 |
| РР” | 35 | 595 |
| РРћРЎ5.1 | 35 | 595 |
| РРћРЎ5.4.1 | 35 | 595 |
| РРћРЎ5.5.1 | 35 | 595 |
| РЎРњ | 34 | 561 |
| РџРћРљР  | 23 | 253 |
| РџРћРЎ | 12 | 66 |

This is the "library = all available sections" mode, not a strict 35 x 8 matrix.

v0.3 median `idf_similarity_v0_3` by section:

| Section | Median |
|---|---:|
| РђР  | 0.6072 |
| РљР  | 0.6628 |
| РР” | 0.9211 |
| РРћРЎ5.1 | 0.5888 |
| РРћРЎ5.4.1 | 0.3227 |
| РРћРЎ5.5.1 | 0.5682 |
| РџРћРљР  | 0.6077 |
| РџРћРЎ | 0.5674 |
| РЎРњ | 0.2340 |

Axis A same-address guard:

- same-address cross-system rows: `40`
- TEI-consistent rows: `32`
- TEI-inconsistent guard rows: `8`
- primary rows after excluding diagnostics and inconsistent same-address pairs: `24`
- Spearman expected rank vs `idf_similarity_v0_3`: `0.9137`
- Spearman expected rank vs section percentile: `0.8653`

Interpretation:

- The `1680/1684` inconsistent same-address case now exercises the guard.
- The guard excludes those rows from clean primary ground truth.
- Axis A still passes the section-gradient sanity check on the expanded corpus.

Axis B disjoint replication:

- correlation rows: `all=3096`, `first18=2400`, `added17=2629`
- replication candidates: first18 rows with `abs_spearman >= 0.45`
- replication threshold for added17 persistence: `abs_spearman >= 0.45`

Replication status counts:

| Status | Count |
|---|---:|
| persisted | 69 |
| regressed_to_zero | 187 |
| sign_flip | 9 |
| insufficient_added17 | 14 |

Interpretation:

- This is the intended anti-overfitting result: many strong first18 correlations do not survive the disjoint added17 check.
- `sign_flip` is emitted only when the added17 correlation is also strong and opposite in sign; weak opposite-sign noise is classified as `regressed_to_zero`.

FDR/significance:

- approximate p-values and BH q-values are emitted in `axis_b_correlations_v0.csv`
- `all`: 32 rows flagged at `q <= 0.10`
- `first18`: 0 rows flagged at `q <= 0.10`
- `added17`: 0 rows flagged at `q <= 0.10`

Interpretation:

- The FDR layer is a sanity filter, not final statistics.
- The disjoint replication table is the primary guard against multiplicity at this checkpoint.

Verification:

- `python -m py_compile E:\repos\DocSpectrum\tools\compare_pairs_v0_3.py E:\repos\DocSpectrum\tools\build_axis_b_correlations_v0.py`
- generated `element_base_v0_35_n2`: 279 documents, 35 objects, 4450 pairs, UNKNOWN=0
- generated `comparison_results_v0_3_35_n2`: 4450 rows
- generated `eval_set_v0_35_n2`: same-address cross-system=40, inconsistent guard=8, Axis A Spearman=0.9137
- generated `axis_b_correlations_v0_35_n2`: 8125 correlation rows across all subsets, 279 replication rows
- pushed DocSpectrum commit `2341727`

Known limitations / reviewer attention:

- Approximate p-values use a normal approximation for rank correlation; please review whether this is acceptable as a v0 sanity layer.
- FDR-significant all35 rows include very small-n cases, for example `tei_norm_apartments_count` has only 4 TEI values; these should be treated cautiously.
- Axis B currently reports correlations, not partial correlations controlling page count.
- `page_signature`-derived entity metrics remain strictness-prone diagnostics unless/until near-match/bucketing is added.
- PQ generic queries now point to `35_n2`; `Comparison_Fast_v0_1_All.pq` intentionally remains on `18_n2` because v0.1 was not regenerated for all35.

Open questions for reviewer:

- Does `axis_b_replication_v0.csv` implement the intended disjoint replication semantics?
- Is the `persisted / regressed_to_zero / sign_flip / insufficient_added17` taxonomy acceptable?
- Is approximate p-value + BH q-value enough for v0, given that disjoint replication is the primary evidence?
- Does the Axis A guard behavior satisfy the `1680/1684` test from T-013?
- Should the next packet prioritize partial correlations/page-count controls, or near-match page bucketing?

Evidence:

- commit: `2341727 Add 35-object replication checkpoint`
- note: `notes/expanded_corpus_35_replication_v0.md`
- generated summary: `E:/output/DocSpectrum/axis_b_correlations_v0_35_n2/axis_b_correlations_v0.json`
- generated eval summary: `E:/output/DocSpectrum/eval_set_v0_35_n2/eval_set_v0.json`
- shared context: `T-20260615-013`, `HC-20260615-007`, `HC-20260615-008`, `RP-20260615-012`
