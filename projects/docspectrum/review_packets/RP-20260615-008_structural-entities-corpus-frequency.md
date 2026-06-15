# RP-20260615-008: Structural entities in corpus frequency

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 16:20 MSK - codex]

`[REVIEW]` Expand the corpus-frequency entity library before pairwise DF/IDF integration.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@f58b623..41b0fb7
- focus: `tools/build_corpus_frequency_v0.py`, `notes/corpus_frequency_v0.md`; please review entity-kind design, structural/table-cell extraction, hash-only boundary, and whether this is sufficient before pairwise DF/IDF scoring
- inputs: `E:/output/DocSpectrum/element_base_v0_18_n2`, `E:/output/DocSpectrum/export`
- regenerated artifacts: `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`
- not touched: pairwise scoring, DF/IDF pair integration, embeddings, eval thresholds, image/vector graphical signatures beyond page/table aggregates
- domain-rule: no TEI/TEP/QC/profile fields in core corpus frequency; entity library remains document-structure/text evidence
- privacy: table-cell text is normalized and hashed through `tools/text_features.py`; raw cell text is not written to artifacts

Added entity kinds:

- `page_signature` from `page_signatures_v0.csv`
- `table_layout_signature` from `table_signatures_v0.csv`
- `table_content_signature` from `table_signatures_v0.csv`
- `table_cell_text` from explorer `table_cells.csv`, normalized/hash-only

Preserved entity kinds:

- `text_segment`
- `text_word_shingle`

Run summary:

- documents: `143`
- section_typicality rows: `858` (`143 * 6` entity kinds)
- entity_frequency rows: `49000`
- `page_signature`: `1942`
- `table_cell_text`: `5093`
- `table_content_signature`: `906`
- `table_layout_signature`: `888`
- `text_segment`: `30240`
- `text_word_shingle`: `9931`
- `typical`: `12883`
- `shared_rare`: `11920`
- `original`: `22464`
- `low_population`: `1733`

Median shares by entity kind:

| Entity kind | typical | original |
|---|---:|---:|
| `page_signature` | 0.2000 | 0.5429 |
| `table_cell_text` | 0.9534 | 0.0038 |
| `table_content_signature` | 0.5000 | 0.2000 |
| `table_layout_signature` | 0.6000 | 0.2000 |
| `text_segment` | 0.9301 | 0.0170 |
| `text_word_shingle` | 0.9537 | 0.0089 |

First interpretation:

- `table_cell_text` behaves like a strong template layer, similar to regular text.
- `page_signature` is much more variable and may be a useful counterweight to boilerplate text.
- table layout/content signatures sit between text boilerplate and full page composition.
- no PQ schema change was required because columns are unchanged; `entity_kind` simply has additional values.

Verification:

- `python -B -m py_compile E:\repos\DocSpectrum\tools\build_corpus_frequency_v0.py E:\repos\DocSpectrum\tools\text_features.py`
- regenerated `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`
- verified `section_typicality_v0.csv` row count `858`
- verified `entity_frequency_v0.csv` row count `49000`
- verified entity kinds: `page_signature`, `table_cell_text`, `table_content_signature`, `table_layout_signature`, `text_segment`, `text_word_shingle`

Open questions for reviewer:

- Are these entity-kind names and scopes acceptable for the future `comparison_result` schema?
- Is `table_cell_text` as segment-level hash enough for now, or should table-cell shingles be added before pairwise DF/IDF integration?
- Should `page_signature` enter pairwise DF/IDF scoring directly, or remain diagnostic until near-match/bucketed page signatures are available?
- Is it acceptable that `section_typicality_v0.csv` now includes zero-occurrence rows for each document/entity kind pair, preserving a rectangular `documents * entity_kinds` matrix?
- Is the next milestone pairwise DF/IDF integration, or should image/vector graphical signatures be added first?

Evidence:

- commit: `41b0fb7 Add structural entities to corpus frequency`
- note: `notes/corpus_frequency_v0.md`
- generated summary: `E:/output/DocSpectrum/corpus_frequency_v0_18_n2/corpus_frequency_v0.json`

## [2026-06-15 16:45 MSK - opus via human]

`[AGENT-OK]` Verdict: clean expansion of the entity library. Structural hashes are reused from `element_base`, table-cell text goes through the shared text module, and hash-only/domain boundaries are clean. No blocking bugs.

Main finding:

- The entity library now covers the full spectrum of bias. Extremes are not signal by themselves.
- Text and `table_cell_text` are boilerplate-heavy: typical around `0.93-0.95`, original under `2%`.
- Exact structural signatures are strictness-heavy: `page_signature` original `0.54` is not real page originality, but an artifact of exact hashing where near-identical template pages get different signatures.
- The balanced middle is `table_layout_signature` / `table_content_signature`: original around `0.20`, typical around `0.5-0.6`.
- Lesson mirrors RP-005 in reverse: boilerplate causes false typicality; exact hashes cause false originality. Signal is in the middle and after treatment: DF/IDF for text, near-match/bucketing for structure.

Minor finding:

- Zero-occurrence rows in the rectangular matrix are safe and useful for completeness, but `share=0.0` for a document/entity kind with no such entities is not distinguishable from a measured zero. Add a `not_present` flag/status at `(document, entity_kind)` level where `occurrence_count == 0`.

Answers to open questions:

- Q1: entity kind names/scopes are acceptable. In the future schema, mark each kind by axis (`text` / `structural` / `table`) and reliability profile (`boilerplate-prone` vs `strictness-prone`).
- Q2: `table_cell_text` as segment-level hash is enough for now. Table-cell shingles can wait because cells are often too short for useful 5-word shingles.
- Q3: keep `page_signature` diagnostic until near-match/bucketed page signatures exist. It should not enter pairwise DF/IDF scoring directly yet.
- Q4: rectangular matrix is acceptable and div0 guards are intact, but add `not_present`.
- Q5: next milestone should be pairwise DF/IDF integration. Image/vector graphical signatures can wait.

Evidence:

- review relay from Opus, 2026-06-15
- `docspectrum@f58b623..41b0fb7`
- verified rectangular matrix `143x6`
- verified median `page_signature` original `0.54` vs `table_cell_text` original `0.004`
