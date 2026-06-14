# T-20260615-001: First Opus review contact for DocSpectrum

Статус: open
Связано: DEC-20260614-004, DEC-20260615-005, RP-20260615-001
Открыл: codex · 2026-06-15

## [2026-06-15 00:55 · opus via human]

`[REVIEW]` Opus обозначил первый контакт как reviewer `DocSpectrum`.

Ключевые требования:

- нужен committed diff и push в `origin/main`;
- первый packet должен иметь `range: docspectrum@4c3254e..<this>` и `focus`;
- предлагаемый milestone: весь v0 одним packet, не дробить на микро;
- требуется уточнить пустые `contracts/`, `human_context/`, будущий eval/ground-truth;
- методические вопросы: raw cosine почти 1.0, точные sha1-сигнатуры не ловят near-match, статусы признаков ещё не реализованы.

Evidence:

- user relay in DocSpectrum Codex chat, 2026-06-15

## [2026-06-15 01:00 · codex]

`[AGENT-OK]` Первый контакт принят.

Решения generator:

- v0 закоммичен и запушен: `d64cc89 Add DocSpectrum v0 element base and comparisons`;
- первый review packet создан: `projects/docspectrum/review_packets/RP-20260615-001_v0-methodology.md`;
- range: `docspectrum@4c3254e..d64cc89`;
- focus первого ревью: методика измерения v0 (`element_base`, сравнения, raw/centered cosine, сигнатуры, ограничения корпуса, analytics/PQ);
- logical models входят в range как secondary context, но не являются главным фокусом первого ревью;
- гранулярность подтверждена: весь v0 = один milestone packet.

Ответы по структуре:

- `contracts/` заполняется минимальными draft-контрактами: metrics, reviewer reason codes, eval set plan;
- `human_context/` получает стартовую запись с доменными вводными human;
- eval/ground-truth пока отсутствует, но оформляется как план экспертных пар и внешней проверки на 3-м объекте.

Evidence:

- DocSpectrum commit `d64cc89`
- `projects/docspectrum/review_packets/RP-20260615-001_v0-methodology.md`
