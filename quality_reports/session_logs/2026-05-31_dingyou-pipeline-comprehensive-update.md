# 2026-05-31 — Dingyou Pipeline Comprehensive Update (0525 + 0527 + research_summary)

## Goal

User ran a deep self-audit of two pipelines (`/Users/xiaoo/Desktop/0525` data construction; `/Users/xiaoo/Desktop/0527` DiD analysis) and the executive summary `~/Downloads/research_summary.tex`. They identified P0/P1/P2 issues and asked for a comprehensive fix + rerun + report-refresh in one session. Quality target: 90+/100.

## Approach (planned + approved)

- Spec-then-plan: clarified four method decisions via AskUserQuestion before drafting plan.
- Plan saved to `~/.claude/plans/frolicking-sleeping-pumpkin.md`; approved with allowedPrompts for Stata/R/LaTeX runs.
- Batched checkpoints: A (config) → B (0525 code) → C (0527 code) → D (report narrative) → CP1 → E.1 (0525 rerun) → E.2 (0527 rerun) → E.3 (LaTeX) → CP2 → F (research_summary) → CP3.

## Method decisions locked in

1. R1/R3/R8 robustness: re-defined as TRUE DiD (sample restriction, controls retained).
2. Matched DiD: WEIGHTED with `[pw=match_w]` (Mahalanobis kernel weights).
3. CS-DiD: keep `method(reg)` regression adjustment; update report wording to match (NOT "doubly-robust").
4. Sequencing: batched with explicit pauses between phases.

## Mid-session clarifications from user (after CP1 "go")

- Remove all paper citations from report narrative (CS-DiD, Goodman-Bacon, Roth, Cengiz, Roodman, Liu-Zhang, Jensen-Zhang).
- Don't lecture in long heuristic-implication paragraphs.
- Primary directive: report data structure and concerns concretely. Data should be what it is.
- Preserve as much existing report content as possible.
- Replace (in place) all reports; regenerate research_summary.tex → PDF.

## Files modified

**0525 code:** 02_title_classification.do (admin_level==9 ordering), 07_location_extraction.do (post_jurisdiction), 03_year_panel.do (had_concurrent_posting persisted), fig6_geo_maps.R + fig7_posting_maps.R + fig8_governors.R (opaque white bg, non-earth-tone palettes).

**0527 code:** 01_prep_did.do (explicit drop of ever_dingyou-unparsed + convention block), 04_did_xunfu.do (dynamic N in tab_lifecycle_xunfu note), 06_robustness.do (R1/R3/R8 true DiD + new R9 no-concurrent-posting + notes rewritten), 08_three_estimators.do (matched-DiD pw=match_w + control_group(never) explicit + estat event capture noisily + fallback CSV writer + table notes), fig_xunfu_lifecycle.R (dynamic subtitle, panel titles shortened), fig_qing_map.R (opaque white bg), fig_combined_eventstudy.R (dynamic caption, 4-panel margins).

**Reports:** 0527/report/did_analysis.tex (R1/R3/R8/R9 narratives, weighted matched DiD wording, regression-adjustment wording, citation removal, combined-figure caption, TODO marker block at §Robustness for post-rerun number reconciliation); ~/Downloads/research_summary.tex (citation removal so far).

**Workflow config:** MEMORY.md (7 new [LEARN] entries), .claude/rules/dingyou-pipeline-conventions.md (§6 corrected csdid default, new §13–§17).

**Obsolete:** xunfu_sample/xunfu_report.{tex,pdf,…} moved to xunfu_sample/_obsolete/ with OBSOLETE banner.

**Scratch cleanup:** 33 logs + 2 tex stubs from 0525, 13 logs from 0527 → _archive/.

## Current status

- Phase A–D complete.
- Phase E.1 (0525 master.do) running in background. Currently in 06_jpe_figures.do (after 01→02→07→03→04→05).
- Pending: E.2 (0527 master.do), E.3 (LaTeX recompile + reconcile inline numbers), F (research_summary update + PDF).

## Open questions / risks

- Several inline numbers in did_analysis.tex will need reconciliation post-rerun (TODO block at §Robustness lists them).
- R3b post-Taiping ATT may flip sign now that controls are retained — narrative will need adjustment.
- CS-DiD fallback CSV writer is new; need to verify it works on the actual rerun.
- 4-panel combined event-study figure needs visual check post-rerun.

## [LEARN] entries already saved to MEMORY.md

Method decisions, multi-province jurisdiction handling, concurrent-posting flag, post=k>=3 definition, true-DiD pattern for robustness, weighted matched DiD, CS-DiD method(reg) wording.

## E.1/E.2 Rerun Update

**0525 master.do**: clean exit. New audits captured. multi_prov_jurisdiction.csv shows 9 jurisdiction×province combos (湖廣→湖北 460 rec, 兩廣→廣東 195, 兩江→江蘇 186, 閩浙→福建 155, 雲貴→雲南, 川陝→四川, 陝甘→陝西, 東三省→奉天, 北洋大臣→直隸). concurrent_posting cells: 155,921/445,543 = 35.0%. Panel still 20,953 officials, 445,543 person-years. Officials with dingyou record now 2,711 (was 2,612 — slight increase, likely from improved title matching).

**0527 master.do — first rerun**: aborted at 08_three_estimators with r(621) on CS-DiD fallback (nested preserve issue). Robustness table (06) did complete with new TRUE-DiD numbers — KEY findings:
  - R1 narrow window: b=0.121 (was effectively 0.412 under fake window) — large shrinkage
  - R3a pre-Taiping: b=0.422, p<0.001 (stable)
  - **R3b post-Taiping: b=0.380, p=0.004** (was -0.054 insig under treated-only; the "Taiping era drives positive estimate" narrative is REFUTED)
  - R4a Banner: 0.228 insig; R4b Han: 0.372 sig — Han subsample drives the effect
  - R7 Tier-3: -0.507 (unchanged)
  - R8 optimal window: b=0.221 (was 0.412 under fake window)
  - **R9 no-concurrent: b=0.430, p<0.001, N=225,398** — concurrent-posting deduplication NOT driving headline

**Bug fix**: Patched 08_three_estimators.do CS-DiD fallback to use `file write` direct CSV emission (not preserve/clear/set obs). Rerunning full 0527 master.do.

**0527 — second rerun in progress**: 01-06 reproduced same numbers. Currently in 07_het_interactions / 08_three_estimators.

## Continuation: 2026-06-01 morning session (Q1–Q4 + PDF review fixes)

### Q1 Concurrent posts preserved
- 0525/03_year_panel.do: added 7 raw cell-level flags (`is_xunfu_yr_raw`, `is_zongdu_yr_raw`, `is_central6_yr_raw`, `is_grand_sec_yr_raw`, `is_grand_council_yr_raw`, `has_acting_record`, `has_concurrent_tag`) computed BEFORE dedup via `bysort group_id year: egen byte X = max(_hit_X)`. Also added 3 rank summaries: `rank_max_cell`, `rank_min_cell`, `rank_mean_cell`. Long panel saved to `03_panel_long.dta` (814,475 rows preserving all concurrent records).
- 0527/01_prep_did.do: uses `is_xunfu_yr_raw` for `ever_xunfu` instead of post-dedup `regexm(履歷, "巡撫")`.
- Empirical impact: 1,649 ever-巡撫 via raw flag vs 938 via legacy (+711, +76%). Governor DiD panel: 1,630 officials / 487 treated / 1,143 controls / 45,652 person-years (was 935 / 271 / 664 / 27,514).
- Governor TWFE shifted: 0.508 → 0.472 (slightly smaller). CS-DiD governor: 0.141 → **−0.259** (sign flip; SE 0.359; CI just excludes full-window TWFE).

### Q2 Coverage anatomy
- New panel "Why the Panel Looks This Way" in research_summary.tex. Explains avg ~1,656 person-years/year (445,543/269), avg 78 new officials/year (20,953/269); 知縣 coverage 2.3% of statutory 1,500 posts; governor coverage ~57% averaged across post-1680 period; spatial/temporal patterns; addressed (Tier-2, 30-year cap, multi-prov jurisdiction, raw flags) vs not addressed (parental-death timestamp, 奪情 year, province-year outcomes).

### Q3 CS-DiD event-study fix
- Root cause: `estat event` after csdid failed with rc=621 (preserve/restore conflict). Csdid 1.x column naming for `agg(event)` is `Tp<k>` / `Tm<k>`, not `Pre_<k>` / `Post_<k>`.
- Fix: replaced `estat event` call with second `csdid ... agg(event)` call directly; parser updated to handle Tp/Tm prefixes. Post-hoc rebuild `08b_csdid_event_fix.do` ran csdid agg(event) for all 4 samples.
- es_coefs_cs_{full,rg6,rg3,xunfu}.csv now contain 80–86 event-time rows each (was 1 line / header only).

### Q4 Implication block
- Added "Dingyou as a State-Capacity Probe" panel to research_summary.tex and Q6 to meeting_agenda.tex. Anchors on historical shocks (Taiping 1851–64, 大饥荒 1876–79, treaty-port openings 1840/1860, 義和團 1900). Three data-side measurements distinguishing bureaucracy-as-machine from individual-talent: (i) vacancy duration under shock vs normal rotation, (ii) successor entry-rank/jinshi share/prior-province experience by shock period, (iii) 奪情 concentration on officials with concurrent military titles. External merges required: tax remittance (地丁), relief (賑災), grain prices, exam quotas.

### PDF visual review (after first compile)
Reviewer flagged 10 issues across the 5 PDFs. All resolved:
1. **Figure 15 wild CS-DiD swings**: R script `fig_combined_eventstudy.R` now filters to k ∈ [−7, +10] and caps y-axis at ±1.5. Cap explained in caption.
2. **Figure 2 (main.pdf) 1735/1796 spikes unexplained**: caption updated to document all three era-midpoint Tier-2 spikes (1652, 1735, 1796).
3. **Figure 4 LaTeX escapes visible** (`$-$$1`, `\ residual`): Stata graph note in `06_jpe_figures.do:245-247` replaced with plain text "minus 1" and "incl. residual".
4. **Stale 1.007 / −0.000 heterogeneity** in 7 places: replaced with rebuild values 1.005 / −0.095 (Table 10 actual).
5. **Same-window range "0.096 to 0.170"** in research_summary: corrected to "−0.092 for governors, 0.071 to 0.170 for the other three".
6. **Floating "TWFE" artifact** in research_summary table: wrapped table in `\begin{center}` and added `\par` after footnote.
7. **Figure 15 caption stale**: replaced `estat event` references with `csdid ... agg(event)`; display-window note added.
8. **Table 12 vs funnel 1,494**: added explanatory note covering the 459-official gap (spell-collapse losses from missing year boundaries, acting-only officials, single-year province ranges).
9. **Two windows coexisting**: display window now stated explicitly in figure caption.
10. **Balance table note**: verified 28 + 148 = 176 decomposition note placement.

### Final state
- main.pdf: 48 pp / did_analysis.pdf: 51 pp / xunfu_sample_report.pdf: 14 pp / research_summary.pdf: 6 pp / meeting_agenda.pdf: 1 pp.
- 0525 pipeline: `ALL STATA STEPS COMPLETE`. 0527 pipeline: `ALL DONE`. 4 CS-DiD CSVs populated.
- All stale-number greps return 0 hits outside explicit "was X, now Y" historical-comparison notes.

### [LEARN] entries
- `[LEARN:concurrent-posting]` Raw concurrent-aware flags computed pre-dedup via `bysort … : egen byte X = max(…)` survive `keep if _n==1` unchanged because they're constant within the cell. Use this pattern for any flag that needs to see all concurrent records but where downstream code wants one-row-per-cell.
- `[LEARN:csdid]` csdid `estat event` is preserve-fragile inside `forvalues … preserve … restore` loops (rc=621). Workaround: re-run csdid with `agg(event)` to populate e(b)/e(V) directly and parse those instead of using estat. Column names are `Tp<k>` (post) and `Tm<k>` (negative), NOT `Pre_<k>` / `Post_<k>` as csdid help suggests.
- `[LEARN:figures]` ggplot `coord_cartesian(ylim=…)` is the right way to cap y-axis without dropping data; `scale_y_continuous(limits=…)` drops out-of-range CI ribbon endpoints and creates jagged edges. For event-study plots with sparse pre-period cells, the y-axis cap is more honest than dropping the cells outright.
- `[LEARN:stata-graphs]` Stata's `legend(order(N "text"))` does NOT process LaTeX escapes. Use plain text only: write "minus 1" or `−1` (unicode) not `$-$1`; write "incl. residual" not `incl.\ residual`.

## Continuation: 2026-06-01 afternoon session

### PDF visual review feedback round
Reviewer scan of all 5 PDFs identified 10 issues; all addressed in-session:
- Figure 15 (combined event study) CS-DiD pre-period swings → clipped to k ∈ [-7,+10] + ymax cap ±1.5 in `fig_combined_eventstudy.R`
- Figure 2 (main.pdf) unexplained spikes at 1735/1796 → caption rewritten to document all three era-midpoint Tier-2 spikes (1652, 1735, 1796)
- Figure 4 (main.pdf) visible LaTeX escapes (`$-$$1`, `\ residual`) → Stata `legend(order(...))` rewritten with plain text in `06_jpe_figures.do:245`
- Stale heterogeneity numbers `+1.007 / -0.000` (7 places) → updated to `+1.005 / -0.095` from rebuilt Table 10
- research_summary "0.096 to 0.170" same-window range → "-0.092 to 0.170" with governor row callout
- Floating "TWFE" table artifact → wrapped in `\begin{center}` + `\par` after footnote
- Figure 15 caption stale `estat event` → `csdid ... agg(event)` with display-window note
- Table 12 vs funnel 1,494 gap → 459-official explanatory note added
- Window choice ambiguity → display window stated explicitly in figure caption
- Balance table note → verified placement (28+148=176 decomposition)

### Title/author/format cleanup
- research_summary and meeting_agenda: removed author + date line, simplified titles ("Dingyou Project — Data Notes" / "Dingyou Project — Discussion Notes"), simpler panel labels ("Panel" / "Coverage" / "Results" / "Identification concerns" / "Data objects" / "Dingyou and local governance")
- Toned down "machine vs man" framing in Q4 implication block to measured descriptive language

### Dated-marker cleanup (no patches in narrative)
- Removed all "2026-06-01 update / rebuild" datestamps (8 places)
- Removed all "Previous build (post-dedup string match) was X / 271 / 664" comparisons (5 places)
- Removed all "(was 0.508 on old build)" parentheticals
- Replaced `\texttt{is_xunfu_yr_raw}`, `\texttt{post_jurisdiction}`, `\texttt{is_multi_prov_jurisdiction}`, `\texttt{03_panel_long.dta}`, `\texttt{position_spells_xunfu.dta}`, `\texttt{vacancy_chains_xunfu.dta}` with plain English ("concurrent-aware identification flag", "long-form panel", etc.)
- Rewrote 0525 main.tex §6.3.x as continuous narrative (no dated-update header)

### Added analytical content
- research_summary: new "Coverage — systematic questions" panel (8 bullets on joint missingness / clustering / 奪情 contamination / etc.) + "Filling in vs designing around it" with cost estimate
- research_summary: new "Treat and control under each DiD specification" panel — 12-row tabularx covering TWFE-full, CS-window, Matched, CS-DiD, Risk Set, R1, R3a/b, R4a/b, R7, R9 with treated and control set per spec
- meeting_agenda: compact versions of the same two new blocks

### New staggered DiD analysis (`09_staggered_rg6_robust.do` + `09b_sun_abraham.do`)
- New scope: rg ≤ 6 senior subsample, outcomes `rank_linear = 14 - rg`, `y_lnrg = -ln(rg)`, ordered rank for ologit/oprobit
- 6 estimators attempted: TWFE / CS-DiD / Sun-Abraham / BJS (`did_imputation`) / dCDH (`did_multiplegt`) / Ordered logit & probit
- Successful: TWFE × 2, CS-DiD × 2 (1750–1810 window, g ∈ [1760,1800]), Ordered logit, Ordered probit
- Failed: Sun-Abraham (single-threaded, 25 min wall-clock without converging — killed); BJS (rc=430 convergence); dCDH (`did_multiplegt` rewritten as a library, new syntax not yet adapted)
- Aggregate ATT highlights (rg ≤ 6 senior sample):
  - TWFE linear = **0.4915*** (matches existing rg≤6 number)
  - CS-DiD linear = 0.2568 (ns) — shrinks ~50% under heterogeneity-robust
  - TWFE log = **0.0606***
  - CS-DiD log = 0.0422 (ns)
  - Ordered logit = **0.7874***
  - Ordered probit = **0.4703***
- First-stage / Δ (k=3 on-impact):
  - **TWFE log at k=3 is significantly negative (-0.013, p<0.05)** — multi-year aggregate builds up after k=3
  - TWFE linear k=3: -0.046 (ns)
  - CS-DiD linear k=3: 0.310 (ns)
  - CS-DiD log k=3: 0.044 (ns)
- New PDF: `/Users/xiaoo/Downloads/staggered_did_rg6.pdf` (2 pages) with ATT table, first-stage table, event-study figure (TWFE vs CS-DiD faceted by linear/log)

### Final 5 PDFs state
- main.pdf: 48 pp
- did_analysis.pdf: 51 pp
- xunfu_sample_report.pdf: 14 pp
- research_summary.pdf: 8 pp (was 6; +2 for Coverage + Treat/control panels)
- meeting_agenda.pdf: 2 pp (was 1; new Treat/control + Coverage blocks pushed Other items to p.2)
- staggered_did_rg6.pdf: 2 pp (new)

### Chat-only substantive answers (not in any file but recorded here)
- 兼职 concurrent posting share: 35.0% of cells pre-dedup; 8.2% inversion rate
- 巡撫 sample retains all 巡撫? — No, the highest-rank dedup masks concurrent governor service when 兼衔 is more senior; raw flag recovers 711 additional officials (+76%)
- Year-0-to-xunfu spike audit: 415 officials with `years_to_xunfu == 0`, of which 320 (77%) cluster at era-midpoint years (1652 / 1729 / 1765 / 1796); 137 of these have dingyou-at-xunfu-start (matching user's "140") — confirms artifact hypothesis
- Why 269-year panel only has 445k obs / 1,656 active per year: panel is structurally senior-officials database; county (knu, rg=13) ~8.6% of statutory coverage; central + military 55% of all observations
- Pre-trend mechanics: immortal-time / length-biased selection + Tier-1 carry-forward + conditioning on k=-1 = in-service produces the smooth approach-to-zero pattern
- Positive post effect decomposition: survival selection + early-career sample driver + cohorts outside 1760-1800 + concurrent-title inflation + Tier-1 carry-forward
- Linear vs ln(rg) vs ln(rank): all three give different scales but same qualitative pattern; ordered logit / binary milestones (`ever_xunfu_after`, `ever_rg3_after`) are the cleaner alternatives
- "on leave" band: 大清會典 27-month rule → k=0,1,2 are the transition window where panel observations are Tier-1 carry-forward artifacts; post = k≥3 is the first unambiguous post-leave year

### [LEARN] entries added
- `[LEARN:staggered-DiD]` Stata's `did_imputation` (BJS) returns rc=430 on large-sample × many-cohort panels (couldn't fit convergence). Sun-Abraham via `eventstudyinteract` is single-threaded and unfit for ~292K obs panels — 25 min wall-clock without converging. For staggered DiD on Qing-scale panels, stick to TWFE + CS-DiD (`csdid` is parallel) + Ordered MLE; budget the heavier estimators or run on subsamples.
- `[LEARN:event-study-figures]` Era-midpoint clusters (1652, 1729, 1765, 1796, 1808) explain 77% of "year-0-to-position" artefact spikes in lifecycle figures. When users see suspicious year-0 spikes, check `xunfu_start` clustering at era-midpoint years AND check whether `first_panel_yr == xunfu_start` (the official appears in the database for the first time AT the imputed midpoint).
- `[LEARN:report-style]` Repeated dated markers ("2026-06-01 update", "previous build was X") read as patch history rather than a finished report. When a rebuild changes numbers, prefer rewriting the narrative to use only the current numbers; document the historical rebuild in a session log, not the report body.
