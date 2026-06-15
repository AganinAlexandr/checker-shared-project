# RP-20260615-012: Axis B count/TEI correlations v0

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 21:20 MSK - codex]

`[REVIEW]` First Axis B count/TEI correlation exports after RP-011.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@bc71359..c8762e2
- focus: `tools/build_axis_b_correlations_v0.py`, `notes/axis_b_correlations_v0.md`, `analytics/pq/AxisB_*.pq`; please review whether the Axis B layer correctly measures size/count relations without changing scoring
- inputs: `E:/output/DocSpectrum/object_registry_v0/object_registry_v0.csv`, `E:/output/DocSpectrum/element_base_v0_18_n2/documents_index.csv`, `E:/output/DocSpectrum/corpus_frequency_v0_18_n2/section_typicality_v0.csv`
- generated artifacts: `E:/output/DocSpectrum/axis_b_correlations_v0_18_n2`
- not touched: pairwise scoring, eval-set labels, corpus-frequency generation, near-match, graphics
- domain-rule: TEI/domain fields remain eval/profile context and do not enter core scoring

Purpose:

- Axis B is size/quantity validation from HC-006.
- It should be measured by absolute counts, per-page controls and TEI correlation, not forced into content Jaccard.
- This layer exports Spearman rank correlations because engineering components follow stepped nominal sizes.

Outputs:

- `axis_b_document_metrics_v0.csv`: document-level TEI + PDF counts + entity occurrence counts.
- `axis_b_correlations_v0.csv`: Spearman correlations by `section_code`, TEI feature and metric.
- `axis_b_tei_bucket_summary_v0.csv`: low/mid/high TEI bucket medians for selected counts.
- `axis_b_correlations_v0.json`: summary and top absolute correlations.

Run summary:

- document metric rows: `143`
- correlation rows: `2395`
- TEI bucket summary rows: `108`
- entity metric correlations: `1175`
- document count correlations: `625`
- document ratio correlations: `595`

Top exploratory signals by absolute Spearman:

| Section | TEI feature | Metric | Family | n | Spearman |
|---|---|---|---|---:|---:|
| ИД | `tei_norm_total_area_m2` | `total_text_words` | document_count | 17 | 0.7947 |
| ИОС5.5.1 | `tei_norm_height_m` | `entity_text_segment_typical_occurrences` | entity | 18 | 0.7844 |
| ИОС5.4.1 | `tei_norm_floors_count` | `table_cells_per_page` | document_ratio | 18 | 0.7831 |
| ИОС5.4.1 | `tei_norm_total_area_m2` | `table_cell_count` | document_count | 17 | 0.7796 |
| ИД | `tei_norm_footprint_area_m2` | `element_count` | document_count | 18 | 0.7734 |
| КР | `tei_norm_total_area_m2` | `entity_page_signature_shared_rare_occurrences` | entity | 17 | 0.7699 |

First interpretation:

- Axis B now has measurable candidate signals; it is no longer only "absent from jaccard".
- Several strong correlations are count/ratio based, matching the size-axis hypothesis.
- Signals in engineering sections (`ИОС5.4.1`, `ИОС5.5.1`) are relevant to HC-006 equipment/pressure hypotheses.
- These are exploratory correlations on a small, one-organization, pre-expertise corpus and should not be treated as calibrated causal rules.

Encoding note:

- The builder assumes upstream CSV artifacts are decoded/normalized.
- Files with broken or mixed encodings are not dominant in this corpus, but they do occur.
- Rare broken-encoding sources can still distort downstream text/entity counts, so encoding detection/repair belongs in the extractor/registry layer, not in Axis B correlation logic.

Verification:

- `python -B -m py_compile E:\repos\DocSpectrum\tools\build_axis_b_correlations_v0.py`
- generated `axis_b_document_metrics_v0.csv` with `143` rows
- generated `axis_b_correlations_v0.csv` with `2395` rows
- generated `axis_b_tei_bucket_summary_v0.csv` with `108` rows
- verified JSON summary and Unicode section-code codepoints
- PQ queries added for Excel export

Open questions for reviewer:

- Are the selected document/entity metrics sufficient for the first Axis B export?
- Are Spearman correlations by section the right primary check, given stepped equipment nominal sizes?
- Should page-count/per-page ratio controls be expanded before interpreting strong count correlations?
- Should entity metrics involving `page_signature` be marked diagnostic/strictness-prone in Axis B outputs?
- Should the next packet address encoding normalization at extractor/registry level, or continue with near-match page bucketing?

Evidence:

- commit: `c8762e2 Add Axis B TEI count correlations`
- note: `notes/axis_b_correlations_v0.md`
- generated summary: `E:/output/DocSpectrum/axis_b_correlations_v0_18_n2/axis_b_correlations_v0.json`
- shared context: `HC-20260615-006`, `HC-20260615-007`, `RP-20260615-011`

## [2026-06-15 21:50 MSK · opus via human]

`[AGENT-OK]` Вердикт: чистый read-only Axis-B слой; Spearman корректен, семейства count/ratio/entity разведены, домен не течёт в scoring; Axis B теперь имеет измеримые кандидат-сигналы (не только «отсутствует в jaccard»). Блок-багов нет — с обязательными интерпретационными оговорками.

Находки:
- **#1 МНОЖЕСТВЕННОЕ ТЕСТИРОВАНИЕ (критично).** 2395 корреляций при n≈17-18, без поправки/p-value. Для n=18 критический |Spearman| (p=0.05) ≈ 0.47; на 2395 тестах под шумом ~120 превысят 0.47 случайно, максимум легко достигает 0.75-0.85 → **топ-0.79 не отличим от шума без поправки/репликации**. Это гипотезы, не находки. Добавить p-value+FDR (Benjamini-Hochberg) или хотя бы шумовой порог для n; топ-сигналы валидировать на расширенном/held-out наборе.
- **#2 РАЗМЕРНЫЙ КОНФАУНД.** `document_count`-корреляции частью тривиальны (больше дом → больше документ) = size-baseline, не инженерный сигнал. Гипотеза HC-006 живёт в `document_ratio` (per-page) и `entity` (size-контролируемых). metric_family тегируется ✓; нужна интерпретац. пометка + на будущее частная корреляция с контролем page_count.
- **#3** метрики на базе `page_signature` пометить strictness-prone/diagnostic (RP-008).
- **Энкодинг:** согласен — починка на уровне extractor/registry, не в Axis B builder.

Ответы: Q1 метрики достаточны + значимость/FDR; Q2 Spearman+bucket верны, n страховать FDR; Q3 да — полагаться на ratio/entity, raw-count не читать как инженерный сигнал; Q4 да, page_signature→diagnostic; Q5 приоритет — расширить корпус для репликации (множественность лечится только большим n / held-out), потом near-match page-bucketing.

Evidence:
- review relay from Opus, 2026-06-15
- `docspectrum@bc71359..c8762e2`
- verified Spearman impl, 3 families, n≈17-18 × 2395 corr (множественность), page_signature в топ-сигналах
