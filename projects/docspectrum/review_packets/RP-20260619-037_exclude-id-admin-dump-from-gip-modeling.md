# RP-20260619-037: Exclude ID administrative dumps from GIP modeling

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 23:20 MSK - codex]

`[REVIEW]` This packet implements the RP-036 finding and HC-014 clarification:
`ИД` remains in the document inventory as a formal PD section ("other
documentation"), but is excluded from the author-handwriting model because the
current capital-repair ID files are compiled dumps of externally authored
inputs (`ТЗ`, contracts, addenda, technical conditions).

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@885849b..d792d66
- focus: section-code exclusion only; verify that H1/H2 conclusions survive
- commit: `d792d66` exclude ID dumps from GIP modeling

## Change

- Added `ИД` to the baseline modeling exclusions beside `ПЗ` and `UNKNOWN`.
- Added regression coverage:
  - `КР`, `ПОС`, `СМ` remain included;
  - `ИД`, `ПЗ`, `UNKNOWN` are excluded.
- The broad selection/inventory is unchanged, so ID evidence remains available
  for audit and other document-analysis tasks.

## Empirical rationale

Before exclusion, the ten H1 ID pairs showed no useful GIP separation:

- same-GIP residual shingle approximately `0.62`;
- different-GIP residual shingle approximately `0.60`.

This is the expected signature of shared source material rather than authored
project content. Its failure to separate GIPs, while authored `КР/ПОС/СМ`
sections do separate them, is an internal negative control for the handwriting
method.

## Rebuilt corpus

- ready known-section rows: `566`;
- pair rows: `398` (`408 -> 398`);
- ID pairs: `0`;
- PZ pairs: `0`;
- UNKNOWN pairs: `0`;
- OВ pairs: `0`;
- same-object pairs: `0`.

## H1 after ID exclusion

Direct pair-level provenance-residual headline:

| relation | pairs | residual shingle median | residual strong-share median |
|---|---:|---:|---:|
| same GIP | 116 | 0.3559 | 0.7851 |
| different GIP | 131 | 0.0394 | 0.0727 |

Section-level residual-shingle medians:

| section | same GIP | different GIP | approximate lift |
|---|---:|---:|---:|
| КР | 0.3738 | 0.0489 | 7.6x |
| ПОС | 0.6933 | 0.0291 | 23.8x |
| СМ | 0.2194 | 0.0596 | 3.7x |

`ИНЖЕНЕРИЯ` has only one same-GIP and two different-GIP pairs and remains
diagnostic, not a headline.

## H2

H2 is unchanged because ID did not participate in the matched H2 cells:

- pair count: `151`;
- same-org / cross-org: `57 / 94`;
- matched cells: `12`;
- qualitative result unchanged:
  - Sergeev: transferable;
  - Ruzaev: organization-bound;
  - Shiryaev: partial transfer.

## Verification

- registry/baseline tests: `7/7 OK`;
- Python compile: OK;
- rebuilt baseline, near-match, provenance-residual and H2 artifacts;
- model pair counts for `ИД/ПЗ/UNKNOWN/ОВ/self`: `0/0/0/0/0`;
- DocSpectrum clean and pushed at `d792d66`.

## Reviewer questions

- Does this close the RP-036 ID finding and correctly preserve ID at the
  inventory layer while excluding it only from authorship modeling?
- Does reviewer agree that the ID result should be retained as a useful
  negative control: shared non-authorial input does not generate GIP
  separation?

## Not touched

- broad selection and explorer bundles;
- title/GIP/organization extraction;
- alias canon;
- near-match and provenance scoring;
- consumer-facing UC thresholds.

