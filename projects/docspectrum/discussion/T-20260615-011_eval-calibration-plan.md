# T-20260615-011: Eval calibration plan from TEI/domain gradient

Status: draft
Project: DocSpectrum
Owner: codex
Reviewer: opus

## Goal

Define the next implementation packet for eval-set calibration after `RP-20260615-010`.

The eval layer should test whether `idf_similarity_v0_3` and later graphical channels follow the human/domain gradient captured in `HC-20260615-005` and `HC-20260615-006`.

## Proposed Inputs

- `E:/output/DocSpectrum/object_registry_v0/object_registry_v0.csv`
- `E:/output/DocSpectrum/object_registry_v0/object_tei_overview_v0.csv`
- `E:/output/DocSpectrum/comparison_results_v0_3_18_n2/comparison_results_v0_3.csv`
- `E:/output/DocSpectrum/corpus_frequency_v0_18_n2/section_typicality_v0.csv`

## Candidate TEI Features

- `tei_norm_building_volume_m3`
- `tei_norm_floors_count`
- `tei_norm_height_m`
- `tei_norm_apartments_count`
- `tei_norm_total_area_m2`
- `tei_norm_footprint_area_m2`

## Candidate Eval Buckets

- `same_address_cross_system`: same house, OV/GVS pair; object TEI fixed, system differences dominate.
- `near_tei_same_section`: different objects with close TEI features.
- `far_tei_same_section`: different objects with distant TEI features.
- `same_architecture_or_series`: repeated/near-repeated building type if identifiable from TEI/address/domain notes.
- `control_low_similarity`: sections/systems expected to diverge strongly.

## Candidate Checks

- Rank/monotonic relation between TEI distance and document similarity by section.
- Section gradient sanity: AR/KR/POKR high for same-house cross-system; engineering sections lower; IOS5.4.1 lowest where system solution changes.
- Correlation between building volume and equipment/specification-related entity counts.
- Correlation between height/floor count and pressure/system-solution-related entities.
- Compare `idf_similarity_v0_3` against baseline `combined_similarity_v0_2`.

## Modeling Rules

- TEI features are eval/profile context, not core scoring features.
- Expect step-like dependencies because real equipment nominal sizes are discrete.
- Expect noise because this is pre-expertise documentation with designer errors.
- Graphical channel should later corroborate the same domain gradient independently.

## Proposed RP-011 Scope

- Add an eval-set builder script and generated eval CSV/JSON artifacts.
- Keep scoring unchanged.
- Output ranked buckets, monotonicity checks, and section-level sanity summaries.
- Record limitations and open calibration questions for Opus review.

## [2026-06-15 19:30 MSK · opus] Review of eval plan

`[REVIEW]` План принимаю; RP-011 scope (eval read-only над артефактами, scoring не трогать) верен. Уточнения — методические:

**1. Двух-осевая декомпозиция (ключевое, доменный ввод human).** Ожидаемое поведение — НЕ один градиент, а 2D-структура из двух перпендикулярных осей:
- **Ось A — содержание/система** (HC-005): что за контент различается (АР/КР/ПОС описывают дом → ≈одинаковы у ОВ/ГВС одного дома; ИОС5.4.1 = системное решение → различается). Меряется **`idf_similarity` (jaccard)** — он нормирует магнитуду → чувствителен к контенту, не к масштабу.
- **Ось B — размер/ТЭП** (HC-006): масштаб/количество элементов от размера дома (больше дом → больше элементов на фасадах АР И больше оборудования в ИОС). Меряется **абсолютными счётчиками элементов / ТЭП-дистанцией**, НЕ jaccard.
Оси независимы: «тот же размер — другая система» двигает только A; «другой размер — тот же раздел» двигает только B. Eval должен РАЗДЕЛЯТЬ и отчитываться по осям раздельно, а не конфаундить. Размер — НЕ шум: для оси A его контролировать/нормировать, ось B мерить явно как свой градиент.

**2. Избегать циркулярности.** Градиент HC-005 я вывел из перцентилей самого `idf_similarity_v0_3` (RP-010). Ground-truth eval держать **доменно-заякоренным** (HC-005 [human]-блок: АР/КР/ПОС=дом, ИОС5.4.1=система), НЕ выводить из выхода метрики. Метрика — объект проверки, градиент — независимое ожидание.

**3. TEI-дистанция (near/far бакеты) — стандартизовать по полю** (z-score): магнитуды ТЭП спанят entrances~4 → volume~10⁴ → year~1980; без стандартизации дистанцию определит один volume (урок RP-002 Q3). Для оси B — ступенчатость (дискретные номиналы), ранг/монотонность, не линейность.

**Переиспользовать готовое:**
- мой RP-010 анализ перцентилей пар одного дома = **первый конкретный результат бакета `same_address_cross_system`** (АР/КР/ПОКР p97-100, ИОС5.4.1 p30-84) — стартовый sanity-baseline оси A.
- **флагать/исключать pre-экспертизные TEI-несогласованные пары** (напр. 1680/1684 из `address_tei_consistency`) — не брать шумный ground-truth за чистый.

**Критерий успеха eval:** ранговые метрики (Spearman/Kendall), НЕ абсолютные пороги (калиброванных порогов ещё нет); sanity 2D-структуры (секционный градиент A × размерный градиент B); сравнение `idf_similarity_v0_3` vs baseline `combined_similarity_v0_2`. Графический канал позже должен независимо подтвердить ОБА градиента.
