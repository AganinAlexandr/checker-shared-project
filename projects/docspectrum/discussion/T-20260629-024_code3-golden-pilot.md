# T-20260629-024: DocSpectrum CODE-3 pilot — golden regression

Date: 2026-06-29
Project: DocSpectrum
Status: closed_reviewed
Profile: CODE-3 pilot validating accepted DEC-20260629-008

## [2026-06-29 · codex]

`[CODE3-CYCLE-OPEN]` First progressive dual-review cycle for the golden regression layer.

### Frozen snapshot

- generator: Codex
- repo: `AganinAlexandr/DocSpectrum`
- range: `docspectrum@e00f34b..6285ddd`
- package_id: `CODE3_DS_GOLDEN_20260629_001`
- work type: validation infrastructure
- claim: golden anchors protect previously validated behavior; they do not establish new model accuracy

### Focused packages

Local common path:

- `E:\commons\DocSpectrum\review_packages\CODE3_DS_GOLDEN_20260629_001\CODE3_DS_GOLDEN_20260629_001_A_FOCUSED.zip`
- SHA256: `D766FBEF2AAE627A84D92CC7ACD080D78A72DBAAB0F58B136063E5AC85E8F1D2`
- `E:\commons\DocSpectrum\review_packages\CODE3_DS_GOLDEN_20260629_001\CODE3_DS_GOLDEN_20260629_001_B_FOCUSED.zip`
- SHA256: `18DF21C1B43C0041D9A752247D238FAAB994A3F2EBC3B2A9B9CB2521F2D73B13`

Evidence files inside A/B are byte-identical. Only scope/instruction/response files differ:

- A: mandatory unguided sweep, then full guided question overlay;
- B: mandatory unguided, less-guided risk review.

### Included

- complete range diff and commit stat;
- four changed files;
- generated 18-anchor summary and per-anchor results;
- package-local SHA256 manifest;
- response template.

### Physically withheld from focused stage

- repository access and browsing;
- HC/RP history behind anchor values;
- underlying source JSON/CSV artifacts;
- full corpora and 13-object holdout;
- every other reviewer result.

### Generator verification (not a review verdict)

- unit tests: 2/2 passed;
- golden assertions: 18/18 passed;
- DocSpectrum worktree clean; `origin/main=6285ddd`.

### Next sequence

1. Human provides A and B ZIPs to separate fresh chat sessions.
2. A/B do not see each other's outputs.
3. `FOCUSED-REVIEW-A` is frozen before A receives full context.
4. A then performs progressive full-context reasoning audit in the same session.
5. T independently reruns tests/artifacts for `reproduction-OK`.
6. Reconciliation occurs only after both focused reviews are frozen.

No full-context material or review result is appended here until human confirms both focused passes are frozen.

## [2026-06-29 · codex]

`[CODE3-FOCUSED-FROZEN]` Human supplied both independent focused reviews; their files are frozen before progressive disclosure.

### Frozen review outputs

- Reviewer A: `review_CODE3_DS_GOLDEN_20260629_001_A_FOCUSED.txt`
  - SHA256: `1489849134DA77393A0364B41E6DEA0C078DEF385A0537E49B7523789BAA2C8A`
  - focused verdict: `CHANGES-REQUIRED`
- Reviewer B: `review_CODE3_DS_GOLDEN_20260629_001_B_FOCUSED.txt`
  - SHA256: `FDA3F78D84FB18FC2C813C4C1908FD7306108F3BCD822FC949C2AAD9F67FE47B`
  - focused verdict: `REASONING-OK-FOCUSED`

Both independently identified snapshot brittleness / over-freezing and missing historical or artifact context. A additionally reproduced unsafe type-coercion passes and identified the missing relational GIP invariant; B independently identified stale-artifact/version binding and path portability. This is an observation about complementarity, not reconciliation: neither reviewer has seen the other's output.

### Progressive package for Reviewer A

- path: `E:\commons\DocSpectrum\review_packages\CODE3_DS_GOLDEN_20260629_001\CODE3_DS_GOLDEN_20260629_001_A_FULL_CONTEXT.zip`
- SHA256: `BEF2D44BE7475F3BD1053B5F87C231B7B45D8AE75333557F6D4E7B512392D05F`
- manifest verification: 41/41 entries match
- disclosure: nine anchored JSON artifacts, full two-commit history, HC-018/019, relevant RP evidence, 475-row training index, and exact-CRC holdout audit
- holdout audit boundary: 129 reserved PDFs vs 475 training CRCs, exact overlap `0`; semantic independence is not claimed

Reviewer B's output is intentionally absent from A's package. The next reasoning action is A full-context review in the same session; no code changes or reconciliation occur before that output is frozen.

## [2026-06-29 · codex]

`[CODE3-FULL-REVIEW-FROZEN]` Reviewer A completed the progressive full-context pass.

- file: `review_CODE3_DS_GOLDEN_20260629_001_A_FULL_CONTEXT.md`
- SHA256: `5B87FFB79636F1E6456FDDAB653C21C1DBDD7DE122519F37D34A90317B7F53F1`
- verdict: `CHANGES-REQUIRED`
- package integrity reproduced by A: 41/41
- reasoning rerun reported by A: 18/18 anchors and 2/2 tests; this is not role-T `reproduction-OK`

Confirmed load-bearing findings: v0 guarded four GIP snapshots instead of the reviewed relation; tolerance bands lacked statistical basis; the suite claim and per-anchor provenance were too broad; type coercion and failure/CLI paths needed low-cost hardening.

### Generator audit of the full-context finding

The first full-context package itself omitted RP-013, RP-015, and RP-020, while RP-037 was present but not used in A's trace. Consequently the claim that several final values were unreviewed was not sustained by the complete RP record:

- `24 / 0.9137` was directly reviewed in RP-013;
- corpus `475` was reported in RP-015;
- text candidates `42993` and bridge `250` were directly reviewed in RP-020;
- final GIP values `0.3559 / 0.0394 / 0.7851 / 0.0727` were reproduced in RP-037 after ID exclusion. The RP-034 aggregation caveat concerned an earlier overall aggregation.

This is recorded as a context-selection defect in the first progressive package. It does not negate A's correct contract-design findings.

### Correction

- DocSpectrum commit: `aeb7cb4` (`golden_anchors_v1`)
- v1 result: 15/15 passed
- generator tests: v0+v1 10/10 passed
- changes: strict numeric types; Axis A floor; reviewed aggregate exact recall; two relational GIP gaps; per-anchor RP provenance; explicit snapshot modes and claim boundary; failure/filter/operator/CLI tests

Next isolated packages:

- Reviewer A correction: `CODE3_DS_GOLDEN_20260629_001_A_CORRECTION.zip`
  - SHA256: `48DAFED0C7A397E86990845D1C31FDF54A855C0BEC70BDF9894DCF7056B423F4`
  - manifest: 18/18
- role T reproduction: `CODE3_DS_GOLDEN_20260629_001_T_REPRODUCTION.zip`
  - SHA256: `74C7C60CA4CF405F9EA5E510AA3747ABE60B82073E7CDA5E7218C84197099CB8`
  - manifest: 13/13

A and T may now proceed independently. Final `[AGENT-OK]` remains gated on `REASONING-OK-CORRECTION ∧ REPRODUCTION-OK`.

## [2026-06-29 · codex]

`[CODE3-CORRECTION-REASONING-OK; T-NOT-EXECUTED]`

Reviewer A correction is frozen:

- file: `CORRECTION_REVIEW_A.md`
- SHA256: `61568FFA634A2D57F54CDCFE94952A2FF55B6A7BA04A028D64100BBC8B4C109A`
- verdict: `REASONING-OK-CORRECTION`
- A verified 18/18 package hashes, 8/8 v1 tests, 15/15 anchors, all seven correction checks, and explicitly retracted full-context N1/N2 after receiving RP-013/015/020/037.

The first role-T response is frozen but does **not** satisfy `reproduction-OK`:

- file: `review_CODE3_DS_GOLDEN_20260629_001_T_REPRODUCTION.txt`
- SHA256: `DA9094EA342B1DE1E74A45AAABE902957D17A7C35D9320FD22458D4FA0A50381`
- stated verdict: conditional `REPRODUCTION-OK`
- actual status: `NOT-EXECUTED`
- evidence: T explicitly did not recompute manifest hashes, described tests as "should pass", supplied no runtime/Python version, no executed return codes, and no generated output hashes. It also reported eight artifact JSONs although the package contains nine.

Therefore the CODE-3 gate remains open: reasoning is OK; technical reproduction is missing.

Retry package:

- `CODE3_DS_GOLDEN_20260629_001_T_REPRODUCTION_RETRY.zip`
- SHA256: `0A39193BDA07FF94C74B165CFBF391EEBD559A8E03844504A797A46DCAEFCACE`
- manifest: 14 entries
- entrypoint: `python verify_reproduction.py`
- the entrypoint physically verifies all hashes, runs 8 tests, runs 15 anchors, executes mutation probes, records Python/runtime and return codes, hashes generated outputs, and writes `T_REPRODUCTION_REPORT.json`.

A new independent T session must return the generated JSON. Code inspection or conditional language is explicitly insufficient.

## [2026-06-29 · codex]

`[CODE3-GATE-CLOSED]` Independent retry T executed the evidence suite.

- file: `review_CODE3_DS_GOLDEN_20260629_001_T_REPRODUCTION_RETRY.txt`
- SHA256: `6E287F94F04E47AF84AE44FAAEDFE863D8D71C6E65A19621DAB82E148B9817BC`
- environment: Python `3.12.12`, GCC 12.2.0 / Linux
- manifest: `14/14`, mismatches `0`
- unit tests: `8/8`, executed
- golden anchors: `15/15`, failed `0`
- mutation probes: `6/6`
- generated output hashes supplied for both CSV and JSON
- verdict: `REPRODUCTION-OK`

The report text contains the full machine-evidence payload although the generated JSON was not copied as a separate file. Unlike the rejected first T attempt, it supplies an independent runtime, actual timings/results, manifest recomputation, mutation outcomes, and output hashes; it is accepted as executed reproduction.

### Reconciliation

- A: `REASONING-OK-CORRECTION`; all blocking contract findings closed; N1/N2 retracted after complete RP trace.
- B: focused `REASONING-OK`; its brittleness/provenance concerns are addressed by explicit v1 stability modes and per-anchor RP provenance. Its portability concern is empirically reduced by the independent Linux run with explicit CLI paths. Artifact-to-code version binding remains optional hardening, not a correctness blocker for this frozen-artifact regression layer.
- T: `REPRODUCTION-OK` on an independent environment.

Final CODE-3 verdict: `[AGENT-OK]` for `docspectrum@e00f34b..aeb7cb4`.

The pilot validates DEC-20260629-008 with two required refinements: full context needs an explicit claim/anchor→source map, and T status requires machine evidence of execution rather than prose inspection.
