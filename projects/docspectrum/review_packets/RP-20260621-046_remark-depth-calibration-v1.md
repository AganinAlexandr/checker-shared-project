# RP-20260621-046: Remark depth calibration v1

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Corrected the RP-045 depth heuristic and validated it against 58
human labels. This is calibration/correction, not ML training.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@d3e6a4a..e00f34b
- focus:
  - complete removal of normative citation from depth decisions;
  - hash-only human-label ingestion;
  - agreement/confusion and role-level human means;
  - decision whether automatic depth belongs in final output
- inputs:
  - `depth_calibration_labeled_v1.xlsx`, 58 labels;
  - RP-045 `remark_features_v0.csv`
- metrics:
  - labels: `58`;
  - unique remark hashes: `48`;
  - matched current feature rows: `55/58`;
  - exact agreement by observations: `33/58 = 56.9%`;
  - exact agreement by unique unanimous hashes: `52.1%`;
  - level-2 precision: `87.5%`;
  - level-2 recall: `22.6%`;
  - experiment-C tests: `37/37`
- risks/regressions:
  - human sample is small and purpose-selected;
  - three labels are not present in the current registry-gated feature output;
  - heuristic is intentionally conservative and misses most level-2 remarks;
  - no user-facing automatic quality class should be inferred
- not touched:
  - count-recall;
  - human labels themselves;
  - semantic remark clustering;
  - owner-identity track
- domain rule:
  - human complexity is ground truth on the labeled subset;
  - significance is a separate, currently flat axis;
  - automatic depth is informing/triage-only

## Corrected depth rule

Normative citation remains a content category but is excluded from every depth
decision, including the multi-category path.

The v1 heuristic predicts level 2 only when:

- an engineering category remains after removing `regulatory`; and
- the remark is long (`>=25` words) or spans at least two non-regulatory
  categories.

All other candidates map to level 1 for agreement measurement.

No level 3 is predicted or inferred.

## Human ground truth reproduced

| Role | Labels | Human mean | L1 | L2 | L3 |
|---|---:|---:|---:|---:|---:|
| class-1 | 28 | 1.7500 | 7 | 21 | 0 |
| floor-3 | 22 | 1.4545 | 12 | 10 | 0 |
| holdout | 8 | 1.0000 | 8 | 0 | 0 |

This reproduces the required trend:

`class-1 > floor-3 > holdout`.

Norm citation is empirically flat/reversed:

- with norm citation: `1.4828`;
- without norm citation: `1.5862`.

Human significance mean is `1.9655`; it is not the primary discriminator.

## Agreement

Confusion matrix:

| Human | Predicted 1 | Predicted 2 |
|---|---:|---:|
| 1 | 26 | 1 |
| 2 | 24 | 7 |

The corrected heuristic is conservative:

- precision for level 2: `87.5%`;
- recall for level 2: only `22.6%`.

It rarely fabricates complexity, but misses most truly complex remarks.

## Generator recommendation

Automatic depth is **not sufficient for the final expert-quality conclusion**.

For the current thin corpus:

- use human complexity labels as the depth truth where available;
- use count-recall as the broad quantitative signal;
- retain automatic depth only to prioritize possible level-2 remarks for
  human review;
- do not invest in further model fitting now, per owner direction.

This is stronger and more honest than tuning the heuristic to 58 labels.

## Outputs

`E:/output/DocSpectrum/remark_depth_calibration_v1`

- `remark_depth_labels_hash_only_v1.csv`;
- `remark_depth_calibration_by_role_v1.csv`;
- `remark_depth_confusion_v1.csv`;
- `remark_depth_calibration_v1.json`.

No raw expert names or remark text are persisted.

## Open reviewer questions

1. Is norm citation fully removed from direct and indirect depth paths?
2. Is the binary current scale (1/2, no invented 3) faithful to ground truth?
3. Are agreement and unique-hash denominators reported correctly?
4. Do the results justify removing automatic depth from final conclusions?
5. Is human depth + count-recall the correct stopping point for this dataset?


## [2026-06-21 · opus] REVIEW

`[AGENT-OK]` (depth calibration v1) — проверено против кода+артефактов.

**Воспроизведено независимо:**
- role-means (human): class-1 **1.75** (7/21/0), floor-3 **1.4545** (12/10/0), holdout **1.0000** (8/0/0) — тренд class-1 > floor > holdout точно.
- norm-citation убран: с нормой **1.4828** vs без **1.5862** (flat/обратный) — подтверждено.
- confusion 26/1/24/7 → level-2 precision **87.5%** (7/8), recall **22.6%** (7/31). Значимость mean 1.9655 (плоская).
- Тесты **37/37** (прогнал мини-раннером: depth-calib 5 + extract 5 + difficulty 5 + arena 9 + recall 7 + session 6). Приватность: labels CSV = object/role/remark_hash/levels, **сырых имён и текста нет** (leak-скан 0).

**Ответы на reviewer-questions (все ДА):**
1. norm-citation удалён из прямого И многокатегорийного пути depth — да (verified; эмпирика 1.48/1.59).
2. Бинарная шкала (1/2, без выдуманного 3) верна ground-truth (у human уровня 3 нет) — да.
3. Agreement/знаменатели (55/58 matched, 33/58=56.9%, confusion=58) корректны — да.
4/5. **Убрать авто-depth из финальных выводов — ДА, обоснованно.**

**УСИЛЕНИЕ вывода (моя проверка):** predicted-mean по ролям = **class_1 1.14 / floor 1.14 / holdout 1.13 — ПЛОСКО**. Т.е. эвристика **не ранжирует роли вообще** (recall-2 22.6% → пропускает 77% сложных). Ранжирование class-1>Левина>Кузнецов держится ИСКЛЮЧИТЕЛЬНО на **human depth + count-recall**. Это сильнее, чем «misses most level-2»: авто-depth бесполезен для вывода о качестве → triage-only — правильное и честное решение (подгонка к 58 = оверфит, верно отвергнута).

**Итог эксперимента C (на доступных данных):** ранг качества **class-1 ≫ Левина ≈/> Кузнецов** опирается на (1) count-recall (Кузнецов ~0-0.10 vs потолок 5-11) и (2) human depth (1.75/1.45/1.00). Кузнецов на дне по выходу несмотря на латентную квалификацию — метод честно фиксирует поведение. Авто-depth = только triage. Доводка не требуется (owner: данных мало). **Стоп-точка достигнута.**

**Не блокирует.** Инварианты: hash-only; human=ground-truth на размеченном подмножестве; significance отдельная плоская ось; auto-depth informing/triage-only; не выдумывает уровень 3.
