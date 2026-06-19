# RP-20260619-035: GIP-control H2 alias canon and matched cells

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 22:40 MSK - codex]

`[REVIEW]` This packet cleans the organization axis before interpreting H2 and
replaces the confounded all-pairs headline with an equal-weight matched-cell
analysis.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@ac14432..6f28a6a
- focus:
  - organization identity resolution through the existing alias canon;
  - preservation of the human-confirmed distinction between
    `ООО Стройразвитие` and `Стройразвитие М`;
  - H2 capacity after removing display/OCR alias artifacts;
  - matched-cell H2 interpretation by GIP;
  - direct pair-level residual headlines (minor from RP-034).
- inputs:
  - `E:/output/DocSpectrum/org_alias_registry_v0/*`
  - `E:/output/DocSpectrum/gip_control_registry_v0/*`
  - `E:/output/DocSpectrum/gip_control_provenance_residual_v0/*`
- commits:
  - `9e88d68` canonicalize GIP organization identity
  - `6f28a6a` add matched-cell H2 analysis

## Organization-axis correction

Before this change, title strings such as:

- `Ватага` / `ООО «Ватага»`;
- `ИнфраСтройИнтекс` / `ООО «ИнфраСтройИнтекс»`;
- `РусСтройГрупп` / its full legal title;
- three OCR/display variants of `СП Стройинвест ГРУПП`

could still reach the GIP registry as different organizations. The registry now
uses the same legal-form-independent identity helper as the alias builder, while
preferring an exact canonical display key before the shortened identity key.

The ordering is important. Human confirmed that these are two different
organizations:

- `1442_25` -> `ООО Стройразвитие`;
- `1519_25` -> `Стройразвитие М`.

They remain separate and form a genuine cross-org H2 case for Korneeva.

Verification:

- ready objects: `189`;
- ready objects resolved through alias registry: `189/189`;
- raw title fallback: `0`;
- H2 eligible registry cells: `26 -> 18` before section exclusions;
- PZ pair count: `0`;
- UNKNOWN pair count: `0`.

## Honest H2 capacity

After alias canon and section exclusions:

- H2 pair rows: `226`;
- same-org pairs: `120`;
- cross-org pairs: `106`;
- H2 cells: `13`;
- matched cells with both same-org and cross-org baselines: `8`;
- cross-only cells: `5`;
- matched GIPs: `Рузаев`, `Сергеев`, `Ширяев`;
- cross-only GIPs: `Корнеева`, plus Sergeev's facade cells.

For comparison, the pre-canon RP-034 artifact had `182` cross-org pairs. Thus
`76/182` (`41.8%`) of the apparent cross-org capacity was removed by proper
identity canon. Same-org capacity rose from `107` to `120`, as expected when
display variants are reunited.

## Primary H2 result: equal-weight matched cells

All values below are medians over the `8` matched cells, so a large cell cannot
dominate the headline.

| axis | same org | cross org | median cell delta |
|---|---:|---:|---:|
| size-invariant style composition | 0.7248 | 0.7860 | +0.0367 |
| page near structural | 0.7975 | 0.6997 | -0.0193 |
| provenance-residual shingle | 0.3708 | 0.1150 | -0.1358 |
| provenance-residual strong share | 0.5329 | 0.3115 | -0.0574 |

The all-pairs pair-weighted view is retained as diagnostic only. It reverses
several conclusions because the largest cells dominate, which confirms why the
matched-cell layer is required.

## H2 is GIP-specific, not universal

### Sergeev: strong cross-organization transfer

Five matched cells across flat/sloped roofs and `КР/ОВ/ПОС`:

- style composition: `0.9193 same -> 0.9134 cross`;
- near structural: `0.8539 -> 0.8737`;
- residual shingle: `0.4333 -> 0.2397`;
- residual strong share: `0.5921 -> 0.5897`.

The structural and strong-page signal is nearly fully retained across
organizations; textual content is partially retained. This is positive H2
evidence for a transferable Sergeev/subcontractor handwriting.

### Ruzaev: organization dominates content

Two matched cells (`КР/ПОС`, flat roof):

- near structural: `0.7579 -> 0.5139`;
- residual shingle: `0.4482 -> 0.0076`;
- residual strong share: `0.5494 -> 0.0`.

The GIP name alone does not preserve the content signal across these
organizations.

### Shiryaev: structural-only case so far

One matched `ПОС` cell:

- near structural: `0.4386 -> 0.5760`;
- residual shingle: `0.0 -> 0.0002`;
- residual strong share: `0.0533 -> 0.0`.

There is no positive text-content evidence yet; the structural increase needs
more cells before interpretation.

## Targeted data gaps

The generated `gip_control_h2_data_gaps_v0.csv` identifies exactly where new
objects are useful:

- Korneeva / `ОВ` / `ИНЖЕНЕРИЯ`, `ПОС`:
  `ООО Стройразвитие <> Стройразвитие М`, but no same-org baseline;
- Sergeev / facade / `АР`, `КР`, `ПОС`:
  `Гамма <> СП Стройинвест ГРУПП`, but no same-org baseline.

If the corpus is expanded, these are higher-value targets than an undirected
increase in object count. Additional same-GIP cross-org subcontractor cases are
still useful for adding new GIPs to H2.

## RP-034 minor closure

`gip_control_provenance_residual_headlines_v0.csv` now aggregates the headline
directly over pair rows. This removes the previous ambiguity between direct
pair medians and median-of-cell summaries.

After alias canon expands the honest within-org H1 controls, H1 remains
positive:

- same-GIP residual shingle `0.0251` vs diff-GIP `0.0059`;
- same-GIP residual strong share `0.0976` vs diff-GIP `0.0`.

The lower absolute headline is expected: previously split organization aliases
now contribute to the within-org control.

## Artifacts

- `E:/output/DocSpectrum/gip_control_h2_alias_canon_v0/`
  - `gip_control_h2_cells_v0.csv`
  - `gip_control_h2_summary_v0.csv`
  - `gip_control_h2_gip_summary_v0.csv`
  - `gip_control_h2_data_gaps_v0.csv`
  - `gip_control_h2_alias_canon_v0.json`
- rebuilt upstream artifacts:
  - `gip_control_registry_v0`
  - `gip_control_baseline_v0_1`
  - `gip_control_near_match_v0`
  - `gip_control_provenance_residual_v0`

## Verification

- registry/H2 assert tests: `9/9 OK`;
- provenance residual tests: `4/4 OK`;
- Python compile: OK;
- `git diff --check`: clean;
- PZ/UNKNOWN pair rows: `0/0`;
- both repositories clean after push.

## Risks / open questions

- H2 matched evidence currently covers only three GIPs; the Sergeev result is
  strong but must not be generalized to all GIPs.
- Some matched cells contain only one same-org pair; cell-level confidence is
  therefore uneven even though equal weighting removes size dominance.
- Does reviewer agree that H2 should now be stated as **GIP-specific
  transferable handwriting**, positively established for Sergeev, rather than
  as one global population effect?
- Should the next data acquisition target the five explicit cross-only cells
  first, or prioritize adding entirely new subcontractor GIPs across multiple
  organizations?

## Not touched

- near-match scorer;
- provenance bands;
- title/GIP extraction;
- legal/borrowing interpretation;
- consumer-facing UC thresholds.

