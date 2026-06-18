# RP-20260618-026: GIP priority corpus manifest v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 22:38 MSK - codex]

`[REVIEW]` Machine-readable implementation of the human-approved priority
corpus for title-based organization/GIP extraction and GIP factor tests.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@3b7b678..50ec9d0
- focus: review exact object membership, factor-cell encoding, unique-object vs membership normalization, registry/title ground-truth boundary and source-readiness diagnostics
- inputs: human corpus list; `gip_corpus_candidates_v0.csv`; `E:/MSE_арх`; HC-013; T-018
- metrics: `46` unique objects; `56` experiment memberships; scheme1 `32`, cross-org `10`, scheme2 `14`; all `46/46` found in registry and archive directory
- risks/regressions: no target PDFs currently extracted; XLSX metadata may be stale; title ground truth not yet available; two source directories are empty
- not touched: archive extraction, explorer exports, title detector, organization/GIP parser, GIP similarity analysis
- domain-rule: human list defines experiment design; XLSX is candidate metadata only; title pages are authoritative; all organization/GIP parties must be retained; same-project splits are invalid

Experiment schemes:

```text
scheme1_fixed_org_worktype_vary_gip: 32 memberships
scheme_cross_org_fixed_gip_worktype: 10 memberships
scheme2_fixed_gip_vary_worktype:     14 memberships
```

Unique object counts by registry GIP:

```text
Локтев:    16
Сергеев:   14
Ефимов:     8
Бородин:    7
Шевченко:   1
total:     46
```

Normalization:

- `gip_priority_objects_v0.csv`: one row per unique object (`46`);
- `gip_priority_memberships_v0.csv`: one row per experiment-cell membership
  (`56`);
- the same 10 Sergeev roof objects participate in both cross-organization and
  cross-worktype schemes without duplicating source objects.

Source readiness:

```text
archive_only:        43
empty_or_missing:     2  (1823_25, 1825_25)
non_target_pdf_only:  1  (1701_25 has only ИД PDF)
target PDF ready:     0
```

Registry discrepancy:

```text
1690_25
human experiment cell: СтройМонтаж / Ефимов / скатная
XLSX registry:         Витмар / Ефимов / скатная
status:                mismatch_needs_title_resolution
```

The mismatch is intentionally preserved. Title-derived organization/GIP pairs
must resolve it.

Ground-truth fields reserved in the object manifest:

- `title_ground_truth_status`;
- `title_organization_count`;
- `title_gip_count`;
- `title_effective_author_gip`.

Code / note:

- `tools/build_gip_priority_manifest_v0.py`;
- `tests/test_gip_priority_manifest.py`;
- `notes/gip_priority_manifest_v0.md`.

Artifacts:

- `E:/output/DocSpectrum/gip_priority_manifest_v0/gip_priority_objects_v0.csv`;
- `E:/output/DocSpectrum/gip_priority_manifest_v0/gip_priority_memberships_v0.csv`;
- `E:/output/DocSpectrum/gip_priority_manifest_v0/gip_priority_manifest_v0.json`.

Verification:

- requested objects: `46`;
- registry matches: `46/46`;
- source directories: `46/46`;
- memberships: `56`;
- scheme2 roof object set equals cross-org roof object set;
- `python -m unittest discover -s tests -p "test_*.py"`: `17/17 OK`;
- `git diff --check`: clean.

Open questions:

- Is the factor-cell representation faithful to the human design?
- Should `1690_25` remain in scheme1 pending title resolution, or be
  quarantined before extraction?
- Is source readiness sufficient for coordinating unpack/export work?
- Should organization/GIP extraction start only after all 46 are ready, or
  incrementally as batches arrive?

Evidence:

- commit/range: `docspectrum@3b7b678..50ec9d0`;
- shared planning: `HC-20260618-013`, `T-20260618-018`.
