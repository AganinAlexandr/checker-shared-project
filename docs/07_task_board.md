# Task Board

Дата создания: 2026-06-13.

Назначение: человекочитаемая доска текущих задач, чтобы видеть общий ход проекта без чтения всех CSV, коммитов и нитей обсуждения.

Статусы:
- `todo` - задача сформулирована, работа не начата.
- `active` - задача в работе.
- `review` - результат получен, нужен просмотр/обсуждение.
- `blocked` - есть препятствие.
- `done` - результат принят и перенесен в решения/контракты/реестры.

## Сводная доска

| ID | Блок | Задача | Статус | Ведущий | Связанные файлы | Результат / следующий шаг |
|---|---|---|---|---|---|---|
| TSK-000 | Координация | Общий слой `checker-shared-project` | done | codex | `docs/`, `contracts/`, `registries/` | Каркас создан, git-коммиты есть |
| TSK-001 | Координация | Append-only discussion channel | done | codex | `discussion/README.md`, `discussion/INDEX.md` | Канал создан, Opus может открыть первую нить |
| TSK-002 | Связь данных | Раздел -> CRC32 -> `doc_<crc32>` -> explorer bundle | done | opus/codex | `contracts/pdf_structure_integration.md`, `DEC-20260615-006` | Канон: crc32 контента → `doc_<crc32>`, рекурсивно (вкл. архив). crc-FIRST резолв (`opus_checker@18a400d`) → иммунитет к мутации общего exports. Тестсет закреплён manifest 143/143 (`build_checker_manifest.py`) |
| TSK-003 | Связь данных | Physical atoms -> Checker atoms | active | codex/opus | `contracts/pdf_structure_integration.md` | Нужна первая реализация `atom_bindings.csv` / `normalized_atoms.csv` |
| TSK-004 | TOC | Детектор пар на ячейках explorer | done | opus | run `opus_tocpairs_20260613_0410`, `opus_checker@7ff5d21` | Все 3 ветки §6.2 ок; тех-долг полей закрыт (raw_row_text/marker/heading/page_ref раздельно); детектор закоммичен 7ff5d21 |
| TSK-011 | TOC | RECALL содержания: разворот на ПП87-матч (без №стр) | done | opus | run `recall_v3_20260613`+`blind_sweep`, `opus_checker@816c425` | UNION recall 70%(10)→**88%(23/26)**. Blind sweep 96 bundle: переобучения нет (late 2/84, anchor-only 4/84). max_gap=1 accepted-for-experiment. Заморожено. Known gaps: ООС/СС scope, ЭС-ИОС1 табличное, 2 late-false TOC |
| TSK-012 | Item-level | present/missing/wrong против `_ввод`/`_тест` | done | opus | `_ввод_*.json`, `opus_checker@3016202`, `runs_registry:itemlevel_toc_20260617` | **ПРИНЯТО human 2026-06-17. TOC 90% p/m (70% 4-класса, 139/1414), BODY 84% p/m (62%, 2170)** на crc-pinned 143/143. Плато item-level-тюнинга достигнуто осмысленно (рост сессии — качество измерения: gt+scope). Остаток ошибок структурный: ok↔wrong_text ~172 (морфология, #6), маркерная ось ~120, kg/табличные 382 → Шаг 2. Дальнейшие рычаги — структурные шаги, не тюнинг |
| TSK-005 | Evaluation | `commons/eval/eval_set.csv` | todo | opus | `E:\commons\checker-shared-project` | Нужен базовый набор разделов/CRC/наличие GT |
| TSK-006 | Шаг 1.1 | Проверка существенности битой кодировки | active | opus | `opus_checker@HEAD`, `batch_validate.page_raw_garble`, `docs/01_plan.md` | ✅ Gate сделан: `broken_encoding`=доля битости 3 видов (`char_garble`: латиница/Latin-1 þú/C0+PUA), НЕ explorer `encoding_status` (он Вид1 пропускал → 1866 АР/КР=0). Валид: 131/137<0.1, фолс-фолзов нет; >0.2 → section_error «находка Приказ 783» (4 разд: 60-23 ВО, 1866 АР/КР, 1325 КР). ◻ Остаток: ПОРЕГИОННОЕ прощение (`mark_broken` уже в batch_validate) для частичной битости (<0.2: 68-23 ОВ, 1039 АР) — битые пункты → замечание «broken», читаемые соседи проверяются по ПП87 (правило human: 1-2 абзаца → зам., остальное смотрим) |
| TSK-007 | Шаг 1.2 | Поиск и отсечение титульной зоны | done | opus | `opus_checker@HEAD:title_zone.py`, ГОСТ Р 21.101-2020 | Детектор: якорь титула = standalone «ПРОЕКТНАЯ ДОКУМЕНТАЦИЯ»+«Раздел» (чистый разделитель cover 64/81 vs контент 0/34) + поглощение пустых разделителей. **cover_end exact 100%/±1 100% на 34 taxonomy, ложных аномалий 0.** Fallback: растровый/скан-титул (image_count+контент-граница)→`image_title`, смета-якорь, `displaced`/`missing`→замечание ГОСТ. **Интегрировано в пайплайн**: title_anomaly = находка (контент проверяется, НЕ section_error); метрики без регрессии (988/1411 70%/90%); на тестсете 6 находок (image_title 2/no_title 3/not_extractable 1). Кросс-проект: `title_pages_by_crc.csv` (451 bundle high443/low8) для DocSpectrum (crc32). Число титулов не хардкодим (1/2/4). |
| TSK-008 | Шаг 1.3 | Поиск первого блока BODY | active | opus/codex | `docs/01_plan.md`, `page_taxonomy` | Сравнить методы только после gates 1.1/1.2 |
| TSK-009 | Шаг 2 | Построение и выбор TOC/BODY корпусов | review | opus/codex | A/B `opus_checker@8f79060`, `out/ab_cutoff_firstblock.csv` | candidate_corpora.csv готов; A/B: отсечка ИНЕРТНА для first-block (0 улучш/0 регресс; cutoff_active 3/35). Редирект усилий → item-level TOC present/missing/wrong (TSK-010); cutoff оставлен, не вредит |
| TSK-010 | Шаг 3 | Item-level verdicts | todo | TBD | `contracts/metrics_contract.md` | Не начинать без согласованных корпусов и метрик |
| TSK-011 | SPDS | Извлечение текста внутри СПДС-рамки | todo | TBD | `contracts/spds_frame_text_contract.md` | Не считать решенным: нужен отдельный эксперимент и метрики |
| TSK-013 | Upstream/explorer | Поправить детекцию битой кодировки в `pdf-structure-explorer` | todo | fable (raise: opus) | `pdf-structure-explorer`, `batch_validate.char_garble` | **explorer — наш upstream-источник выгрузок**, поэтому это и наша забота, хоть проект ведёт другая ветка. `encoding_status` ловит НЕ все виды: Вид 1 (ASCII-латиница, подмена кириллицы) НЕ флагается → 1866 АР/КР (garble ~0.9-1.0) показывал `encoding_status=ok`. Виды: 1 ASCII-латиница (токен), 2 Latin-1 þ/ú (символ), 3 C0/PUA (символ). Эталон-детектор есть у нас (`batch_validate.page_raw_garble`). Предложить explorer считать `raw_garble_ratio`/`garble_kind` на сегмент/страницу при выгрузке → потребителям не пере-выводить |

## Ближайшие задачи

1. Opus открывает первую нить в `discussion/threads/` с разбором синергии и планом по explorer-pairs.
2. Codex отвечает в той же нити: подтверждение контракта, риски, минимальный формат результата.
3. Opus строит детектор пар на `table_cells.csv` explorer.
4. Параллельно создается `E:\commons\checker-shared-project\eval\eval_set.csv`.
5. После первого прогона результат заносится в `registries/runs_registry.csv` и кратко отражается здесь.
6. Отдельно запустить TSK-011: проверить, что текст внутри СПДС-рамки извлекается надежно, а штамп/внешние элементы не смешиваются с body.

## Подпланы активных задач (Checker, обновлено 2026-06-16, opus)

### TSK-012 (review → done): добивка item-level
- ✅ **scope-корректность СДЕЛАНО** (opus_checker@3016202): шифры нормализованы (ИОС5.N/ПЗУ→СПОЗУ), scope из имени `_ввод`; 66-23 ТХ/68-23 ОДИ больше не мерят против чужого ПП87; no_scope 21→5. Истинно «вне ПП87»: ИЛО/ТКР/ППО (scope пуст) — теперь в явном списке с причиной.
- ✅ **gt вычищен**: 28 правок за сессию; gt_suspect=1 (66-23 ТХ·ж — принятый детекторный лимит, open_q методы#6).
- ✅ **метрики занесены**: `runs_registry:itemlevel_toc_20260617` / `body_item_20260617`.
- ◻ **TOC без аннотации**: часть разделов в BODY, но не в TOC (нет `tocResult`) — дозаполнить или пометить kg (некритично для done).
- ◻ **критерий done**: приёмка human (метрики TOC 70%/90%, BODY 62%/84% на 143/143).

### TSK-008 (Шаг 1.3): первый блок BODY
- зафиксировать метрику первого блока на закреплённом 143/143 (`bodyPageStart` vs gt-начало body);
- блок = `marker + heading + immediate body_text`; оформить правило поглощения вложенного уровня (таблица/подзаголовок сразу после heading);
- сравнение методов (TS `detectBodyPageStart` vs Python) — только ПОСЛЕ закрытия gates 1.1/1.2.

### Шаг 1.1 (TSK-006): существенность битой кодировки
- правило: 1-2 пункта → локальные замечания, проверка продолжается; >2 / существенная часть → общее замечание (Приказ 783) + ограниченный режим;
- в коде уже есть `section_error="broken_encoding"` (порог >50) — формализовать в правило + reason_code + метрику на тестсете.

### Шаг 1.2 (TSK-007): титульная зона
- признаки обложек/титулов (обычно 2, при субподряде 4+); исключение зоны из пространства поиска первого блока;
- сейчас работает НЕЯВНО через `bodyPageStart`/`tocPageEnd` (TS exact 24/26) — выделить в отдельный gate с собственной метрикой.

## Правило обновления

Эта доска обновляется после значимых изменений, но не заменяет:
- `discussion/` для обсуждений;
- `docs/06_decisions.md` для принятых решений;
- `registries/runs_registry.csv` для формальных прогонов;
- `contracts/` для обязательных форматов данных.
