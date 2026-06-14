# HC-20260615-002: Object registry and TEI layer

Status: active
Project: DocSpectrum
Source: human via DocSpectrum chat

## Context

Human provided the Podolsk UUiR object list:

- `E:/commons/DocSpectrum/Капремонт_дома_УУиР_Подольск.xlsx`

The registry should be a separate object-level file with:

- `object_id`
- address
- `ГВС` / `ОТ` / `ОВ` type
- source PD folder

The registry is object-level context and must not be mixed into `section_passport`, which remains tied to a concrete parsed section.

## TEI / OKEI

TEI indicators are available in explanatory note XML files, under `<TEI>`.

The `Measure` value in TEI is an OKEI code. The transition table should be read from Minstroy XML schema files:

- `E:/Projects/Minstroy/XML_sсhems/XML_ПЗ_версия_01.06/explanatorynote-01-06.xsd`

Observed examples:

- `006` = `Метр`, symbol `м`
- `055` = `Квадратный метр`, symbol `м^2`
- `113` = `Кубический метр`, symbol `м^3`
- `796` = `Штука`, symbol `шт`

## Corpus note

Most object folders still contain archives only, but explanatory note XML files are available externally for TEI extraction.

For `1683_25`, the required explanatory note XML is absent. A stray `statement_*.xml` was present temporarily, but human confirmed it is unrelated and should not be used.

Current expected registry state:

- objects: `35`
- source folders found: `35`
- objects with TEI: `34`
- missing explanatory note XML: `1683_25`
- same-address groups: `5`
- inconsistent same-address groups: `1`

Human clarified that TEI values for one address must be identical. If same-address TEI differs, it is a designer/source-PD error in the pre-expertise documentation, not a valid house distinction.

Current known inconsistency:

- `1680_25` / `1684_25`, `Подольск, мкр. Климовск, ул. Симферопольская, 13`

## Methodological use

TEI values form an independent domain similarity layer for object/house closeness, for example:

- number of entrances
- number of floors
- building dimensions
- footprint area
- building volume
- total area

This layer can support validation rows before or alongside PDF element-spectrum comparison.
