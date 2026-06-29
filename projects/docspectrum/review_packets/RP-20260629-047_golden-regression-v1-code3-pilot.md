# RP-20260629-047: Golden regression v1 and CODE-3 pilot

Status: reviewed
Project: DocSpectrum
Generator: codex
Reasoning reviewers: A progressive, B blind focused
Technical verifier: T independent execution
Strategic owner: human

## Scope

- repo: `github.com/AganinAlexandr/DocSpectrum`
- range: `docspectrum@e00f34b..aeb7cb4`
- work type: P0 validation infrastructure
- claim: protects frozen-artifact behavior and reviewed relational conclusions; does not establish new model accuracy

## Generator result

Golden v1 replaced the pilot v0 contract after focused/full-context findings:

- strict value types; boolean/integer and numeric-string coercions no longer pass;
- Axis A guarded as reviewed floor `>=0.90`, not rounded snapshot tolerance;
- exact page recall guarded as reviewed aggregate `5344 + 137 = 5481`;
- GIP H1 guarded by two relational gaps (`0.3165 >= 0.25`, `0.7124 >= 0.60`) rather than four hand-bracketed values;
- every anchor carries RP provenance, reviewed-value status, and stability mode;
- failure, selector, operator, report, and CLI paths covered by tests;
- v0 retained only as the superseded pilot snapshot.

Generator checks: v0+v1 tests `10/10`; v1 anchors `15/15`.

## CODE-3 evidence

### Reviewer A

- focused verdict: `CHANGES-REQUIRED`;
- full-context verdict: `CHANGES-REQUIRED`;
- correction verdict: `REASONING-OK-CORRECTION`;
- correction verification: manifest `18/18`, tests `8/8`, anchors `15/15`, seven required checks met;
- A explicitly retracted N1/N2 after the correction package supplied omitted RP-013/015/020 and correctly linked RP-037.

### Reviewer B

- blind focused verdict: `REASONING-OK-FOCUSED`;
- independently identified snapshot brittleness, portability, and stale-artifact/version-binding risks;
- v1 addresses brittleness/provenance; independent Linux reproduction reduces portability risk; version binding remains non-blocking hardening.

### Technical verifier T

The first T response was rejected as `NOT-EXECUTED`: it did not recompute hashes or run tests.

The retry executed a machine evidence harness in Python `3.12.12` / Linux:

- manifest `14/14`, mismatches `0`;
- tests `8/8`;
- anchors `15/15`;
- mutation probes `6/6`;
- generated CSV/JSON output hashes supplied;
- verdict `REPRODUCTION-OK`.

## Final verdict

`[AGENT-OK] = REASONING-OK-CORRECTION ∧ REPRODUCTION-OK`.

The 13-object natural holdout remains untouched for model scoring. Exact CRC audit established `0` byte-identical overlaps against the 475-document training index, but semantic independence is not claimed.

## Workflow findings

The CODE-3 pilot validates DEC-20260629-008 and adds two mandatory controls:

1. progressive full context requires an explicit claim/anchor→RP/source map; merely supplying more files is insufficient;
2. T must provide machine evidence of execution. Conditional prose such as “tests should pass” cannot produce `reproduction-OK`.

No further correction is required before the next P0 validation milestone.
