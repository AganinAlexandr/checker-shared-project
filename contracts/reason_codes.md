# Reason Codes

Черновой справочник причин для методов Checker.

## Candidate reject reasons

- `inside_table_not_toc_pair` - кандидат находится в таблице, но используется не как пара TOC-корпуса.
- `bad_encoding` - текст кандидата имеет признаки битой кодировки.
- `local_bad_encoding_item` - битая кодировка локальна и относится к 1-2 пунктам, по которым можно выставить отдельные замечания.
- `substantial_bad_encoding_783` - битая кодировка существенна для раздела и требует общего замечания по нарушению требований Приказа 783.
- `scanned_page` - страница требует OCR.
- `text_outlined_suspect` - текст подозрительно представлен кривыми.
- `excluded_title_page` - кандидат находится в титульной зоне, исключенной из поиска первого блока.
- `not_marker` - найденный маркер не прошел грамматику маркеров.
- `marker_level_mismatch` - уровень маркера не подходит текущей последовательности.
- `toc_page_number_tail` - после heading идет номер страницы, а не body_text.
- `no_body_text_after_heading` - после heading нет раскрывающего текста блока.
- `low_pp87_score` - слабое совпадение с ПП87.
- `wrong_zone` - кандидат принадлежит другой зоне.

## Corpus selection reasons

- `selected_by_structure_and_pp87` - выбран по сочетанию структуры и ПП87-score.
- `selected_by_manual_gt` - выбран по ручной истине для проверки.
- `rejected_toc_copy` - отклонена TOC-копия, перетягивающая BODY.
- `rejected_no_blocks` - корпус не содержит достаточного числа блоков.
- `rejected_table_block` - корпус пытается использовать таблицу как источник блоков.
- `rejected_non_monotonic` - нарушена монотонность последовательности.
- `rejected_low_coverage` - недостаточное покрытие ожидаемых пунктов.

## Verdict reasons

- `evidence_text_match` - текст совпал с ПП87.
- `evidence_marker_match` - маркер совпал с ожидаемым.
- `missing_no_candidate` - кандидат по пункту не найден.
- `wrong_text_marker_points_elsewhere` - маркер указывает на один пункт, текст соответствует другому.
- `wrong_text_old_edition` - текст соответствует старой редакции ПП87, но не последней редакции; substantive verdict остается `wrong_text`, reason информирует о причине.
- `wrong_marker_text_points_expected` - текст соответствует пункту, но маркер неверный.
- `wrong_marker_format` - текст и канонический маркер указывают на ожидаемый пункт, но raw-форма маркера отличается от ожидаемой формы; информационный подтип, не substantive item-level defect.
- `wrong_version_old_template` - найдено соответствие старой редакции ПП87.
- `not_applicable_scope` - пункт не применим к объекту/виду работ.
- `unchecked_input_quality` - проверка невозможна из-за качества входного PDF.
