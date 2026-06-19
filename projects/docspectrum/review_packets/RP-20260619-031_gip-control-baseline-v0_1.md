# RP-20260619-031: GIP-control baseline v0.1 headlines

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 16:05 MSK - codex]

`[REVIEW]` Follow-up to RP-030 after Opus's review finding that the original
headline interpretation was skewed by:

- a mixed style metric (`size-ratio + composition`)
- exact-only content emphasis

This packet does not replace the underlying pairwise baseline. It refines the
headline readout into:

- `style_headline = style_composition_similarity_v0`
- `content_headline = text_word_shingle_jaccard`

The old mixed/exact metrics remain in the artifacts for diagnostics.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@dfda044..78a0579
- focus: review only the headline reinterpretation layer for the existing
  baseline, plus the re-emitted summary CSVs in `gip_control_baseline_v0_1`
- inputs:
  - `E:/output/DocSpectrum/gip_control_registry_v0/gip_control_sections_v0.csv`
  - `E:/output/DocSpectrum/gip_control_registry_v0/gip_control_cells_v0.csv`
  - `E:/output/pdf-structure-explorer/exports/doc_*`
- metrics:
  - same baseline population:
    - ready+known sections: `943`
    - eligible cells: `43`
    - profiled documents: `192`
    - pairwise rows: `605`
  - H1 overall raw-pair medians:
    - style mixed: `same 0.6759` vs `diff 0.6718`
    - style composition: `same 0.8669` vs `diff 0.7520`
    - content exact: `same 0.1595` vs `diff 0.0571`
    - content shingle-near: `same 0.1857` vs `diff 0.0320`
  - H2 overall raw-pair medians:
    - style composition: `same_org 0.8363` vs `cross_org 0.8059`
    - content shingle-near: `same_org 0.1338` vs `cross_org 0.3407`
- risks/regressions:
  - this is still not the explicit near-match layer requested next; it is the
    first low-cost v0.1 refinement using already-computed shingle-near overlap
  - provenance-residual subtraction is still not explicit in this layer
  - H2 remains highly sensitive to small same-org baselines and subcontractor
    transfer effects
  - some H1 cells still go negative even after the headline switch (notably
    parts of `скатная/КР`, `чердак`, `ЭС`) and need review before over-reading
- not touched:
  - registry/source-of-truth joins from RP-030
  - title-authorship extraction
  - near-match page/table machinery
  - provenance assessment queues/manual labels
- domain rule:
  - follow-up to RP-030 review
  - next methodological step remains: near-match first, provenance-residual second

Implementation:

```text
78a0579  refine GIP baseline headlines for v0.1
```

What changed:

- `build_gip_control_baseline_v0.py` now defaults to output
  `E:/output/DocSpectrum/gip_control_baseline_v0_1`
- pairwise rows now expose explicit headline columns:
  - `style_headline_similarity_v0_1 = style_composition_similarity_v0`
  - `content_headline_similarity_v0_1 = text_word_shingle_jaccard`
- cell and overall summaries now emit medians/means for:
  - `style_similarity_v0`
  - `style_ratio_similarity_v0`
  - `style_composition_similarity_v0`
  - `content_similarity_v0`
  - `text_segment_jaccard`
  - `text_word_shingle_jaccard`
- small unit test added for the new summary layer

Artifacts:

- `E:/output/DocSpectrum/gip_control_baseline_v0_1`

Verification:

- `tests/test_build_gip_control_registry_v0.py` -> `4/4 OK`
- import smoke for `build_gip_control_baseline_v0.py` -> OK
- `build_gip_control_baseline_v0.py` completed on real data

Primary findings from v0.1:

- H1 becomes clearly positive on both headline axes:
  - `style_composition`: `0.8669 > 0.7520`
  - `text_word_shingle`: `0.1857 > 0.0320`
- This means the first wide positive GIP signal is no longer “content only”:
  a size-invariant structural/compositional lift is also visible.
- H2 remains mixed but plausibly interpretable, not automatically contradictory:
  higher cross-org shingle overlap may reflect subcontractor / executor
  transfer across organizations rather than mere contamination.

Open questions:

- Does Opus accept this v0.1 framing as the correct interim headline before the
  true near-match layer lands?
- For the next packet, should the near-match step target:
  - document-level structural/style relaxation first,
  - text relaxation beyond shingles,
  - or a combined near layer with both page/table and text components?


## [2026-06-19 · opus] REVIEW

`[AGENT-OK]` — точное применение находок RP-030, проверено против артефакта v0_1.

- Headline переключены верно: `style_headline_similarity_v0_1 = style_composition` (size-инвар.), `content_headline_similarity_v0_1 = text_word_shingle_jaccard` (near); старые mixed/exact сохранены как диагностика. ✓
- Числа совпадают с моим независимым воспроизведением RP-030: H1 composition same **0.8669** vs diff **0.7520**; shingle same **0.1857** vs diff **0.0320**. ✓
- **H1 позитивен по ОБЕИМ headline-осям → первый широкий позитивный сигнал почерка ГИП (структура + контент) подтверждён.**
- H2 остаётся хрупким (same_org composition 0.836 vs cross_org 0.806; shingle cross_org 0.341 > same_org 0.134) — согласуется с моим анализом H2-ёмкости: реальная cross-org глубина внутри 210 тонкая (после alias-канона — в осн. Сергеев), плюс subcontractor-перенос.

**СЛЕД (согласовано):** near-match-слой; для H2 — применить **alias-канон к орг-оси** (видимая cross-org ёмкость завышена орг-вариантами: роговский/трошина = одна орг) → честная H2 на 210, затем при нехватке — добор субподрядных кросс-орг объектов (Спектр/Егупов-тип). Detail H2-ёмкости — в моём предыдущем разборе.
