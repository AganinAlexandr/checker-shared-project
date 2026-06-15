# RP-20260615-007: Text helper refactor

Status: reviewed
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

## [2026-06-15 15:50 MSK - opus via human]

`[AGENT-OK]` Verdict: the RP-006 text-hash compatibility issue is closed correctly. No blocking bugs.

Verified:

- `text_features.py` is now the single source for `normalize_text`, `sha1_text`, `text_tokens`, `word_shingles`.
- Both tools import the same four primitives.
- `normalize_text` is unified on the version with `ё→е`.
- Behavior-preserving evidence is acceptable: corpus-frequency counts are unchanged (`text_segment=30240`, `text_word_shingle=9931`, same buckets); v0.2 text medians moved by at most `0.0001`, so the refactor is correctness-positive and practically score-neutral.
- Different shingle scope is intentionally preserved: v0.2 uses the document token stream, corpus-frequency uses per-segment shingles. This is okay because primitives are shared, while the entity extraction contract is not yet shared.

Minor finding:

- `from text_features import ...` is a sibling import and depends on `tools/` being on `sys.path`. It works for current script execution, but is fragile if tools are run as modules from the repo root or imported externally. Low priority; address when the number of consumers grows by packaging `tools/` or adding an explicit import strategy.

Answers to open questions:

- Q1: yes, this is sufficient to close the RP-006 `normalize_text` correctness finding.
- Q2: keep `text_features.py` limited to primitive helpers for now. High-level entity extraction is intentionally different between v0.2 and corpus-frequency; merge it later when table-cell/common entity contracts are added.
- Q3: defer CSV/math helper refactor. There is no current correctness divergence there; revisit when structural entities enter corpus-frequency.

Evidence:

- review relay from Opus, 2026-06-15
- `docspectrum@80a0d35..f58b623`
- verified imports
- verified `comparison_results_v0_2.csv` text delta <= `0.0001`
- verified corpus-frequency counts unchanged
