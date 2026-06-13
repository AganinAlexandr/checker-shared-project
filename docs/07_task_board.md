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
| TSK-002 | Связь данных | Раздел -> CRC32 -> `doc_<crc32>` -> explorer bundle | done | opus/codex | `contracts/pdf_structure_integration.md` | Используем `Elements_PDF_document_links.xlsx`, отдельный objects registry не нужен |
| TSK-003 | Связь данных | Physical atoms -> Checker atoms | active | codex/opus | `contracts/pdf_structure_integration.md` | Нужна первая реализация `atom_bindings.csv` / `normalized_atoms.csv` |
| TSK-004 | TOC | Детектор пар на ячейках explorer | done | opus | run `opus_tocpairs_20260613_0410`, `opus_checker@7ff5d21` | Все 3 ветки §6.2 ок; тех-долг полей закрыт (raw_row_text/marker/heading/page_ref раздельно); детектор закоммичен 7ff5d21 |
| TSK-011 | TOC | RECALL содержания: разворот на ПП87-матч (без №стр) | active | opus | run `recall_v3_20260613`, `opus_checker@577439c` | UNION recall 70% (10) → **88% (23/26)** после production-интеграции: best-of-both записи+сырые, anchor «Содержание», earliest-cluster (ложных стр 11→5), резолв по индексу выгрузок. Остаток: ООС/СС scope, ЭС-ИОС1 табличное (table-pillar). Далее: item-level present/missing/wrong |
| TSK-005 | Evaluation | `commons/eval/eval_set.csv` | todo | opus | `E:\commons\checker-shared-project` | Нужен базовый набор разделов/CRC/наличие GT |
| TSK-006 | Шаг 1.1 | Проверка существенности битой кодировки | todo | TBD | `docs/01_plan.md`, `reason_codes.md` | Нужны правила подсчета локальной/существенной битой кодировки |
| TSK-007 | Шаг 1.2 | Поиск и отсечение титульной зоны | todo | TBD | `docs/01_plan.md` | Нужны признаки обложек/титулов и связь с Explorer/Checker |
| TSK-008 | Шаг 1.3 | Поиск первого блока BODY | active | opus/codex | `docs/01_plan.md`, `page_taxonomy` | Сравнить методы только после gates 1.1/1.2 |
| TSK-009 | Шаг 2 | Построение и выбор TOC/BODY корпусов | review | opus/codex | A/B `opus_checker@8f79060`, `out/ab_cutoff_firstblock.csv` | candidate_corpora.csv готов; A/B: отсечка ИНЕРТНА для first-block (0 улучш/0 регресс; cutoff_active 3/35). Редирект усилий → item-level TOC present/missing/wrong (TSK-010); cutoff оставлен, не вредит |
| TSK-010 | Шаг 3 | Item-level verdicts | todo | TBD | `contracts/metrics_contract.md` | Не начинать без согласованных корпусов и метрик |
| TSK-011 | SPDS | Извлечение текста внутри СПДС-рамки | todo | TBD | `contracts/spds_frame_text_contract.md` | Не считать решенным: нужен отдельный эксперимент и метрики |

## Ближайшие задачи

1. Opus открывает первую нить в `discussion/threads/` с разбором синергии и планом по explorer-pairs.
2. Codex отвечает в той же нити: подтверждение контракта, риски, минимальный формат результата.
3. Opus строит детектор пар на `table_cells.csv` explorer.
4. Параллельно создается `E:\commons\checker-shared-project\eval\eval_set.csv`.
5. После первого прогона результат заносится в `registries/runs_registry.csv` и кратко отражается здесь.
6. Отдельно запустить TSK-011: проверить, что текст внутри СПДС-рамки извлекается надежно, а штамп/внешние элементы не смешиваются с body.

## Правило обновления

Эта доска обновляется после значимых изменений, но не заменяет:
- `discussion/` для обсуждений;
- `docs/06_decisions.md` для принятых решений;
- `registries/runs_registry.csv` для формальных прогонов;
- `contracts/` для обязательных форматов данных.
