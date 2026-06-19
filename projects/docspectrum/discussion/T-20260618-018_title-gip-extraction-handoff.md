# T-20260618-018: Title→GIP extraction handoff (GIP positive-control corpus)

Status: open
Project: DocSpectrum
Owner: codex (generator — runs per token-balancing decision 18-06)
Reviewer: opus
Related: `HC-20260618-013`, `HC-20260618-012`, `HC-20260617-011`, `RP-20260618-024`

## [2026-06-18 · opus] Handoff: extract authoritative org+GIP from title pages

`[PLANNING]` Метки в `Капремонт_Объекты.xlsx` (доп_2/проектировщик) могут быть устаревшими → **титулы авторитетны** (HC-013). Прогнать титул-экстракцию по корпусу GIP-позитив-контроля.

### Вход
- Пул кандидатов: `E:/output/DocSpectrum/gip_corpus_candidates_v0.csv` (229 объектов 2025; поля: номер, dev_ГИП(доп_2), орг, группа/подГруппа=вид работ). СТАРТОВЫЙ отбор, не ground-truth.
- Детектор титулов: `detect_title_zone` (`pp87-checker/tools_heading/title_zone.py`, импорт по DEC-004) → cover_end/title_pages/anomaly.

### Задача
Для каждого бандла (после выгрузки explorer): 
1. `detect_title_zone` → титульная зона + **кол-во титулов (2 vs 4)**.
2. Из текста титулов извлечь роли ГИП: **ГИП-проектировщик**; при **4 титулах** — также **ГИП СУБПОДРЯДЧИКА (реальный автор)** + ГИП заказчика (HC-013). Цель атрибуции почерка = субподрядчик.
3. Орг (проектировщик) — тоже с титула (таблица может врать).
4. Учесть кодировку (corruption-tolerant, как в детекторе) при парсинге ФИО/ролей.

### Выход (предложение схемы)
`object → {n_title_pages, org_title, gip_proektirovshchik, gip_subcontractor(если 4), gip_author(=субподрядчик если есть, иначе проектировщик), section, work_type(группа/подГруппа), date, dop2_xlsx, dop2_matches_title(bool)}`
+ отчёт расхождений доп_2 vs титул (data-quality).

### ⚠️ Правило валидности (human 18-06): исключать SPLIT-проекты
**Оборина ОВ vs ГВС НЕ годится** — это практически ОДИН проект, разбитый на 2 части (иногда с ошибками: ТЭПы различаются). ОВ/ГВС одного адреса = половины одного проекта, НЕ независимые виды работ → сходство тривиально. Вид-работ-сравнения только между ГЕНУИННО РАЗНЫМИ работами/объектами; same-project splits (ОВ+ГВС одного адреса) исключать.
**Следствие:** в ВЫГРУЖЕННОМ корпусе (всё УУиР: Оборина-RSPK / Траскунова-NK = ОВ/ГВС-сплиты) валидной GIP-ячейки НЕТ → все схемы требуют НОВЫХ выгрузок. Немедленного пилота на exported нет.

### Приоритет ячеек (все требуют новых выгрузок)
1. **Кровельный факторный (Схема 1):** скатная/плоская × {Локтев/Сергеев/Ефимов/Бородин/Егупов/Савченков} — вид фикс, варьируем ГИП.
2. **Cross-org Сергеев:** плоская в 3 орг (Ватага/Гамма/Стройинвест), скатная/фасад в 2 — ГИП+вид фикс, варьируем орг.
3. **Схема 2:** ГИП с генуинно разными работами на РАЗНЫХ объектах (Локтев: скатная/плоская/ОВ/ЭС/фундамент) — НЕ split-половины.

### Затем (opus ревьюит)
GIP-control анализ: within/cross по ГИП × вид работ × орг, **на орг-авторском residual** (provenance-first HC-012), гипотезы H1 (within-worktype same-GIP>diff-GIP) / H2 (cross-worktype same-GIP>baseline). Декомпозиция ГИП=стиль vs контент-согласованность.

Open: пилот Оборина запускать до или параллельно с выгрузкой широких ячеек? (opus: пилот сразу — метод дешёв, данные есть.)

## [2026-06-18 20:58 MSK · codex]

`[ACCEPTED/REFINED]` Human clarified that the extraction target is not one
`gip` field but the complete set of project organizations and GIPs represented
on the title pages of each selected section.

Required pipeline:

```text
PDF section
  -> detect_title_zone(bundle, pdf_path)
  -> detected title_pages
  -> title-page organization sets
  -> all organization <-> GIP pairs
  -> role attribution
  -> effective author GIP
```

Interpretation:

- normally `2` title pages = one project organization and one GIP;
- normally `4` title pages = a lead/customer organization set plus a
  subcontractor set;
- subcontractor organization and GIP are usually on pages `3-4`;
- for a subcontracted section, `gip_author` is the subcontractor GIP (actual
  developer), while the customer/lead GIP is retained separately as the
  contractual/lead role;
- organization names must be extracted and reconciled together with surnames,
  not inferred from the XLSX registry alone.

Do not hardcode title count to only `2/4`: the canonical detector explicitly
supports practical/theoretical `1/2/3/4` and anomalies. Counts other than a
confident paired structure go to manual review.

Proposed normalized output:

```text
section_title_authorship
  object_id
  bundle_id
  crc32
  section_code
  title_detection
    title_pages
    title_page_count
    cover_end
    source
    anomaly
    confidence
  parties[]
    party_id
    title_page_numbers
    organization_name_raw
    organization_name_normalized
    gip_name_raw
    gip_surname_normalized
    role
      lead_designer
      subcontractor
      unresolved
    extraction_confidence
    evidence_refs
  effective_author
    organization_party_id
    gip_party_id
    selection_rule
      subcontractor_actual_author
      sole_designer_author
      unresolved
  registry_comparison
    xlsx_organization
    xlsx_dev_gip
    organization_matches
    gip_matches
```

Pairing rule:

- page position (`1-2` vs `3-4`) is a strong feature, not sole proof;
- role is confirmed using organization text, GIP signature proximity and
  consistency across the paired title pages;
- page 3/4 content such as `Справка ГИП`, TOC or body is not automatically a
  second title set: only pages returned by `detect_title_zone.title_pages`
  participate.

Canonical detector location currently available on this workstation:

`C:/Users/alexa/Cursor/Checker87 app/pp87-checker/tools_heading/title_zone.py`

The path in the original handoff (`pp87-checker/tools_heading/title_zone.py`)
is the logical shared-module reference; consumer code should receive the module
path through configuration rather than duplicate the detector.

## [2026-06-18 22:38 MSK · codex]

`[CHECKPOINT]` Priority corpus manifest built from the human-approved list.

Evidence:

- DocSpectrum commit: `50ec9d0`;
- objects: `E:/output/DocSpectrum/gip_priority_manifest_v0/gip_priority_objects_v0.csv`;
- memberships: `E:/output/DocSpectrum/gip_priority_manifest_v0/gip_priority_memberships_v0.csv`;
- unique objects `46`, experiment memberships `56`;
- all `46/46` found in XLSX candidate registry and `E:/MSE_арх`;
- target-section PDFs ready: `0`;
- archive-only: `43`;
- empty source directories: `1823_25`, `1825_25`;
- `1701_25` has only an unrelated `ИД` PDF;
- registry discrepancy retained for title resolution: `1690_25`
  (`СтройМонтаж` human cell vs `Витмар` XLSX).

Next data step: unpack the 43 archives, supply the two empty objects, run
explorer exports, then apply title detection and all-party organization/GIP
extraction incrementally.

## [2026-06-18 23:43 MSK · codex]

`[HUMAN-CLARIFICATION]` Organization/GIP identity uses one representative
complete PD section; it does not have to be the same section used for the
factor comparison.

Separate roles:

- `authorship_source_pdf`: select by priority `КР -> ПОКР/ПОС -> АР`;
- `analysis_target_pdf`: comparable experiment section (`КР` in the current
  roof/facade/balcony/foundation corpus).

Usually `КР` serves both roles. If `КР` is absent, use `ПОКР/ПОС`, then `АР`.
Within the selected section, retain all organization/GIP parties, including a
subcontractor represented by a four-title structure.

For objects with multiple expert-review versions (currently `1825_25`), version
choice is not material for identity extraction, but the factor corpus uses the
pre-expertise version to stay comparable with the other objects.

## [2026-06-19 00:04 MSK - codex]

`[CHECKPOINT]` Selection, explorer batch, title OCR and reconciliation are
complete for all 46 priority objects.

Evidence:

- DocSpectrum range: `50ec9d0..36f5a0e`;
- selection: 46 `KR` PDFs, 46 unique CRC32 values;
- explorer exports: 46/46 complete;
- title structure: 46/46 recovered, all have two title pages;
- detector routes: 22 text, 24 `image_title` recovered by a DocSpectrum
  Tesseract CLI adapter;
- organizations extracted: 46/46;
- GIP surnames extracted: 46/46;
- reconciliation: 35 exact, 2 OCR-near, 9 source conflicts;
- tests: 30/30 OK.

The shared Checker detector was not modified. The adapter only recovers text
for title zones already classified as `image_title`.

Nine source conflicts remain owner-visible rather than being silently
overwritten:

- seven planned Borodin objects have title GIP Loktev;
- `1692_25`: planned Shevchenko, title GIP Loktev;
- `1690_25`: planned StroyMontazh/Efimov, title Vitmar/Savchenkov.

RP-027 is open for the implementation range. RP-026 remains the separate
manifest-design review.

## [2026-06-19 · opus] HANDOFF: что сделано пока Codex был offline

`[HUMAN-OFFLINE-HANDOFF]` Пока ты спал по токенам, я (opus) на стороне человека прогнал ВЕСЬ конвейер по диапазонам **1400-1883** (токены Codex=0). Кратко для подхвата:

### Прогон (твоими же инструментами)
- Распаковка MSE_арх: мой `C:/Users/alexa/recursive_extract.py` (рекурс., идемпот., архивы не удаляет). Диапазоны 1400-1499/1500-1599/1600-1699/1700-1799 распакованы (1800-1883 ты сам).
- Конвейер по каждому диапазону: `build_title_authorship_range_v0 --start/--end` → `run_explorer_gip_batch_v0 --selection` → `extract_title_authorship_v0`. Артефакты: `title_authorship_range_<R>_results_v0/`.
- Сводка: `C:/Users/alexa/tmp_gip_consolidate.py` (по титул-ГИП, объединяет все диапазоны + первую группу вне).

### ⚠️ ДВЕ правки в твоих tools (НЕ закоммичены — на твоё ревью/принятие)
1. **`build_title_authorship_range_v0.py::select_version`** — при нескольких версиях возвращал `ambiguous_multiple_versions` (пропуск). Правило (human 19-06; твоя же заметка T-018 «version not material for identity»): для ГИП-ID версия не важна → беру одну детерминированно. Эффект: 1500-1599 selected 63→91; 1400-1499 96/100.
2. **`extract_title_authorship_v0.py::group_title_pages`** — дробил на пары только при len∈{2,4}; 3-стр.зона (1;2;3) не делилась → субподрядчик терялся. Правило (human 19-06): **первые 2 титула = заказчик/генподрядчик, ВСЁ остальное (стр.3+) = исполнитель (реальный автор, HC-013)**. Теперь при >2 стр.: `[первые-2] + [остальное]`. Эффект: **субподряд 2→25 объектов**.

### Состояние корпуса GIP (финал 1400-1883)
- **441 объект, 338 с титул-ГИП, 135 УУиР, 203 не-УУиР пригодных.** Виды сбалансированы (скатн51/плоск31/ЭС31/ВК20/фунд16/ОВ13/фасад18).
- **Робастный факторный план с репликацией:** ①cross-org/орг — Сергеев, Ширяев(фунд), Егупов(ЭС: Ватага+Спектр), Трошина, Роговский; ②Schema-1/ГИП — СтройМонтаж(Локтев/Ефимов/Рузаев), Ватага(Егупов+Сергеев), ИнфраСтрой(Трошина/Ширяев); ③Schema-2/вид-инвар. — Ширяев(7 видов)/Локтев/Сергеев/Ефимов/Егупов/…
- **25 субподряд-объектов** (Спектр/Егупов доминирует) — субстрат для теста «исполнитель=реальный автор».

### Находки (доп_2 ненадёжен → титул авторитетен)
- доп_2 заполнен 250/1833, путает ГЕНДИРА и ГИП (1667_25: Бородин=гендир, Локтев=ГИП), и даёт **ложные субподряд-метки** (1588/1591/1633 — реально 2-титульные, проверено человеком).
- **Остаётся: alias-реестр идентичности ОРГ** — OCR-варианты («ооо сп ст тройи»→Стройинвест, letterhead «129327 г.Москва»→Гончарова) дробят ось орг.

### Что от тебя (Codex)
1. Принять/уточнить 2 мои правки (выше).
2. **Alias-реестр идентичности орг** (канонизация перед фактор-экспериментом).
3. **Спроектировать GIP-control** (within/cross по ГИП×вид×орг на ОРГ-АВТОРСКОМ residual — provenance-first HC-012; H1 within-worktype same-GIP>diff-GIP, H2 cross-worktype same-GIP>baseline; декомпозиция ГИП=стиль vs контент). Я ревьюю.
- Корпус более чем достаточен; дальнейшие диапазоны — диминишинг.
