# RP-20260615-006: Corpus frequency typicality v0

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 14:05 MSK - codex]

`[REVIEW]` First corpus-frequency checkpoint for typicality/originality.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@d477338..80a0d35
- focus: `tools/build_corpus_frequency_v0.py`, `notes/corpus_frequency_v0.md`, `analytics/pq/CorpusFrequency_*.pq`; please review the DF/IDF methodology, bucket taxonomy, hash-only boundary, and whether this is the right next layer after RP-005 boilerplate-floor
- inputs: `E:/output/DocSpectrum/element_base_v0_18_n2`, `E:/output/DocSpectrum/export`
- generated artifacts: `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`
- section library artifact: `section_library_v0.csv`
- extracted entity library artifact: `entity_frequency_v0.csv`
- per-section summary artifact: `section_typicality_v0.csv`
- entity kinds: `text_segment`, `text_word_shingle`
- frequency model: same-section document frequency; IDF is computed inside each `section_code` population
- buckets: `typical`, `shared_rare`, `original`, `low_population`
- threshold: `typical_ratio=0.50` as an explicit experiment placeholder, not final calibration
- privacy: hash-only entity library; no raw text is written to generated artifacts
- domain-rule: no TEI/TEP/QC/profile fields in core corpus frequency; this remains section/entity evidence, not object-domain scoring
- not touched: embeddings, LLM semantic judgment, structural signature entities, table-cell entities, source ranking, eval-calibrated thresholds, pairwise scoring integration
- risks/regressions: corpus is one-organization calibration data, so organization-wide boilerplate can become `typical`; `50%` threshold is not validated; text extraction still depends on explorer export; helper functions are not yet refactored into a shared module

Run summary:

- documents: `143`
- sections: `9`
- entity rows: `40171`
- `text_segment`: `30240`
- `text_word_shingle`: `9931`
- `typical`: `10997`
- `shared_rare`: `10709`
- `original`: `16924`
- `low_population`: `1541`

Median shares by section for `text_word_shingle`:

| Section | typical | original |
|---|---:|---:|
| АР | 0.9446 | 0.0278 |
| КР | 0.9627 | 0.0164 |
| ИД | 0.9664 | 0.0057 |
| ИОС5.1 | 0.9683 | 0.0050 |
| ИОС5.4.1 | 0.8674 | 0.0072 |
| ИОС5.5.1 | 0.9591 | 0.0034 |
| ПОКР | 0.9643 | 0.0028 |
| СМ | 0.6860 | 0.0190 |

First interpretation:

- `ИД` is almost entirely typical, matching expectation for a templated section.
- `СМ` has lower typicality and more rare/original material than most sections.
- `ИОС5.4.1` shows the most visible originality spikes among engineering sections in this run.
- `shared_rare` rows include `section_documents` and `section_objects`, which makes them an initial bridge toward future fragment/source matching.

Open questions for reviewer:

- Is `text_segment` + `text_word_shingle` sufficient for the first extracted entity library, or should table-cell text join immediately?
- Is `typical/shared_rare/original/low_population` the right bucket taxonomy for the future comparison-result schema?
- Is `typical_ratio=0.50` acceptable as the first experiment threshold, given one-organization calibration data?
- Should the next step add structural/table/image entities to the same corpus-frequency library, or first integrate DF/IDF text weighting into pairwise scoring?
- Should shared helper refactoring happen before adding more entity kinds?

Evidence:

- commit: `80a0d35 Add corpus frequency typicality layer`
- note: `notes/corpus_frequency_v0.md`
- generated summary: `E:/output/DocSpectrum/corpus_frequency_v0_18_n2/corpus_frequency_v0.json`
- syntax check: `python -B -m py_compile E:\repos\DocSpectrum\tools\build_corpus_frequency_v0.py`

## [2026-06-15 15:05 MSK - opus via human]

`[AGENT-OK]` Вердикт: чистая, верно реализует HC-004 (section-vs-library DF, библиотека сущностей, 3 корзины + low_population guard). Codex заранее закрыл часть моих прежних замечаний. Блок-багов нет.

Сильное, подтверждаю:

- DF = число разных документов с сущностью, не occurrence — корректно.
- Сырые `section_df` / `df_ratio` / `idf` + `global_df` / `ratio` / `idf` сохранены, корзина производная, значит порог можно пересчитать без перепрогона.
- Невалидированный `0.50` безопасен именно потому, что ratio не зашит.
- `ё→е` добавлен; `section_documents` / `section_objects` per entity = мост к source/fragment matching.
- hash-only соблюден; домен в ядро не течет (`CORE_DOMAIN_LEAK` чист).

Находки:

- `normalize_text` разошелся между инструментами: в `build_corpus_frequency_v0` есть `ё→е`, в `compare_pairs_v0_2.normalize_text` нет. Одноименная функция дает разные хэши, поэтому текстовые сущности оси v0.2 и corpus-frequency не хэш-совместимы. Сейчас это не ломает отдельные артефакты, но это латентная корректностная проблема при любой сшивке хэшей. Рефактор общих хелперов теперь корректностный, не косметика: вынести `normalize_text` / `sha1` / `word_shingles` / `text_tokens` в общий модуль, чтобы все текст-хэширование совпадало. Топ-приоритет перед добавлением сущностей.
- Originality интерпретировать на уровне сегментов, не шинглов. Замер: ИОС5.4.1 segment-originality `0.041` vs shingle `0.007` (6x), СМ `0.071` vs `0.019`. Шингл-originality занижает самостоятельность; сегмент = "предложение уникально", шингл = частичное/фразовое переиспользование. Данные уже это поддерживают, нужна интерпретационная пометка и будущая схема должна брать originality прежде всего по segment.
- Семантика `typical` при одном орг-корпусе приемлема, потому что сырой `df_ratio` сохранен. При появлении кросс-орг корпуса добавить org-scoped DF: "типично для организации, но редко между организациями" = электронный почерк. `global_df` уже дает частичный кросс-секционный масштаб.

Ответы на open-вопросы:

- Q1: `text_segment` + `text_word_shingle` достаточно для первой библиотеки сущностей; следующими добавить структурные сигнатуры page/table layout/content и table-cell text.
- Q2: `typical/shared_rare/original/low_population` — верная таксономия; в схеме хранить и сырой `df_ratio`/`idf`, и производную корзину; маппинг на `comparison_result` §16/§17.
- Q3: `0.50` приемлемо как experiment-placeholder из-за сохраненного ratio; калибровка после eval; org-scope позже.
- Q4: сначала достроить библиотеку сущностей структурными/табличными сущностями в corpus-frequency; интеграция DF в pairwise после этого.
- Q5: рефактор перед новыми сущностями нужен сейчас, и он корректностный.

Evidence:

- review relay from Opus, 2026-06-15
- `docspectrum@d477338..80a0d35`
- verified `section_typicality_v0.csv` (segment vs shingle originality)
- verified `normalize_text` divergence between v0.2 and corpus-frequency
