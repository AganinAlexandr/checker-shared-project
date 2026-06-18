# T-20260618-018: Title→GIP extraction handoff (GIP positive-control corpus)

Status: open
Project: DocSpectrum
Owner: codex (generator — runs per token-balancing decision 18-06)
Reviewer: opus
Related: `HC-20260618-013`, `HC-20260618-012`, `HC-20260617-011`, `RP-20260618-024`

## [2026-06-18 · opus] Handoff: extract authoritative org+GIP from title pages

`[PLANNING]` Метки в `Капремонт_Объекты.xlsx` (доп_2/проектировщик) могут быть устаревшими → **титулы авторитетны** (HC-013). Прогнать титул-экстракцию по корпусу GIP-позитив-контроля.

### Вход
- Пул кандидатов: `E:/output/DocSpectrum/gip_corpus_candidates_v0.csv` (229 объектов 2025; поля: номер, dev_ГИП(доп_2), орг, группа/подГруппа=вид работ). СТАРТОВЫЙ отбор, не ground-truth.
- Детектор титулов: `detect_title_zone` (`pp87-checker/tools_heading/title_zone.py`, импорт по DEC-004) → cover_end/title_pages/anomaly.

### Задача
Для каждого бандла (после выгрузки explorer): 
1. `detect_title_zone` → титульная зона + **кол-во титулов (2 vs 4)**.
2. Из текста титулов извлечь роли ГИП: **ГИП-проектировщик**; при **4 титулах** — также **ГИП СУБПОДРЯДЧИКА (реальный автор)** + ГИП заказчика (HC-013). Цель атрибуции почерка = субподрядчик.
3. Орг (проектировщик) — тоже с титула (таблица может врать).
4. Учесть кодировку (corruption-tolerant, как в детекторе) при парсинге ФИО/ролей.

### Выход (предложение схемы)
`object → {n_title_pages, org_title, gip_proektirovshchik, gip_subcontractor(если 4), gip_author(=субподрядчик если есть, иначе проектировщик), section, work_type(группа/подГруппа), date, dop2_xlsx, dop2_matches_title(bool)}`
+ отчёт расхождений доп_2 vs титул (data-quality).

### Приоритет ячеек
1. **Пилот: Оборина ОВ vs ГВС** (УУиР, УЖЕ выгружена) — Схема 2, метод можно гонять сразу.
2. Кровельный факторный: скатная/плоская × {Локтев/Сергеев/Ефимов/Бородин/Егупов/Савченков} (Схема 1).
3. **Cross-org Сергеев** (плоская в 3 орг, скатная/фасад в 2) — отделение орга от ГИП.

### Затем (opus ревьюит)
GIP-control анализ: within/cross по ГИП × вид работ × орг, **на орг-авторском residual** (provenance-first HC-012), гипотезы H1 (within-worktype same-GIP>diff-GIP) / H2 (cross-worktype same-GIP>baseline). Декомпозиция ГИП=стиль vs контент-согласованность.

Open: пилот Оборина запускать до или параллельно с выгрузкой широких ячеек? (opus: пилот сразу — метод дешёв, данные есть.)
