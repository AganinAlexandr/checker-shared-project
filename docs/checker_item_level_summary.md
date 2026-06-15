# Сводка Checker item-level (opus_checker, pp87-checker)

Покрытие размеченных `_ввод`-разделов: **144/144** (ПЗ/ИУЛ исключены).
Версионный оверлей ПП87 активен (старая ред. → wrong_text_old_edition).

## TOC (содержание) — vs `_ввод.tocResult`

- verdict: **1269** (известных пробелов вне метрики — см. ниже)
- present/missing: **1111/1269 (88%)**
- 4-класса: **831/1269 (65%)**

Матрица (строка=факт gt, столбец=предсказание):

| gt\pred | ok | missing | wrong_text | wrong_marker |
|---|---|---|---|---|
| ok | 432 | 57 | 104 | 6 |
| missing | 0 | 133 | 20 | 5 |
| wrong_text | 79 | 16 | 105 | 2 |
| wrong_marker | 11 | 60 | 78 | 161 |

reason-коды: ok=522, missing=266, wrong_text=142, wrong_marker_decimal_for_letter=112, wrong_text_shortened_or_missing_tokens=105, wrong_marker_format=35, wrong_marker_text_points_expected=27, wrong_text_marker_points_elsewhere=23, wrong_text_old_edition=20, wrong_text_extra_or_overflow=15, wrong_text_rephrased_or_shuffled=2

## BODY (тело) — vs `_ввод.bodyResult`

- verdict: **2170** (известных пробелов вне метрики — см. ниже)
- present/missing: **1831/2170 (84%)**
- 4-класса: **1342/2170 (62%)**

Матрица (строка=факт gt, столбец=предсказание):

| gt\pred | ok | missing | wrong_text | wrong_marker |
|---|---|---|---|---|
| ok | 524 | 97 | 182 | 58 |
| missing | 4 | 297 | 63 | 30 |
| wrong_text | 73 | 41 | 197 | 43 |
| wrong_marker | 3 | 104 | 130 | 324 |

reason-коды: ok=604, missing=539, wrong_text=255, wrong_marker_text_points_expected=229, wrong_text_shortened_or_missing_tokens=205, wrong_marker_decimal_for_letter=202, wrong_text_extra_or_overflow=59, wrong_text_old_edition=31, wrong_marker_format=24, wrong_text_marker_points_elsewhere=19, wrong_text_rephrased_or_shuffled=3

## Known gaps (вне чистой метрики)

- TOC section-error: {'broken_encoding': 3, 'scan': 13}
- BODY false-missing по причинам: {'no_nearby_candidate': 215, 'rejected_inside_table': 21, 'body_zone_or_corpus_miss': 5, 'heading_continuation_missed': 1}
  - no_nearby_candidate = графические/спец. тела (gt_suspect, не гоняем)
  - rejected_inside_table = правило «таблица не источник BODY-блока» ([NEEDS-HUMAN])

Источник: out/item_level_toc.csv, out/body_item_verdicts.csv, out/body_missing_audit.csv