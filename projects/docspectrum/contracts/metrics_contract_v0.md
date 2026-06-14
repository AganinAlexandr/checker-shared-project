# DocSpectrum Metrics Contract v0

Статус: draft
Дата: 2026-06-15

## Назначение

Этот контракт фиксирует текущие исследовательские метрики `DocSpectrum v0` для review-loop.

Метрики v0 не являются финальной моделью сходства. Они нужны, чтобы:

- воспроизводимо сравнивать первые пары разделов;
- видеть, какие оси дают сигнал;
- отделять raw cosine от нормализованных и объяснимых метрик;
- подготовить переход к v0.1 с более честным informative subspace.

## Текущие оси

- `feature_vector` - компактный числовой вектор раздела.
- `feature_subvectors` - подвекторы `volume`, `density`, `text`, `tables`, `graphics`, `element_mix`.
- `page_signature` - точные сигнатуры страниц.
- `table_layout` - точные сигнатуры табличной структуры.
- `table_content` - точные хэши табличного содержимого.
- `exact_text_segments` - точные хэши нормализованных текстовых сегментов.

## Текущие метрики

- `feature_cosine_v0` - raw cosine по положительным количественным признакам; диагностический, не достаточный для вывода о сходстве.
- `subvector_*_cosine` - raw cosine по подвекторам.
- `subvector_*_centered_cosine` - cosine по центрированным признакам относительно текущего корпуса.
- `*_jaccard` - multiset Jaccard по точным сигнатурам/хэшам.
- `detailed_similarity_v0` - временная weighted mix-метрика для исследовательского сравнения.

## Предупреждение по cosine

Raw cosine по положительным количественным признакам часто завышает сходство, если доминирующие координаты почти не изменились.

Для ревью и интерпретации обязательно смотреть:

- подвекторы;
- centered cosine;
- относительные дельты;
- page/table/text axes;
- coverage и причины исключения признаков после их появления.

После review `RP-20260615-001` raw cosine считается диагностической метрикой, а не валидированным scoring-компонентом.

До появления eval_set и нормализации по сопоставимой популяции:

- не повышать вес raw cosine;
- не трактовать `feature_cosine_v0` как доказательство сходства;
- помечать centered cosine по текущему корпусу как `global_mixed_corpus_diagnostic`.

## Текущие веса detailed_similarity_v0

Статус: deprecated for v0.1 planning.

- `feature_cosine_v0`: 0.35
- `page_signature_jaccard`: 0.20
- `table_layout_jaccard`: 0.15
- `table_content_jaccard`: 0.15
- `exact_text_segment_jaccard`: 0.15

## Известные ограничения

- Нет near-match для страниц/таблиц.
- Нет статусов `measured_zero/not_applicable/missing/low_confidence`.
- Корпус пока содержит только 2 объекта.
- Веса временные и требуют eval_set.
