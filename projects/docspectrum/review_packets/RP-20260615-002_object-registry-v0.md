# RP-20260615-002: Object registry v0 and TEI layer

Status: reviewed
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-15 01:40 MSK - codex]

`[REVIEW]` Object-level registry builder for the Podolsk UUiR validation corpus.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@d64cc89..6986e9b
- focus: `tools/build_object_registry_v0.py`, object registry artifact contract, TEI extraction, OKEI unit decoding, same-address TEI consistency check, separation between object-level registry and section-level passport
- inputs: `E:/commons/DocSpectrum/Капремонт_дома_УУиР_Подольск.xlsx`, source folders under `E:/MSE_арх`, Minstroy `explanatorynote-01-06.xsd`
- metrics: `35` objects in Excel, `35` source folders found, `34` objects with TEI, `1683_25` missing required explanatory note XML, OKEI dictionary parsed with `588` units, `5` same-address groups, `1` inconsistent same-address group
- generated artifacts: `E:/output/DocSpectrum/object_registry_v0/object_registry_v0.csv`, `object_tei_overview_v0.csv`, `address_tei_consistency_v0.csv`, `object_tei_long_v0.csv`, `object_registry_v0.jsonl`, `summary.json`
- not touched: no PDF/explorer reprocessing, no N-object pair comparison, no section-passport schema changes, no UI/API changes
- risks/regressions: TEI canonical-name mapping is v0 heuristic; some TEI names vary by wording/case; `1683_25` remains incomplete until the proper explanatory note XML is restored; `1680_25` / `1684_25` same-address inconsistency is treated as source-PD designer error before expertise, not legitimate house difference; full generated CSV/JSON are intentionally outside git
- domain-rule: object registry is library-independent object context; `section_passport` must remain specific to a concrete section; OKEI codes must be decoded from Minstroy schema, not hard-coded as unexplained numbers

Open questions for reviewer:

- Is the artifact split acceptable: script and PQ in repo, generated registry under `E:/output/DocSpectrum`?
- Should normalized TEI fields stay in `object_registry_v0.csv`, or should all canonicalization live only in `object_tei_long_v0.csv`?
- Is the v0 canonical field set sufficient for the first architectural-nearness vector: `entrances_count`, `floors_count`, `apartments_count`, `length_m`, `width_m`, `height_m`, `footprint_area_m2`, `building_volume_m3`, `total_area_m2`, `build_year`?
- Is `address_tei_consistency_v0.csv` sufficient as the first quality-control artifact for same-address TEI mismatches?

## [2026-06-15 02:10 MSK · opus via human]

`[AGENT-OK]` Вердикт: чистый, хорошо отделённый объектный слой. Блокирующих багов нет, artifact split соблюдён, ОКЕИ декодируется из схемы Минстроя (не хардкод). Идея ТЭП как независимой доменной оси близости домов — ценная (объективная геометрия, не зависит от качества извлечения PDF → якорь для eval).

Проверено по факту (читал сгенерированный артефакт в `E:/output`, не только код):

- canonical-маппинг ТЭП крепкий: `34/34` по геометрии, `0` несмапленных имён. `apartments`/`build_year` редки (`4/34`) — их нет в большинстве ПЗ, не сбой маппинга.
- consistency-слой работает: 5 same-address групп, 4 consistent, 1 inconsistent.

Находки:

- `DOMAIN_RULE` (ЗАКРЫТО, не баг): «Симферопольская, 13» (1680 ОВ / 1684 ГВС) — пара комплектов ОДНОГО дома (вид=УУиР, подвид ОВ|ГВС), но ТЭП несовместимы (12эт/79кв/98505м³ vs 5эт/129кв/26072м³). Проверил: каждый XML взят из своей папки объекта (1680→17431.xml, 1684→17472.xml), ТЭП распарсены верно → это реальная ошибка проектировщика в ПД ДО экспертизы, а не баг инструмента и не коллизия адреса. По human: после-экспертизный апдеит такие расхождения устранит. Consistency-слой отработал по назначению.
- `QC` (рекомендация): добавить `mismatch_scope` в `address_tei_consistency` — «1-2 поля врозь» = вероятная опечатка проектировщика, «все поля несовместимы» = эскалация (чужой/мислейбл источник). Разведёт обычные ошибки от грубых.
- `METRIC_NORMALIZATION_MISSING` (превентивно, ответ на open-Q3): набор полей ок для ХРАНЕНИЯ, но nearness-вектор ТЭП НЕ должен быть raw cosine — магнитуды entrances~4 vs volume~10⁴ vs build_year~1980 задавят счётные; геом-коллинеарность (V≈footprint×высота, footprint≈длина×ширина) даёт избыточные оси. Повтор урока RP-001, хуже (4 порядка). Стандартизовать по полю (z-score) + раздельные подоси counts / geometry / year, относительные дельты.
- минор: consistency сравнивает строки `value_number or value_raw` → ложный mismatch если одно парсится, другое нет; сравнивать численно с допуском. `tei_canonical.setdefault` берёт первое при нескольких именах→один canonical.
- минор/масштаб: `find_explanatory_note_xml` читает байты каждого XML ради `<TEI>` (ок на 35); ОКЕИ-xsd хардкод версии `01.06` (будущие версии схемы не найдутся); xlsx-ридер только `sheet1.xml`+sharedStrings (без inlineStr) — флаг под новые Excel расширенного корпуса.

Ответы на open-вопросы:

- artifact split: ✓ принимаю (как RP-001 Q4).
- normalized в registry.csv vs только long: держать оба — `object_tei_long` авторитетен, `tei_norm_*` в wide = производное удобство; задокументировать.
- canonical field set для nearness: достаточен как хранилище; как метрика — стандартизовать + подоси (см. выше).
- `address_tei_consistency_v0.csv` как первый QC: да, достаточен и ценен; усилить `mismatch_scope` и численным допуском.

Доменные следствия для следующего HC/итерации: registry нужен флаг до/после-экспертизы (provenance); eval не должен брать pre-экспертизные ошибки как ground-truth; пара до↔после экспертизы = кейс для оси `version_diff`.

Evidence:

- review relay from Opus, 2026-06-15
- `docspectrum@d64cc89..6986e9b`
- verified artifacts: `E:/output/DocSpectrum/object_registry_v0/{object_tei_overview_v0,address_tei_consistency_v0,object_registry_v0}.csv`
