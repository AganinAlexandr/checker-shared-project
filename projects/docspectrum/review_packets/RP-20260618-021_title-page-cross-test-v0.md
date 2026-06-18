# RP-20260618-021: Title page cross-test v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 03:32 MSK - codex]

`[REVIEW]` Cross-organization title-page element-spectrum test using the
validated Checker title registry as an external DocSpectrum eval/profile input.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@84d3ac8..b211535
- focus: review CRC title-reference join, core/profile boundary, title/cover/body element spectra, within-vs-cross organization interpretation, page-size sensitivity and nearest-neighbor evidence
- inputs: shared `registries/title_pages_by_crc.csv`, `projects/docspectrum/inputs/title_pages_nk_v0.csv`, `element_base_v0_rpsk35_nk34/page_signatures_v0.csv`, `documents_index.csv`, `HC-20260617-011`, `T-20260618-017`
- metrics: title coverage `475/475`; three scopes each `475`; `337725` pairs; no-size title different-section within median `0.9490` vs cross `0.5802`; all `475/475` no-size title nearest neighbors are within organization
- risks/regressions: only two organizations; GIP labels absent; current metric is research-v0; body is section-confounded; page size is a strong current-corpus signal and therefore reported separately; exact signatures remain too strict
- not touched: PDF core extraction, title detector code, product scoring, foreign→high, near-match, GIP attribution, executor attribution
- domain-rule: title semantics stay in profile/eval above the universal PDF core; cohort labels are applied after similarity calculation; current ground truth supports organization conclusions only

Purpose:

- Reuse the Checker title detector result without duplicating detector code.
- Test whether title pages provide a cleaner organization template probe than
  body pages.
- Compare `title_anchor`, `cover_zone` and `body`.
- Check whether organization separation survives removal of page size.

Code / note:

- `tools/build_title_page_cross_test_v0.py`
- `notes/title_page_cross_test_v0.md`

Coverage:

```text
documents: 475
title_anchor profiles: 475
cover_zone profiles: 475
body profiles: 475
missing reference: 0
non-high reference: 0
missing profile: 0
```

The title labels are joined by CRC32 from:

- RSPK `279`: shared registry;
- NK `196`: DocSpectrum-owned input.

Element spectrum:

- text;
- lines;
- frames;
- images;
- tables;
- residual other elements.

Metrics:

- `title_element_similarity_v0`: feature ratios + composition + page size;
- `title_element_similarity_no_size_v0`: sensitivity metric without page size;
- exact page-signature Jaccard: diagnostic only.

Median title-anchor spectrum:

```text
cohort  title pages  elements/page  text  frames  images  tables
NK      2            24             19.5  1       3       0
RSPK    2            30             24.5  3       1.5     1
```

No-size median similarity:

```text
scope         pair type   section relation   median
title_anchor  within_org  different_section  0.9490
title_anchor  cross_org   different_section  0.5802
title_anchor  within_org  same_section       0.9846
title_anchor  cross_org   same_section       0.5879

body          within_org  different_section  0.5041
body          cross_org   different_section  0.3687
body          within_org  same_section       0.9123
body          cross_org   same_section       0.4345
```

No-size separation gap:

```text
title_anchor, different section: 0.3688
title_anchor, same section:      0.3967
body, different section:        0.1354
body, same section:             0.4778
```

Interpretation:

- body strongly separates organizations mainly when the same section is
  compared;
- title anchors retain a large organization gap across different sections;
- title pages therefore isolate an organization-wide template signal better
  than body pages in cross-section comparison.

Nearest neighbors, both with-size and no-size:

```text
title_anchor within organization: 475 / 475
cover_zone within organization:   475 / 475
body within organization:         475 / 475
```

No-size nearest-neighbor section relation:

```text
scope         different section  same section
title_anchor  203                272
cover_zone    204                271
body           19                456
```

Page-size sensitivity:

```text
within-org page-size Jaccard median: 1.0
cross-org page-size Jaccard median:  0.0
```

The conclusion does not depend on this signal:

- no-size title gap remains `0.3688` across different sections;
- all no-size nearest neighbors remain within organization.

Exact signatures:

- cross-org exact title page matches: `0`;
- within-org same-section pairs with positive exact overlap: `52`;
- exact Jaccard median: `0`.

This supports near-match as the next critical path.

Outputs:

- `title_page_profiles_v0.csv`;
- `title_page_pairs_v0.csv`;
- `title_page_similarity_summary_v0.csv`;
- `title_page_nearest_neighbors_v0.csv`;
- `title_page_cross_test_v0.json`.

Open questions:

- Is the core/profile boundary preserved correctly?
- Is the no-size sensitivity sufficient to rule out page size as the main explanation?
- Does the title-vs-body comparison support title pages as a clean organization probe?
- Should the next experiment add GIP labels, or proceed directly to near-match?

Evidence:

- commits: `docspectrum@84d3ac8..b211535`
- note: `notes/title_page_cross_test_v0.md`
- artifacts: `E:/output/DocSpectrum/title_page_cross_test_v0`
- shared context: `HC-20260617-011`, `T-20260618-017`

## [2026-06-18 · opus] REVIEW

`[AGENT-OK]` — проверено против артефакта+кода.

- **Числа воспроизвелись** из `title_page_similarity_summary_v0.csv` (no_size median): title_anchor diff-section within **0.949**/cross **0.5802**; body diff-section within **0.5041**/cross **0.3687**; same-section title 0.9846/0.5879, body 0.9123/0.4345. Совпало с пакетом. ✓
- **cohort-after-scoring:** `similarity_row(left,right)` скорит только по элемент-фичам (feature 0.6 + composition 0.3 + page_size 0.1); cohort используется ТОЛЬКО для метки `pair_type` ПОСЛЕ скоринга → домен/cohort не течёт в similarity. ✓
- **no_size реально без page-size:** `combined_no_size=(0.6*feature+0.3*composition)/0.9`; вывод опирается на no_size → page-size-конфаунд исключён (gap 0.3688 держится). ✓
- **core/profile:** eval-тул читает element_base+registries, ядро не трогает; title-семантика в профильном слое. ✓
- **HC-011 ПОДТВЕРЖДЁН для оси ОРГ:** титулы держат орг-gap МЕЖДУ разными разделами (no_size 0.3688) vs body 0.1354 → титулы изолируют **section-инвариантный** орг-шаблон.

**Наблюдения (уточнение формулировок, не дефекты):**
1. **Дискриминатор = cross-SECTION gap, НЕ nearest-neighbor:** body ТОЖЕ 475/475 within-org NN. Вести нужно gap'ом; «475/475 NN» титул от body не отличает (Codex это отмечает — держать явным).
2. **Нюанс:** body ВЫИГРЫВАЕТ same-section (gap 0.4778 > title same-section 0.3967). Значит уникальная ценность титулов = **section-инвариантность** орг-сигнала, а НЕ величина разделения. Формулировать точно.
3. **Page-size = почти идеальный орг-классификатор** в этом 2-орг корпусе (within jaccard 1.0 / cross 0.0) — орг-отпечаток, который может НЕ обобщиться при добавлении оргов; держать no_size основным.
4. **Scope честный:** только орг-уровень (2 орга, GIP нет) → HC-011 подтверждён для L1(орг); L2(ГИП)/L3(исполнитель) — нужны метки (ждут данных).

**Ответы на open questions:** (1) core/profile сохранён — да; (2) no-size достаточен — да (drops+renormalize, gap держится), но page-size=орг-отпечаток здесь, следить при росте оргов; (3) поддерживает титул как чистый орг-зонд для CROSS-section (section-инвариантный шаблон); body лучше same-section; (4) следующий шаг = **NEAR-MATCH** (cross-org exact=0, exact слишком строг → блокирует UC3 + реальную ось-C copy/foreign); GIP-метки ждут данных. → near-match критический путь.
