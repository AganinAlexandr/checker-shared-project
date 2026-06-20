# RP-20260621-041: IUL personnel validation and roster relation v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-21 MSK - codex]

`[REVIEW]` Historical IUL PDFs are harvested as hash-only external validation
for the eight `owner_or_template` edges, with separate GIP quality control.

- repo: github.com/AganinAlexandr/DocSpectrum
- branch: main
- range: docspectrum@c43d7a0..4a0f0e0
- focus:
  - strict IUL PDF-only inventory (`*.sig` excluded);
  - hash-only person/role extraction;
  - weak declared-developer overlap validation;
  - descriptive title-page GIP relation to the IUL roster;
  - resource-safe MuPDF processing
- inputs:
  - `8` owner-or-template edges;
  - `12` organizations;
  - `224` objects;
  - historical IUL PDFs in `E:/MSE_арх`
- metrics:
  - IUL PDF files: `1037`;
  - unique PDF contents: `1033`;
  - text-layer parsed: `1020`;
  - image-only: `13`;
  - detached `.sig` read: `0`;
  - person-role hash evidence: `3809`;
  - tests: `15/15` (`6` IUL + `9` owner v0/v1)
- risks/regressions:
  - all IUL roster names are weak, potentially formal declarations;
  - surname-only overlap is weaker than exact person-key overlap;
  - image-only IUL requires an explicit OCR pass;
  - actual cryptographic signer is not verified because `.sig` is excluded;
  - IUL is validation-only and must not feed the handwriting model
- not touched:
  - RP-039/040 similarity and typed graph;
  - OCR for 13 image-only IUL;
  - future UKEP metadata;
  - legal conclusions about falsification/authorship
- domain rule:
  - HC-015: IUL validation-only, future personnel signal migrates to UKEP;
  - HC-016: library-relative method, not oracle;
  - declared roster mismatch is a review signal, not proof;
  - disclosed subcontracting is not falsification

## File boundary

Only IUL PDF files are read.

- accepted: `ИУЛ_003_АР.pdf`, `уил Раздел №7 ПОКР.pdf`;
- rejected: `ИУЛ_003_АР.pdf.sig`, all other `.sig`, ordinary PD PDFs.

The artifact reports `detached_signature_file_count=0`.

## Privacy

Artifacts contain:

- object and canonical organization;
- role class;
- SHA1 of normalized full person key;
- separate SHA1 of normalized surname.

They do not contain raw names or extracted IUL text.

## Resource correctness

The first run parsed only `548` files because MuPDF page/document objects and
the WASM store were not released between PDFs. Individual failed PDFs opened
normally.

After explicit `destroy()` plus `emptyStore()`:

- unique contents: `1033`;
- parsed text-layer PDFs: `1020`;
- image-only PDFs: `13`;
- parse errors: `0`.

This closes a real resource-exhaustion defect rather than classifying valid
files as damaged.

## Declared personnel overlap

Because capital-repair IUL rosters may be filled formally, non-GIP names are
weak declared labels.

### Weak exact developer overlap

1. `Комтех <-> Сфера`
   - developer sets `2 / 2`;
   - shared developers `2`;
   - Jaccard `1.0`;
   - shared GIP `1`.
2. `АО ССУ № 3 <-> Сфера`
   - developer sets `1 / 2`;
   - shared developers `1`;
   - Jaccard `0.5`;
   - shared GIP `1`.
3. `Мир <-> ООО К1`
   - developer sets `4 / 2`;
   - shared developers `1`;
   - Jaccard `0.2`;
   - shared GIP `0`.

These are weak declared-roster correlations, not proof of a shared real team
or owner.

### No declared overlap, not dispositive

- `ЛСТехникс <-> ТрансРегионСервис`;
- `СВАЙТЕК-М <-> Экономный Дом`;
- `ТрансРегионСервис <-> Экономный Дом`.

Given owner guidance on weak IUL filling, absence does not weaken the
handwriting result by itself.

### Needs OCR

- `Горизонт <-> Экономный Дом`;
- `РусСтройГрупп <-> СитиГазСтрой`.

All IUL PDFs on Horizon/CityGazStroy side are image-only.

## Descriptive title-page GIP roster relation

The title page is authoritative for who is shown as GIP for the section.
The IUL roster does not have to include that person when other required names
are properly provided. Presence, another role, or absence are descriptive
states, not correctness verdicts. A qualified engineer may appear as a
developer. `.sig` signer identity is not inspected.

For `224` objects:

- title-page GIP appears as IUL `ГИП`: `152`;
- title-page GIP appears in another IUL role: `2`;
- title-page GIP not observed in extracted IUL roster: `1`;
- title-page GIP reference unavailable: `60`;
- image-only IUL: `3`;
- no IUL PDF: `6`.

Among `155` directly comparable objects, the title-page GIP appears somewhere
in the IUL roster in `154` cases (`99.4%`).

At file level:

- parsed IUL with GIP role: `1009 / 1020`;
- parsed IUL without GIP role: `11`.

### Two allowed role differences

1. `1059_24`, АО ССУ №3:
   - title GIP: Питанов;
   - IUL role GIP: Жиров;
   - Питанов listed as developer.
2. `1070_24`, АО ССУ №3:
   - same role split.

These are not violations: the title-page GIP is present in the IUL roster and
may also act as a developer.

### One example without the title-page GIP in the roster

`1366_25`, Сфера:

   - title GIP: Шпаков;
   - all five IUL PDFs list Жиров as GIP;
   - all five list Питанов as developer;
   - Шпаков is absent from the extracted rosters.

This is not an error or review candidate by itself. It is also not a statement
about the cryptographic signer because sidecar signatures are outside scope.

## Evidence

- commits:
  - `f9eb75f` harvest hash-only IUL rosters;
  - `4cf609c` refine title-GIP roster-presence extraction;
  - `4a0f0e0` make title-GIP/IUL relation descriptive
- code:
  - `tools/build_iul_personnel_validation_v0.mjs`
  - `tests/test_build_iul_personnel_validation_v0.mjs`
- report:
  - `notes/iul_personnel_validation_v0.md`
- artifacts:
  - `E:/output/DocSpectrum/iul_personnel_validation_v0/`
  - `iul_pdf_inventory_v0.csv`
  - `iul_person_hash_evidence_v0.csv`
  - `iul_org_personnel_overlap_v0.csv`
  - `iul_object_gip_qc_v0.csv`
  - `iul_organization_gip_qc_v0.csv`

## Reviewer questions

1. Is the validation correctly weakened from "personnel ground truth" to
   "declared-roster correlation", with title-GIP presence treated only as
   descriptive context?
2. Is exact developer overlap useful as weak corroboration while absence is
   correctly non-dispositive?
3. Is the title-GIP relation correctly framed: two developer-role appearances
   and one absence, none treated as an error?
4. Is excluding every `.sig` correct for this IUL/PDF layer while reserving
   signature metadata for a future UKEP layer?
5. Should the next step be targeted OCR of the 13 image-only IUL, or a first
   IUL-handwriting mismatch demo on readable objects?
