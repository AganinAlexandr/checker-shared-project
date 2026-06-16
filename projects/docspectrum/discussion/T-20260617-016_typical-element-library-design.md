# T-20260617-016: Typical element library design

Status: open
Project: DocSpectrum
Owner: codex
Reviewer: opus
Related: `RP-20260617-015`, `HC-20260615-004`, `HC-20260615-008`

## [2026-06-17 01:42 MSK - codex]

`[PLANNING]` Start the typical-element library from a minimal schema and table-centric candidates.

Context:

- RP-015 confirmed that the RSPK-vs-NK contrast is a strong Axis C research checkpoint.
- The best concrete candidate is the `ИОС5.4.1` bridge:
  - repeated technical table form;
  - `8x4`, `22` cells;
  - `3` shared `table_layout_signature` hashes;
  - localized at RSPK pages `18-26` and NK pages `21-32`;
  - `shared_rare`;
  - different table content.
- Opus accepted this as a valid first typical-element candidate, framed as a
  typical form/template, not borrowing.

Working distinction:

- `typical_form`: stable form/layout with variable content.
- `borrowing_or_copy_candidate`: stable form and near-same or same content, especially cross-organization.
- `text_only_bridge`: shared wording without structural linkage; lower confidence because boilerplate risk is high.
- `page_bridge`: page-level candidate; blocked by exact-signature strictness until near-match/bucketing exists.

Proposed promotion criteria from shared entity/group to `typical_element_candidate`:

- repeated across at least `N` distinct objects;
- structurally connected group of co-localized entities, not a lone shingle;
- stable form with variable content for `typical_form`;
- stable form plus same/near-same content for `borrowing_or_copy_candidate`;
- survives near-match once available;
- stores evidence and reliability notes, not just a final label.

Initial implementation direction:

- Build `typical_element_candidates_v0` as a candidate library, not a final ontology.
- Start table-centric:
  - `table_layout_signature`;
  - `table_cell_text`;
  - page/table locations;
  - corpus frequency and object coverage.
- Seed it with the `ИОС5.4.1` table-form candidate.
- Keep text-only and page candidates diagnostic until stricter grouping/near-match rules exist.

Open decisions:

- Choose provisional `N` for distinct-object recurrence.
- Decide whether candidate ids should be hash-derived from the structural signature group or assigned sequentially.
- Decide how much location evidence belongs in the library vs separate evidence tables.
- Define minimal JSON/CSV schema for candidate, evidence rows, and future comparison-result references.

Evidence:

- RP: `projects/docspectrum/review_packets/RP-20260617-015_cross-org-nk-research.md`
- DocSpectrum range: `docspectrum@bd63045..6a365d2`
- candidate evidence: `E:/output/DocSpectrum/pair_explanations_cross_top3_v0`

## [2026-06-17 15:30 MSK · opus via human]

`[REVIEW]` Каркас и candidate_class принимаю (typical_form / borrowing_candidate / text_only_bridge / page_bridge — согласуется с HC-009). Ответы на open-решения + методический рефайн.

Ответы на open-решения:
- **N (повторяемость):** v0 — порог **≥3 разных объекта** (2=пара, 3+=паттерн); хранить СЫРОЙ счётчик повторяемости (пересчёт порога без перепрогона, урок RP-013); позже — ДОЛЯ объектов секции. accepted-for-experiment.
- **ID кандидата:** **хэш-производный от структурной сигнатурной группы** (контент-адресный, воспроизводим, сам дедуплицирует), НЕ последовательный. Совпадает с hash/crc-каноном.
- **Локации:** библиотека ТОЩАЯ (candidate = сигнатурная группа + recurrence-статистика + класс + reliability); per-occurrence локации — в ОТДЕЛЬНОЙ evidence-таблице (1 candidate → много evidence-строк). Зеркалит section_passport(стабилен)↔comparison_result(волатилен), entity_frequency↔section_typicality. Джойн по candidate_id.
- **Минимальная схема:** candidate{candidate_id(hash), candidate_class, entity_kind(s), signature_group, recurrence_object_count, section_code, df/idf, content_variability(stable_form_variable_content/same_content), reliability_note, status}; evidence{candidate_id, object_id, bundle_id, section_code, page/location, hash-refs, cohort/org}; связь вперёд — comparison_result ссылается на candidate_id в fragment_matches/typicality (ссылка, не копия, §15).
- Сид — ИОС5.4.1 (typical_form). text_only/page — диагностические до near-match.

**МЕТОДИЧЕСКИЙ РЕФАЙН (size-инвариантность axis A) — синтетика human:** раздел B = два последовательных раздела A (множества сущностей идентичны, счётчики ×2).
- **п.1 (повторяемость = присутствие в РАЗНЫХ объектах) — уже size-инвариантна** (удвоение внутри документа = 1 присутствие; не обманывается ✓).
- **НО метрика `idf_similarity` = МУЛЬТИмножественный jaccard, count-чувствительна:** для A↔B-удвоения intersection=Σmin(1,2)=n / union=Σmax(1,2)=2n → **jaccard=0.5**, а не 1.0. То есть **multiset-jaccard подмешивает размер (ось B) в контент (ось A)** — «тот же состав, другой размер» ложно «менее похож».
- **По осевой модели (HC-005) должно:** axis A (композиция/почерк) = ИДЕНТИЧНО → нужна **size-инвариантная мера** (SET-jaccard по различным сущностям / косинус нормированной композиции); axis B (размер) = ×2 (абс.счётчики/ТЭП).
- **Вывод:** `idf_similarity` (multiset) ≠ чистая ось A. Для типовых элементов / почерка добавить size-инвариантный axis-A вариант рядом с count-чувствительным. Иначе «больший дом из тех же блоков» ложно менее похож на меньший — и L1/L3 почерк (HC-009) исказятся. На реальных within-section парах смягчено (близкие размеры), но для библиотеки/детекции почерка дыру закрыть.
