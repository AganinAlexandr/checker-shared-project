# RP-20260620-040: Owner-identity typed graph v1

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-20 MSK - codex]

`[REVIEW]` RP-039 finding #3 is implemented: owner-identity edges and
components are now separated by causal evidence type.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@1cf0ee2..c43d7a0
- focus:
  - typed rename/network/shared-GIP/owner-or-template edges;
  - separate connected components per edge type;
  - attachment guard preventing handwriting-only links from expanding
    rename/network cores;
  - explicit large-organization template-noise risk
- input:
  - committed RP-039 v0 matrix;
  - `696` organization-pair rows;
  - `12` v0 candidate edges;
  - no pairwise similarity recalculation
- metrics:
  - typed edges: `12`;
  - rename: `1`;
  - disclosed subcontract network: `2`;
  - shared-GIP handwriting: `1`;
  - owner-or-template: `8`;
  - typed components: `7`;
  - unvalidated core attachments: `3`;
  - tests v0+v1: `9/9`
- risks/regressions:
  - temporal rename threshold `0.40` is a research threshold;
  - handwriting-only candidates remain template-noise prone;
  - typed components are still library-relative candidates, not owner facts;
  - sub-organization clustering is not implemented yet
- not touched:
  - RP-039 similarity scores and p95 discovery threshold;
  - IUL validation/falsification;
  - consumer/legal thresholds;
  - Tivolion-K1 coverage
- domain rule:
  - HC-015 owner identity signals;
  - HC-016 method-not-oracle;
  - four-title disclosed subcontracting is not hidden ownership;
  - attachments do not inherit core identity

## Edge taxonomy

Priority:

1. `disclosed_subcontract_network`
   - explicit four-title evidence;
2. `rename_candidate`
   - handwriting + shared GIP + temporal handoff `>=0.40`;
3. `shared_gip_handwriting`
   - handwriting + shared GIP, without rename-level handoff;
4. `owner_or_template_candidate`
   - handwriting without independent identity evidence.

Four-title evidence has priority. A disclosed network is not upgraded to a
hidden-owner hypothesis.

## Clean cores

### Rename

`АО ССУ № 3 | Комтех`

The confirmed rename pair is now the entire rename core.

### Disclosed production network

`Ватага | ИнфраСтройИнтекс | Спектр`

All edges are backed by four-title disclosure.

### Shared-GIP handwriting

`Ватага | СП Стройинвест ГРУПП`

Shared GIP: `сергеев`. Temporal handoff is below rename threshold, so the link
is not called rename or common ownership.

## Sphere attachment finding

The two Sphere links are no longer part of the rename component:

- `АО ССУ № 3 <-> Сфера`;
- `Комтех <-> Сфера`.

Both are:

- `owner_or_template_candidate`;
- `unvalidated_attachment_to_core`;
- `large_org_template_noise_risk=True`;
- without shared GIP or four-title corroboration.

Sphere has `79` objects and is the largest organization in the registry. The
links remain available for investigation but cannot expand the confirmed
rename core.

## Other ambiguous components

Owner-or-template components:

1. `АО ССУ № 3 | Комтех | Сфера`;
2. `Горизонт | ЛСТехникс | СВАЙТЕК-М | ТрансРегионСервис | Экономный Дом`;
3. `Мир | ООО К1`;
4. `РусСтройГрупп | СитиГазСтрой`.

They are intentionally separate from rename/network components. The label
preserves both explanations: common owner or shared template/process.

## Anchor behavior

- Комтех→ССУ: `rename_candidate`;
- Ватага↔Спектр: `disclosed_subcontract_network`;
- Тиволион↔К1: remains a coverage gap.

## Evidence

- commit: `c43d7a0`
- code:
  - `tools/build_owner_identity_typed_graph_v1.py`
  - `tests/test_build_owner_identity_typed_graph_v1.py`
- report:
  - `notes/owner_identity_typed_graph_v1.md`
- artifacts:
  - `E:/output/DocSpectrum/owner_identity_typed_graph_v1/`
  - `owner_identity_typed_edges_v1.csv`
  - `owner_identity_typed_components_v1.csv`
  - `owner_identity_core_attachments_v1.csv`
  - `owner_identity_anchor_validation_v1.csv`

## Reviewer questions

1. Does the priority taxonomy prevent causal mixing without discarding useful
   hypotheses?
2. Is the rename criterion (handwriting + shared GIP + temporal handoff)
   sufficiently conservative for v1?
3. Is Sphere now correctly framed as template-noise-prone attachment rather
   than part of the rename core?
4. Is `Ватага <-> СП Стройинвест ГРУПП` correctly separated as shared-GIP
   handwriting rather than merged into the disclosed four-title network?
5. After this cleanup, should the next packet be IUL validation against typed
   components?
