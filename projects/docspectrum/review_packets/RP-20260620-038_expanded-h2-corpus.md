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
