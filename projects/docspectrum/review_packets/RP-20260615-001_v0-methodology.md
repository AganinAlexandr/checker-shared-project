# RP-20260615-001: DocSpectrum v0 methodology checkpoint

Статус: open
Проект: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 00:58 MSK · codex]

`[REVIEW]` Первый полноценный milestone для Opus-review: `element_base_v0`, быстрые и детальные сравнения, подвекторная диагностика и аналитический Excel/PQ слой.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@4c3254e..d64cc89
- focus: методика измерения v0 — `tools/build_element_base_v0.py`, `tools/compare_pairs_v0.py`, `tools/build_detailed_comparison_v0.py`, `samples/*_v0`, `analytics/*`; прошу ревьюить косинусы/центрирование, веса, сигнатуры страниц/таблиц, ограничения текущего корпуса и пригодность артефактов для следующего объекта
- secondary context: логические модели `schemas/section_passport_model.md` и `schemas/comparison_result_model.md` включены в range, но не являются главным фокусом первого ревью
- inputs: explorer exports из `E:/output/DocSpectrum/export` по двум объектам `1724_25` / `1725_25`, 16 bundle, 8 пар одинаковых разделов
- metrics: `element_base_v0` = 16 паспортов, 384 страницы, 158938 элементов, 37230 текстовых сегментов, 253 таблицы, 11415 ячеек, 343 изображения; `detailed_comparison_results_v0` = 8 пар; для `ИОС5.4.1`: `detailed_similarity_v0=0.5514`, `feature_cosine_v0=0.9998`, `tables_centered_cosine=0.6595`, `graphics_centered_cosine=0.7326`, `exact_text_segment_jaccard=0.4538`
- risks/regressions: текущая логика пар берёт первые 2 объекта по разделу и не готова к N>2 без расширения; raw cosine по положительным признакам завышает сходство; точные SHA1-сигнатуры страниц/таблиц не ловят near-match; статусы `missing/not_applicable/low_confidence/measured_zero` пока не реализованы в v0
- not touched: не реализован production API/UI; не реализованы формальные JSON Schema; не добавлен 3-й объект; не построен экспертный eval_set; не реализована графическая near-match нормализация; не решалась юридическая интерпретация заимствования
- domain rule: `DEC-20260614-004`, `DEC-20260615-005`, `notes/collaboration_workflow_v2.md`, `notes/validation_corpus_strategy.md`, `notes/first_element_base_v0_results.md`; human-принцип: текущая элементная база v0 является экспериментальной и проверяет идею, а не финальную онтологию элементов

Evidence:

- commit: `d64cc89 Add DocSpectrum v0 element base and comparisons`
- GitHub range: `github.com/AganinAlexandr/DocSpectrum` `4c3254e..d64cc89`
- generated artifacts: `samples/element_base_v0`, `samples/comparison_results_v0`, `samples/detailed_comparison_results_v0`, `analytics/excel/DocSpectrum_dynamics_v0.xlsx`
- scripts: `tools/build_element_base_v0.py`, `tools/compare_pairs_v0.py`, `tools/build_detailed_comparison_v0.py`, `tools/build_excel_snapshot_v0.py`

Open questions for reviewer:

- Считать ли `feature_cosine_v0` только диагностическим сигналом и убрать/сильно снизить его вес в `weighted_similarity_v0`?
- Какой near-match слой для страниц/таблиц лучше ввести первым: бакетирование признаков, tolerance по layout, MinHash/SimHash или другой подход?
- Какие минимальные статусы признаков нужны в v0.1 для informative subspace: `measured_zero`, `not_applicable`, `missing`, `low_confidence`?
- Достаточно ли текущих CSV/JSON для ревью и Excel/PQ-витрины, или нужно отделить generated samples от repo и хранить их в output?
