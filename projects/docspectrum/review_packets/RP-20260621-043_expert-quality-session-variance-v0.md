# RP-20260621-043: Expert quality session variance v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Experiment C step 4 measures outcome-proxy variance across review
sessions rather than across correlated objects.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@8f70c56..a4b3a44
- focus:
  - session key and independence semantics;
  - duplicate object-section collapse;
  - unresolved-outcome handling;
  - variance denominators and multi-object control;
  - prime AO SSU No. 3 foundation batch reproduction
- inputs:
  - RP-042 hash-only registry rows:
    `expert_quality_experiment_c_v0/expert_quality_registry_rows_v0.csv`
- metrics:
  - source registry rows: `8605`;
  - collapsed object-section records: `7849`;
  - sessions: `1035`;
  - experts: `12`;
  - multi-object sessions: `320`;
  - tests: `15/15` (`9` RP-042 + `6` session)
- risks/regressions:
  - clean/remark is a registry proxy, not remark recall;
  - one date may still combine separate same-day work episodes;
  - singleton sessions are mechanically binary, so multi-object variance is
    reported separately;
  - no quality class is inferred from this artifact
- not touched:
  - difficulty proxy (step 5);
  - remark content recall/typing;
  - pre/post-expertise delta;
  - owner-identity track
- domain rule:
  - unit = expert x organization x work type x start date;
  - KR and POS in one transferred batch remain one session;
  - class-1 anchors define the future recall ceiling;
  - holdout is evaluated through cross-session variability, not only mean

## Session boundary

The session key is:

`expert_hash × organization × work_type × session_start_date`.

`section_code` is deliberately not part of the key. KR and POS received in the
same organization/work-type/date batch are one observation, with section codes
preserved as session composition.

Duplicate registry rows for the same expert/object/section/date are collapsed
before aggregation.

## Prime batch acceptance

AO SSU No. 3 / foundation:

- holdout sessions: `2` (`2025-02-13`, `2025-03-20`);
- ceiling-1b sessions: `1` (`2025-02-21`);
- February holdout batch:
  - objects: `10`;
  - KR/POS records: `20`;
  - clean pass: `20`;
  - remarks: `0`;
  - independent observations: `1`.

`--assert-reference` verifies all six values.

## Session-level result

All-session clean-share proxy:

| Role | Sessions | Mean | Variance |
|---|---:|---:|---:|
| ceiling-1a | 140 | 0.1202 | 0.1024 |
| ceiling-1b | 18 | 0.0000 | 0.0000 |
| floor-3 | 568 | 0.4691 | 0.1672 |
| holdout | 163 | 0.6365 | 0.1733 |

Classified-outcome-only variance:

- ceiling-1a: `0.1050`;
- ceiling-1b: `0.0000`;
- floor-3: `0.1701`;
- holdout: `0.1683`.

Multi-object-session variance:

- ceiling-1a: `0.0448` (`49` sessions);
- ceiling-1b: `0.0000` (`9`);
- floor-3: `0.1360` (`173`);
- holdout: `0.1535` (`48`).

## Interpretation

The holdout has broad session outcomes and the highest raw/all-session and
multi-object variance. However, on classified-only sessions the floor-3 anchor
is slightly higher (`0.1701` vs `0.1683`).

Therefore step 4 supports a conditional/high-variance pattern but does not
establish that the holdout is uniquely or robustly the most variable expert.
True recall variance remains blocked on remark content.

This corrects the earlier monthly-probe statement without erasing its useful
hypothesis.

## Privacy and outputs

No raw anchor names occur in the generated artifacts. Identity remains SHA1
plus generic anchor role.

Output:
`E:/output/DocSpectrum/expert_quality_session_variance_v0`.

## Open reviewer questions

1. Is the session key correct, especially combining KR/POS in one batch?
2. Is duplicate object-section collapsing conservative enough?
3. Are all-sections, classified-only, and multi-object denominators separated
   clearly enough?
4. Is the revised interpretation appropriately cautious?
5. Should step 5 difficulty remain a separate packet as agreed in RP-042?
