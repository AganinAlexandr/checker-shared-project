# Решения

Формат записи решения:

```text
## DEC-YYYYMMDD-NNN: Название
Статус: proposed / accepted / superseded / rejected
Дата:
Контекст:
Решение:
Последствия:
Ссылки:
```

## DEC-20260613-001: Общий координационный слой

Статус: accepted
Дата: 2026-06-13

Контекст:
При переходе от работы двух участников к работе трех участников нужен общий слой планов, метрик, исходных условий, граничных условий и результатов.

Решение:
Создать общий проект `checker-shared-project` в стандартной структуре `E:\repos`, `E:\commons`, `E:\output`.

Последствия:
Рабочие репозитории участников остаются владельческими лабораториями. Общий слой фиксирует планы, решения, контракты, метрики, ссылки на реализации и результаты.

Ссылки:
- `E:\repos\checker-shared-project`
- `E:\commons\checker-shared-project`
- `E:\output\checker-shared-project`

## DEC-20260613-002: Append-only discussion channel

Статус: accepted
Дата: 2026-06-13

Контекст:
При совместной работе `human`, `codex`, `opus` и `fable` нужен git-дружелюбный аналог общего чата разработки, где можно обсуждать предложения до переноса итогов в решения.

Решение:
Создать `discussion/` внутри `E:\repos\checker-shared-project`.
Одна тема обсуждения хранится в одном файле `discussion/threads/T-YYYYMMDD-NNN_slug.md`.
Ответы добавляются append-only блоками с автором, датой и evidence.

Последствия:
`discussion/` используется для кросс-агентного обсуждения.
`docs/06_decisions.md` остается долговременным журналом принятых решений.
`handoff/` остается для снимков состояния рабочих сессий.

Ссылки:
- `E:\repos\checker-shared-project\discussion\README.md`
- `E:\repos\checker-shared-project\discussion\INDEX.md`

## DEC-20260613-003: Этапный протокол обмена (теги + триггеры человека)

Статус: accepted
Дата: 2026-06-13

Контекст:
Человек не успевает per-step разбирать детали и гонять PQ/Excel по большим объёмам; агенты работают намного быстрее. Нужна этапность обмена по завершении участков работы + точечное внимание человека к заметным изменениям.

Решение:
Каденция — чекпойнт в канал ПО ЗАВЕРШЕНИИ участка работы (человеко-читаемая выжимка + evidence + тег; сырьё в `output/`, не в канал). Теги `[FYI]` / `[AGENT-OK]` / `[NEEDS-HUMAN]`. Триггеры `[NEEDS-HUMAN]`: метрика за порог/регрессия; смена доменного правила/контракта; необратимое действие; неразрешимое evidence-расхождение opus↔codex. Медиум — прямая git-нить `discussion/`; человеческий релей = страховка; человек вмешивается в любой момент.

Последствия:
Петля движется в темпе агентов; человек смотрит заметное async и обсуждает по завершении участков.

Ссылки:
- `discussion/threads/T-20260613-002_collaboration-protocol.md`

## DEC-20260614-004: Collaboration workflow v2 (ретро 2 дней)

Статус: accepted
Дата: 2026-06-14

Контекст:
Ретро после 2 дней (human — 15 лет PM). Схема рабочая, но нужна формализация: доменные основания решений терялись, когда пояснения human шли только в одном чате; вскрыт пробел — код Opus был ЛОКАЛЬНО (не пушился) → Codex ревьюил описания, не код. Консенсус opus+codex+human.

Решение:
1. **Модель — ГЕНЕРАТОР-РЕВЬЮ** (не «равные_роли» — неустойчивое равновесие, скатывается к одному краю). Checker: **Opus generator / Codex reviewer+contract-owner / human strategic owner**. DocSpectrum: **Codex generator / Opus reviewer / human owner**. Драйвер — по рабочему участку; Codex-генератор для контрактов/словарей/shared-структуры/eval/version-overlay-policy. ПРАВИЛО: в одном проекте не два независимых генератора без явного разделения зон.
2. **Код — на GitHub по ссылкам, НЕ в shared.** opus_checker → `github.com/AganinAlexandr/pp87-checker @ heading-template-matching`; пуш после КАЖДОГО заметного коммита. Shared = координатный слой (ссылка на commit + что проверено + входные данные + метрики + выводы + открытые вопросы), не монорепа кода.
3. **Канал human_context:** `discussion/human_context/HC-YYYYMMDD-NNN_<topic>.md`; формат блоков `[human]` / `[opus]` / `[codex]`; ТОЛЬКО сообщения, меняющие правило/приоритет/термин/метрику/тип замечания/граничное условие. Digest делает агент, ПОЛУЧИВШИЙ сообщение (триггер «занеси в human_context» или сам распознаёт существенность).
4. **Checkpoint = review packet:** коммит, входные данные, главные reason-коды, регрессии, что НЕ трогалось, какое доменное правило использовано/изменено. Атомарно (пуш кода + чекпойнт в одном ходе).
5. **Запись по слоям:** threads (сырое) → 06_decisions (финал) → contracts (данные/reason/метрики) → task_board (план/статус) → human_context (доменные вводные).

Последствия:
Ревью становится настоящим (по коду); доменные основания фиксируются; роли явные; Checker/DocSpectrum — зеркальный эксперимент.

Ссылки:
- `discussion/threads/T-20260613-001`, `T-20260613-002`
- `discussion/human_context/` (HC-записи)
- `registries/repositories_registry.csv`

## DEC-20260615-005: DocSpectrum namespace in shared coordination hub

Статус: accepted
Дата: 2026-06-15

Контекст:
Для `DocSpectrum` принят workflow v2: `Codex` — generator / основной исполнитель, `Opus` — reviewer, `human` — strategic owner. Рабочий репозиторий `DocSpectrum` имеет режим `owner_only`, поэтому reviewer не должен писать в него напрямую. Нужен общий пишущий слой для review packets, обсуждений и проектных решений.

Решение:
Не создавать отдельный `docspectrum-shared` репозиторий на старте. Использовать существующий `checker-shared-project` как общий coordination hub с проектными неймспейсами.

Добавить:

- `projects/docspectrum/`
- `projects/docspectrum/discussion/`
- `projects/docspectrum/decisions/`
- `projects/docspectrum/contracts/`
- `projects/docspectrum/review_packets/`
- `projects/docspectrum/human_context/`

Корневой workflow, включая `DEC-20260614-004`, остаётся единой канонической точкой. Checker-специфичные legacy-файлы не переносить, чтобы не ломать существующие ссылки.

Последствия:
`DocSpectrum` получает общий координационный слой для `Codex` и `Opus` без дублирования workflow. Изоляция проектов обеспечивается неймспейсами. Переименование репозитория в более нейтральное имя возможно позже, но не является блокером.

Ссылки:
- `projects/docspectrum/README.md`
- `registries/repositories_registry.csv`
