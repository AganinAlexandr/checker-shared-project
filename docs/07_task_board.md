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
| TSK-004 | TOC | Детектор пар на ячейках explorer | review | opus | `discussion/threads/T-20260613-001...md`, run `opus_tocpairs_20260613_0201` | Первый прогон ок: KR храм 25 TOC + 40 Состав; открыт гейт Содержания с голым №стр (1480) |
| TSK-005 | Evaluation | `commons/eval/eval_set.csv` | todo | opus | `E:\commons\checker-shared-project` | Нужен базовый набор разделов/CRC/наличие GT |
| TSK-006 | Шаг 1.1 | Проверка существенности битой кодировки | todo | TBD | `docs/01_plan.md`, `reason_codes.md` | Нужны правила подсчета локальной/существенной битой кодировки |
| TSK-007 | Шаг 1.2 | Поиск и отсечение титульной зоны | todo | TBD | `docs/01_plan.md` | Нужны признаки обложек/титулов и связь с Explorer/Checker |
| TSK-008 | Шаг 1.3 | Поиск первого блока BODY | active | opus/codex | `docs/01_plan.md`, `page_taxonomy` | Сравнить методы только после gates 1.1/1.2 |
| TSK-009 | Шаг 2 | Построение и выбор TOC/BODY корпусов | active | opus/codex | `contracts/data_contract.md` | Уточнить роль explorer-pairs и ПП87-score |
| TSK-010 | Шаг 3 | Item-level verdicts | todo | TBD | `contracts/metrics_contract.md` | Не начинать без согласованных корпусов и метрик |

## Ближайшие задачи

1. Opus открывает первую нить в `discussion/threads/` с разбором синергии и планом по explorer-pairs.
2. Codex отвечает в той же нити: подтверждение контракта, риски, минимальный формат результата.
3. Opus строит детектор пар на `table_cells.csv` explorer.
4. Параллельно создается `E:\commons\checker-shared-project\eval\eval_set.csv`.
5. После первого прогона результат заносится в `registries/runs_registry.csv` и кратко отражается здесь.

## Правило обновления

Эта доска обновляется после значимых изменений, но не заменяет:
- `discussion/` для обсуждений;
- `docs/06_decisions.md` для принятых решений;
- `registries/runs_registry.csv` для формальных прогонов;
- `contracts/` для обязательных форматов данных.
