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


## [2026-06-21 · opus] REVIEW

`[AGENT-OK]` (ИУЛ personnel validation + GIP QC v0) — проверено против кода+артефактов, не по тексту. Числа воспроизведены из JSON/CSV; тесты прогнаны.

**Воспроизведено точно:**
- inventory 1037 / unique 1033 / parsed 1020 / image-only 13 / **detached .sig = 0** (инвентарь: 0 строк со `.sig`); person-role hash evidence 3809; parsed с GIP-role 1009, без 11.
- Тесты **15/15** (прогнал: ИУЛ 6/6 `pass`; owner v0 5 + v1 4 = 9/9 ранее).
- Overlap (developer): **Комтех↔Сфера J=1.0** (2/2, shared 2, +shared GIP 1); **ССУ↔Сфера J=0.5** (1/2, +GIP 1); **Мир↔К1 J=0.2** (1, GIP 0). Остальные 5 = no_overlap/insufficient. Совпало.
- GIP-QC: title-GIP как ИУЛ-ГИП 152 + в иной роли 2 = 154; не наблюдён 1 → **154/155 = 99.4%**; missing-ref 60, image-only 3, no-IUL 6. Совпало.
- **Приватность подтверждена:** `iul_person_hash_evidence_v0.csv` = object/org/content_sha1/person_hash/surname_hash/role_class/kind — **сырых имён нет**. (Inventory хранит file_name/file_path — это имена ФАЙЛОВ/секций, не персональные данные → допустимо.)

**Ответы на reviewer-questions (все ДА):**
1. Ослабление «personnel ground truth»→«declared-roster correlation» + title-GIP как описательный контекст — **корректно** и согласуется с HC-015 (ростеры могут быть формальны/подложны в капремонте). Понижение консервативно и уместно.
2. Exact dev-overlap как слабое подтверждение, absence non-dispositive — **да**.
3. Title-GIP framing (2 dev-роли + 1 отсутствие, не ошибки; 1366_25 Шпаков отсутствует, Жиров/Питанов в ростере — описательно, .sig-подписант не инспектируется) — **корректно**.
4. Исключение всех `.sig` для ИУЛ/PDF-слоя с резервом подписи под будущий УКЭП — **верно** (HC-015: персонал-сигнал мигрирует ИУЛ→УКЭП).

**ГЛАВНАЯ НАХОДКА (смысл результата для owner-трека):** цель RP-041 — **расщепить 8 owner_or_template рёбер** на owner (personnel-shared) vs template (нет overlap). Итог: **ИУЛ-overlap НЕ расщепляет** — declared-overlap есть лишь у 3 рёбер, и все Сфера-/малочисленные:
- **n-хрупкость:** Комтех↔Сфера J=1.0 держится на **2/2 разработчиках** — крошечная выборка, не «доказанный общий штат», а скорее **малый формальный/шаблонный ростер**.
- Все 3 overlap-ребра упираются в **Сферу** (79 объ, уже large-template-noise в RP-040) → согласуется с **template**-объяснением (Сфера = крупный пул/шаблон, используемый Комтех/ССУ), а не доказанным владением. Плюс Комтех=ССУ (rename-пара) → «оба↔Сфера» фактически одно отношение.
→ Вывод: **ИУЛ не снял неоднозначность 8 рёбер** (честно показано); долговечный сигнал = ПОЧЕРК, ИУЛ — слабый валидационный контекст. Это ценный отрицательный результат (подтверждает HC-015 «ростеры формальны»).

**Q5 — следующий шаг:** OCR 13 image-only — **низкий ROI** (разблокирует только Горизонт/СитиГазСтрой, у которых coverage и так insufficient). Выше ценность — **демо рассогласования ИУЛ↔почерк на читаемых объектах** (UC-фальсификация, HC-015; адресует «подписи лиц, не участвовавших», Минстрой 4420-КМ/14). НО: живой приоритет human сейчас — **C-эксперимент + поиск контента замечаний** (HC-017), не owner-трек → рекомендую держать owner-трек на паузе до отмашки.

**Не блокирует.** Инварианты соблюдены (hash-only; library-relative; disclosed≠falsification; .sig out-of-scope; declared mismatch=сигнал не доказательство). Resource-fix (MuPDF destroy+emptyStore: 548→1020 parsed) — реальное исправление, не переклассификация.
