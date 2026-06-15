# RP-20260615-007: Text helper refactor

Status: pending
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 15:30 MSK - codex]

`[REVIEW]` Correctness follow-up for RP-006 `normalize_text` divergence.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@80a0d35..f58b623
- focus: `tools/text_features.py`, imports in `tools/compare_pairs_v0_2.py` and `tools/build_corpus_frequency_v0.py`; please review that text normalization/hash helpers are now a single source of truth and that no scoring or corpus-frequency semantics changed beyond hash compatibility
- inputs: unchanged from RP-005/RP-006: `E:/output/DocSpectrum/element_base_v0_18_n2`, `E:/output/DocSpectrum/export`
- regenerated artifacts: `E:/output/DocSpectrum/comparison_results_v0_2_18_n2`, `E:/output/DocSpectrum/corpus_frequency_v0_18_n2`
- not touched: entity taxonomy, DF/IDF bucket logic, pairwise weights, structural signatures, table-cell text, embeddings, eval calibration
- risk addressed: text hashes from pairwise v0.2 and corpus-frequency v0 could diverge because similarly named local `normalize_text` functions differed on `ё→е`
- current policy: all text-based entity hashes should use `tools/text_features.py`

Implementation:

- added `tools/text_features.py` with `normalize_text`, `sha1_text`, `text_tokens`, `word_shingles`
- removed duplicated helper definitions from `compare_pairs_v0_2.py`
- removed duplicated helper definitions from `build_corpus_frequency_v0.py`
- preserved existing shingle scope behavior: v0.2 still builds word shingles over the document token stream; corpus-frequency still builds word shingles per segment

Verification:

- `python -B -m py_compile E:\repos\DocSpectrum\tools\text_features.py E:\repos\DocSpectrum\tools\compare_pairs_v0_2.py E:\repos\DocSpectrum\tools\build_corpus_frequency_v0.py`
- regenerated `comparison_results_v0_2_18_n2`: pair count remains `1190`
- regenerated `corpus_frequency_v0_18_n2`: document count remains `143`; entity counts remain `text_segment=30240`, `text_word_shingle=9931`; bucket counts remain `low_population=1541`, `original=16924`, `shared_rare=10709`, `typical=10997`
- direct import check: `compare_pairs_v0_2.normalize_text is text_features.normalize_text == True`; `build_corpus_frequency_v0.normalize_text is text_features.normalize_text == True`
- direct sample check: both tools normalize ` Ёлка  ёж ` identically and hash the same source strings identically

Open questions for reviewer:

- Is this sufficient to close the RP-006 `normalize_text` correctness finding?
- Should `text_features.py` also own higher-level text entity counters before adding table-cell text, or keep it limited to primitive helpers for now?
- Should the future refactor move shared CSV/math helpers too, or defer that until another metric actually needs them?

Evidence:

- commit: `f58b623 Unify text feature hashing`
- RP-006 review finding: `normalize_text` divergence between v0.2 and corpus-frequency
