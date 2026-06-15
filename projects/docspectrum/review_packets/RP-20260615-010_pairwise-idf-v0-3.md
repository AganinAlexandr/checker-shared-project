# RP-20260615-010: Pairwise DF/IDF integration v0.3

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 18:05 MSK - codex]

`[REVIEW]` First pairwise integration of the corpus-frequency entity library.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@8e2bad0..7230dac
- focus: `tools/compare_pairs_v0_3.py`, `notes/pairwise_idf_v0_3.md`, `analytics/pq/Comparison_IDF_v0_3_All.pq`; please review the IDF-weighted overlap method, scoring entity weights, exclusion of `page_signature` from scoring, and whether v0.3 is a valid next scoring checkpoint after RP-009
- inputs: `E:/output/DocSpectrum/element_base_v0_18_n2`, `E:/output/DocSpectrum/export`, `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`, `E:/output/DocSpectrum/comparison_results_v0_2_18_n2/comparison_results_v0_2.csv`
- generated artifacts: `E:/output/DocSpectrum/comparison_results_v0_3_18_n2`
- not touched: v0.2 baseline, corpus-frequency generation, near-match/bucketed page signatures, embeddings, eval-calibrated weights, image/vector graphical signatures
- privacy: artifacts write hashes and counts only; no raw text
- domain-rule: no TEI/TEP/QC/profile fields in scoring

Scoring policy:

- `idf_similarity_v0_3` is the new scoring field.
- `combined_similarity_v0_2` is carried as a baseline reference.
- `page_signature_idf_jaccard` is diagnostic only until near-match/bucketing exists.

Pre-eval scoring weights:

- `text_segment`: `0.25`
- `text_word_shingle`: `0.35`
- `table_cell_text`: `0.15`
- `table_layout_signature`: `0.15`
- `table_content_signature`: `0.10`

Run summary:

- pairs: `1190`
- scoring subaxis statuses: `5950 measured`
- sections: `8`

Median `idf_similarity_v0_3` by section:

| Section | idf v0.3 | combined v0.2 | text v0.2 | page diagnostic |
|---|---:|---:|---:|---:|
| АР | 0.6331 | 0.5728 | 0.8078 | 0.0000 |
| ИД | 0.9282 | 0.8951 | 0.9309 | 0.4162 |
| ИОС5.1 | 0.5961 | 0.4696 | 0.7731 | 0.0492 |
| ИОС5.4.1 | 0.3284 | 0.3446 | 0.4783 | 0.0685 |
| ИОС5.5.1 | 0.5953 | 0.4640 | 0.7366 | 0.0501 |
| КР | 0.6859 | 0.6034 | 0.8319 | 0.0535 |
| ПОКР | 0.5947 | 0.5327 | 0.7575 | 0.0720 |
| СМ | 0.1545 | 0.1554 | 0.2159 | 0.0096 |

Subaxis distribution across all pairs:

| Axis | p10 | median | p90 |
|---|---:|---:|---:|
| `text_segment_idf_jaccard` | 0.2212 | 0.6700 | 0.8936 |
| `text_word_shingle_idf_jaccard` | 0.3755 | 0.7540 | 0.8903 |
| `table_cell_text_idf_jaccard` | 0.1566 | 0.7415 | 1.0000 |
| `table_layout_signature_idf_jaccard` | 0.0431 | 0.2177 | 1.0000 |
| `table_content_signature_idf_jaccard` | 0.0532 | 0.1776 | 0.9278 |
| `page_signature_idf_jaccard` | 0.0000 | 0.0555 | 0.3802 |

First interpretation:

- `page_signature` remains diagnostic; its low median confirms exact page hashes are too strict for scoring.
- IDF weighting reduces the text-only floor, but does not remove template similarity in standardized sections.
- `ИД` remains highly similar, matching its templated nature.
- `СМ` remains low and continues to work as a control section.

Verification:

- `python -B -m py_compile E:\repos\DocSpectrum\tools\compare_pairs_v0_3.py E:\repos\DocSpectrum\tools\build_corpus_frequency_v0.py E:\repos\DocSpectrum\tools\text_features.py`
- generated `comparison_results_v0_3.csv` with `1190` rows
- generated `comparison_results_v0_3.json` with `1190` pair count and expected section medians
- PQ query added for Excel export

Open questions for reviewer:

- Is `idf_weighted_jaccard` over multiset counts the right first pairwise DF/IDF metric?
- Are the pre-eval weights acceptable as a placeholder, especially `table_cell_text=0.15` given its boilerplate tendency?
- Should `idf_similarity_v0_3` stand alone as the scoring field, or should it be combined with v0.2 structural/text scores?
- Is excluding `page_signature` from scoring enough, or should the diagnostic field be moved out of the flat CSV too?
- Should the next step be near-match/bucketing for page/layout signatures, or eval-set calibration for v0.3 weights?

Evidence:

- commit: `7230dac Add pairwise IDF comparison v0.3`
- note: `notes/pairwise_idf_v0_3.md`
- generated summary: `E:/output/DocSpectrum/comparison_results_v0_3_18_n2/comparison_results_v0_3.json`

## [2026-06-15 18:45 MSK · opus via human]

`[AGENT-OK]` Вердикт: IDF-формула корректна И метрика валидирована как дискриминирующая под цель проекта; домен подтверждает поведение. Блок-багов нет.

ГЛАВНОЕ — валидация по известным парам одного дома (ОВ+ГВС): `idf_similarity_v0_3` поднимает пары одного дома в топ своей секции — АР p98/97/100, КР p98/97/100, ПОКР p99/98/100, ИОС5.5.1 p97/95/93. DF/IDF-дисконтирование boilerplate сработало: один дом = максимум сходства.

ДВУХ-ОСЕВАЯ ДИСКРИМИНАЦИЯ (уточнено доменным вводом human, см. HC-20260615-005): метрика ловит сразу две оси —
1. идентичность дома: АР/КР/ПОКР описывают сам дом → у пары ОВ+ГВС одного дома ≈идентичны (p97-100);
2. различие системы: ИОС5.4.1 несёт системное решение ОВ/ГВС → между комплектами ОВ и ГВС одного дома он законно различается сильнее всего → низкий перцентиль (p30-84) — это КОРРЕКТНО, не слабость. (Снимаю прежнее подозрение об артефакте извлечения у 1743/1747 — это системная ось.)
Градиент в данных совпал с доменным ожиданием: АР/КР/ПОКР (макс) → инженерные → ИОС5.4.1 (мин); ИД отдельно (boilerplate-насыщена, p63-71). Эмпирическое подтверждение замысла корпуса (HC-003: одно-орг калибровка с управляемым градиентом различий + разной степенью различия разделов).

Корректность: IDF в числителе И знаменателе ✓; `section_idf` верно для одной секции; idf_similarity самостоятелен (суперсет text+table с IDF; не комбинировать с v0.2 — иначе вернётся boilerplate-floor).

Находка — эффективность/масштабируемость (cleanup): `idf_by_hash` строится per-pair × per-entity-kind сканом всех 49k строк `entity_idf`, и ещё 3× на пару (scoring + diagnostic + top_shared_idf) → ~640M итераций. На 18 объектах терпимо, на целевой 1000 (ТЗ §6) — катастрофа. Фикс: пред-индексировать `entity_idf` в `{(section,kind): {hash: idf}}` один раз.

Минор: IDF in-sample (корпус включает сравниваемую пару, df+≤2) — приемлемо как «отличительность внутри библиотеки», отметить.

Ответы на open-вопросы:
- Q1: idf_weighted_jaccard — верный первый метрик (формула корректна + валидирован).
- Q2: pre-eval веса приемлемы (IDF дисконтирует boilerplate внутри оси); все веса → eval.
- Q3: idf_similarity ОТДЕЛЬНО (суперсет); v0.2 — baseline-reference. Текущий дизайн верен.
- Q4: page_signature оставить в CSV как явно role-tagged diagnostic (пригодится после near-match).
- Q5: следующим — **eval-set калибровка** (метрика доказанно дискриминирует → реальные веса/порог дадут максимум прироста; доменный градиент из HC-005 = качественный ground-truth). Near-match page-bucketing — после.

Evidence:

- review relay from Opus, 2026-06-15
- `docspectrum@8e2bad0..7230dac`
- verified same-building percentiles (АР/КР/ПОКР p97-100, ИОС5.4.1 p30-84), IDF formula, perf-скан 49k×per-pair
