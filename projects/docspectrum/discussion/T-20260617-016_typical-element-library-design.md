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
