# T-20260615-013: RP-013 replication planning for expanded RSPK corpus

Status: draft
Project: DocSpectrum
Owner: codex
Reviewer: opus
Related: `T-20260615-011`, `HC-20260615-007`, `HC-20260615-008`, `RP-20260615-012`

## [2026-06-15 22:05 MSK - opus via human]

`[PLANNING]` RP-013 must be a replication packet, not a new methodology and not just "rerun on bigger n".

Required additions before/inside RP-013:

- **Disjoint replication.** Recompute the full chain on the expanded RSPK corpus, but evaluate Axis B signals from first-18 against the added-17 subset separately. For each top correlation from the first-18 run, emit `replication_status`: `persisted`, `regressed_to_zero`, or `sign_flip`. A signal is strong only if it holds in both disjoint subsets. The all-35 run is useful, but by itself it is not replication.
- **FDR/significance.** Add p-value plus Benjamini-Hochberg FDR, or at least a clear noise threshold and count of correlations above it. RP-013 is the right checkpoint to address the multiple-comparisons risk found around RP-012.
- **Pre-index IDF before rerun.** v0.3 currently risks scaling badly because IDF lookup is rebuilt per pair by scanning the entity library. At n=35, pair count becomes roughly 4k-5k. Pre-index `(section_code, entity_kind) -> {entity_hash: idf}` before the expanded rerun; this is no longer "later for 1000 objects", it is needed now.
- **Axis A guard test.** Expanded same-address pairs should raise the same-address evidence from 3 houses toward 5 houses. Check whether the section gradient still holds. The 1680/1684 pair should also exercise the `ground_truth_risk` guard for TEI-inconsistent same-address data and be excluded from primary clean ground truth.
- **Manifest and coverage.** Require an expanded export manifest. Verify section coverage and `infer_section_code` / `SECTION_ORDER` behavior on the added objects, especially that sections do not fall into `UNKNOWN`.

Scope boundaries:

- Do not touch Axis C in RP-013. Keep organization/handwriting validation for a later cross-org corpus.
- Do not retune scoring weights from the replication outcome. This remains HC-007 validation, not fitting.
- Keep Axis B count/TEI and Axis A content/system reports separate.

Expected RP-013 evidence:

- first-18 vs added-17 vs all-35 comparison tables;
- replication status for Axis B top correlations;
- FDR/significance summary;
- v0.3 runtime/perf evidence after IDF pre-indexing;
- same-address Axis A summary with TEI-inconsistent guard behavior;
- manifest/coverage summary for added objects.

## [2026-06-15 22:10 MSK - codex]

Accepted as RP-013 planning constraints.

Implementation implications:

- RP-013 should include an explicit subset label for each document/object: `first18`, `added17`, or `all35`.
- Axis B correlation artifacts should support subset-specific runs and joined replication comparison.
- `compare_pairs_v0_3.py` should be optimized with a pre-indexed IDF structure before the 35-object rerun.
- Replication is evaluated on disjoint subsets, while the all-35 run is reported as a larger combined estimate.
- Axis C remains out of scope until a cross-organization corpus is available.
