# Session Log: 2026-07-13 — Dingyou Framing Package (slides + proposal + todo)

**Status:** COMPLETED

## Objective

Build the conceptual-framework strategy package for Paper 1 in `~/Downloads/JMP_1_DingYou/3_reports/5_framing/`: `slides.pdf` (framing strategy deck: story, dream top-5 intro ¶1–3, literature dialogue, 恢弘×微观, 张力/弹性), `proposal.pdf` (prose mirror), `todo.pdf` (decision tree + weaving plan + sequenced horizons). Copies of all three PDFs at the JMP root. Plan: `quality_reports/plans/2026-07-13_dingyou-framing-package.md` (APPROVED).

## Changes Made

| File | Change | Reason | Quality Score |
|------|--------|--------|---|
| `JMP_1_DingYou/3_reports/5_framing/slides.tex` → `slides.pdf` | NEW — 29-page beamer 16:9 framing deck (5 parts: story / engine / dream intro / literature / tension–elasticity / titles) | The user's requested strategy deck | 92/100 |
| `.../5_framing/proposal.tex` → `proposal.pdf` | NEW — 9-page prose mirror (narrative-strategy document; full bibliography, 23 entries) | The requested 文本 proposal | 92/100 |
| `.../5_framing/todo.tex` → `todo.pdf` | NEW — 4-page operational plan (status table, W1–W7 workstreams w/ kill criteria, decision tree, weaving plan, dated horizons) | The requested todo | 90/100 |
| `.../5_framing/refs.bib` | NEW — copied from R2 + 7 additions (AAK 2016, Bertrand–Schoar, Iyer–Mani, KKO 2019, Sng, Evans–Rauch, Dixit–Pindyck) | Lit-dialogue citations | — |
| `JMP_1_DingYou/{slides,proposal,todo}.pdf` | Root copies | Literal request: PDFs 在该文件夹中 | — |
| `JMP_1_DingYou/README.md` | +2 lines in folder-layout block for `5_framing/` | Keep the self-contained repo map accurate | — |
| Memory: `project_paper1_dingyou`, `reference_latex_cjk_env`, `MEMORY.md` | Framing-package block; new CJK/beamer gotchas | Persistence | — |

## Design Decisions

| Decision | Alternatives Considered | Rationale |
|----------|------------------------|-----------|
| English throughout | 中文为主; mixed | **User choice** (AskUserQuestion): liftable into the paper, committee-shareable |
| Dream intro = B-upside main + ¶3-A variant displayed side by side | ask which "perfect world"; write A-floor main | The two-¶3 structure *implements* the requested 弹性; respects the locked A-floor+B-upside frame |
| Slides cite plain author–year text (no natbib) | beamer bib machinery | Zero "?"-risk; proposal carries the bibliography |
| `3_reports/5_framing/` + root PDF copies; NOT wired into run_all | root sources; new top-level dir | Strategy docs ≠ pipeline deliverables |
| CLAUDE.md untouched | update Paper-1 rows | codex-migration has a large uncommitted migration in flight (07-04 audit warning) |

## Incremental Work Log

- Plan-mode traversal: README, CODE_MAP, R2 (intro/framework/mechanisms/conclusion/appendix), option-value model, R3 full, R4 full, governance memo, 07-04 strategic audit, 07-05 hardening plan, conventions §18.9–18.13. Confirmed the 2026-07-05 full rebuild completed cleanly (master.log clean end; 63 tables; 4 reports recompiled).
- Wrote slides.tex (29pp); fixed: 《》 tofu (CJK-Symbols block exits ucharclasses transitions even inside `\zh{}` → dropped 《》 per house precedent), STSong bold-italic warning (declare BoldItalicFont), 10 overfull boxes (flushleft trivlist padding; table widths vs the 14.2cm beamer text width; tikz scalebox).
- Wrote proposal.tex (9pp) + todo.tex (4pp); fixed float-drift dangling headings in todo (anchor sentences + labels); protected {C}hina/{I}ndia capitalization in added bib entries.
- Visual QA of all 42 PDF pages; two rounds of micro-polish (quote-box `\ignorespaces`, conversations-map line breaks).

## Learnings & Corrections

- [LEARN:latex] `《》` (U+300A/B) live in the CJK-Symbols ucharclasses block: the *exit-CJK* transition fires mid-`\zh{}` → tofu in the Latin font. Avoid 《》 in this toolchain (house precedent: "Qian Shifu's \zh{清代職官年表}").
- [LEARN:latex] beamer 16:9 @ 9mm margins = 14.2cm text width; a booktabs table's p-widths + 12pt/gap must total under it — "Overfull hbox in paragraph at ⟨table-end line⟩" means the *table*, not the prose.
- [LEARN:latex] `flushleft` inside beamer frames costs ~8pt of trivlist padding; `{\raggedright …\par}` is the frame-safe form.
- [LEARN:workflow] Identical overfull magnitudes across recompiles = the edited frame wasn't the culprit; map log line numbers to source before editing.

## Verification Results

| Check | Result | Status |
|-------|--------|--------|
| slides.pdf compile | 29 pages, exit 0, 0 overfull, 0 missing glyphs | PASS |
| proposal.pdf compile | 9 pages, exit 0, 0 overfull, 0 missing, 0 undefined citations (23 bib entries) | PASS |
| todo.pdf compile | 4 pages, exit 0, 0 overfull, 0 missing, 0 undefined (3 entries) | PASS |
| Visual QA | All 42 pages read; CJK renders; tables/TikZ intact; float-drift + hyphenation issues found and fixed | PASS |
| Numbers vs baseline_results.tex | 26.8/6.2, +20.6pp p=0.038, +8.6pp p=0.013, +5.6pp p=0.265, +0.21 p=0.42 N=78, MDEs 0.060/0.061–0.062/0.014, 1.7pp/0.006/±3%, 2,853/254/216/44/16, 259,002/38,815, gradient p=0.116/17 exits, 13%/14.7%, 9,599/20,774 | PASS |
| Root copies + README | 3 PDFs at JMP root; README layout block updated | PASS |

## Open Questions / Blockers

- [ ] Advisor ratification of the frame (2-week horizon; circulate slides+proposal)
- [ ] W1 roster GO/NO-GO after the pilot-reign digitization prices the job

## Next Steps

- Per `todo.pdf` §4: W2(a) exact-triple de-dup, W3 county v1, W1 roster sourcing (by Jul 27); roster-powered re-runs resolve the ¶3 choice (by Aug 24).
