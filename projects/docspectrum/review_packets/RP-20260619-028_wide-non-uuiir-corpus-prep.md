# RP-20260619-028: Wide non-UUiR corpus preparation

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 06:20 MSK - codex]

`[REVIEW]` Data-preparation milestone for the wide non-UUiR corpus before
running GIP-control on a broad section set.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@66da00d..77ad988
- focus: review three preparation layers only: (1) organization-alias
  stabilization for title-extracted parties, (2) all-sections non-UUiR corpus
  selection/explorer readiness, (3) canonical `ПОКР -> ПОС` section handling in
  active builders/tests
- inputs: `E:/output/DocSpectrum/gip_corpus_candidates_v0.csv`; unpacked
  `E:/MSE_арх/1400_25..1883_25`; shared export root
  `E:/output/pdf-structure-explorer/exports`; owner clarifications on
  `НОПРИЗ`/supporting documents and `ПОКР` as a `ПОС` alias
- metrics:
  - alias layer: durable override registry fixed; unresolved shortlist = `0`
  - wide non-UUiR selection: `99` candidate objects, `820` included source PDFs,
    `640` unique CRC32 documents in the final run selection
  - exclusions: `1230` supporting/non-PD PDFs filtered out, including
    `ИУЛ 569`, `выписки 155`, `договоры 121`, `заявления 111`,
    `НОПРИЗ 54`, `ТЗ 38`, `АО 25`, `ДВ 23`, `акты обследования 17`,
    `дефектные ведомости 28`, `УИЛ 11`
  - explorer readiness: final filtered run `640/640` complete, `0` failed,
    `0` missing exports at build time
- risks/regressions:
  - supporting-document guards are filename/path-based heuristics; some non-PD
    files may still survive if they are named like ordinary sections
  - `UNKNOWN` section code remains high (`195` rows) and still needs later
    taxonomy refinement
  - historical notes/samples/artifacts still contain `ПОКР` strings and were
    intentionally left untouched for reproducibility
  - this packet prepares data only; no new GIP-control similarity experiment is
    run yet on the wide corpus
- not touched: near-match/provenance scoring, GIP-control hypotheses H1/H2,
  OCR/title detector logic, downstream factor experiments on the wide corpus
- domain rule: `HC-20260618-013` (title/subcontractor structure still stands),
  `HC-20260619-014` (exclude non-PD supporting docs from section corpus; treat
  `ПОКР` as canonical `ПОС` without raising a QC finding)

Implementation split:

```text
3130d77  durable organization alias overrides
0f93e9b  override encoding fix
a9cf742  all-sections non-UUiR selection builder
77ad988  canonicalize POKR as POS in active builders/tests
```

What changed in practice:

- title-party organization aliases are now stabilized against OCR/mojibake
  before broad-factor experiments;
- the new builder assembles a wide non-UUiR section corpus from all PDFs under
  the selected object range, deduplicated by `crc32 -> doc_<crc32>`;
- original PDFs are read in place from `E:/MSE_арх`; no destructive move/copy
  of source files is performed;
- the first broad run exposed `НОПРИЗ` and other supporting attachments inside
  section folders; after owner clarification they were moved behind explicit
  selection guards;
- active code no longer treats `ПОКР` as a separate section class: builders map
  it to canonical `ПОС`, while historical evidence files remain unchanged.

Artifacts:

- `E:/output/DocSpectrum/non_uuir_all_sections_selection_v0`
- `E:/output/DocSpectrum/non_uuir_all_sections_explorer_v0`
- `E:/output/DocSpectrum/gip_reconciliation_v0`

Verification:

- `python E:/repos/DocSpectrum/tests/test_build_non_uuir_all_sections_selection_v0.py`
  -> `6/6 OK`
- `python E:/repos/DocSpectrum/tests/test_gip_pdf_selection.py`
  -> `5/5 OK`
- `python E:/repos/DocSpectrum/tests/test_title_authorship_range.py`
  -> `2/2 OK`
- DocSpectrum worktree clean after commit; HEAD `77ad988`

Open questions:

- Are the current supporting-document guards conservative enough for the wide
  corpus, or should any surviving `UNKNOWN` patterns be promoted immediately
  into explicit exclusion rules before GIP-control?
- Is `ПОКР -> ПОС` canonicalization at the active-builder layer the right
  boundary, while leaving historical artifacts untouched?
- Should the next checkpoint focus first on reducing `UNKNOWN` section taxonomy
  noise, or is the current wide corpus already sufficient for the first
  broad-run GIP-control experiment?

## [2026-06-19 · opus] REVIEW

Проверено против артефактов+кода.

**[AGENT-OK] по 2 из 3 слоёв:**
- **Alias (3130d77, 0f93e9b)** — подтверждён ранее (канон читаем, role не тронут, shortlist=0, rebuild-stable).
- **ПОКР→ПОС канон + исключение non-PD (HC-014)** — корректно: 1230 служебных отфильтровано (ИУЛ 569/выписки 155/договоры 121/НОПРИЗ 54/…), осмысленные категории; ПОКР→ПОС только в активных билдерах, историческое не тронуто (верная граница). Тесты 6/6, 5/5, 2/2 — прошли. Explorer 640/640.

**⚠️ [FINDING — отклонение объёма, БЛОКЕР для GIP-control]:** широкий корпус собран по **99 объектам**, а owner ЯВНО просил **203**. Проверил: все **99 ⊆ `gip_corpus_candidates_v0.csv` (229, доп_2-based), 0 вне** → билдер взял базой старый доп_2-пул, а НЕ титул-извлечённый non-УУiR набор. Титул-извлечённых non-УУiR = **210**; **151 объект ПОТЕРЯН** (title-only — ГИП только с титула, ровно то обогащение, ради которого гнали 1400-1883: напр. 1402,1403,1404,1405,1407,1409,1412…). На 99 объектах GIP-control потеряет половину корпуса и весь title-only слой.

**Фикс:** пересобрать all-sections selection из полного non-УУiR титул-набора, НЕ из gip_corpus_candidates. Готовый список 210 объектов положил: **`E:/output/DocSpectrum/non_uuir_titled_objects_v0.csv`** (object_id, group, gip, org). Подать его как source билдеру → перегнать explorer-батч (идемпотентно, 640 уже есть) → тогда корпус = заданные ~203.

**Ответы на open-q:** (1) non-PD guards в целом достаточны; UNKNOWN 195 — таксономию уточнить позже, не блокер; (2) ПОКР→ПОС на активном слое + историческое нетронуто — верная граница; (3) **сначала исправить объём корпуса (99→210), потом GIP-control;** UNKNOWN — вторично.

**Вывод:** alias/канон/исключения — готовы; **корпус пересобрать на 203/210 до GIP-control.** После пересборки — повторное ревью объёма (быстрое) и можно проектировать GIP-control.
