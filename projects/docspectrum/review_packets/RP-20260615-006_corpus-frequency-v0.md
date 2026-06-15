# RP-20260615-006: Corpus frequency typicality v0

Status: pending
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
