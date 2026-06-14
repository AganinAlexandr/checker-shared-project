# DocSpectrum Review Packets

Чекпойнты для Opus-review по проекту `DocSpectrum`.

Канонический шаблон задан в:

- `discussion/README.md`
- `DEC-20260614-004 Collaboration workflow v2`

Этот файл фиксирует DocSpectrum-адаптацию без дублирования общего workflow.

## Обязательный принцип

`range` + `focus` обязательны и приоритетны.

Reviewer смотрит committed diff и последствия конкретной дельты, а не перечитывает весь проект.

## Минимальный шаблон

```markdown
# RP-YYYYMMDD-NNN: Краткое название milestone

Статус: open
Проект: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [YYYY-MM-DD HH:MM · codex]

`[FYI|AGENT-OK|NEEDS-HUMAN]` Краткая суть.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@<prev>..<this>
- focus: что именно ревьюить (файлы/схемы/метрики/решения) и последствия
- inputs: какие данные/выгрузки использованы
- metrics: что изменилось и какие значения получены
- risks/regressions: что могло сломаться или исказиться
- not touched: что сознательно не трогалось
- domain rule: использовано/изменено (ссылка HC-/DEC-/contract/notes)

Evidence:
- commit / paths / artifacts / metrics
```

## Когда создавать packet

Review packet создаётся на заметный milestone, а не на каждый микрокоммит.

Примеры milestone для `DocSpectrum`:

- новая версия `element_base`;
- новая версия `section_passport`;
- новая версия `comparison_result`;
- изменение нормализации или метрик;
- добавление нового корпуса или внешнего объекта;
- изменение доменного правила или словаря.

## Атомарность

Пуш кода/артефактов в рабочий репозиторий и review packet в shared должны выполняться в одном ходе.

Если код ещё не закоммичен или не запушен, packet можно подготовить как draft, но он не считается полноценным review packet.
