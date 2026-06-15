# HC-20260615-006: TEI engineering correlation hypotheses

Date: 2026-06-15
Project: DocSpectrum
Source: human via DocSpectrum chat
Digest by: codex (receiving agent)

## [human]

Current corpus is pre-expertise project documentation, so domain signals contain both real engineering dependencies and designer-error noise.

Expected engineering dependencies:

- Larger building volume means higher heat demand. This should affect pipe diameters, fittings, heat-exchange equipment and related components. The dependency is not purely linear because continuous building-volume changes map to stepped industrial nominal sizes.
- Building height / floor count affects required heating and DHW pressure levels.
- Larger buildings may require two circulation pumps in the internal coolant circulation loop instead of one.
- Thermometers, meters, valves and similar equipment vary in quantity and in how designers arrange them; this creates both quantitative and layout differences.
- These differences appear in multiple channels: text, specification tables, graphical sheets, captions, and graphical scheme elements.

Therefore DocSpectrum can test correlations between PDF element/entity counts and object features such as:

- building volume;
- floor count / building height;
- apartment count;
- total area / footprint area.

## [codex digest]

This is a causal domain hypothesis layer for eval/calibration, not a direct scoring rule.

Available TEI/object-registry fields already support the first experiments:

- `tei_norm_building_volume_m3`
- `tei_norm_floors_count`
- `tei_norm_height_m`
- `tei_norm_apartments_count`
- `tei_norm_total_area_m2`
- `tei_norm_footprint_area_m2`

Likely dependent variables from DocSpectrum artifacts:

- section-level PDF element counts: text segments, tables, table cells, images, vector/group counts;
- corpus-frequency entity counts and shares by `entity_kind`;
- section/pair similarities from `idf_similarity_v0_3`;
- future graphical/vector signatures when that channel is added.

Expected first tests:

- Monotonic or rank correlation between `building_volume_m3` and equipment/specification-related table/text entities.
- Correlation between `height_m` / `floors_count` and pressure/system-solution related entities, especially in engineering sections.
- Step-like rather than purely linear relationships, because engineering components are selected from discrete nominal ranges.
- Noise should be expected due to pre-expertise errors and inconsistent component selection by designers.

Important modeling rule:

- TEI/TEP fields remain object/profile context and eval-ground-truth features. They should not leak into the neutral core comparison score.

## Implications

For eval-set calibration:

- Use TEI similarity/distance to create expected gradients between objects.
- Treat same-address OV/GVS pairs as controlled comparisons where object TEI is fixed and system differences dominate.
- Use same-architecture or close-TEI houses to test whether document similarity is high for object-driven sections.
- Use distant-TEI houses to test whether equipment/table/graphic differences increase.

For future analytics:

- Add correlation exports: object TEI features joined to section passports / entity summaries.
- Prefer rank/monotonic checks first; do not assume linearity.
- Add step/bucket analysis for building volume, height/floor count, and apartment count.
- Later validate the same domain gradient independently through graphical signatures.

## Related

- `HC-20260615-002` (object registry and TEI extraction)
- `HC-20260615-003` (axis priority and corpus design)
- `HC-20260615-005` (section/system similarity gradient)
- `RP-20260615-010` (pairwise IDF v0.3)
