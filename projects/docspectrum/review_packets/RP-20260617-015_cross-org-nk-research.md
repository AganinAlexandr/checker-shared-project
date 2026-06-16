# RP-20260617-015: Cross-org NK research and typical-element prework

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-17 01:20 MSK - codex]

`[REVIEW]` Research checkpoint for the NK-Engineering cross-organization dataset and preparation for the typical-element library design.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@bd63045..6a365d2
- focus: review methodology and interpretation of the cross-org/NK research layer: CRC linkage, RSPK35+NK34 comparison, nearest-neighbor diagnostics, pair explanation/localization, page-level checks, NK internal clustering, and whether this is enough to start a `typical_element_candidates_v0` library
- inputs: `E:/output/DocSpectrum/export` (RSPK35), `E:/output/DocSpectrum/export_nk_34` (raw NK doc_crc32 export), `E:/output/DocSpectrum/export_nk_34_object_view`, `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`, `E:/commons/DocSpectrum/Капремонт_Объекты.xlsx`, `E:/output/DocSpectrum/cross_org_manifest_v0`, `E:/output/DocSpectrum/crc_export_linkage_nk_34`
- generated artifacts: NK-only element/corpus/pairwise outputs, combined RSPK35+NK34 outputs, cross-org summaries, nearest-neighbor tables, pair-explanation tables, page-level findings, NK section clusters
- risks/regressions: interpretation risk around clusters vs human authorship; exact page signatures remain too strict; graphics/vector groups are not yet extracted; text/table-cell bridges can still be boilerplate; combined v0.3 full JSON generation is heavy at 13.5k pairs
- not touched: core scoring weights, v0.3 metric definition, Axis A/B validated RSPK artifacts, embeddings, near-match page bucketing, graphics/vector extraction, final typical-element schema
- domain-rule: HC-008 Axis C applies; cohort labels are post-scoring research metadata and do not enter core similarity; `УУиР` and `Узел учета` remain separate repair types; NK interpretation corrected: 32 objects are `ОВ+ГВС`, 2 are `ОВ`-only, so different `ОВ/ГВС` packaging is not a main cluster explanation

Purpose:

- Use the new NK-Engineering UUiR dataset as the first cross-organization contrast against RSPK.
- Check whether Axis C (organization/project handwriting) is visible.
- Check whether cross-org “bridges” are whole-section similarity, text-only, table-layout, table-content, page-level, or something else.
- Check whether NK itself is internally uniform or splits into clusters that may reflect object series, inherited templates, or possible section-level performers.
- Prepare the next design discussion: how to start a library of typical elements without prematurely overclaiming.

Code / notes added in DocSpectrum:

- `tools/build_cross_org_manifest_v0.py`
- `tools/build_crc_export_linkage_v0.py`
- `tools/materialize_export_view_v0.py`
- `tools/summarize_org_similarity_v0.py`
- `tools/find_cross_org_neighbors_v0.py`
- `tools/explain_pair_shared_entities_v0.py`
- `tools/cluster_section_similarity_v0.py`
- `notes/cross_org_manifest_v0.md`
- `notes/crc_export_linkage_v0.md`
- `notes/cross_org_research_v0.md`
- `notes/pair_explanations_v0.md`
- `notes/nk_section_clusters_v0.md`

CRC linkage result:

- NK source folders found: `34/34`
- source PDFs scanned: `789`
- source unique CRC32: `691`
- explorer export docs: `196`
- matched export docs: `196/196`
- matched objects: `34/34`
- export-only docs: `0`
- `doc_<crc32>` folder mismatches: `0`

NK corpus snapshot:

- NK documents/bundles: `196`
- NK objects: `34`
- NK section counts:
  - `ИОС5.1`: `33`
  - `АР`: `32`
  - `КР`: `32`
  - `ИОС5.4.1`: `32`
  - `ПОКР`: `27`
  - `СМ`: `27`
  - `ПОС`: `6`
  - `ИОС5.5.1`: `6`
  - `UNKNOWN`: `1`
- The single `UNKNOWN` is kept diagnostic, not forced into a section.

Combined RSPK35+NK34 snapshot:

- documents/bundles: `475`
- objects: `69`
- pairwise rows: `13534`
- pair type counts:
  - `within_rspk`: `4450`
  - `within_nk`: `2748`
  - `cross_nk_rspk`: `6336`

Cross-org separation:

| Section | Within RSPK median | Within NK median | Cross median | Separation gap |
|---|---:|---:|---:|---:|
| `ПОКР` | 0.6437 | 0.7231 | 0.0114 | 0.6720 |
| `АР` | 0.6441 | 0.6118 | 0.0549 | 0.5731 |
| `ИОС5.5.1` | 0.5732 | 0.6125 | 0.0469 | 0.5460 |
| `КР` | 0.6930 | 0.4345 | 0.0650 | 0.4987 |
| `ИОС5.1` | 0.6004 | 0.5633 | 0.1023 | 0.4795 |
| `ПОС` | 0.5788 | 0.2997 | 0.0294 | 0.4099 |
| `СМ` | 0.2381 | 0.5460 | 0.0587 | 0.3334 |
| `ИОС5.4.1` | 0.3567 | 0.2610 | 0.0381 | 0.2707 |

Interpretation:

- Axis C is strongly visible on this contrast: cross-organization similarity is much lower than within-organization similarity across all comparable sections.
- This is one contrast pair (RSPK vs NK), so it is a strong research signal, not final cross-org validation.

Nearest-neighbor result:

- No cross-org pair reaches within-organization similarity levels.
- Cross maxima by section:
  - `СМ`: `0.1515`
  - `ИОС5.4.1`: `0.1196`
  - `ИОС5.1`: `0.1157`
  - other sections lower.
- Most top cross neighbors are text/table-cell bridges only; structural axes are mostly zero.

Pair-explanation result:

- Top cross pairs were decomposed into shared entity hashes and localized to pages, text segments, table cells, table layouts, table content signatures and page signatures.
- Raw text is not written; explanations remain hash-only.
- Most top cross pairs are `text_only` bridges.
- `ИОС5.4.1` is the meaningful exception:
  - shared text/table-cell hashes;
  - `3` shared `table_layout_signature` hashes;
  - `0` shared `table_content_signature` hashes;
  - `1` page signature, but it is a typical image-only page and should stay diagnostic.
- The repeated `ИОС5.4.1` table layouts are localized around:
  - RSPK pages `18-26`;
  - NK pages `21-32`;
  - layout form `8x4`, `22 cells`;
  - frequency bucket `shared_rare`.

Interpretation:

- The `ИОС5.4.1` bridge is not whole-section copying.
- It is also not merely boilerplate text.
- It is the first concrete candidate for a “typical group of elements”: repeated technical table form plus recurring text/table-cell hashes, but changed full table content.

Page-level check:

- Exact cross-org `page_signature` matches: `1`.
- The only exact shared page signature is trivial: image-only page (`elements=1`, `text=0`, `tables=0`, `images=1`).
- No exact or coarse cross-org title-page structural keys for page `1` or page `2`.
- Within-cohort title structures are stable but different:
  - RSPK title pages are dominated by frames + tables;
  - NK title pages are dominated by frames/images without tables.

Interpretation:

- Title pages support organization/package style separation.
- Exact page signature is too strict for near-same diagrams; page-bucketing or visual/vector signatures are still needed.

NK internal clustering:

- Same sections inside NK are similar, but NK is not one uniform mass.
- Cluster thresholds used: `0.75`, `0.60`, `0.45`.
- `ИОС5.4.1` is the most fragmented mass section:
  - at `0.75`, only small tight components;
  - at `0.60`, six non-singleton components remain.
- `АР`, `КР`, `ИОС5.1`, `ПОКР` have clear high-similarity families.
- `СМ` has one large medium-threshold family but almost no very-high threshold clusters.
- `ПОКР` is mostly homogeneous, but `1140_24` is an outlier and has contractor metadata `Эргана`.
- NK cluster interpretation was corrected after human feedback: since 32 objects are `ОВ+ГВС` and 2 are `ОВ`-only, different `ОВ/ГВС` packaging should not be used as a main cluster explanation.

Interpretation:

- NK clusters may reflect object/geography series, inherited templates, internal preparation packages, or possible section-specific authors.
- They do not prove different project engineers without external author labels.
- The next discriminating test should be cluster co-occurrence across sections: if object groups recur in many sections, it is likely object-series/package; if only one section splits, it is a stronger section-specific performer/template signal.

Generated artifact paths:

- `E:/output/DocSpectrum/cross_org_manifest_v0`
- `E:/output/DocSpectrum/crc_export_linkage_nk_34`
- `E:/output/DocSpectrum/export_nk_34_object_view`
- `E:/output/DocSpectrum/export_rpsk35_nk34_object_view`
- `E:/output/DocSpectrum/element_base_v0_nk_34`
- `E:/output/DocSpectrum/corpus_frequency_v0_nk_34`
- `E:/output/DocSpectrum/comparison_results_v0_3_nk_34`
- `E:/output/DocSpectrum/element_base_v0_rpsk35_nk34`
- `E:/output/DocSpectrum/corpus_frequency_v0_rpsk35_nk34`
- `E:/output/DocSpectrum/comparison_results_v0_3_rpsk35_nk34`
- `E:/output/DocSpectrum/cross_org_research_v0`
- `E:/output/DocSpectrum/pair_explanations_cross_top3_v0`
- `E:/output/DocSpectrum/nk_section_clusters_v0`

Verification:

- `python -m py_compile` on all added tools.
- Regenerated cross-org summaries with `tools/summarize_org_similarity_v0.py`.
- Generated nearest-neighbor diagnostics with `tools/find_cross_org_neighbors_v0.py`.
- Generated pair explanations with `tools/explain_pair_shared_entities_v0.py`.
- Generated NK section clusters with `tools/cluster_section_similarity_v0.py`.
- DocSpectrum current commit: `6a365d2 Clarify NK cluster interpretation`

Known limitations / reviewer attention:

- The research layer is not yet a formal typical-element library.
- Text and table-cell bridges may be boilerplate or normative text.
- `page_signature` exact matching is too strict; near-match/bucketing remains open.
- Graphics/vector/image groups are not yet extracted as independent entities.
- NK cluster labels are unsupervised and should not be interpreted as human authors without external author/performer labels.
- Combined v0.3 full pair JSON generation is too heavy for much larger corpora; summary-only mode is needed before scaling.

Open questions for reviewer:

- Is the interpretation of Axis C separation from this RSPK-vs-NK contrast methodologically sound?
- Is the `ИОС5.4.1` table-layout bridge a valid first candidate for a “typical group of elements,” or should it stay only as a diagnostic example until near-match/table-content rules improve?
- Should `typical_element_candidates_v0` start from table-centric candidates first (`table_layout_signature + table_cell_text + locations`) before text-only/page candidates?
- Should the next tool be cluster co-occurrence across sections, or should we first formalize candidate-element schema?
- What minimum evidence should promote a hash/entity group from “shared entity” to “typical element candidate”?

Evidence:

- commits: `docspectrum@bd63045..6a365d2`
- notes: `notes/cross_org_manifest_v0.md`, `notes/crc_export_linkage_v0.md`, `notes/cross_org_research_v0.md`, `notes/pair_explanations_v0.md`, `notes/nk_section_clusters_v0.md`
- shared context: `HC-20260615-008`, `RP-20260616-014`

## [2026-06-17 14:30 MSK · opus via human]

`[AGENT-OK]` Вердикт: сильный research-чекпойнт, методика корректна, ось C подтверждена на контрасте РСПК↔НК. Блок-проблем нет.

Подтверждаю: CRC-linkage 196/196 (надёжная привязка); cohort-метки ПОСЛЕ скоринга (домен не в ядре ✓); ось C разделена (gap 0.27–0.67); nearest-neighbor — ни одна cross-пара не дотягивает до within; корректировка ОВ+ГВС принята.

**Уточнение ground-truth (по `Капремонт_Объекты.xlsx` / pivot ГИП_по):** РСПК=Оборина, НК=Траскунова — это РАЗНЫЕ девелоперские ГИПы (с титулов разделов); экспертный ГИП (Аганин) — бланкетный оверсайт, в документах почерка не оставляет, для оси C нерелевантен. → разделение РСПК/НК ожидаемо (разные орги + разные dev-ГИПы) = чистый негативный контроль.

**Лучшая находка — мост ИОС5.4.1:** 3 общих `table_layout_signature` (8×4/22 ячейки, РСПК стр.18-26 / НК стр.21-32, shared_rare), при РАЗНОМ содержимом. Валидный первый кандидат «типовой группы элементов» — рамить как типовую ФОРМУ (шаблон), не заимствование.

**Критический пункт:** разделение оси C «лёгкое» отчасти потому, что точные сигнатуры между оргами не совпадают НИКОГДА по построению. → near-match теперь на КРИТИЧЕСКОМ пути продуктовой цели (кросс-орг заимствование/ГИП-почерк = near-identical, точными не ловится), не просто scoring.

Каветы: NK неровное покрытие (ИОС5.5.1/ПОС=6); перф 13.5к пар → нужен summary-only режим; кластеры ≠ авторы без меток; графика/вектор не извлечены.

Ответы на open-вопросы:
- ось C методически здорова (один контраст, сильный сигнал, не финал — нужны позитивные same-ГИП орг-пары);
- ИОС5.4.1 — валидный первый typical-element кандидат (как ФОРМА); промоутить через near-match + повторяемость по объектам;
- table-centric первым — да (`table_layout + table_cell_text + локации`, наименее boilerplate);
- критерий промоушена shared-entity → typical-element: (1) повторяемость по ≥N разным объектам (DF); (2) структурная связность (со-локализованная группа, не одиночный шингл); (3) форма стабильна / содержимое варьируется (шаблон, не копия); (4) переживает near-match;
- typical-element library = дискриминатор «поделился шаблонами vs реально сделал vs только подписал» (см. HC-009).

Evidence: `docspectrum@bd63045..6a365d2`; verified `cross_org_research_v0/org_separation_v0.csv`, pivot ГИП_по (РСПК=Оборина/НК=Траскунова).
