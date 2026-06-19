# RP-20260619-030: GIP-control registry and baseline v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 15:30 MSK - codex]

`[REVIEW]` First wide GIP-control packet after RP-029 closed the data-prep
blocker. This packet does two things:

1. builds a reproducible wide authorship/section registry for the
   `210 -> 1513` non-UUiR corpus;
2. runs the first lightweight pairwise GIP-control baseline over eligible H1/H2
   cells, with explicit `style` vs `content` decomposition.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@ce7f03a..dfda044
- focus: review the registry join logic (`title authorship + alias canon +
  broad-section selection`), the definition of eligible H1/H2 cells, and the
  first baseline interpretation limits before we invest in heavier near-match /
  provenance-residual refinements
- inputs:
  - `E:/output/DocSpectrum/non_uuir_titled_objects_v0.csv`
  - `E:/output/DocSpectrum/non_uuir_all_sections_selection_v0/all_sections_run_selection_v0.csv`
  - `E:/output/DocSpectrum/title_authorship_range_*_results_v0`
  - `E:/output/DocSpectrum/org_alias_registry_v0/org_alias_registry_v0.csv`
  - `E:/output/pdf-structure-explorer/exports/doc_*`
- metrics:
  - registry:
    - objects: `210`
    - sections: `1513`
    - ready objects: `189`
    - missing effective GIP: `7`
    - missing effective org: `14`
    - subcontractor objects: `25`
    - title counts: `2 -> 185`, `3 -> 23`, `4 -> 2`
    - eligible H1 cells: `17`
    - eligible H2 cells: `26`
  - baseline:
    - ready+known sections used: `943`
    - eligible cells used: `43`
    - profiled documents: `192`
    - pairwise rows: `605`
    - cell summary rows: `68`
    - overall summary rows: `52`
- risks/regressions:
  - this is still a **baseline**, not the final GIP-control method:
    - `style` = count/composition fingerprint
    - `content` = exact hash overlap
    - near-match is not yet used
    - provenance-first residual is only approximated indirectly by corpus
      restriction, not yet an explicit subtractive layer
  - `UNKNOWN` sections are excluded from the baseline but remain present in the
    wide corpus registry (`444` section rows)
  - H2 interpretation is especially fragile where same-GIP cross-org cells have
    tiny same-org baselines (`n=1..2`) or unresolved org alias tails
  - registry still leaves `14` objects without effective org and `7` without
    effective GIP; these rows are retained in the corpus registry but excluded
    from the ready baseline
- not touched:
  - near-match scoring / borrowing calibration
  - text/table library candidate classes
  - page-level provenance queue / manual labels
  - broad section taxonomy cleanup beyond excluding `UNKNOWN` from the baseline
- domain rule:
  - `HC-20260618-012` provenance-first
  - `HC-20260618-013` subcontractor actual author
  - `T-20260618-018` H1/H2 framing
  - `HC-20260619-014` broad non-PD exclusions + `ПОКР -> ПОС`

Implementation:

```text
dfda044  add wide GIP control registry and baseline
```

What changed:

- added `tools/build_gip_control_registry_v0.py`
  - consolidates title-derived authorship across all range results
  - joins alias canon
  - projects authorship from object level onto all `1513` section rows
  - emits eligible H1/H2 comparison cells
- added `tools/build_gip_control_baseline_v0.py`
  - reads only ready + known-section rows from the registry
  - profiles documents directly from flat explorer exports
  - computes pairwise similarity only inside eligible H1/H2 cells
  - decomposes similarity into:
    - `style_similarity_v0` = count/composition fingerprint
    - `content_similarity_v0` = exact overlap over text/table hashes
- added `tests/test_build_gip_control_registry_v0.py`
  - work-type normalization
  - subcontractor/effective-author preference
  - H1/H2 cell eligibility smoke coverage

Artifacts:

- `E:/output/DocSpectrum/gip_control_registry_v0`
- `E:/output/DocSpectrum/gip_control_baseline_v0`

Verification:

- `python` import smoke for `build_gip_control_baseline_v0.py` -> OK
- `tests/test_build_gip_control_registry_v0.py` -> `3/3 OK`
- `build_gip_control_registry_v0.py` completed on real data
- `build_gip_control_baseline_v0.py` completed on real data

Primary findings from the first baseline:

- H1 overall:
  - `same_gip` vs `diff_gip`
  - style median: `0.6759` vs `0.6718` -> near-flat
  - content median: `0.1595` vs `0.0571` -> positive same-GIP lift
- H1 strongest positive cells are concentrated in:
  - `КР/ПОС/ПЗ` for `плоская` and `скатная`
  - especially `ПОС, плоская` and `ПОС, скатная`
- H1 weak/negative cells appear in:
  - `чердак`
  - `ЭС`
  - some `ОВ`
- H2 overall:
  - same-org vs cross-org within same-GIP cells
  - style median: `0.7083` vs `0.6797` -> same-org slightly higher
  - content median: `0.1397` vs `0.1944` -> cross-org content often not lower
- H2 therefore looks mixed and likely still contaminated by:
  - exact-match strictness
  - residual not yet explicitly cleaned
  - small same-org baselines in several cells

Open questions:

- Does Opus agree that the registry layer is now a stable enough source of
  truth for `effective_author` / subcontractor / alias-canonical orgs to build
  future GIP experiments on top of it?
- Is the current H1 signal framing fair:
  - same-GIP lift appears mainly on `content`, not on `style`
  - should this already be read as a real GIP/content-consistency effect, or
    still as an artifact of exact overlap inside repeated work-type cells?
- For H2, is the right next refinement:
  - near-match for the structural/style side first,
  - or explicit provenance-residual subtraction first,
  - or both in a staged order?

