# Plan — 0601 Identification Strategy + Empirical Reconfiguration

**Status:** DRAFT (awaiting approval)
**Date:** 2026-06-01
**Author:** Xiao Han / Claude
**Branch:** codex-migration
**Sources to read (read-only):** `/Users/xiaoo/Desktop/0525/`, `/Users/xiaoo/Desktop/0527/`
**Output destination:** `/Users/xiaoo/Desktop/0601/`

---

## Context

Paper 1 (Dingyou, Bureaucratic Careers, Qing) has two existing external pipelines:

- **0525** — data construction (Stata 01–08 + 4 R files). Builds `03_panel.dta` (445,543 person-years, 20,953 officials). Keeps all concurrent postings via `03_panel_long.dta` and a "highest-rank-per-cell" main panel with `had_concurrent_posting` flag. Validated; **do not modify.**
- **0527** — DiD analysis (Stata 01–09 + 6 R files). Implements TWFE, Matched DiD, CS-DiD (`control_group(never)`), Sun-Abraham on five samples (full, rg≤6, rg≤3, 巡撫, no-concurrent). Validated; **do not modify.**

The user wants a **re-cut** of the DiD evidence under five changes, with all output in **`/Users/xiaoo/Desktop/0601/`** and the existing 0527 report carried forward (text preserved, structure re-organised, results re-run):

1. Open with a clean **identification-strategy section** — what the control group is, what the first vs second difference identifies, why pre-trends look bad when late-treated officials sit in the control mean.
2. **Show the first difference before the DiD** — pre-vs-post within-treated, then within-control, then the second difference, then DiD with FE and controls (step by step).
3. **Replace `rank = 14 - rg` with `rank = -ln(rg)`** as the primary outcome everywhere. Re-run full + all subsamples + event studies.
4. **One staggered-DiD table** stacking five estimators (TWFE, CS-DiD, Sun-Abraham, BJS, dCDH) per sample, plus one combined event-study figure per sample. Reference k = −1, k ∈ {0,1,2} greyed out (27-month dingyou window), post-return path on k ≥ 3.
5. A **standalone 兼職 chapter**: institutional context, dedup-rule sensitivity, no-concurrent robustness, and treatment heterogeneity by `had_concurrent_posting`.

The unifying purpose is to reconfigure the identification + evidence so the reader can trace the DiD operator step by step and audit the control-group construction explicitly.

---

## Approach

### A. Workflow-config updates (light touch — most things are already project-fit)

The Phase-1 exploration of `/Users/xiaoo/claude-code-my-workflow` found CLAUDE.md, rules, hooks, templates, and settings already customised for this dissertation (no placeholders). Only three small additions are needed; everything else stays.

| File | Change |
|------|--------|
| `CLAUDE.md` → §Paper 1 Working Locations | Add three rows for the 0601 pipeline (code, report, executive-summary path). |
| `MEMORY.md` (root, committed) | Add `[LEARN:dingyou-method]` entries: (a) primary outcome is `-ln(rg)`; (b) five-estimator staggered-DiD stack; (c) first-difference exhibit precedes DiD in identification sections. |
| `data-manifest.md` | Add §8 "Paper 1 — 0601 pipeline" mirroring §6/§7 (inputs, outputs, refresh date). |
| `quality_reports/session_logs/2026-06-01_0601-identification-reconfig.md` | New session log per `session-logging.md` (post-plan trigger). |
| `quality_reports/plans/2026-06-01_0601-identification-reconfig.md` | Copy this plan file into the repo on approval. |

**Not changed:** all `.claude/rules/*`, all `.claude/agents/*`, all `.claude/skills/*`, all `.claude/hooks/*`, `templates/*`, `.claude/settings.json`. They are generic-template files and already correct.

**Deferred:** Paper 2/3 data-manifest entries are still `[TBD]` — not blocking 0601 work; leave for a later session.

---

### B. 0601 pipeline scaffolding

```
/Users/xiaoo/Desktop/0601/
├── code/
│   ├── 00_setup.do                     # adapt path globals from 0527; install new estimators
│   ├── 01_prep_did.do                  # COPY of 0527/01 with ONE change: rank = -ln(rg). Keep all logic, flags, drops, sanity checks. Variable label updated.
│   ├── 02_first_difference.do          # NEW. Step-by-step DiD decomposition exhibit.
│   ├── 03_did_full.do                  # adapted 0527/02_did_full.do (rank now -ln(rg))
│   ├── 04_did_subsamples.do            # adapted 0527/03_did_subsamples.do (rg≤6, rg≤3)
│   ├── 05_did_xunfu.do                 # adapted 0527/04_did_xunfu.do
│   ├── 06_staggered_all.do             # NEW. One sample at a time, run TWFE/CS-DiD/SA/BJS/dCDH, export aggregate-ATT + event-study coefs to CSV.
│   ├── 07_concurrent.do                # NEW. 兼職 chapter: dedup sensitivity, robustness, heterogeneity.
│   ├── 08_robustness.do                # adapted 0527/06_robustness.do (R1–R9; outcome now -ln(rg)).
│   ├── 09_heterogeneity.do             # adapted 0527/07_het_interactions.do (parent type × outcome).
│   ├── 10_vacancy_chains.do            # adapted 0527/05_vacancy_chains.do (kept as-is, vacancy outcomes unchanged).
│   └── master.do                       # orchestrator: 00 → 10 → R figures.
├── code/  (R scripts)
│   ├── fig_first_diff.R                # NEW. Means panel + first-diff event study (treated only, control only).
│   ├── fig_es_combined.R               # adapted 0527/fig_combined_eventstudy.R — now overlays 5 estimators.
│   ├── fig_es_grid.R                   # NEW. 2×2 grid: Full, rg≤6, rg≤3, 巡撫 (single estimator panels).
│   ├── fig_concurrent_dedup.R          # NEW. Dedup-rule sensitivity (4 rules side-by-side).
│   ├── fig_lifecycle_xunfu.R           # adapted from 0527.
│   ├── fig_qing_map.R                  # adapted from 0527.
│   └── fig_network.R                   # adapted from 0527.
├── output/
│   ├── tables/                         # all .tex
│   ├── figures/                        # .pdf + .png
│   └── audit/                          # .csv diagnostic exports
└── report/
    ├── identification.tex              # the carried-forward 0527 report, restructured (see §D below)
    └── identification.pdf              # latexmk -xelatex artifact
```

**Copy strategy:** start by `cp -R 0527/* 0601/` then mutate in place. Preserves all content (per the user's "be smart, maximize preservation" directive). Original 0525/0527 untouched.

---

### C. Code changes — specifics

#### C.1 Outcome change (`01_prep_did.do` line 142)
- From: `gen rank = 14 - rg`
  - label: `"Rank grade (higher = more senior; rank = 14 - rg)"`
- To: `gen rank = -ln(rg) if rg > 0 & rg < .`
  - label: `"Rank score (higher = more senior; rank = -ln(rg))"`
- `delta_rank`, `promoted`, `demoted` definitions stay (still operate on the new `rank`).
- Propagates everywhere `rank` is used downstream — no other edits needed except table/figure axis labels and report wording (§D).

#### C.2 First-difference exhibit (`02_first_difference.do`, NEW)
Goal: visibly walk through the DiD operator before regressions.

Outputs (`output/tables/tab_first_diff.tex`, `output/audit/first_diff_means.csv`):
1. **Level means** by group × period:
   - Treated, pre-window (k ∈ [−5, −1]): mean rank.
   - Treated, on-leave (k ∈ [0,2]): mean rank (informational, not used downstream).
   - Treated, post-return (k ∈ [3, +5]): mean rank.
   - Never-treated, matched-year pre-window: mean rank.
   - Never-treated, matched-year post-window: mean rank.
2. **First differences** (within-unit):
   - ΔRank_treated = mean(post-return) − mean(pre-window), per official, then averaged. With SEs clustered by official.
   - ΔRank_control = mean(post-window) − mean(pre-window), per official, then averaged. Matching the calendar window on the control side using the cohort distribution of the treated (matched-window control mean).
3. **Second difference (raw DiD)**: ΔRank_treated − ΔRank_control. Reported without controls — this is the textbook DiD point estimate.
4. **Progressive DiD specifications** (single rank-score outcome, full sample):
   - Spec (i): raw DiD (no controls, no FE). Just the 2nd difference.
   - Spec (ii): + year FE.
   - Spec (iii): + individual FE (this is TWFE; equals the spec in `02_did_full.do`).
   - Spec (iv): + keju_bg, banner, entry rank, career-stage controls.

Visualisation (`code/fig_first_diff.R`): two-panel plot — left = mean rank profile for treated only (raw event study, no comparison); right = same for never-treated using calendar-year alignment.

This directly addresses the user's "我想看一下第一次差分的结果 再看did step by step的".

#### C.3 Staggered-DiD master script (`06_staggered_all.do`, NEW)
- Install packages once in `00_setup.do`: `ssc install did_imputation, replace`, `ssc install did_multiplegt_dyn, replace`, `ssc install eventstudyinteract, replace` (already used by 0527), `ssc install drdid, replace`, `ssc install csdid, replace`.
- Loop over five samples: Full, rg≤6, rg≤3, 巡撫, no-concurrent.
- For each sample, run:
  - **TWFE**: `reghdfe rank post i.keju_bg, absorb(group_id year) vce(cluster group_id)`.
  - **CS-DiD**: `csdid rank, ivar(group_id) time(year) gvar(gvar) method(reg) control_group(never)` → aggregate `estat simple`, `estat event` for ES coefficients.
  - **Sun-Abraham**: `eventstudyinteract rank lag* lead*, cohort(gvar) control_cohort(ever_dingyou==0) absorb(group_id year) vce(cluster group_id)`.
  - **BJS imputation**: `did_imputation rank group_id year gvar, fe(group_id year) horizons(0/10) pretrends(8)`.
  - **dCDH**: `did_multiplegt_dyn rank group_id year post, effects(10) placebo(8) cluster(group_id)`.
- Export per-estimator CSV: `es_coefs_{estimator}_{sample}.csv` (event-time, coef, se, lci, uci).
- Export aggregate ATT to `agg_atts_{sample}.csv` (estimator, ATT, SE, N).
- Build a single LaTeX table `tab_staggered_all.tex` with rows = estimator, columns = sample.

#### C.4 Concurrent-positions chapter (`07_concurrent.do`, NEW)

Four sub-analyses:

1. **Dedup-rule sensitivity** — rebuild the analysis sample under four rules using `03_panel_long.dta` upstream:
   - Rule A (current): highest-rank per (group_id, year), `_rg_sort` ascending.
   - Rule B: lowest-rank per cell.
   - Rule C: mean rg per cell (continuous outcome).
   - Rule D: all concurrent rows kept (treated as separate person-year-posting observations; cluster on group_id).
   - Run TWFE + CS-DiD under each. Output: `tab_concurrent_dedup.tex` + `fig_concurrent_dedup.pdf`.
2. **No-concurrent robustness** — restrict to `had_concurrent_posting==0` (already coded in 0527/06; carry forward and report on -ln(rg) outcome).
3. **Heterogeneity** — interact `post × ever_concurrent` (official-level: official ever held a concurrent post in their career). Report ATT for ever-concurrent vs never-concurrent groups.
4. **Within-treatment heterogeneity** — for the treated group only, split into those whose dingyou occurred while holding a concurrent post vs not. Compare ATT.

Outputs: `tab_concurrent_dedup.tex`, `tab_concurrent_het.tex`, `fig_concurrent_dedup.pdf`, `fig_concurrent_het.pdf`. ~3 tables + 2 figures, matching the user's "depth + heterogeneity" choice.

#### C.5 Event-study standards (applies to all R figures)
- x-axis: event time k.
- y-axis: -ln(rg) coefficient (label: "Rank score (-ln(rg))").
- Reference: k = −1 (omitted in all regressions).
- Grey band: k ∈ {0, 1, 2}, displayed with shading and no point estimate (the 27-month dingyou window).
- CI: 95% pointwise.
- Combined figure: 5 estimators overlaid on one panel with distinct colours + the Morandi palette already used (per `r-code-conventions.md`).
- Grid figure: 2×2, one panel per sample (Full, rg≤6, rg≤3, 巡撫). For the user's "把多种event study的图放到一起" request.

---

### D. Report restructuring (`report/identification.tex`)

Start by copying `0527/report/did_analysis.tex` → `0601/report/identification.tex`. Preserve all prose. Re-organise sections, rewire \input{} paths, swap outcome labels (14−rg → −ln(rg)), and re-run tables/figures. Approximate target structure:

```
1. Introduction                        [carry from 0527 §1, lightly updated]
2. Setting & Data                      [carry from 0527 §2]
3. Identification Strategy             [NEW — assembled from scattered 0527 paragraphs]
   3.1 Treatment definition (dingyou, 27-month window, k = year − first_dy_year)
   3.2 Control group: who is in, who is dropped (the 0000000;1111111 discussion)
   3.3 First difference: what within-treated trajectories identify
   3.4 Second difference: what comparison to never-treated identifies
   3.5 Identifying assumptions (parallel trends, no anticipation, SUTVA, 奪情 attenuation)
   3.6 Controls (keju_bg, banner, entry rank, era, etc. — explicit list)
4. First Difference Results            [NEW chapter using 02_first_difference.do outputs]
5. DiD Results                         [restructured 0527 §4]
   5.1 Full sample — staggered-DiD master table
   5.2 Event studies by sample (2×2 grid)
   5.3 Subsamples (rg≤6, rg≤3, 巡撫)
6. Concurrent Positions (兼職)         [NEW chapter using 07_concurrent.do outputs]
   6.1 Institutional context
   6.2 Dedup-rule sensitivity
   6.3 Subsample robustness
   6.4 Heterogeneity
7. Robustness                          [carry from 0527 §5, outcome relabelled]
8. Heterogeneity (parent type)         [carry from 0527 §4.x]
9. Vacancy Chains                      [carry from 0527 §x — unchanged]
10. Conclusion & Caveats               [carry from 0527 §6, append -ln(rg) interpretation note]
```

Compilation: `cd /Users/xiaoo/Desktop/0601/report && latexmk -xelatex identification.tex` (matches 0527 convention).

---

### E. Master orchestration

`/Users/xiaoo/Desktop/0601/code/master.do`:

```stata
do "00_setup.do"           // path globals + install five DiD packages
do "01_prep_did.do"        // build did_panel.dta with rank = -ln(rg)
do "02_first_difference.do"// first-difference exhibit
do "03_did_full.do"        // TWFE main spec
do "04_did_subsamples.do"  // rg≤6, rg≤3
do "05_did_xunfu.do"       // 巡撫 sample
do "06_staggered_all.do"   // five-estimator stack across five samples
do "07_concurrent.do"      // 兼職 chapter
do "08_robustness.do"      // R1–R9 with new outcome
do "09_heterogeneity.do"   // parent-type interactions
do "10_vacancy_chains.do"  // vacancy outcomes (unchanged)

// R figures
shell Rscript fig_first_diff.R
shell Rscript fig_es_combined.R
shell Rscript fig_es_grid.R
shell Rscript fig_concurrent_dedup.R
shell Rscript fig_lifecycle_xunfu.R
shell Rscript fig_qing_map.R
shell Rscript fig_network.R
```

Run end-to-end from one command: `cd /Users/xiaoo/Desktop/0601 && stata -b do code/master.do`. After it completes, compile the report.

---

## Critical files to consult (read-only)

| File | What we use it for |
|------|---------------------|
| `/Users/xiaoo/Desktop/0527/code/01_prep_did.do` | Template for new `01_prep_did.do`; change line 142 only. |
| `/Users/xiaoo/Desktop/0527/code/02_did_full.do` ff. | Templates for spec, sample filters, FE structure. |
| `/Users/xiaoo/Desktop/0527/code/06_robustness.do` (line 890 area) | The `had_concurrent_posting==0` filter pattern. |
| `/Users/xiaoo/Desktop/0527/code/08_three_estimators.do` | Matched-DiD weight construction, CS-DiD invocation pattern. |
| `/Users/xiaoo/Desktop/0527/code/09_staggered_rg6_robust.do` lines 21–23 | Existing `-ln(rg)` definition (validated). |
| `/Users/xiaoo/Desktop/0525/code/03_year_panel.do` | Concurrent-posting flag definitions; do not modify. |
| `/Users/xiaoo/Desktop/0527/report/did_analysis.tex` | Source prose for new `identification.tex`. |
| `.claude/rules/dingyou-pipeline-conventions.md` | Canonical panel/treatment/control conventions; non-negotiable. |
| `MEMORY.md` lines 84–111 | Method decisions of record (post=k≥3, CS-DiD `method(reg)`, matched-DiD weights). |

---

## Verification

End-to-end checks before declaring done:

1. **Pipeline runs clean.**
   - `stata -b do /Users/xiaoo/Desktop/0601/code/master.do` exits with no errors.
   - All log files in `output/audit/` show `end of do-file`.
2. **Funnel reconciliation table updated.** Counts in `tab_sample_funnel.tex` trace from `03_panel.dta` rows → final analysis sample with every drop accounted for. (Per `dingyou-pipeline-conventions.md` §4.)
3. **Outcome propagation.** `grep -rn "14 - rg\|14-rg" /Users/xiaoo/Desktop/0601/` returns no live code (only comments referencing the old definition for context).
4. **Reference-period audit.** For every event-study `.csv` in `output/audit/`, confirm row `k=-1` has coef = 0, SE = . (omitted reference). Confirm rows `k ∈ {0,1,2}` are present but flagged for greying in the R script.
5. **Estimator count.** `output/tables/tab_staggered_all.tex` has 5 rows (TWFE, CS-DiD, SA, BJS, dCDH) × 5 columns (samples). All 25 cells populated or marked `--` with reason.
6. **Concurrent-chapter outputs.** Four files present: `tab_concurrent_dedup.tex`, `tab_concurrent_het.tex`, `fig_concurrent_dedup.pdf`, `fig_concurrent_het.pdf`.
7. **Report compiles.** `latexmk -xelatex identification.tex` produces `identification.pdf` with no LaTeX errors, no overfull-hbox warnings exceeding 10pt.
8. **Spot-check figures.** Open `fig_es_combined_full.pdf` and `fig_es_grid.pdf` to verify: 5 colours visible, k=−1 anchored at zero, k∈{0,1,2} greyed.
9. **Session log written.** `quality_reports/session_logs/2026-06-01_0601-identification-reconfig.md` summarises: scope, decisions (the 3 AskUserQuestion answers), files created, verification results.
10. **Quality score.** Run the rubric from `.claude/rules/quality-gates.md`. Target ≥ 90 (PR-ready); minimum 80 to ship.

If any check fails, the orchestrator loops (review-fix, max 5 rounds per `orchestrator-protocol.md`) before reporting back.

---

## Open questions / risks

- **BJS + dCDH package install:** requires network. If `ssc install` is blocked, fall back to a 4-estimator table (TWFE, CS-DiD, SA, Matched) and flag in the session log.
- **`-ln(rg)` interpretation in the existing prose:** every "promotion of one rank" sentence in the carried-forward 0527 text needs rephrasing. The proofreader agent (per `orchestrator-protocol.md`) catches these in the review phase.
- **奪情 (imperial exemption) officials** stay in the never-treated group — same as 0527. Mentioned in §3.5 as a known attenuation source. Not addressed by this reconfiguration (separate analysis).
- **Run time:** 0527's master.do takes ~25 minutes. 0601 will be longer (~40-50 min) given two extra estimators × 5 samples + concurrent-chapter loops. Acceptable for a batch run.
