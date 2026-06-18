# T-20260618-017: Title-page method handoff to Codex + cross-test task

Status: open
Project: DocSpectrum
Owner: codex (generator)
Reviewer: opus
Related: `HC-20260617-011`, `RP-20260617-015`, `registries/title_pages_by_crc.csv`, `T-20260613-001`

## [2026-06-18 · opus] Handoff: title-page detector → DocSpectrum cross-test

`[PLANNING]` Метод идентификации титульных созрел (100% precise на 451, corruption-tolerant + OCR-on-suspect) → отдаём Codex в работу для title-cross-test.

### Доступ к методу (НЕ дублировать код — импорт по DEC-004)
- `title_zone.detect_title_zone(bundle: Path)` из `C:/Users/alexa/Cursor/Checker87 app/pp87-checker/tools_heading/` (`sys.path.insert` на эту папку; импортит `toc_text_pillar`).
- Вход: explorer-bundle-каталог (`text_segments.csv`, `page_summary.csv`). Выход: `{cover_end, title_pages, anomaly}`.
- Якорь: corruption-толерантный фаззи-матч CAPS-фразы «ПРОЕКТНАЯ ДОКУМЕНТАЦИЯ» (ловит порчу Ð/ö/þ без карты) + OCR-on-suspect fallback; SMETA-спецкейса нет (СПДС-2020: смета=ПД).
- Проверено opus: прогон по DocSpectrum NK exports → 196/196 high (6 СМ, ранее ложно image_title из-за порчи «Р»→«Ð», теперь корректны).

### Состояние данных (title-покрытие 475/475 по crc32, DEC-006)
- **RSPK 279** ← `registries/title_pages_by_crc.csv` (Checker, АВТО-РЕГЕНЕРИРУЕТСЯ через export_all из Checker-индекса — **NK туда НЕ писать, затрётся**).
- **NK 196** ← `projects/docspectrum/inputs/title_pages_nk_v0.csv` (DocSpectrum-владение, переживает Checker-реген).
- Джойн обоих по crc32 → 475 титульных, все high.

### Задача cross-test (Codex строит, opus ревьюит)
- Для каждой титульной (crc32 + `title_anchor_pages`/`cover_pages`) собрать **элемент-спектр титульных** (из DocSpectrum element_base), затем сравнить **within-org vs cross-org** → отделяет ли орг/ГИП-почерк на титульных.
- Обоснование (HC-20260617-011): титульные = низкий L3-след + контролируемая поверхность → чистый зонд орг/ГИП-почерка. Ожидание (RP-015): чистое орг-разделение (РСПК рамки+таблицы vs НК рамки+изображения).
- Инварианты ревью: hash-only; cohort-метки ПОСЛЕ скоринга (домен не в ядре); honest interpretation; near-match-пометка где exact.

### Параллельно — DocSpectrum encoding route (a)
Порча кодировки (Ð→Р/ö→ц/þ→ю, `encoding_status` ложно ok) бьёт НЕ только титулы, а ВЕСЬ текст-слой → деградированы хэши в `text_features`/corpus_freq/text-lib/coverage. **`text_features.normalize_text` += карта порчи**, перепрогнать текст-слои затронутых бандлов. (Титулы уже закрыты Checker-детектором; тела — нет.)

Open: порядок — cross-test сначала или encoding route(a) сначала? (route(a) портит почерк/coverage везде; cross-test титулов от него мало зависит — титулы уже чистые). Предлагаю параллельно: route(a) приоритетнее по охвату, cross-test независим.
