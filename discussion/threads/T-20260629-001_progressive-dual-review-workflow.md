# T-20260629-001: Progressive dual-review workflow

Статус: open / proposed
Связано: DEC-20260614-004, DEC-20260621-007, projects/docspectrum/discussion/T-20260629-023
Открыл: human; зафиксировал codex · 2026-06-29

## [2026-06-29 · codex]

`[PROPOSAL FOR DISCUSSION]` Полный предлагаемый формат двухуровневого независимого ревью.

### Основание обсуждения

За последние недели практически испытаны два разных режима.

**Режим A: генератор + основной full-context ревьюер.**

- DocSpectrum: Codex generator / Opus reviewer.
- Checker: Opus generator / Codex reviewer.
- Ревьюер читает committed diff, код, тесты, артефакты и общий контекст.
- Сильная сторона: воспроизводимость, системность, проверка причин и знаменателей.
- Слабая сторона: один набор слепых зон и последовательная стоимость.

**Режим B: генератор + несколько blind focused-ревьюеров.**

- Испытан human в новом проекте по логистике: обучение домену распределительного центра,
  объединённого с фулфилментом, перед хакатоном.
- Generator: Opus (роль не привязана к модели; generator также может быть Codex).
- Для ревью заранее готовились ограниченный пакет документации и перечень вопросов.
- Два качественных ревьюера работали независимо и не видели отзывы друг друга.
- На старте был третий ревьюер, исключённый из-за недостаточного качества ответов.
- Два оставшихся ревьюера во многом совпали, но дали и взаимодополняющие замечания.
- Сильная сторона: независимость, параллельность, разнообразие слепых зон, внимание к деталям.
- Главный пробел: никто отдельно не проверял, достаточно ли generator выбрал материалов и
  вопросов. Одинаковый focused-пакет создаёт общий риск пропуска отсутствующего контекста.

Дополнительный прецедент DocSpectrum: Codex начал feature-level augmentation как вариант
controlled injection из общего backlog. Human выявил циркулярность до reviewer design-review.
Код не был закоммичен, результат отклонён. Это подтверждает необходимость проверки не только
готовой реализации, но и состава контекста и методического дизайна до неё.

### Базовые принципы

1. Роли model-agnostic: generator/reviewer определяются проектом, а не названием модели.
2. Независимый отзыв фиксируется до доступа к другим отзывам.
3. Focused-review является выводом только в пределах focused-пакета.
4. Полный `[AGENT-OK]` требует full-context проверки и воспроизведения.
5. Согласие моделей не заменяет ground truth и не является голосованием.
6. Уникальное замечание принимается по evidence, а не по числу поддержавших моделей.
7. Human остаётся источником доменной истины и стратегическим owner, а не первым техническим
   фильтром циркулярных метрик или неполного контекста.

## Предлагаемый progressive-disclosure процесс

### Phase 0. Design gate для методических изменений

Обязателен до реализации новых:

- метрик и агрегатов;
- benchmark и ground-truth;
- threshold/decision rules;
- способов инъекции или аугментации;
- продуктовых verdict-правил;
- существенных изменений состава данных.

Design packet фиксирует:

- цель и связь с задачей продукта/ТЗ;
- тип работы: validation / data research / product assembly;
- проверяемую гипотезу;
- входы, expected outputs и ground truth;
- anti-circularity;
- acceptance и stop criteria;
- ограничения и `not_touched`.

До `[DESIGN-OK]` generator не реализует метод. Простые рефакторы и реализация уже принятого
контракта отдельного design-review не требуют.

### Phase 1. Generator готовит два пакета одного snapshot

**Full-context package:**

- полный committed range/snapshot;
- исходные документы, код, тесты и артефакты;
- карта домена, источники и допущения;
- перечень подсистем и связей;
- полный context manifest с хэшами/путями;
- известные ограничения и отрицательное пространство задачи.

**Focused package:**

- цель и проверяемые утверждения;
- идентификатор того же frozen snapshot;
- отобранные документы/evidence;
- список вопросов для улучшения внимания;
- acceptance criteria;
- ограничения пакета и `not_touched`;
- manifest только доступных focused-материалов;
- указание, что полный контекст существует и будет раскрыт позднее.

В focused-пакет не входят предлагаемые ответы на вопросы и результаты других ревьюеров.

### Phase 2. Два независимых blind focused-review

**Reviewer A (основной full-context reviewer, первая стадия):**

- получает физически только focused-пакет;
- не имеет рабочих ссылок/доступа к full-context на этой стадии;
- сначала делает свободный обзор, затем отвечает на вопросы generator;
- фиксирует `FOCUSED-REVIEW-A` до раскрытия полного контекста.

**Reviewer B (дополнительный focused reviewer):**

- получает тот же frozen focused-пакет;
- не видит `FOCUSED-REVIEW-A` и последующий context audit;
- сначала делает свободный обзор, затем отвечает на вопросы;
- фиксирует `FOCUSED-REVIEW-B`.

Формат focused-результата:

```text
findings
confidence
context_gaps
requested_materials
verdict_within_packet
```

Если данных недостаточно, корректный результат — `CONTEXT_INSUFFICIENT`, а не догадка.

### Phase 3. Progressive full-context audit основным ревьюером

Только после фиксации `FOCUSED-REVIEW-A` Reviewer A получает full-context package и проверяет:

- достаточность focused-пакета;
- корректность отбора материалов и вопросов;
- отсутствующие подсистемы/источники;
- framing и selection bias;
- воспроизводимость кода, тестов и артефактов;
- изменились ли первоначальные выводы после раскрытия контекста.

Формат `CONTEXT-AUDIT`:

```text
confirmed_findings
withdrawn_findings
new_findings
focused_packet_omissions
question_framing_effects
context_requests_resolved
```

Разница `FOCUSED-REVIEW-A -> CONTEXT-AUDIT` является измерением качества focused-пакета.

### Phase 4. Reconciliation

После завершения обоих слепых отзывов Reviewer A:

- сопоставляет общие и уникальные findings A/B;
- проверяет уникальные findings Reviewer B против полного контекста;
- не отвергает замечание только потому, что второй reviewer его не нашёл;
- разрешает конфликт по evidence и воспроизведению;
- формирует единую reconciliation table.

Рекомендуемые поля:

```text
finding_id
reviewer_source
severity
shared_or_unique
evidence
full_context_status
generator_response
resolution
verification_status
```

### Phase 5. Generator response и второй круг

Generator исправляет подтверждённые findings или аргументированно отклоняет их. Второй круг
обязателен только для:

- blocker/high findings;
- расхождений ревьюеров;
- изменившегося методического контракта;
- признанного неполным focused-пакета;
- изменений, способных затронуть ранее доказанные golden anchors.

### Phase 6. Финальный вердикт

Основной reviewer выпускает итоговый verdict после full-context и reconciliation:

- `[AGENT-OK]`;
- `[NEEDS-HUMAN]` для доменной/стратегической развилки;
- `[BLOCKED-BY-CONTEXT]`;
- `[CHANGES-REQUIRED]`.

Вердикт Reviewer B остаётся focused-вердиктом и сам по себе не означает full milestone approval.

## Когда нужен третий reviewer

Не по умолчанию. Подключать при:

- high-stakes результате;
- неразрешимом конфликте A/B;
- подозрении на общий framing bias;
- необходимости иной специализации;
- доказанной добавочной ценности модели на calibration cases.

Количество моделей не является целью. Reviewer исключается, если его уникальные ответы дают больше
непроверяемого шума, чем подтверждённых findings.

## Risk tiers

- `R0`: простой refactor/implementation accepted contract — generator + основной reviewer.
- `R1`: новая метрика/метод/существенный data rule — design-review + progressive Reviewer A.
- `R2`: benchmark, ground truth, thresholds, product verdict — design-review + два blind focused-review
  + full-context audit + reconciliation.
- `R3`: high-stakes или конфликт — R2 + третий специализированный reviewer/human escalation.

## Как оценивать сам механизм ревью

- overlap findings A/B;
- уникальные подтверждённые findings каждого;
- false-positive/withdrawn findings;
- findings, добавленные только после full-context;
- пропуски focused-пакета;
- влияние question framing;
- стоимость по времени/токенам;
- доля blocker/high findings, пойманных до реализации;
- устойчивость на исторических реальных дефектах проектов.

## Открытые вопросы для участников

1. Должен ли Reviewer A всегда физически не иметь full-context на focused-стадии, или достаточно
   процедурного запрета?
2. Одинаков ли список вопросов A/B, либо одному reviewer нужен полностью unguided пакет?
3. Кто кроме generator может дополнять focused-вопросы до freeze: human, основной reviewer или никто?
4. Должен ли full-context audit начинаться только после фиксации обоих focused-review, либо достаточно
   зафиксировать только `FOCUSED-REVIEW-A`?
5. Кто выполняет финальный synthesis при генераторе Opus/Codex: всегда основной reviewer или отдельная роль?
6. Какие risk tiers обязательны для Checker, DocSpectrum и новых проектов?
7. Нужна ли отдельная reviewer-calibration suite из исторических дефектов?
8. Какие части предложения избыточны при трёхдневном хакатонном цикле?

## Предлагаемый статус

Это discussion proposal, не DEC. До ответов участников действуют DEC-20260614-004 и
DEC-20260621-007. После обсуждения принятые части переносятся отдельной DEC-записью; отклонённые
части сохраняются в этой нити как evidence эволюции процесса.

Evidence:
- shared `docs/06_decisions.md`: DEC-20260614-004, DEC-20260621-007;
- DocSpectrum `e6bda4a`: golden regression harness;
- shared `e7485ba`: T-20260629-023, pre-implementation design-review request;
- human report, 2026-06-29: blind multi-review experiment in the logistics project;
- DocSpectrum session, 2026-06-29: feature-level augmentation rejected before commit.
