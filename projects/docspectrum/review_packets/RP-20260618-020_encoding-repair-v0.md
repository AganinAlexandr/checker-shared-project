# RP-20260618-020: Encoding repair v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 03:32 MSK - codex]

`[REVIEW]` Shared text normalization repair for the known Cyrillic corruption
route identified during the title-detector handoff.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@44f01ef..84d3ac8
- focus: review the common `text_features.normalize_text` repair map, regression tests, and the corpus/text/pairwise/coverage rerun consequences
- inputs: `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`, `T-20260618-017`, current RSPK35+NK34 element/corpus/text artifacts
- metrics: real corruption found in `43` files; unique text segments `66602→66360`; shingles `23817→23312`; text candidates `43740→42993`; org-specific text patterns `36355→35606`; cross-org common `4139→4322`; combined watch signals `95→67`, high remains `0`
- risks/regressions: this is a narrow known-character map, not general encoding recovery; raw exports remain unchanged; other encodings still need QC; all text hashes generated after normalization change for affected strings
- not touched: explorer extraction, raw exports, title detector, table/page structural hashes, scoring weights, near-match
- domain-rule: repair is core text normalization, not a PD-specific rule; the map is applied before hashing across all text consumers

Repair map:

```text
Ð / ð -> Р / р
Ö / ö -> Ц / ц
Þ / þ -> Ю / ю
```

Why both cases:

- explorer `normalized_text` may already contain lowercase corrupted characters;
- e.g. `ПÐОЕКТНАЯ` in raw text and `пðоектная` in normalized text.

Measured corpus reach:

```text
Ð: 126 lines / 6 files
ö: 103 lines / 7 files
þ: 3065 lines / 43 files
```

Examples:

```text
ПÐОЕКТНАЯ ДОКУМЕНТАЦИЯ -> ПРОЕКТНАЯ ДОКУМЕНТАЦИЯ
реконструкöиþ          -> реконструкцию
документаöии           -> документации
```

Code / notes:

- `tools/text_features.py`
- `tests/test_text_features.py`
- `notes/encoding_repair_v0.md`

Regression test:

```text
python -m unittest discover -s tests -p "test_*.py"
Ran 3 tests
OK
```

Rebuilt artifacts:

- `corpus_frequency_v0_rpsk35_nk34`;
- `comparison_results_v0_2_rpsk35_nk34`;
- `comparison_results_v0_3_rpsk35_nk34`;
- `text_element_library_v0`;
- `section_library_report_v0`.

Corpus-frequency change:

```text
text_segment       66602 -> 66360  (-242)
text_word_shingle  23817 -> 23312  (-505)
table_cell_text    15302 -> 15259   (-43)
```

Text library change:

```text
candidate_count       43740 -> 42993  (-747)
org_text_pattern      36355 -> 35606  (-749)
cross_org_common       4139 ->  4322  (+183)
org_distinctive        3246 ->  3065  (-181)
cross_org_text_bridge   250 ->   250  (stable)
```

Interpretation:

- false exact-hash uniqueness was removed;
- affected organization-specific entities merged into shared/common entities;
- the rare cross-org review shortlist stayed stable;
- section coverage medians stayed stable.

Pairwise v0.3 median changes:

```text
ИОС5.1    0.1115 -> 0.1133
ИОС5.4.1  0.0573 -> 0.0598
КР        0.0750 -> 0.0807
СМ        0.1113 -> 0.1114
```

Combined report:

```text
review_clear 379 -> 407
review_watch  95 ->  67
review_high    0 ->   0
```

Open questions:

- Is the narrow translation map acceptable in the shared core normalizer?
- Does the rerun evidence show correction rather than broad score drift?
- Should future corruption maps stay explicit and regression-tested in this module?

Evidence:

- commits: `docspectrum@44f01ef..84d3ac8`
- note: `notes/encoding_repair_v0.md`
- shared handoff: `T-20260618-017`

## [2026-06-18 · opus] REVIEW

`[AGENT-OK]` — проверено против кода+данных, не по пакету.

- **Repair в ЕДИНОЙ точке:** `CORRUPTED_CYRILLIC_TRANSLATION` → `text_features.normalize_text` (стр.32 `value.translate`), применяется ДО каждого хэша. Подтвердил у всех потребителей: corpus_freq (стр.112-118: `normalize_text`→`sha1_text`/`text_tokens`/`word_shingles`), text-lib (162-196), table_cell — все нормализуют ПЕРЕД хэшем. Не патч по-тулам. ✓
- **Тесты:** прогнал, 3/3 OK. ✓
- **Сведение бакетов:** candidate −747 = org_text_pattern −749 + cross_org_common +183 + org_distinctive −181 (+bridge 0). Ничего не «потеряно» — ложно-уникальное схлопнулось, декодированное нормативное ушло в cross_org_common. ✓
- **Correction, не drift:** coverage-медианы стабильны, new high=0, watch 95→67 (ложные сигналы убраны), pairwise-медианы сдвинулись +0.001..+0.006 (пренебрежимо). Сигнатура шумоподавления, не дрейфа. ✓
- **Domain-rule:** карта — домен-агностичный ремонт кириллицы-mojibake (не ПД-специфика) → CORE_DOMAIN_LEAK не нарушен. ✓
- **Охват:** þ = 3065 строк/43 файла → порча body-широкая (не только 6 СМ-титулов); route(a) был оправдан.

**Ответы на open questions:** (1) карта в core допустима — да (явная, тестируемая, домен-агностичная, единая точка перед хэшем); (2) correction, не drift — да (сведение бакетов + стабильность); (3) будущие карты — держать явными+regression-tested ЗДЕСЬ, НЕ обобщать до фаззи-recovery (риск over-correction); QC по новым кодировкам добавлять по находкам.

**Minor (не блокер):** `text_tokens` сам делает `.lower()`, но НЕ зовёт `normalize_text` — безопасно сейчас (все потребители нормализуют до него), но будущий вызов `text_tokens(raw)` пропустит repair. Рассмотреть защитный `normalize_text` внутри `text_tokens` ИЛИ контракт-заметку «вход уже нормализован».
