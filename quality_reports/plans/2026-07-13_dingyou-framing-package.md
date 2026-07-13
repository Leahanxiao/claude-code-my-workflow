# Plan — Paper 1 (Dingyou) Conceptual-Framework Package: slides.pdf + framing proposal + todo.pdf

**Date:** 2026-07-13 · **Status:** APPROVED (user, 2026-07-13; language locked = English throughout)
**Harness plan file:** `~/.claude/plans/sunny-toasting-dragon.md`

## Context

User request: inside `~/Downloads/JMP_1_DingYou/`, build (1) **slides.pdf** on the *conceptual framework only* — the story, how to tell it, the dream first-three-paragraphs of a top-5 introduction, the literature/theory dialogue, the 恢弘理论 × 微观精妙 balance, the framing's 弹性 and 张力; (2) a matching **text proposal**; (3) **todo.pdf** — next steps + how to weave the unsettled threads. LaTeX sources stay alongside PDFs.

Anchored on the 2026-07-05 rebuild (clean master.log, 63 tables, 4 reports recompiled). Canonical spine (locked 07-04/07-05): **office over person** — caretaker margin certified (gov acting 26.8% vs 6.2% after deaths, +20.6pp, perm p=0.038; prefect proxy +8.6pp, p=0.013; death placebo clean); option-value model with 3 propositions; native-place null after reinstatement fix (+5.6pp, p=0.265); patron +0.21 uncertified (p=0.42, N=78); prefect selection + governance outputs tight nulls; career ATT no causal claim. Strategic frame: A-floor + B-upside (07-04 audit). Open items: 錢實甫《清代職官年表》 roster, group_id split-side de-dup (~13%), county build, relief records, duoqing recovery, F1–F7.

## Deliverables

New standalone folder `~/Downloads/JMP_1_DingYou/3_reports/5_framing/` (NOT wired into run_all):

| File | Output | Shape |
|---|---|---|
| `slides.tex` | `slides.pdf` | beamer 16:9, ~22–26 frames, custom-styled default theme, rp* palette |
| `proposal.tex` | `proposal.pdf` | article ~8–10 pp, prose mirror of the slides |
| `todo.tex` | `todo.pdf` | article ~5–6 pp, operational plan + weaving strategy |
| `refs.bib` | — | copied from `2_baseline_results/refs.bib` + additions |

Then copy the three PDFs to the `JMP_1_DingYou/` root; add one README line for `3_reports/5_framing/`.

## Slide outline (approved)

Part 0: title/reading guide · story in three sentences + one-breath version · one-story-not-six-results (revealed-preference move).
Part I: the institution (忠×孝, 27 months, 奪情 valve) · the 2×2 shock taxonomy (sudden × anticipated-return ρ) · the option-value model in one picture (V_A vs V_P, ρ*, three propositions) · what the model buys · dissertation spine (talent wedge, L_static+L_repro).
Part II: perfect-world claim matrix · dream intro ¶1–¶3 verbatim with craft annotations (B-upside world, real numbers) · exemplar benchmarking (Jones–Olken QJE 2005, Xu AER 2018, Fenizia ECMA 2022) · intro elasticity (¶1–¶2 invariant; two ¶3s).
Part III: four-conversations map (Weber state theory · personnel econ of the state · leaders/substitutability · Qing state capacity) · the four "we add" sentences · positioning sentence bank.
Part IV: zoom architecture (grand↔micro pairing rules + table) · 张力 as features (忠/孝, rule/discretion, office/person, null-rhetoric/positive-culture, thin-top evidence tension) · 弹性 as insurance (4 branches + invariants) · referee red team with pre-committed answers.
Part V: title + abstract-first-sentence candidates; committed vs open → todo pointer.

## Proposal outline

§1 story · §2 framework (institution → taxonomy → model → wedge spine) · §3 perfect-world intro (3 block-quote paragraphs + craft commentary + A-floor ¶3 variant) · §4 four conversations + contribution sentences · §5 恢弘×微观 method · §6 tensions & elasticities + red team · §7 commitments. Cross-references `4_research_proposal` (evidence prospectus) without duplication.

## Todo outline

§1 status table (certified/suggestive/tight-null/open, with numbers) · §2 decision tree: roster → patron p=0.42 + gradient power + province-level outcomes; de-dup phase 2; county (coarser strata; pooled 514); relief records; duoqing (dissertation) — each with trigger/effort/kill criterion · §3 weaving plan (编织): R2→JMP draft mapping per branch, intro invariants, demotions · §4 horizons (2-week / 6-week / semester), referencing R3 §4 networks program + F1–F7 · §5 hygiene (run_all cadence, stale-count sweeps, CLAUDE.md refresh deferred — codex-migration uncommitted).

## Build

House pattern: XeLaTeX; Times/Helvetica/Menlo + STSong via ucharclasses; ALL CJK wrapped in `\zh{}`; natbib (proposal/todo; slides use plain-text author-year, no bib machinery); rp* palette (#1F3A5F/#B23A48/#2E7D5B/#EEF3F9/#9DB8D6); `latexmk -xelatex`.

## Verification

Compile 0-error; log grep `Overfull|Undefined|Missing character`; visual QA of every page via PDF read; numbers cross-checked vs `baseline_results.tex`; root copies present; quality gate ≥80.

## Not touched

Four existing reports, pipeline code/data, run_all/master, CLAUDE.md (migration in flight).
