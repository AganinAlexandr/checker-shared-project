# RP-20260619-036: GIP-control section-classification correction

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 23:05 MSK - codex]

`[REVIEW]` This packet resolves the anomalous
`Сергеев | скатная | ОВ` cell found in RP-035. The anomaly was not a valid
degenerate OВ profile: it exposed a broad-corpus filename-classification bug.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@6f28a6a..885849b
- focus:
  - token-boundary section inference;
  - exclusion of supporting/source documents from the PD-section corpus;
  - prohibition of same-object document pairs;
  - clean rebuild of H1/H2 and replacement of RP-035 metrics.
- commit:
  - `885849b` fix broad corpus section classification

## Root cause

Two-letter section codes were previously searched as arbitrary substrings.
Therefore:

- `ОВ` matched the letters in `Советская`;
- files explicitly named `СМ`, `АО`, `ДВ`, and `ТЗ` could enter the same
  synthetic OВ family;
- objects with multiple such files produced same-object comparisons.

The anomalous RP-035 cell contained:

- `4.1.1 СМ...`;
- `АО...`;
- `ДВ...`;

and no real OВ section.

## Correction

- Section abbreviations are recognized only as standalone tokens: no letters or
  digits may touch them on either side.
- The normative engineering notation `ИОС5.1`, `ИОС5.2`, etc. is handled by an
  explicit exception: digits after `ИОС` are subsection numbers.
- `СД` is canonicalized to `СМ`.
- `ИД` remains a distinct analyzable document section, per the human decision;
  it is not treated as a PP87 section.
- Standalone `ТЗ`, `АО`, `ДВ`, `ТУ`, acts, letters, notifications, technical
  assignments, and defect statements are excluded as supporting/source
  documents.
- Pair generation now explicitly rejects any pair whose left and right
  documents belong to the same object.

## Corpus effect

Wide selection:

- source PDFs: `1654 -> 1083`;
- unique CRC bundles: `1513 -> 972`;
- excluded supporting rows: `2764`;
- existing exports: `972/972`, so no explorer rerun was needed.

Clean modeling corpus:

- ready known-section rows: `587`;
- pair rows: `408`;
- self-object pairs: `0`;
- PZ pairs: `0`;
- UNKNOWN pairs: `0`;
- OВ pairs: `0`.

The previous OВ H2 cell disappears completely, as expected.

## Corrected H1

Direct pair-level provenance-residual headlines:

| relation | pair count | residual shingle median | residual strong-share median |
|---|---:|---:|---:|
| same GIP | 122 | 0.3717 | 0.7851 |
| different GIP | 135 | 0.0398 | 0.0758 |

The author signal is stronger after removing supporting documents and synthetic
section collisions.

## Corrected H2

Capacity:

- H2 pairs: `151`;
- same-org: `57`;
- cross-org: `94`;
- cells: `19`;
- matched same+cross cells: `12`;
- cross-only cells: `7`;
- matched GIPs: Ruzaev, Sergeev, Shiryaev.

Equal-weight matched-cell headline:

| axis | same org | cross org | cell-median delta |
|---|---:|---:|---:|
| style composition | 0.9790 | 0.9278 | -0.0183 |
| near structural | 0.9389 | 0.8193 | -0.0872 |
| residual shingle | 0.4271 | 0.1664 | -0.2975 |
| residual strong share | 0.8879 | 0.5335 | -0.1422 |

### Sergeev: transferable handwriting remains positive

Six clean cells, sections `КР/ПОС/СМ`, flat and sloped roofs:

- style composition `0.9702 -> 0.9453`;
- near structural `0.9491 -> 0.8758`;
- residual shingle `0.5417 -> 0.2283`;
- residual strong share `0.9177 -> 0.6713`.

No OВ cell is used. The RP-035 conclusion survives on clean `КР/ПОС/СМ`.

### Ruzaev: organization-bound result remains

Three clean cells, `КР/ПОС/СМ`:

- near structural `0.9316 -> 0.5559`;
- residual shingle `0.4556 -> 0.0152`;
- residual strong share `0.8475 -> 0.0`.

### Shiryaev: upgraded from one cell to a three-section partial-transfer case

Three clean cells, `КР/ПОС/СМ`:

- style composition `0.9833 -> 0.9147`;
- near structural `0.9171 -> 0.8022`;
- residual shingle `0.2036 -> 0.1746`;
- residual strong share `0.8701 -> 0.5119`.

The evidence is now stronger than in RP-035: Shiryaev shows partial structural
and content transfer, although on only one work type.

## Verification

- selection tests: `7/7 OK`;
- registry/baseline tests: `7/7 OK`;
- provenance tests: `4/4 OK`;
- H2 tests: `3/3 OK`;
- Python compile: OK;
- all 972 selected bundles already exist;
- self/PZ/UNKNOWN/OВ pair counts: `0/0/0/0`;
- DocSpectrum clean and pushed at `885849b`.

## Reviewer questions

- Does this fully close the RP-035 OВ anomaly?
- Does reviewer agree that RP-036 metrics supersede the numerical tables in
  RP-035 while preserving its qualitative H2 conclusion?
- For the next corpus expansion, should priority remain the explicit cross-only
  cells, or shift to adding new GIPs with same-work-type cross-org
  subcontractor evidence?

## Not touched

- title/GIP extraction;
- alias identities;
- near-match scoring;
- provenance bands;
- consumer-facing thresholds.

