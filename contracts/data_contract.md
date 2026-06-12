# Контракт данных методов

Каждый метод, претендующий на сравнение или включение в основной алгоритм, должен уметь отдавать диагностический экспорт в общей форме.

## method_run.csv

- `run_id`
- `method_id`
- `method_version`
- `agent_id`
- `repo_path`
- `git_commit`
- `document_id`
- `file_crc32`
- `pp87_version_id`
- `input_bundle_path`
- `parameters_json`
- `started_at`
- `finished_at`
- `status`

## candidate_elements.csv

- `run_id`
- `candidate_id`
- `document_id`
- `page_number`
- `zone_guess`
- `source_kind`
- `element_id`
- `text_segment_id`
- `table_id`
- `cell_id`
- `x1`
- `y1`
- `x2`
- `y2`
- `marker_text`
- `marker_type`
- `marker_level`
- `heading_text`
- `body_tail_text`
- `is_inside_table`
- `font_name`
- `font_size`
- `is_bold`
- `is_caps`
- `pp87_item_id`
- `token_score`
- `idf_score`
- `bigram_score`
- `structure_score`
- `final_score`
- `reject_reason`

## candidate_corpora.csv

- `run_id`
- `corpus_id`
- `document_id`
- `zone`
- `candidate_ids`
- `start_page`
- `end_page`
- `marker_sequence_type`
- `pair_count`
- `block_count`
- `table_candidate_count`
- `coverage_score`
- `monotonic_score`
- `pp87_match_score`
- `structure_score`
- `final_score`
- `is_selected`
- `selection_reason`

## item_verdicts.csv

- `run_id`
- `document_id`
- `pp87_item_id`
- `expected_marker`
- `found_marker`
- `status`
- `evidence_candidate_id`
- `evidence_corpus_id`
- `confidence_score`
- `reason_code`
- `comment`

## PDF structure integration

Связь данных `pdf-structure-explorer` с атомами Checker описана в `contracts/pdf_structure_integration.md`.
Если метод использует структурный PDF-bundle, он должен экспортировать `atom_bindings.csv` и `normalized_atoms.csv` либо указать причину, почему связь не построена.
