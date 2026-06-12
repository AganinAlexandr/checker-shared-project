# Интеграция pdf-structure-explorer с атомами Checker

Статус: draft
Дата: 2026-06-13

## 1. Назначение

`pdf-structure-explorer` дает независимый физический/геометрический слой PDF.
Checker использует этот слой для построения доменных атомов, пар, блоков, корпусов и доказательств замечаний.

Интеграция нужна минимум для сравнения методов, максимум - как основание основного алгоритма.

## 2. Входные таблицы pdf-structure-explorer

Источник: `E:\output\pdf-structure-explorer\exports\doc_<crc32>`.

Связь раздела/объекта с export-bundle уже ведется через:
`E:\output\pdf-structure-explorer\Elements_PDF_document_links.xlsx`, лист `document_links`.

Ключевые поля связи:
- `document_id` / `export_document_id`;
- `file_crc32`;
- `object_code`;
- `section_type`;
- `file_name`;
- `match_mode` при наличии в выгрузке.

Отдельный реестр объектов для этой связи не требуется, пока `document_links` покрывает нужные разделы.

Правило резолва bundle:
1. использовать `export_document_id`, если он заполнен и папка `exports\<export_document_id>` существует;
2. иначе пробовать `doc_<lower(file_crc32)>`;
3. после выбора папки обязательно сверять `documents.csv` по `file_crc32` и `file_name`.

Нельзя без проверки предполагать, что `document_id` из `document_links` всегда равен `doc_<file_crc32>`.

Обязательные таблицы:
- `documents.csv` - `document_id`, `file_crc32`, `file_name`, `page_count`, `has_text_layer`, `parse_status`.
- `page_summary.csv` - `page_kind`, counts, `broken_encoding_count`.
- `text_segments.csv` - текстовые фрагменты, координаты, язык, кодировка.
- `elements.csv` - тип элемента, bbox, font/style поля, связи с text/table/image.
- `tables.csv` - области таблиц, confidence, размеры.
- `table_cells.csv` - ячейки таблиц, текст, координаты.

## 3. Слои сущностей

### 3.1 Physical atoms

Физические атомы приходят из PDF Explorer:
- `text_segment`;
- `table_cell`;
- `table_region`;
- `page_quality`;
- `vector_element`;
- `image_region`.

Они не являются напрямую `marker`, `heading` или `body_text`, но служат исходным материалом и evidence.

### 3.2 Checker atoms

Checker строит доменные атомы:
- `marker_atom`;
- `heading_atom`;
- `body_text_atom`;
- `toc_pair_atom`;
- `page_quality_atom`;
- `style_signal_atom`;
- `punctuation_signal_atom`.

### 3.3 Higher entities

Из атомов собираются:
- `pair = marker + heading`;
- `block = marker + heading + сразу body_text`;
- `toc_corpus`;
- `body_corpus`;
- `item_verdict`.

## 4. Таблица связи physical -> checker atoms

Предлагаемая таблица: `atom_bindings.csv`.

Поля:
- `run_id`
- `document_id`
- `file_crc32`
- `page_number`
- `checker_atom_id`
- `checker_atom_type`
- `source_bundle_path`
- `source_table`
- `source_row_id`
- `source_element_id`
- `source_text_segment_id`
- `source_table_id`
- `source_cell_id`
- `x1`
- `y1`
- `x2`
- `y2`
- `source_text`
- `normalized_text`
- `binding_role`
- `binding_confidence`
- `binding_reason`

`binding_role` примеры:
- `marker_source`
- `heading_source`
- `body_text_source`
- `toc_pair_source`
- `table_exclusion_source`
- `page_quality_source`
- `style_signal_source`

## 5. Нормализованные атомы Checker

Предлагаемая таблица: `normalized_atoms.csv`.

Поля:
- `run_id`
- `document_id`
- `file_crc32`
- `page_number`
- `atom_id`
- `atom_type`
- `x1`
- `y1`
- `x2`
- `y2`
- `text`
- `normalized_text`
- `is_inside_table`
- `table_role_guess`
- `font_name`
- `font_size`
- `is_bold`
- `is_caps`
- `marker_text`
- `marker_type`
- `marker_level`
- `pp87_item_id`
- `score_json`
- `quality_flags`
- `source_binding_ids`

## 6. Правила использования таблиц

1. Таблица не может быть источником `block`.
2. Таблица может быть источником только `toc_pair_atom` и далее `pair` для TOC-корпуса.
3. Текстовые сегменты внутри таблицы не участвуют в построении body-блоков.
4. Для каждого `checker_atom` должна сохраняться связь с физическим источником через `atom_bindings.csv`.
5. Если метод не использует PDF Explorer, он всё равно должен уметь экспортировать совместимый `normalized_atoms.csv` с пустыми source-полями или альтернативными source-полями.
6. Для сравнения методов главным ключом является `document_id + page_number + bbox + normalized_text`.

## 6.1. Минимальный контракт чтения TOC-пары из `table_cells.csv`

Статус: accepted-for-experiment.

TOC-пара из таблицы строится только из `table_cells.csv` / `tables.csv` explorer-bundle.

Минимальный кандидат `toc_pair_atom` должен содержать:
- `document_id`;
- `file_crc32`;
- `page_number`;
- `table_id`;
- `cell_id` или список `cell_id`;
- `row_index`;
- `column_index` или роль колонки;
- `marker_text`;
- `heading_text`;
- `page_ref_text` при наличии;
- `x1`, `y1`, `x2`, `y2`;
- `normalized_text`;
- `binding_confidence`;
- `binding_reason`.

Минимальные правила:
1. `toc_pair_atom` не может становиться `block`.
2. `body_text_atom` из таблицы не строится.
3. Если строка таблицы содержит `marker + heading + page_ref`, это кандидат пары TOC.
4. Если в таблице есть колонки вида `Наименование`, `Примечание`, `Лист`, `Стр.`, они используются для определения ролей колонок.
5. Если маркер и heading находятся в разных ячейках одной строки, связь фиксируется через несколько `source_cell_id`.
6. Если таблица похожа на `Состав проектной документации`, это отдельный тип кандидата и не должен напрямую соревноваться как правильный TOC без проверки ПП87-score и контекста.
7. Строка таблицы читается как базовая единица: группировка `table_id + row_index`, затем роли ячеек (`marker`, `heading`, `page_ref`, `composition_code`) определяются внутри строки.
8. Шапка таблицы пропускается по словам колонок (`Наименование`, `Примечание`, `Лист`, `Стр.`, `Обозначение`) и/или отсутствию маркерного кандидата.
9. Для таблиц состава ПД используется отдельный `binding_role=project_composition_source`.

## 7. Открытые вопросы

1. Делать ли `normalized_atoms.csv` обязательным входом всех методов или только рекомендуемым диагностическим экспортом?
2. Как лучше задавать `source_row_id` для CSV без стабильного row id?
3. Нужно ли хранить агрегированные строки/абзацы как отдельные physical atoms или только как checker atoms?
4. Где фиксировать преобразование нескольких `text_segment` в один `heading_atom`?
5. Как учитывать OCR-слой, когда он появится?
6. Нужна ли необязательная надстройка `has_page_taxonomy` / `has_vvod` поверх `document_links`, или наличие ground truth вычисляется на лету?
