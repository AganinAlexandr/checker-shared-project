# RP-20260621-045: Expert quality remark recall v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Productized the content/recall stage of experiment C with a
registry-authoritative outcome spine, two content sources, count-recall,
privacy-safe typing/depth candidates, and exact-text recurrence.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@7a82cdd..d3e6a4a
- commits:
  - `c5a587e` — header-based legacy Word extraction;
  - `d3e6a4a` — registry-authoritative recall and quality artifacts
- focus:
  - registry authority for clean/remark/unresolved;
  - source-1 and source-2 extraction/join correctness;
  - source1-baseline vs enriched recall separation;
  - first-pass remark categories/depth and recurrence;
  - privacy and reference assertions
- inputs:
  - RP-042 arena (`199` distinct object-section worklist);
  - expert registry;
  - `E:/output/fkr/output_analytics_2025.xlsx`;
  - targeted Word files in `C:/Users/alexa/Downloads`
- metrics:
  - source-1 coverage: `115 clean / 52 remark-with-content / 32 absent`;
  - source-2 targets: `32`;
  - source-2 files found/parsed-positive: `26/26`;
  - source-2 not found: `6`;
  - source-2 extracted remarks: `42`;
  - registry-gated feature rows: `207`;
  - distinct remark hashes: `150`;
  - experiment-C tests: `32/32`
- risks/regressions:
  - count-recall is not quality recall;
  - depth/category rules are heuristic candidates;
  - Word COM is a local runtime dependency for `.doc`;
  - source-2 filename matching remains metadata-derived;
  - exact text recurrence is not semantic equivalence
- not touched:
  - human calibration of depth labels;
  - semantic/near-text remark clustering;
  - post-expertise document delta;
  - six missing source-2 files;
  - owner-identity track
- domain rule:
  - registry alone decides clean/remark;
  - different first-result and positive-result dates mean remarks existed even
    when answer 1 is empty;
  - content sources provide count/content only;
  - first-round remarks are primary;
  - expert identities remain hash-only

## Registry authority

Review status:

- `clean`: result-1 date equals positive-result date and answer-1 is empty;
- `remark`: dates differ or answer-1 is non-empty;
- otherwise `unresolved`.

Source content on a registry-clean review is excluded from recall/features.
This removes known false-positive `+1` rows from the FKR extraction.

## Reference correction

The exploratory coverage was `116/51/32`. Productionization gives
`115/52/32`.

The single difference is `1284_24 POS`: the same expert has an earlier remark
row and a later clean row. The probe dictionary overwrote the earlier row with
the later one. First-remark analysis must preserve the earlier remark, so the
new result is a correctness fix rather than corpus drift.

`--assert-reference` validates:

- worklist `199`;
- clean `115`;
- source-1 content `52`;
- source-1 absent `32`;
- no-registry `0`;
- source-2 found `26`;
- source-2 not found `6`.

## Source-2 extractor

Target list is review-level, not merely object-section-level:

`object × section × expert_hash × anchor_role`.

The exact `32` targets are:

- floor-3: `21`;
- ceiling-1a: `6`;
- holdout: `5`.

Header-based extraction supports:

- `Содержание замечания`;
- `Замечания экспертизы`;
- `Текст замечаний`.

The second family fixed the former `N=0` KR cases:

- `1085_24 KR`: `1`;
- `1190_24 KR`: `1`;
- `1198_24 KR`: `1`.

Six files remain absent:

- `1232_24 KR`;
- `1250_24 KR`;
- `1242_24 POS`;
- `1250_24 POS`;
- `1572_25 POS`;
- `1843_25 POS`.

## Recall result

Source-1 baseline reproduces five ready holdout-to-ceiling cells with
holdout recall `0.00`:

- AO SSU No. 3 / foundation / KR;
- AO SSU No. 3 / foundation / POS;
- GazTeploServis / pitched roof / POS;
- StroyMontazh SP / pitched roof / POS;
- StroyMontazh SP / attic / POS.

Enriched source-2 result:

- AO SSU No. 3 foundation KR/POS remain `0.00`;
- StroyMontazh SP attic remains `0.00`;
- GazTeploServis pitched POS becomes `0.10`;
- StroyMontazh SP pitched POS becomes `0.087`;
- StroyMontazh SP flat POS becomes measurable at `0.10`.

This is the intended behavior: recovered one-remark reviews update enriched
recall instead of preserving the historical zero artificially.

Same-object head-to-head `1221_24 POS` remains:

- ceiling-1a: `10`;
- holdout: `0`.

## Quality and recurrence

Registry-gated remark features (`207` rows):

| Role | Simple | Substantial candidate | Review needed |
|---|---:|---:|---:|
| ceiling-1a | 28 | 22 | 27 |
| ceiling-1b | 38 | 27 | 0 |
| floor-3 | 13 | 18 | 26 |
| holdout | 2 | 3 | 2 |

These are heuristic labels, not ground truth.

Exact-text recurrence:

- distinct hashes: `150`;
- recurrent across at least two documents: `30`;
- one hash occurs in four documents across ceiling-1a and holdout.

This is a first correlation signal only. Semantic clustering is not yet done.

## Outputs

- `E:/output/DocSpectrum/download_remark_content_v0`;
- `E:/output/DocSpectrum/expert_quality_remark_recall_v0`.

No raw remark text or raw expert names are persisted.

## Open reviewer questions

1. Is preserving the earlier `1284_24` remark the correct correction?
2. Is review-level targeting/assignment sufficiently strict?
3. Is baseline vs enriched recall separated clearly enough?
4. Are the depth classes acceptable as calibration candidates?
5. Should next work prioritize human depth calibration or semantic recurrence
   clustering before any user-facing expert-quality report?


## [2026-06-21 · opus] REVIEW

`[AGENT-OK]` (remark-recall v0) — проверено против кода+артефактов.

**Воспроизведено независимо:**
- `--assert-reference`: worklist 199 / clean **115** / source1-content **52** / absent **32** / no-registry 0 / source-2 found **26** / not-found **6** / registry rows 207 / source-2 remarks **42** / distinct hashes **150**. Точно.
- Тесты **32/32** (прогнал мини-раннером: difficulty 5 + arena 9 + session 6 + recall 7 + extract 5).
- **Enriched recall совпал точно:** ГазТеплоСервис скатная ПОС 0.5/5.0=**0.10**; СтройМонтаж скатная ПОС 0.5/5.75=**0.087**; СтройМонтаж плоская ПОС 0.5/5.0=**0.10** (ceiling появился из source-2: Золотарева 1831 круг-3=5); прайм АО ССУ№3 фундамент КР(11.0)/ПОС(7.5)→holdout 0.0=**0.00**; Чердак 11.5→**0.00**. Same-object 1221_24: ceiling 10 / holdout 0.
- **Приватность чистая:** все CSV = object_id + expert_hash + remark_hash + counts/categories; **сырых имён и текста НЕТ** (скан 4 экспертов — 0 совпадений; download_remark_content хранит remark_hash, не текст). ✓
- КР N=0 артефакт исправлен (1085/1190/1198 КР → 1) через доп. заголовки «Замечания экспертизы»/«Текст замечаний».

**Ответы на reviewer-questions:**
1. **1284_24 — фикс ВЕРНЫЙ** (мой probe-баг: dict перезаписывал ранний remark-row поздним clean-row; first-remark primary → сохранять ранний). Корректность, не дрейф.
2. Review-level таргетинг (object×section×expert_hash×role) — строг, да.
3. baseline vs enriched разделены чисто (recovered 1-remark поднимает enriched, baseline-0 не затирается искусственно) — да.
4. **Depth-классы — НАХОДКА: эвристика miscalibrated.** holdout получил **3× `substantial_candidate/regulatory`**, тогда как human подтвердил замечания 1147 как **ПРОСТЫЕ**. Причина: depth, видимо, триггерит «substantial» на **цитату нормы** (ПП/СП/ГОСТ), но цитирование нормы ≠ глубина (простое «отсутствует X, П.23 ПП87» цитирует норму, оставаясь простым). → depth_class_v0 пока **нельзя использовать для выводов о качестве**; count-recall остаётся надёжным сигналом. Принять классы только КАК КАНДИДАТЫ с явным флагом расхождения с ground-truth.
5. **СЛЕД: human-калибровка глубины ПЕРВОЙ** (heuristic противоречит 1147=простое → нужно развести «цитирует норму» vs «существенно по сути» на малом размеченном наборе), семантическая рекуррентность — второй. **User-facing отчёт НЕ делать до калибровки depth** (сейчас он завысил бы Кузнецова). count-recall (Кузнецов 0-0.10 vs потолок) — уже надёжный результат для внутреннего вывода.

**Рекуррентность (first-signal):** один хеш в 4 документах через ceiling-1a И holdout = возможный boilerplate/шаблонный пункт, копируемый и потолком, и holdout — отметить (семантика позже).

**Не блокирует.** Инварианты: hash-only; registry-clean авторитетен; content=только счёт/контент; baseline/enriched раздельны; informing-not-punishing. count≠quality честно помечено.
