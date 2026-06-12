# Контракт метрик

## Входное качество PDF

- `pages_total`
- `page_kind_vector_count`
- `page_kind_hybrid_count`
- `page_kind_scanned_count`
- `page_kind_text_outlined_suspect_count`
- `broken_encoding_segments_count`
- `has_text_layer`
- `has_tables`

## Шаг 1: первый блок

- `first_block_page_exact`
- `first_block_page_pm1`
- `first_block_marker_exact`
- `first_block_heading_exact_or_equivalent`
- `first_block_fail_reason`

## Шаг 2: корпуса

- `body_corpus_selected_correct`
- `toc_corpus_selected_correct`
- `toc_to_body_leakage_count`
- `body_to_toc_leakage_count`
- `corpus_coverage_score`
- `corpus_monotonic_score`

## Шаг 3: замечания

Считаются отдельно по типам:
- `missing_precision`, `missing_recall`, `missing_f1`
- `wrong_text_precision`, `wrong_text_recall`, `wrong_text_f1`
- `wrong_marker_precision`, `wrong_marker_recall`, `wrong_marker_f1`
- `wrong_version_precision`, `wrong_version_recall`, `wrong_version_f1`
- `macro_f1`
- `any_defect_precision`, `any_defect_recall`, `any_defect_f1`

## Обязательное правило

Presence-F1 не считается продуктовой метрикой качества замечаний. Он может использоваться только как внутренняя диагностическая метрика поиска.
