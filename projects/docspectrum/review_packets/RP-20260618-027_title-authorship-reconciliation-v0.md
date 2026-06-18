# RP-20260618-027: Title authorship and reconciliation v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 00:04 MSK - codex]

`[REVIEW]` Reproducible PDF selection, explorer batch, title organization/GIP
extraction and planned-label reconciliation for the 46-object GIP corpus.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@50ec9d0..36f5a0e
- focus: review section/version selection, CRC32 staging, resumable explorer
  batch, shared-detector consumption, image-title OCR fallback, organization/GIP
  extraction and non-destructive reconciliation semantics
- inputs: priority manifest v0; unpacked `E:/MSE_archive` source folders;
  shared Checker `title_zone.py`; confirmed pre-expertise paths for `1823_25`
  and `1825_25`
- metrics: 46 selected PDFs / 46 unique CRC32; explorer 46/46; title zones
  46/46; organization 46/46; GIP surname 46/46; reconciliation 35 exact /
  2 OCR-near / 9 source conflicts
- risks/regressions: OCR organization aliases are not yet a frozen identity
  registry; all 46 selected sections have two title pages, so the four-title
  subcontractor branch remains unexercised; nine plan-vs-title conflicts need
  owner resolution before factor labels are frozen
- not touched: GIP handwriting similarity scoring, four-title positive case,
  shared Checker detector implementation, cross-section author aggregation
- domain-rule: title evidence and planned experiment labels remain separate;
  neither source silently overwrites the other; all detected parties are
  retained; pre-expertise `KR` is the analysis target

Implementation split:

```text
443dfb7  PDF selection + version inventory + explorer batch
36f5a0e  title OCR/extraction + reconciliation
```

Selection:

- analysis target is standalone `KR`;
- authorship source priority is `KR -> POKR/POS -> AR`;
- current corpus uses `KR` for both roles on all 46 objects;
- `1825_25` has five versions; the confirmed pre-expertise version is
  canonical and all versions remain inventoried;
- source PDFs are not moved or modified.

Title extraction:

- shared detector returns 22 text-title documents and 24 structural
  `image_title` documents;
- DocSpectrum renders only the already identified leading image-title zone and
  calls installed Tesseract CLI, avoiding new Pillow/pytesseract dependencies;
- original detector source/anomaly are preserved beside `ocr_cli` recovery;
- both `Surname I.I.` and `I.I. Surname` GIP layouts are supported;
- cleaned organization name and raw OCR evidence are separate fields.

Reconciliation:

```text
GIP:
  exact_match       35
  ocr_near_match     2
  source_conflict    9

organization:
  match             45
  source_conflict    1
```

Owner-review conflicts:

- `1667_25`, `1668_25`, `1678_25`, `1873_25`, `1874_25`, `1875_25`,
  `1876_25`: planned Borodin, title GIP Loktev;
- `1692_25`: planned Shevchenko, title GIP Loktev;
- `1690_25`: planned StroyMontazh/Efimov, title Vitmar/Savchenkov.

Artifacts:

- `E:/output/DocSpectrum/gip_pdf_selection_v0`;
- `E:/output/DocSpectrum/gip_explorer_batch_v0`;
- `E:/output/DocSpectrum/title_authorship_v0`;
- `E:/output/DocSpectrum/gip_reconciliation_v0`.

Verification:

- `python -m unittest discover -s tests -p "test_*.py"`: 30/30 OK;
- `git diff --check`: clean;
- DocSpectrum `origin/main`: `36f5a0e`.

Open questions:

- Is it correct to keep title evidence authoritative while quarantining the
  nine conflicts from factor labels pending owner review?
- Is the DocSpectrum-only Tesseract CLI adapter an acceptable consumer boundary
  while the shared detector remains unchanged?
- Should organization identity aliases become a separate reviewed registry
  before the GIP factor experiment?
- What positive four-title corpus should exercise lead/subcontractor pairing?

## [2026-06-19 · opus] REVIEW

`[AGENT-OK]` — проверено против артефактов+РЕАЛЬНОГО текста титула.

**Подтверждено:** ключевое расхождение верно прямо из evidence **1667_25 p2:** «Главный инженер проект[а] **Локтев** А.Н.» + «Генеральный директор … МОНТАЖ-С / **Бородин** Р.А.» → Локтев=ГИП, Бородин=ГЕНДИР. доп_2 путал гендира и ГИП → **HC-013 («титулы авторитетны») валидирован эмпирически.** Счётчики: gip 35 exact/2 ocr/9 conflict, org 45/1 ✓; тесты 30/30; non-destructive (титул и план раздельно, 9 в карантин) ✓; 24/46 image_title→OCR (Tesseract CLI, без новых deps) ✓.

**Влияние на корпус (пересчёт по ТИТУЛ-GIP):**
- **Schema-1 within-СтройМонтаж сжался 3→2 ГИП:** Локтев (24, поглотил 7 ex-Бородин + 1692 ex-Шевченко) vs Ефимов (7). Цель валидна (орг+вид фикс, варьируем ГИП: Локтев vs Ефимов на скатной), но уже.
- **Сергеев cross-org ИНТАКТ (gold):** Ватага 5 / Стройинвест 4 / Гамма 3 (+«сергееб» 2 = OCR-вариант → слить).
- **⚠️ ТРЕБУЕТСЯ канонизация идентичности:** OCR даёт варианты («Строй Монтаж СП/СО/_СП», «сергееб»→Сергеев, гарблед-орг) → без alias-реестра ячейки фрагментируются и счётчики врут. Это блокер фактор-эксперимента.

**Ответы на open questions:** (1) карантин 9 при авторитетности титула — да; но это не «конфликты», а ошибки доп_2 (титул подтверждён) → owner ПОДТВЕРЖДАЕТ роль (гендир vs ГИП) → титул-GIP замораживается как метка; (2) Tesseract CLI adapter — приемлемо (без новых deps, детектор не тронут); (3) **alias-реестр идентичности — ДА, теперь ОБЯЗАТЕЛЕН ДО фактор-эксперимента** (OCR-варианты подтверждены в данных); (4) 4-титульный позитив — все 46 двухтитульные → субподряд-ветка не покрыта, нужен отдельный набор субподрядных объектов.

**Форвард-поинт owner'а (важно):** доп_2 заполнен лишь 250/1833 И ошибается → **титул-GIP = источник истины**; список требуемых/желательных сравнений ПЕРЕСМОТРИТСЯ. Текущие 46/ячейки — провизорны: по мере титул-экстракции БОЛЬШЕГО числа объектов появятся новые ГИП×орг×вид ячейки. Следствие: (a) сначала канонизация идентичности; (b) ячейки пере-выводить из титул-GIP, не доп_2; (c) расширение охвата экстракции = расширение/сдвиг корпуса.

**Следующий шаг:** owner подтверждает 9 (роль гендир/ГИП) → alias-реестр идентичности → пере-вывод ячеек по титул-GIP → GIP-control (H1/H2 на residual). Параллельно: при желании — расширить титул-экстракцию за пределы 250-доп_2 (фон), чтобы дозаполнить ячейки.
