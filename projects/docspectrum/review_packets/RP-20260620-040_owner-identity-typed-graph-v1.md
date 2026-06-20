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


## [2026-06-20 · opus] REVIEW

`[AGENT-OK]` (typed owner-identity graph v1) — проверено против кода (`build_owner_identity_typed_graph_v1.py`) + артефактов, не по тексту пакета. Числа воспроизведены из JSON/CSV; тесты прогнаны.

**Воспроизведено точно:**
- 12 typed edges = 1 rename + 2 disclosed_network + 1 shared_gip_handwriting + 8 owner_or_template; 7 typed components; 3 unvalidated_attachment; p90=24.0 (Сфера=79 объектов → noise=True). Тесты 9/9 (4 v1 + 5 v0, прогнал оба файла — `OK`).
- Якоря: rename Комтех↔АО ССУ№3 (`handwriting_shared_gip_temporal_handoff`, handoff=0.4286); Ватага↔Спектр `disclosed_subcontract_network` (4t=17); Тиволион↔К1 — честный coverage gap (`matrix_pair_present=False`, не подогнан).

**Ответы на reviewer-questions (все ДА), с проверкой логики:**
1. **Приоритет таксономии не смешивает причинность** — да. `four_title` ПЕРВЫМ (раскрытая сеть ≠ скрытое владение, HC-013), потом rename (конъюнкция 3 сигналов), потом shared_gip, потом owner_or_template как fallback с СОХРАНЕНИЕМ обеих гипотез (owner ИЛИ template). Полезные гипотезы не отброшены.
3. **Сфера — корректно** template-noise-prone attachment: owner_or_template + unvalidated_attachment_to_core + noise=True (79≥p90=24), без shared-GIP и без 4-титула → не может расширять rename-ядро. ✓ Ровно исправление находки RP-039.
4. **Ватага↔СП Стройинвест ГРУПП — корректно** отделена: 4t=0 (не сеть), handoff=0.2917<0.40 (не rename), shared_gip=1 → `shared_gip_handwriting`; помечена как unvalidated_attachment к сетевому ядру, ядро НЕ мержит. ✓ Чистое разграничение «общий ГИП Сергеева» vs «раскрытый конвейер».

**НАХОДКА (Q2 — порог rename НЕ консервативен сам по себе):** порог 0.40 калиброван на ЕДИНСТВЕННОМ подтверждённом позитиве (handoff=0.4286) → выборка n=1, по сути не калибровка. Реальную консервативность несёт **конъюнкция** (handwriting ∧ shared_gip ∧ handoff), НЕ значение порога. Доказательство в самих данных: **РусСтройГрупп↔СитиГазСтрой handoff=0.5417 — ВЫШЕ самого подтверждённого rename (0.4286)**, но корректно удержан как owner_or_template ТОЛЬКО потому, что shared_gip=0. → Вывод: shared-GIP — несущий gate; порог 0.40 не трогать в изоляции (с одним якорем у него нет настоящей опоры). Это усиливает, а не опровергает дизайн v1; но в risks стоит явно записать «threshold n=1, gating держится на GIP-конъюнкции».

**Q5 — следующий пакет ИУЛ-валидация: ДА, с точным таргетом.** Per HC-015/016 ИУЛ = только валидационный слой (отменяется 01-03-2026, Минстрой 4420-КМ/14). Главная ценность ИУЛ ИМЕННО для v1: **8 owner_or_template рёбер конфлейтят «owner» vs «template»** — это ровно та неоднозначность, которую разрешает ИУЛ-персонал-overlap. Тест: добавить personnel-overlap → owner_or_template со СПЛИТом на (а) personnel-shared = owner-кандидат, (б) нет overlap, только похожее оформление = template/process. Если handwriting-типизированные ядра (rename+network) ≈ ИУЛ-персонал-кластеры → доказано, что долговечный метод воспроизводит истину без ИУЛ (HC-015 «главный выигрыш»). Доп.: измерить долю несоответствий ИУЛ↔почерк (UC-фальсификация, HC-015) — но это можно отдельным пакетом после disambiguation.

**Не блокирует.** Дизайн чистый, инварианты соблюдены (library_relative_typed_candidate_not_owner_verdict на всех компонентах; attachments не наследуют ядро; raw similarity v0 не пересчитан). СЛЕД: ИУЛ-валидация типизированных компонентов (таргет = разрешить 8 owner_or_template через personnel-overlap), затем — UC-фальсификация ИУЛ↔почерк.
