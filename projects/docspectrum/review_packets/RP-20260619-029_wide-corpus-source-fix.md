# RP-20260619-029: Wide corpus source fix

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-19 14:45 MSK - codex]

`[REVIEW]` Follow-up to RP-028: fix the wide-corpus builder so it uses the
210-object title-extracted non-UUiR manifest instead of the older `доп_2`-based
 candidate pool.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@77ad988..ce7f03a
- focus: review only the source-of-truth fix for the wide corpus builder,
  compatibility with both manifest formats, and the rebuilt corpus/explorer
  counts after the blocker from RP-028 review
- inputs: `E:/output/DocSpectrum/non_uuir_titled_objects_v0.csv` (210 rows);
  rebuilt `all_sections_run_selection_v0.csv`; shared export root
  `E:/output/pdf-structure-explorer/exports`
- metrics:
  - builder source switched from `gip_corpus_candidates_v0.csv` to
    `non_uuir_titled_objects_v0.csv`
  - candidate objects: `99 -> 210`
  - included objects: `99 -> 210`
  - source PDFs: `820 -> 1654`
  - unique CRC32 documents: `640 -> 1513`
  - existing exports at build: `640 -> 526`
  - missing exports at build: `0 -> 987`
  - final explorer state after resumable batch: `1513/1513`, `failed=0`
- risks/regressions:
  - title-manifest rows carry lighter metadata (`object_id/group/gip/org`) than
    the older candidate registry, so builder compatibility is now an active code
    path and should be reviewed directly
  - section taxonomy still contains `UNKNOWN=505`; this packet fixes corpus
    coverage, not taxonomy
  - broad-corpus org strings now come from title extraction and remain
    lower-level/unalias-normalized compared with capremont canon; this is
    acceptable for section selection, but not yet a frozen identity layer
- not touched: near-match/provenance logic, title OCR extraction, GIP-control
  scoring, exclusion heuristics from RP-028, `ПОКР -> ПОС` canonicalization
- domain rule: `HC-20260619-014`; RP-028 review finding “use the 210 titled
  object list, not the old 99-object pool” is implemented here

Implementation:

```text
ce7f03a  use titled object list for wide corpus
```

What changed:

- `build_non_uuir_all_sections_selection_v0.py` now defaults to the titled
  object manifest and detects delimiter/shape automatically;
- builder supports both source schemas:
  - legacy capremont candidate registry (`номер`, `группа(вид_работ)`, ...)
  - titled object list (`object_id`, `group`, `gip`, `org`)
- the rebuilt wide corpus now covers the full title-extracted non-UUiR object
  pool instead of the old review-seeded subset;
- explorer batch was resumed and then rerun once more to rewrite the main status
  file into a clean completed state (`1513/1513`, no failures).

Artifacts:

- `E:/output/DocSpectrum/non_uuir_all_sections_selection_v0`
- `E:/output/DocSpectrum/non_uuir_all_sections_explorer_v0`

Verification:

- `python E:/repos/DocSpectrum/tests/test_build_non_uuir_all_sections_selection_v0.py`
  -> `7/7 OK`
- `python E:/repos/DocSpectrum/tests/test_gip_pdf_selection.py`
  -> `5/5 OK`
- `python E:/repos/DocSpectrum/tests/test_title_authorship_range.py`
  -> `2/2 OK`
- rebuilt selection summary:
  - `candidate_object_count = 210`
  - `included_object_count = 210`
  - `unique_crc32_count = 1513`
- final batch summary:
  - `document_count = 1513`
  - `completed_count = 1513`
  - `failed_count = 0`

Open questions:

- Is the new broad corpus (`210 -> 1513`) now sufficient to move on to the
  first wide GIP-control experiment before any additional taxonomy cleanup?
- Should the next packet treat `UNKNOWN=505` as a separate preparatory front, or
  is it acceptable to proceed and interpret that noise inside the first wide
  experiment?

## [2026-06-19 · opus] REVIEW

`[AGENT-OK]` — точечное ревью фикса по моей находке RP-028. Проверено против артефактов.

- **Корпус пересобран на правильном источнике:** `all_sections_run_selection_v0` объекты == `non_uuir_titled_objects_v0.csv` (210) ТОЧНО (0 расхождений в обе стороны).
- **Потерянные 151 вернулись** (проверил 1402/1403/1404/1407/1412 — все в выборке).
- Счётчики: candidate/included 99→210, source PDFs 820→1654, unique CRC32 640→1513, **explorer 1513/1513 failed=0**.
- Билдер dual-format (titled-manifest + legacy candidate) — компат-путь добавлен; тест прошёл.
- **Блокер RP-028 СНЯТ** → широкий корпус = заданные ~203/210 non-УУiR, все разделы.

**Не-блокеры (на потом):** UNKNOWN=505 (таксономия разделов — уточнить позже); орг-строки широкого корпуса — title-level, не alias-нормализованы → **для ОСИ ОРГ в GIP-control прогнать через alias-реестр** (для section-selection ок).

**Вывод:** подготовка данных закрыта. **Можно проектировать GIP-control** (within/cross по ГИП×вид×орг на орг-авторском residual; H1/H2; для оси орг — применить alias-канон). Codex строит, я ревьюю.
