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
