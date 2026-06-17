# RP-20260617-016: Typical element library v0

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-17 16:29 MSK - codex]

`[REVIEW]` First consumer-facing typical-element library layer: table-centric
candidates, organization distinctiveness, and section-level coverage/residual.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@6a365d2..9753855
- focus: review the first `typical_element_candidates_v0` library layer end-to-end: candidate promotion rules, hash-only evidence, P1 coverage, P2 organization distinctiveness, derived `candidate_class`, trivial micro-form handling, and the new section coverage output for UC1a/UC1v table-layer checks
- inputs: `E:/output/DocSpectrum/element_base_v0_rpsk35_nk34`, `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`, `E:/output/DocSpectrum/export` (RSPK35 cohort root), `E:/output/DocSpectrum/export_nk_34_object_view` (NK34 cohort root), shared context `T-20260617-016`, `HC-20260617-009`, `HC-20260617-010`
- metrics: `531` table-form candidates; `7485` evidence rows; `426` meaningful forms; `105` diagnostic trivial micro-forms; `522` org-specific, `7` cross-org common, `2` org-distinctive; `0` borrowing candidates in the RSPK-vs-NK negative-control corpus; coverage built for `474/474` documents
- risks/regressions: v0 covers table forms only, not full section originality; exact `table_layout_signature` remains too strict until near-match; `residual` means unexplained by the v0 table-form library, not proven original authorship; org-conformance has only a negative-control test so far and still needs positive foreign-form/borrowing cases; current org-distinctiveness thresholds are experimental for two cohorts
- not touched: pairwise scoring weights, v0.3 similarity metrics, page/graphics/vector candidates, text-only candidate promotion, embeddings, near-match/bucketing, executor-level handwriting, raw-text artifacts
- domain-rule: follows HC-010 consumer-use-case priority and T-016 library design; `form coverage != copied content != original residual`; using organization templates is expected, while suspicious copying requires content-level or residual-level evidence beyond the typical form layer; cohort labels are applied after candidate extraction and do not affect core extraction

Purpose:

- Start the library as a product-facing layer, not a decorative catalog of entities.
- Convert recurring table layout signatures into candidate typical forms.
- Preserve per-occurrence evidence separately from the thin candidate row.
- Add P1 coverage and P2 organization distinctiveness immediately, per HC-010/T-016.
- Provide the first section-level answer to: what part of the table layer is explained by known typical forms, what remains residual, and whether the matched forms look like the document organization's own handwriting.

Code / notes added in DocSpectrum:

- `tools/build_typical_element_candidates_v0.py`
- `tools/build_typical_element_coverage_v0.py`
- `notes/typical_element_candidates_v0.md`

Candidate model:

- v0 starts table-centric.
- One candidate is one `(section_code, table_layout_signature)` group.
- Promotion threshold: at least `3` distinct objects.
- `candidate_id` is hash-derived from section, candidate kind and signature group.
- Candidate row stays thin; occurrence-level locations and table-cell hashes are stored in a separate evidence table.
- Coverage is counted by distinct objects/documents, not raw occurrences.
- Organization distinctiveness is computed after extraction.
- Raw text is not written; table-cell evidence stores hashes only.

Candidate classes and statuses:

- `candidate_class` is derived:
  - `cross_org + same_content/mostly_same_content` -> `borrowing_candidate`;
  - otherwise `typical_form`.
- In this corpus, `borrowing_candidate = 0`, so the RSPK-vs-NK contrast remains a clean negative borrowing control.
- Small stamp-like forms are not dropped silently.
- They are retained as `candidate_status = diagnostic_trivial` with `triviality_status = trivial_micro_form`.
- Main coverage excludes diagnostic trivial forms by default.

Candidate results:

```text
candidate_count: 531
evidence_row_count: 7485
table_cell_hash_cache_document_count: 473
skipped_layout_groups_below_min_objects: 1830
```

Candidate counts by section:

```text
АР        21
ИД        28
ИОС5.1    86
ИОС5.4.1 183
ИОС5.5.1  74
КР        24
ПОКР      22
ПОС       22
СМ        71
```

Organization distinctiveness:

```text
cross_org_common   7
org_distinctive    2
org_specific     522
```

Candidate status:

```text
meaningful_form       426
trivial_micro_form    105
```

IOS5.4.1 seed interpretation:

- The expected bridge was promoted as cross-organization table-form candidates.
- Main seed shape: `8 rows x 4 columns / 22 cells`.
- Object distribution: `NK:10 | RSPK:3`.
- Content signatures vary.
- Correct interpretation: stable form, variable content.
- Therefore it is a normative/typical form candidate, not borrowing by itself.

Section coverage model:

- New tool: `tools/build_typical_element_coverage_v0.py`.
- Output: `typical_element_section_coverage_v0.csv`.
- Coverage is table-form-only in v0.
- `table_form_coverage_ratio` counts meaningful matched table-form occurrences over all table occurrences.
- `table_form_residual_ratio` is unexplained by the v0 table-form library, not proven original authorship.
- `org_form_conformance_ratio` counts matched forms that are either cross-org common or dominant for the document cohort.
- `foreign_org_form_ratio` detects non-common forms whose dominant organization differs from the document cohort.

Coverage result:

- documents measured: `474/474`;
- median org conformance ratio: `1.0` across all sections;
- foreign organization form ratio: `0.0` in the current negative-control corpus;
- borrowing candidate occurrences: `0`.

Median table-form coverage by section:

```text
АР        0.5000
ИД        0.8966
ИОС5.1    0.6818
ИОС5.4.1  0.7143
ИОС5.5.1  0.4615
КР        0.6000
ПОКР      0.9667
ПОС       0.5903
СМ        0.2222
```

Interpretation:

- P2 works: the library separates a large organization-specific layer from a small cross-organization normative layer.
- The RSPK-vs-NK corpus remains a negative borrowing control: no cross-org same/mostly-same content table-form candidates.
- Section coverage gives the first practical UC1a/UC1v table-layer report.
- The high `ПОКР`/`ИД` coverage and low `СМ` coverage look plausible and should be treated as a first sanity signal, not a final product metric.
- `org_form_conformance_ratio = 1.0` is expected here because this corpus does not include positive foreign-template cases.

Generated artifact paths:

- `E:/output/DocSpectrum/typical_element_candidates_v0/typical_element_candidates_v0.csv`
- `E:/output/DocSpectrum/typical_element_candidates_v0/typical_element_candidate_evidence_v0.csv`
- `E:/output/DocSpectrum/typical_element_candidates_v0/typical_element_candidates_v0.json`
- `E:/output/DocSpectrum/typical_element_coverage_v0/typical_element_section_coverage_v0.csv`
- `E:/output/DocSpectrum/typical_element_coverage_v0/typical_element_section_coverage_v0.json`

Verification:

- `python -m py_compile` on `build_typical_element_candidates_v0.py` and `build_typical_element_coverage_v0.py`.
- Regenerated candidate artifacts on RSPK35+NK34.
- Generated coverage artifacts on RSPK35+NK34.
- Checked JSON summaries for candidate counts, class counts, triviality counts, org-distinctiveness counts, and section median coverage.
- DocSpectrum current commit: `9753855 Add typical element coverage v0`.

Known limitations / reviewer attention:

- Coverage is only table-form coverage.
- Full UC1v "share created by performer" still needs text, graphics, near-match and executor labels.
- Positive copy-catching cases are not present in RSPK-vs-NK, so `foreign_org_form_ratio` and `borrowing_candidate` are hooks validated only as negative controls.
- Exact layout signatures miss near-identical forms and should not be the final mechanism for UC3.
- Micro-forms are flagged but still present in the candidate catalog for diagnostics.

Open questions for reviewer:

- Does the candidate-class derivation close the UC3 hook sufficiently for v0?
- Is `diagnostic_trivial` the right handling for small stamp-like forms, or should they be split into a separate artifact later?
- Is the section coverage report a sufficient close of HC-010 "Build 1" for table-layer UC1a/UC1v?
- Should the next step prioritize near-match for table/page forms, or expand coverage to text candidates first?
- Should `org_form_conformance_ratio` remain a simple ratio, or should it be weighted by candidate IDF / distinctiveness in v0.1?

Evidence:

- commits: `docspectrum@6a365d2..9753855`
- notes: `notes/typical_element_candidates_v0.md`
- artifacts: paths listed above
- shared context: `T-20260617-016`, `HC-20260617-009`, `HC-20260617-010`

## [2026-06-17 18:00 MSK · opus via human]

`[AGENT-OK]` Вердикт: первый потребительский слой (каталог + coverage) работает end-to-end, методически здраво, честно ограничен. Блок-проблем нет. Range `6a365d2..9753855`.

Подтверждаю (проверил код+артефакты):
- директивы HC-010/T-016 выполнены: хэш-ID, тощий candidate + отдельный evidence, **coverage по РАЗНЫМ объектам** (size-aware), P2 org-distinctiveness В v0, content_variability, N≥3, hash-only, near-match honestly pending;
- обе мои находки по 223af5a закрыты: **candidate_class теперь DERIVE** (cross_org + same/mostly_same content → borrowing_candidate) ✓; **diagnostic_trivial** для микро-форм ✓ (426 meaningful + 105 trivial);
- **P2 работает:** 522 org_specific (орг-почерк) vs 7 cross_org_common (нормативный слой);
- **coverage-инструмент = UC1а + UC1в(табл):** conformance/foreign, coverage/residual, borrowing-хук; interpretation_note честный.

Результаты (мой факт-чек): RSPK и NK **conformance=1.0, foreign=0.0** (каждый док использует формы своего орга, чужих нет → орг-почерк консистентен); coverage **NK 0.90 vs RSPK 0.50** (NK сильнее шаблонизирован); borrowing=0. Чистый негативный контроль на уровне форм.

Находки (минор/forward): (1) coverage = только таблицы → полный UC1в нужен текст/структурные формы; (2) **copy-catching (UC1а) и borrowing (UC3) — только негативный контроль**, ловящая способность не испытана (нет позитивного кейса чужих форм; ждёт данных/near-match); (3) exact-layout-сигнатуры мимо near-identical → near-match для UC3 обязателен.

Ответы на open-вопросы:
- **Q1 (UC3-хук derive class):** достаточно для v0 (логика верна); РЕАЛЬНЫЙ UC3 = near-match-gated (exact-content only сейчас).
- **Q2 (diagnostic_trivial):** оставить в каталоге со статус-флагом (coverage уже фильтрует через candidate_statuses); отдельный артефакт — только если каталог разрастётся.
- **Q3 (закрывает ли HC-010 «Сборка 1»):** да, для ТАБЛИЧНОГО слоя «Сборка 1» закрыта; полный UC1в (весь раздел) = расширение на текст.
- **Q4 (след: near-match ИЛИ текст-coverage):** по приоритету HC-010 (UC1 > UC3) — **сначала текст-кандидаты + coverage** (доводит UC1а/1в до уровня всего раздела, не только таблиц = приоритет #1); near-match — следом (UC3 #3 + ось C). near-match остаётся критическим, но текст-слой ближе к #1.
- **Q5 (взвешивать conformance по IDF/distinctiveness):** да в v0.1 — использование высоко-отличительной формы = сильнее почерк-свидетельство, чем общей; взвесить conformance по org_distinctiveness/section_idf (согласуется с P2). v0 простой ratio ок.

Evidence: `docspectrum@6a365d2..9753855`; verified candidate_class derive + triviality, `typical_element_section_coverage_v0.csv` (RSPK/NK conformance 1.0/foreign 0.0, coverage 0.50/0.90, borrowing 0).
