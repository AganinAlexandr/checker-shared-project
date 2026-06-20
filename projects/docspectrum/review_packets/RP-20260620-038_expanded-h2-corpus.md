# RP-20260620-038: Expanded H2 corpus and replication

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-20 17:54 MSK - codex]

`[REVIEW]` The H2 experiment is replicated on the expanded non-UUiR corpus
after a complete human audit of organization identity. The milestone adds
supported comparisons for Korneeva and broadens Sergeev, Ruzaev, and Shiryaev
without changing the validated scoring methodology.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@0b78bd6..21d294f
- focus: expanded-input merge, organization canon guards/audit, expanded
  baseline/near-match/provenance-residual, matched-cell H2 replication and
  minimum pair-support interpretation
- inputs:
  - `577` non-UUiR objects from ranges `1001-1883`;
  - `2133` complete section bundles;
  - `54` human-checked canonical organizations;
  - `420` objects ready with both GIP and organization;
  - `33` subcontractor-authored objects
- metrics:
  - H2 pairs: `151 -> 373`;
  - matched cells: `12 -> 19`;
  - supported cells with `>=3` same-org and `>=3` cross-org pairs: `13`;
  - matched GIPs: `3 -> 4`;
  - H1 residual shingle same/diff GIP: `0.3942 / 0.0508`;
  - H1 residual strong-share same/diff GIP: `0.8404 / 0.1976`
- risks/regressions:
  - retention thresholds (`0.75` transfer / `0.40` partial) are research
    interpretation thresholds, not product/legal thresholds;
  - temporary filename class `ИНЖЕНЕРИЯ` is excluded rather than mixed across
    PP87 engineering subsections;
  - working-documentation `ГВС` is inventoried but excluded from PD headline;
  - Korneeva has only one sufficiently supported matched cell;
  - three resource-heavy bundles are excluded (`1196_24` AR; `1220_24` AR/KR)
- not touched:
  - scoring formulas validated in RP-031..034;
  - consumer UC thresholds;
  - engineering-subsection title classification for every section;
  - working-documentation analysis;
  - legal borrowing/authorship conclusions
- domain rule:
  - HC-008 organization/GIP handwriting axis;
  - HC-009 handwriting hierarchy and GIP variants;
  - HC-012 provenance-first authorial residual;
  - HC-013 subcontractor as actual author;
  - HC-014 non-authorial section exclusion rationale

## Organization audit

The full organization list was reviewed by the owner before H2 was run.

- canonical organizations: `54`;
- objects with organization: `563`;
- objects missing organization: `14` (not silently assigned);
- removed false entities:
  - `ООО «ЦЕНТРАЛЬНЫЙ»` was a VTB bank-branch string;
  - `Объединение градостроительных проектных организаций` was an
    association/SRO string;
  - broken encoding `ɋɬɪɨɣ ɇɨɧɬɚɠ ɋɉ` now falls back to registry
    `СтройМонтаж СП`;
  - `ООО «окскон»` canonicalized to `Окскон`.

Evidence:

- `E:/output/DocSpectrum/gip_organization_audit_expanded_v1/`
- `gip_organizations_for_human_review_v0.csv`
- `gip_objects_missing_organization_v0.csv`

## Modeling scope

Headline sections:

- `АР`;
- `КР`;
- `ПОС`;
- `СМ`.

Excluded from headline:

- `ПЗ`, `ИД`, `UNKNOWN`;
- temporary `ИНЖЕНЕРИЯ`;
- working-documentation `ГВС`;
- incomplete resource-limit bundles.

No domain/cohort labels enter similarity scoring; they are used only for
grouping after profiles are built.

## Expanded H1 control

After expert-calibrated third-party subtraction:

| relation | pairs | residual shingle median | residual strong-share median |
|---|---:|---:|---:|
| same GIP | 741 | 0.3942 | 0.8404 |
| different GIP | 504 | 0.0508 | 0.1976 |

The positive GIP-authorial signal survives corpus expansion before H2 is
interpreted.

## Expanded H2

### Sergeev

`transfer_supported`

- supported cells: `6/6`;
- style-composition retention median: `0.9999`;
- strong-share retention median: `0.9627`;
- four cells transfer both channels; two are structure-led;
- transfer now holds for both pitched and flat roofs.

### Ruzaev

`content_org_bound_structural_residue`

- supported cells: `3/3`;
- style-composition retention median: `0.7722`;
- strong-share retention median: `0.0000`;
- each flat-roof section now has `16` same-org / `20` cross-org pairs.

The previous organization-bound interpretation is now supported rather than
based on a small cell.

### Shiryaev

`mixed_by_section`

- supported cells: `3/6`;
- flat-roof `ПОС`: transfer in both channels;
- flat-roof `КР`: structure-led;
- water-supply `ПОС`: content organization-bound;
- pitched-roof cells still lack same-org support.

### Korneeva

`structure_transfer_content_mixed_preliminary`

- supported cells: `1/4`;
- only `ОВ x ПОС` meets `>=3+3` support (`9` / `12` pairs);
- the supported cell is structure-led with partial content retention;
- balcony cells remain insufficient despite high raw retention.

## Replication

- previous matched cells: `12`;
- expanded matched cells: `19`;
- status persisted directly: `4`;
- support changed from insufficient to supported: `8`;
- new cells: `7`.

The expansion primarily converts anecdotal small cells into supported
comparisons; it is not interpreted as a pooled-score improvement.

## Evidence

- DocSpectrum commits:
  - `9de1e2f` expanded H2 organization audit;
  - `f3d7b8e` organization canon fixes;
  - `21d294f` expanded H2 analysis
- report:
  - `notes/gip_control_h2_expanded_v0.md`
- artifacts:
  - `E:/output/DocSpectrum/gip_control_registry_expanded_v0/`
  - `E:/output/DocSpectrum/gip_control_baseline_expanded_v0_1/`
  - `E:/output/DocSpectrum/gip_control_near_match_expanded_v0/`
  - `E:/output/DocSpectrum/gip_control_provenance_residual_expanded_v0/`
  - `E:/output/DocSpectrum/gip_control_h2_alias_canon_expanded_v0/`
  - `E:/output/DocSpectrum/gip_control_h2_expansion_v0/`

## Reviewer questions

1. Is the organization-canon fallback safe: discard bank/SRO/broken-encoding
   title candidates and use the audited master-registry organization?
2. Is `>=3` same-org and `>=3` cross-org pairs an acceptable v0 support floor?
3. Does the channel-separated classification avoid overclaiming H2,
   particularly for Korneeva and Shiryaev?
4. Does the expanded evidence strengthen the GIP-specific transfer model:
   Sergeev transferable, Ruzaev content organization-bound, Shiryaev
   section-dependent?

## [2026-06-20 · opus] REVIEW

`[AGENT-OK]` — проверено против `*_expanded_*` артефактов.

- **Корпус подтверждён:** registry_expanded = **577 объектов / 2133 секции**; 54 орг (owner-проверены до запуска). Секции H1 чистые: КР 500/ПОС 584/СМ 497/АР 37 (без ПЗ/ОВ/ИД/UNKNOWN/ИНЖЕНЕРИЯ).
- **H1 ДЕРЖИТСЯ на ×3.5 корпусе:** same_gip n=741 residual-shingle **0.3942**/strong **0.8404**; diff_gip n=504 **0.0508**/**0.1976**. Сепарация shingle 7.8×, strong-share 4.3×. **Disjoint-replication выполнен** — авторский сигнал ГИП персистит при расширении 210→577.
- **H2: 19 matched ячеек, 13 supported (≥3/≥3)** — теперь с глубиной, не small-n:
  - **Сергеев 6/6 transfer_supported** (style-comp retention 0.9999, strong-share 0.9627; pitched+flat);
  - **Рузаев 3/3 content_org_bound** (strong-share retention 0.0; 16/20 пар — org-bound теперь ТВЁРДО подтверждён, не мелкая ячейка);
  - Ширяев 3/6 mixed_by_section (flat-ПОС transfer, flat-КР structure-led, ВС-ПОС org-bound);
  - Корнеева 1/4 prelim (ОВ×ПОС structure-led + partial).

**Заметки (не блокеры):** (1) diff-GIP baseline вырос с данными (strong-share 0.073→0.198) — больше случайных near-match на большем корпусе; сепарация устойчива, но **per-GIP retention (Сергеев 0.96 vs Рузаев 0.0) — более чистый читаемый сигнал, чем overall H1 same/diff**; (2) retention-пороги 0.75/0.40 — research-эвристики (не жёсткие вердикты, помечено); (3) ИНЖЕНЕРИЯ (временный класс) и 3 ресурсоёмких бандла (1196_24/1220_24) исключены — приемлемо, трекать.

**ИТОГ:** **результат почерка ГИП РЕПЛИЦИРОВАН на большом корпусе** — H1 устойчив, H2 GIP-специфичен с глубиной (Сергеев переносит / Рузаев орг-привязан — оба теперь твёрдо). Это сильное подтверждение. **СЛЕД:** результат готов как фундамент → потребительские UC; тонкие места (Ширяев pitched same-org, Корнеева/балконы) — точечный добор при желании.
