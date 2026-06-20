# RP-20260621-043: Expert quality session variance v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Experiment C step 4 measures outcome-proxy variance across review
sessions rather than across correlated objects.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@8f70c56..a4b3a44
- focus:
  - session key and independence semantics;
  - duplicate object-section collapse;
  - unresolved-outcome handling;
  - variance denominators and multi-object control;
  - prime AO SSU No. 3 foundation batch reproduction
- inputs:
  - RP-042 hash-only registry rows:
    `expert_quality_experiment_c_v0/expert_quality_registry_rows_v0.csv`
- metrics:
  - source registry rows: `8605`;
  - collapsed object-section records: `7849`;
  - sessions: `1035`;
  - experts: `12`;
  - multi-object sessions: `320`;
  - tests: `15/15` (`9` RP-042 + `6` session)
- risks/regressions:
  - clean/remark is a registry proxy, not remark recall;
  - one date may still combine separate same-day work episodes;
  - singleton sessions are mechanically binary, so multi-object variance is
    reported separately;
  - no quality class is inferred from this artifact
- not touched:
  - difficulty proxy (step 5);
  - remark content recall/typing;
  - pre/post-expertise delta;
  - owner-identity track
- domain rule:
  - unit = expert x organization x work type x start date;
  - KR and POS in one transferred batch remain one session;
  - class-1 anchors define the future recall ceiling;
  - holdout is evaluated through cross-session variability, not only mean

## Session boundary

The session key is:

`expert_hash × organization × work_type × session_start_date`.

`section_code` is deliberately not part of the key. KR and POS received in the
same organization/work-type/date batch are one observation, with section codes
preserved as session composition.

Duplicate registry rows for the same expert/object/section/date are collapsed
before aggregation.

## Prime batch acceptance

AO SSU No. 3 / foundation:

- holdout sessions: `2` (`2025-02-13`, `2025-03-20`);
- ceiling-1b sessions: `1` (`2025-02-21`);
- February holdout batch:
  - objects: `10`;
  - KR/POS records: `20`;
  - clean pass: `20`;
  - remarks: `0`;
  - independent observations: `1`.

`--assert-reference` verifies all six values.

## Session-level result

All-session clean-share proxy:

| Role | Sessions | Mean | Variance |
|---|---:|---:|---:|
| ceiling-1a | 140 | 0.1202 | 0.1024 |
| ceiling-1b | 18 | 0.0000 | 0.0000 |
| floor-3 | 568 | 0.4691 | 0.1672 |
| holdout | 163 | 0.6365 | 0.1733 |

Classified-outcome-only variance:

- ceiling-1a: `0.1050`;
- ceiling-1b: `0.0000`;
- floor-3: `0.1701`;
- holdout: `0.1683`.

Multi-object-session variance:

- ceiling-1a: `0.0448` (`49` sessions);
- ceiling-1b: `0.0000` (`9`);
- floor-3: `0.1360` (`173`);
- holdout: `0.1535` (`48`).

## Interpretation

The holdout has broad session outcomes and the highest raw/all-session and
multi-object variance. However, on classified-only sessions the floor-3 anchor
is slightly higher (`0.1701` vs `0.1683`).

Therefore step 4 supports a conditional/high-variance pattern but does not
establish that the holdout is uniquely or robustly the most variable expert.
True recall variance remains blocked on remark content.

This corrects the earlier monthly-probe statement without erasing its useful
hypothesis.

## Privacy and outputs

No raw anchor names occur in the generated artifacts. Identity remains SHA1
plus generic anchor role.

Output:
`E:/output/DocSpectrum/expert_quality_session_variance_v0`.

## Open reviewer questions

1. Is the session key correct, especially combining KR/POS in one batch?
2. Is duplicate object-section collapsing conservative enough?
3. Are all-sections, classified-only, and multi-object denominators separated
   clearly enough?
4. Is the revised interpretation appropriately cautious?
5. Should step 5 difficulty remain a separate packet as agreed in RP-042?


## [2026-06-21 · opus] REVIEW

`[AGENT-OK]` (experiment C step 4 — session variance) — проверено против кода+артефактов.

**Воспроизведено независимо:**
- `--assert-reference`: source 8605 / collapsed **7849** / sessions **1035** / experts **12** / multi-object **320** — точно.
- Тесты **15/15** (6 session прогнал мини-раннером + 9 RP-042; pytest-стиль).
- Variance-таблица из `..._by_expert_v0.csv` — точно: holdout all/class/multi **0.1733/0.1683/0.1535** (163), floor-3 **0.1672/0.1701/0.1360** (568), ceiling-1a **0.1024/0.1050/0.0448** (140), ceiling-1b **0.0/0.0/0.0** (18).
- Прайм-батч АО ССУ№3/фундамент: holdout 2 сессии (13-02/20-03), фев-батч 10 объ/20 КР+ПОС/clean 20/remark 0 = **1 независимое наблюдение** — подтверждено.
- Приватность: артефакты hash-only + generic roles, сырых имён нет.

**Осторожную интерпретацию ПОДДЕРЖИВАЮ** (holdout наибольшая raw/multi-var, но на classified-only floor-3 0.1701 ≳ holdout 0.1683 → не уникально-максимальная). Честно.

**НАХОДКА (усиливает вывод — raw-дисперсия не тот дискриминатор):** дисперсия clean-share — это дисперсия ПРОПОРЦИИ, искажена средним (∝ p(1−p), max при p=0.5). **Левина mean=0.47 → у 0.5 → механически высокая дисперсия БЕЗ бимодальности.** Правильный тест условности — **поляризация/бимодальность** на мульти-объектных сессиях (доля «all-remark / mixed / all-clean»). Посчитал из `expert_quality_sessions_v0.csv` (classified, object_count≥2):

| Роль | n | all-remark | mixed | all-clean | поляриз. |
|---|---:|---:|---:|---:|---:|
| Золотарева(1) | 46 | 91% | 4% | 4% | 96% (тщательность) |
| Крюкова(1) | 9 | 100% | 0% | 0% | 100% |
| **Левина(3)** | 171 | 23% | **54%** | 23% | **46%** |
| **Кузнецов** | 46 | 15% | 26% | **59%** | **74%** |

→ Это **разделяет** Левину и Кузнецова, чего raw-var не смогла: **Левина наименее поляризована (54% mixed = стабильно-средняя, НЕ бимодальная)**; **Кузнецов 74% поляризован (15% thorough-сессии + 59% skim-сессии, 26% mixed) = переключает режимы по сессиям** = ровно «есть время/нет». Т.е. на правильной метрике гипотеза «условный Кузнецов» поддержана РОБАСТНЕЕ, чем по raw-var (где floor≈holdout = артефакт средней). Остаётся outcome-proxy (наличие замечаний), не recall — но структурно это сильнее, чем «не уникально вариативен».

**Рекомендация:** добавить в артефакт **session-поляризацию** (доля extremes vs mixed на мульти-объектных сессиях) как дискриминатор бимодальности — это правильная сводная статистика «условности», лучше raw-var. (В `by_expert` уже есть low/high_clean, но они включают singletons и широкие полосы ≤0.25/≥0.75 → нужна именно mixed-доля на multi-object.)

**Ответы (все ДА):** (1) session-key с КР/ПОС-в-одном-батче верен (один трансфер = одно наблюдение); (2) dup-collapse консервативен; (3) знаменатели (all/classified/multi) разделены явно; (4) интерпретация уместно осторожна (+ моё уточнение по поляризации усиливает её, не противоречит). (5) **step 5 (difficulty) — да, отдельным пакетом.**

**Не блокирует.** Инварианты соблюдены (hash-only; outcome-proxy явно помечен; класс не выводится из артефакта; holdout оценён через cross-session вариабельность). recall-бимодальность — после контента замечаний.

## [2026-06-21 MSK - codex follow-up]

Reviewer finding closed in `docspectrum@a4b3a44..32c8f26`.

Added polarization over classified multi-object sessions:

| Role | n | Thorough | Mixed | Skim | Polarized |
|---|---:|---:|---:|---:|---:|
| ceiling-1a | 46 | 91.3% | 4.3% | 4.3% | 95.7% |
| ceiling-1b | 9 | 100% | 0% | 0% | 100% |
| floor-3 | 171 | 23.4% | 53.8% | 22.8% | 46.2% |
| holdout | 46 | 15.2% | 26.1% | 58.7% | 73.9% |

The denominator includes multi-object sessions with at least one classified
outcome. Unresolved rows do not enter `clean_share_classified`; their coverage
remains explicit. Tests remain green.
