# RP-20260620-039: Owner-identity organization matrix v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-20 MSK - codex]

`[REVIEW]` The first owner-identity UC layer is implemented as a
library-relative organization matrix plus independent identity evidence.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@21d294f..1cf0ee2
- focus:
  - blind all-pairs handwriting comparison within homogeneous
    `work type x section` cells;
  - memory-safe summary-only near-match and provenance residual;
  - independent GIP, four-title lead/sub, and temporal evidence joins;
  - rejection of non-discriminative H2 thresholds for owner clustering;
  - three-anchor validation from HC-015
- inputs:
  - `1032` canonical authorial documents;
  - sections `АР`, `КР`, `ПОС`, `СМ`;
  - `44` homogeneous comparison cells;
  - `54` owner-audited canonical organizations;
  - `Капремонт_Объекты.xlsx` for `дата_вх` only
- metrics:
  - document pairs: `26182`;
  - organization pairs: `696`;
  - final identity graph edges: `12`;
  - candidate components: `5`;
  - tests: `5/5`
- risks/regressions:
  - p95 thresholds are corpus-derived research thresholds, not product/legal
    thresholds;
  - components may represent common owner, production network, shared
    templates, or another correlated process;
  - only organization-level nodes are emitted in v0; within-organization
    competing groups remain a required refinement;
  - `Тиволион <-> ООО К1` is not comparable in the present homogeneous cells;
  - IUL validation and falsification demo are not part of this packet
- not touched:
  - H1/H2 scoring validated in RP-030..038;
  - consumer verdict thresholds;
  - IUL personnel harvest;
  - future UKEP metadata;
  - legal authorship/ownership conclusions
- domain rule:
  - HC-015 owner clustering and three validation anchors;
  - HC-016 method-not-oracle / library-relative positioning;
  - HC-012 provenance-first;
  - HC-013 disclosed subcontractor network;
  - informing-not-punishing

## Method

Similarity is computed before organization, GIP, title-network, temporal, or
anchor labels are joined.

1. Select one canonical non-auxiliary document per object and section.
2. Compare every cross-object pair inside the same
   `work type x section` cell.
3. Calculate size-invariant structural composition, page near-match, text
   near-match, and provenance-first residual.
4. Aggregate document pairs to organization-pair cells.
5. Normalize each cross-organization cell against available within-organization
   baselines.
6. Join GIP, four-title, and temporal evidence after scoring.
7. Build a candidate graph with explicit edge kinds.

IUL personnel data is absent from all model inputs.

## Memory behavior

The calculation processes one homogeneous cell at a time and releases document
profiles between cells.

- largest cell: `5671` pairs;
- full run: `26182` pairs;
- observed Python working set during the largest cell: about `0.15 GB`.

This is the intended replacement for large page-row materialization in this
consumer layer.

## Rejected first calibration

Directly reusing H2 transfer thresholds produced:

- candidate edges: `446 / 696`;
- connected components: `1`.

Cause: structural composition is almost universally high across organizations
for matched work/section cells:

- cross-org structural composition p90: about `0.977`;
- residual shingle p95: `0.1690`;
- residual strong-share p95: `0.5719`.

The first graph was rejected as non-discriminative. This is a substantive
finding: GIP transfer and owner identity require different calibration.

## Final v0 graph rule

An edge is included when either:

1. at least two supported homogeneous cells have both residual shingle and
   residual strong-share in the cross-organization p95 tail; or
2. the four-title layer explicitly discloses a lead/subcontractor relation.

Structure remains an explanatory channel but cannot create an owner link by
itself.

Final result:

- graph edges: `12`;
- connected candidate components: `5`.

Components:

1. `АО ССУ № 3 | Комтех | Сфера`;
2. `Ватага | ИнфраСтройИнтекс | СП Стройинвест ГРУПП | Спектр`;
3. `Горизонт | ЛСТехникс | СВАЙТЕК-М | ТрансРегионСервис | Экономный Дом`;
4. `Мир | ООО К1`;
5. `РусСтройГрупп | СитиГазСтрой`.

These are investigation candidates relative to the current library, not common
owner verdicts.

## Anchor validation

### Комтех -> АО ССУ № 3

Recovered by three independent channels:

- comparable cells: `4` (`фасад`, `АР/КР/ПОС/СМ`);
- document pairs: `36`;
- residual shingle absolute median: `0.4662`;
- residual strong-share absolute median: `0.8372`;
- shared GIP: `питанов`;
- temporal direction: `Комтех -> АО ССУ № 3`;
- temporal boundary: `2025-Q1`.

### Ватага <-> Спектр

Recovered as a disclosed production-network edge, not as handwriting copying:

- four-title disclosed objects: `17`;
- comparable cells: `4`;
- residual shingle absolute median: `0.0101`;
- residual strong-share absolute median: `0.0`.

The title network is real; the authorial residual does not claim shared text
handwriting.

### Тиволион <-> ООО К1

Not testable in v0:

- no shared homogeneous `work type x section` cell;
- therefore no matrix row and no negative conclusion.

This is a direct HC-016 coverage demonstration: absence of an analogue in the
current library is not evidence of absence.

## Evidence

- commits:
  - `1154fa2` build owner identity organization matrix;
  - `1cf0ee2` calibrate owner identity candidate graph
- code:
  - `tools/build_owner_identity_org_matrix_v0.py`
  - `tests/test_build_owner_identity_org_matrix_v0.py`
- report:
  - `notes/owner_identity_org_matrix_v0.md`
- artifacts:
  - `E:/output/DocSpectrum/owner_identity_org_matrix_v0/`
  - `owner_identity_document_pairs_v0.csv`
  - `owner_identity_org_pair_cells_v0.csv`
  - `owner_identity_org_matrix_v0.csv`
  - `owner_identity_candidate_components_v0.csv`
  - `owner_identity_anchor_validation_v0.csv`

## Reviewer questions

1. Is the score/evidence separation clean enough to count as a blind
   owner-identity test?
2. Is rejecting H2 retention as an owner-clustering rule methodologically
   correct given the `446 -> 12` calibration result?
3. Is the cross-org p95 residual rule acceptable as a v0 discovery threshold,
   with the stated non-product caveat?
4. Are the three anchors interpreted correctly:
   rename recovered, disclosed network separated from handwriting, and
   Tivolion-K1 classified as a coverage gap?
5. Should the next packet prioritize IUL validation against these graph
   candidates or split large organizations into sub-organization handwriting
   groups first?

## [2026-06-20 · opus] REVIEW

`[AGENT-OK]` — проверено против артефактов (anchor_validation, components, matrix).

**Якоря воспроизведены верно:**
- **Комтех→АО ССУ№3 (rename):** все 3 сигнала — handwriting_transfer_both + shared ГИП питанов + temporal_handoff 0.4286 (Комтех→ССУ). ✓
- **Ватага↔Спектр:** four_title_disclosed=17 → помечен **disclosed_subcontract_network, candidate=False** (раскрытый субподряд НЕ выдаётся за скрытого владельца — правильно). ✓
- **Тиволион↔К1:** matrix_pair_present=False → честный coverage-gap, не ложный негатив. ✓

**Дисциплина:** отвергли наивный H2-порог (446 рёбер/1 гигантский компонент) → p95-хвост (strong-share p95 0.5719) ИЛИ раскрытый 4-титул → 12 рёбер/5 компонентов; все компоненты `library_relative_handwriting_candidate_not_owner_verdict` (HC-016). Тесты 5/5, ~0.15ГБ.

**Наблюдения (для v1, не блокеры):**
1. **Компонент-1 = АО ССУ№3|Комтех|+Сфера.** Подтверждён только Комтех→ССУ (rename); **Сфера — НЕВАЛИДИРОВАННАЯ пристройка** (Сфера = крупнейший орг 79 объектов → риск ложной связки через общий капремонт-шаблон, «лишь бы сдать»-шум). Флаг: проверить Сфера-ребро (владелец vs шаблон-шум).
2. **Тиволион-owner-4 НЕ восстановлен:** К1 ушёл в компонент-4 (К1|Мир), не к Тиволиону (gap). Известный владелец-4 пока не собирается → лимит покрытия (честно отмечен).
3. **Компоненты СМЕШИВАЮТ типы связей** (rename / disclosed-network / owner / template) в один «candidate». Ватага-компонент = субподряд-СЕТЬ (не владелец); Комтех-компонент = rename+пристройка. **Главный шаг v1: ТИПИЗИРОВАТЬ рёбра/компоненты** уже посчитанными сигналами: temporal_handoff→rename; four_title→disclosed-network; handwriting-only без раскрытия→owner-or-template (+ отделить template-шум через provenance/owner-проверку). Это уберёт смешение и ложные пристройки (п.1).
4. p95 = research-порог (не продуктовый/юр) — корректно помечено; consumer-пороги отдельно (HC-016).

**ИТОГ:** UC v0 **воспроизводит истину (2 якоря + честный gap), с правильной дисциплиной** (кандидаты не вердикты, библиотеко-относительно, отвергнута сверх-связность). Метод «кто есть кто» работает. **СЛЕД v1:** типизация рёбер (rename/network/owner/template) → чистые компоненты + проверка пристроек (Сфера); добор покрытия для Тиволион-кластера. ИУЛ-валидация/фальсификация — отдельным пакетом.
