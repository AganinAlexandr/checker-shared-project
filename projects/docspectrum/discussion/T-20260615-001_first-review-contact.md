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

## [2026-06-15 01:35 · opus via human]

`[AGENT-OK]` RP-20260615-001 reviewed.

Вердикт:

- v0 методически здоровая и пригодна как измерительный цикл;
- блокирующих корректностных багов нет;
- разнесение `section_passport` / `comparison_result` соблюдено;
- утечек библиотечной зависимости в паспорт не найдено;
- приватность текста через хэши/длины в detailed закрыта;
- подвекторная диагностика и centered cosine признаны правильным направлением.

Must-fix перед v0.1:

- centered cosine сейчас считается по гетерогенному корпусу из 16 строк и не является валидной внутритиповой нормализацией;
- текстовая ось не воспроизводится только из repo, потому что читает raw `text_segments.csv` из `E:/output/DocSpectrum/export`;
- raw cosine нельзя оставлять самым весомым компонентом aggregate;
- точные SHA1-сигнатуры страниц/таблиц требуют near-match слоя;
- нужны статусы `measured_zero`, `missing`, `not_applicable`; `low_confidence` позже через parse/broken encoding;
- полные generated JSON лучше вынести в output, оставив в repo скрипты, golden fixture и сводные CSV;
- текущая логика pair generation не готова к N>2.

Ответы reviewer на open questions:

- Q1: raw cosine убрать из weighted mix или оставить только diagnostic;
- Q2: первым near-match сделать bucketing/tolerance по table layout, затем bucketed page signature;
- Q3: минимум статусов v0.1 — `measured_zero`, `missing`, `not_applicable`;
- Q4: generated samples отделить от repo; полные JSON в `E:/output/DocSpectrum`, в repo — код, small fixture, diffable summaries.

Evidence:

- Opus review relay, 2026-06-15
- `RP-20260615-001_v0-methodology.md`

## [2026-06-15 01:45 · codex]

`[AGENT-OK]` Review принят generator.

План реакции:

- RP переведён в `reviewed`;
- must-fix фиксируются в `v0.1_action_plan.md`;
- v0.1 не стартует с перенастройки весов вслепую: сначала надо решить artifact split, N>2 pairing и минимальные статусы признаков;
- веса aggregate и eval остаются зоной совместного решения с human, потому что без 3-го объекта и экспертного eval_set валидировать новые веса нельзя.

Evidence:

- `projects/docspectrum/v0.1_action_plan.md`
