# DocSpectrum Coordination

Координационный слой проекта `DocSpectrum` внутри общего `checker-shared-project`.

## Роли

- `Codex` - generator / основной исполнитель.
- `Opus` - reviewer.
- `human` - strategic owner.

Основание:

- `DEC-20260614-004 Collaboration workflow v2`
- `DEC-20260615-005 DocSpectrum namespace in shared coordination hub`

## Рабочий репозиторий

- `E:\repos\DocSpectrum`
- `github.com/AganinAlexandr/DocSpectrum`

Код и проектные артефакты живут в рабочем репозитории `DocSpectrum`.

Этот shared-неймспейс хранит только координацию:

- review packets;
- обсуждения reviewer/generator;
- доменные вводные human;
- проектные decisions;
- контракты, если они нужны обеим сторонам.

## Папки

- `discussion/` - обсуждения и вопросы по review/generator loop.
- `decisions/` - принятые проектные решения `DocSpectrum`, не являющиеся общим workflow.
- `contracts/` - проектные контракты данных, метрик и review packets.
- `review_packets/` - чекпойнты для Opus-review.
- `human_context/` - доменные вводные human по `DocSpectrum`.

## Правило изоляции

Материалы `Checker` сюда не переносятся.

Материалы `DocSpectrum` не пишутся в Checker-legacy области, кроме ссылок в общих registry/decision, когда это нужно для координации.
