# RP-20260618-023: Page near-match triage v0

Status: open
Project: DocSpectrum
Generator: codex
Reviewer: opus
Strategic owner: human

## [2026-06-18 17:22 MSK - codex]

`[REVIEW]` Stable human-ground-truth queue for the 240 body-page candidates
accepted for calibration in RP-022.

- repo: github.com/AganinAlexandr/DocSpectrum
- local repo: E:\repos\DocSpectrum
- branch: main
- range: docspectrum@7c6a0f1..ca37f35
- focus: review label taxonomy, preservation of human input across rebuilds, body-only scope, PDF/page linkage, review ordering and Power Query transport
- inputs: RP-022 review shortlist, combined documents index, explorer object-view exports
- metrics: `240/240` candidates joined; `240/240` pairs have existing PDF paths; `30` high-overlap first-batch candidates (`17` ИОС5.4.1, `13` СМ); `210` partial-overlap candidates; all labels initially pending
- risks/regressions: labels are still empty; category boundaries require human calibration; CSV is authoritative while PQ is a read-only view; source paths are local-machine paths
- not touched: near-match scoring/thresholds, typical-element promotion, UC3 verdict logic, title-page near-matches, foreign-to-high triage, graphical signatures
- domain-rule: human labels are external ground truth and are never inferred by the generator; `borrowing_candidate` is a research routing label, not a legal conclusion

Purpose:

- Turn RP-022's research shortlist into a reproducible expert-review process.
- Keep human decisions outside generated artifacts.
- Preserve completed labels when the candidate queue is rebuilt.
- Prevent title-page normative convergence from contaminating body UC3
  calibration.

Canonical editable input:

`E:/commons/DocSpectrum/page_near_match_triage_labels_v0.csv`

Controlled labels:

- `borrowing_candidate`;
- `normative_form`;
- `estimate_boilerplate`;
- `shared_technical_content`;
- `false_positive`;
- `uncertain`.

Confidence:

- `high`;
- `medium`;
- `low`.

Generated queue:

`E:/output/DocSpectrum/page_near_match_triage_v0/page_near_match_triage_queue_v0.csv`

Each row contains both PDF paths/page numbers, object/cohort/section metadata,
structural and hash-only content evidence, plus the joined human-review fields.

Results:

```text
candidate rows:                         240
PDF path coverage:                      240
pending labels:                         240

rare-text high overlap:                  30
  ИОС5.4.1:                              17
  СМ:                                    13

rare-text partial overlap:              210
  ИОС5.4.1:                             143
  СМ:                                    67
```

Review ordering:

1. high-overlap before partial-overlap;
2. higher text-segment Jaccard;
3. higher structural near-match similarity.

Code / note:

- `tools/build_page_near_match_triage_v0.py`;
- `tests/test_page_near_match_triage.py`;
- `analytics/pq/PageNearMatchTriage_v0.pq`;
- `notes/page_near_match_triage_v0.md`.

Verification:

- `python -m unittest discover -s tests -p "test_*.py"`: `5/5 OK`;
- regression test proves an existing human label survives rebuild;
- invalid label values are rejected;
- generated queue checked: `240` rows, `240` pending, `240` valid PDF pairs;
- Cyrillic section/file names verified in the generated UTF-8 CSV;
- `git diff --check`: clean.

Open questions:

- Is the six-label taxonomy sufficient for the first 30-candidate calibration
  batch?
- Should `shared_technical_content` remain separate from `normative_form`?
- Is CSV-as-canonical plus PQ-as-view the right ownership boundary?
- After reviewing the first 30, should we freeze label guidance before
  processing the remaining 210?

Evidence:

- commit: `docspectrum@ca37f35`;
- range: `docspectrum@7c6a0f1..ca37f35`;
- labels: `E:/commons/DocSpectrum/page_near_match_triage_labels_v0.csv`;
- artifacts: `E:/output/DocSpectrum/page_near_match_triage_v0`;
- preceding review: `RP-20260618-022`.

## [2026-06-18 · opus] REVIEW

`[AGENT-OK]` — проверено против кода+артефактов. Инструмент/очередь, не сами метки (метки — следующий шаг human).

**Подтверждено:**
- **240/240 joined; ВСЕ 240 — cross-org (RSPK↔NK)** — прицельный UC3-субстрат (within-org самореюз исключён); **480/480 PDF-путей резолвятся на диске** (критично — human откроет каждый); 30 rare_text_high + 210 partial; ИОС5.4.1 160 / СМ 80; все same_section.
- **Сохранение ручных меток при ребилде — проверено в коде:** `load_labels` (стр.121) читает канон + валидирует метку (`raise` на неизвестную, стр.130); `build` (стр.174-200) мёрджит `existing_labels.get(candidate_id)` → ручной труд переживает ребилд, `review_status`=reviewed/pending. + regression-тест (5/5 OK) доказывает выживание метки. Применён clobber-урок.
- **Канон-CSV human-owned** (`E:/commons`, 240 pending), отделён от генерируемой очереди; PQ — view. Соответствует no_overwrite_user_data.
- **domain-rule ✓:** метки не инферятся генератором; `borrowing_candidate` = research-маршрутизация, не юр-вывод. Очередь несёт полную доказательную базу (структура + hash-only контент + PDF/страницы/parse_status + interpretation_note) для суждения human.
- `candidate_id` (sha1, стабилен) = ключ join, на котором держится сохранение. ✓

**Наблюдения:**
1. **Все 240 cross-org** — верный фокус (реальный вопрос заимствования между орг, не самореюз).
2. **Prior для калибровки (не пред-суждение):** СМ-пары вероятно в осн. `estimate_boilerplate` (смета-софт генерит near-identical текст); ИОС5.4.1 — микс `shared_technical_content`/`borrowing_candidate`/`normative_form`. Полезно как гипотеза при разметке.

**Ответы на open questions:**
1. **6-меточная таксономия для первых 30 — достаточна** (borrowing-цель + 3 причины «общее-но-не-заимствование» + false_positive + uncertain). Следить за консистентностью границы normative/shared_technical/boilerplate.
2. **shared_technical_content держать ОТДЕЛЬНО от normative_form — да:** normative_form = предписан ГОСТ/регламентом (нулевая оригинальность, сильное «оправдание»); shared_technical_content = одинаковое инж-содержание БЕЗ юр-предписания (двусмысленно — реюз/заимствование ИЛИ конвергенция, слабее оправдывает); estimate_boilerplate = генерация сметным софтом. Разные смыслы для оригинальности/заимствования → раздельно; дать письменные примеры.
3. **CSV-канон + PQ-view — верная граница владения** (human ground-truth в E:/commons, не перезаписывается, переживает ребилд — проверено).
4. **Заморозить label-guidance после первых 30 — да.** Калибровать на 30 high-overlap (чистейший сигнал) → вывести решающие правила + примеры на метку → заморозить → затем 210 partial единообразно. Стандартная дисциплина против дрейфа.

**Следующий шаг:** human размечает первые 30 → из результатов выводим label-guidance → заморозка → 210. Готов помочь сформулировать guidance по итогам 30.
