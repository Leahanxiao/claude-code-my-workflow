---
paths:
  - "/Users/xiaoo/Desktop/0525/**"
  - "/Users/xiaoo/Desktop/0527/**"
  - "/Users/xiaoo/Desktop/0601/**"
  - "/Users/xiaoo/Downloads/research_summary*"
  - "/Users/xiaoo/Downloads/coverage_audit*"
---

# Dingyou Pipeline Conventions (Paper 1)

**Scope:** Paper 1 (Dingyou, Bureaucratic Careers, and State Capacity).
This rule composes with `stata-code-conventions.md` and `r-code-conventions.md`.
Where they conflict, **this file wins for paths matched above** — because the
generic Stata rule assumes city-year panels (Paper 2 / fertility) while Paper 1
is an official-year panel and has different cluster levels, control-group
defaults, and report-compilation paths.

---

## 1. Panel Structure

- **Unit:** `group_id` (one Qing official, persistent across years and postings)
- **Time:** `year` (also called `wy` in some intermediate datasets — never mix the two)
- Always set: `xtset group_id year` immediately after loading any panel `.dta`
- Run `xtdescribe` to confirm balance and gaps; record gaps in the script comment

The fertility-paper convention (`xtset city_code year`) **does not apply here**.

## 2. Globals & Paths

- Each pipeline's `master.do` defines: `$ROOT`, `$CODE`, `$DATA`, `$OUT` (and sometimes `$TABLES`, `$FIGURES`, `$LOGS`)
- All other `.do` files must use globals; never hardcode `/Users/xiaoo/Desktop/0525/...` outside `master.do`
- For R scripts, paths are derived relative to the script location or from environment variables set by `master.do` via `shell Rscript`

## 3. Clustering

- **Default:** `vce(cluster group_id)` for officer-level outcomes (rank, exit, promotion)
- **Province-level outcomes** (vacancy duration, state-capacity proxies): `vce(cluster post_province)`
- Any deviation must be documented in a one-line comment immediately above the regression

## 4. The Funnel Principle (THE most important rule)

Any number reported in the analysis (counts of officials, records, governor cells, vacancy spells, etc.) **must** be paired with:

1. The source `.dta` file it was computed from
2. The exact filter chain (the `keep if` / `drop if` / `bysort` sequence)
3. The distinct-vs-record distinction (records ≠ unique officials)

If a count appears in text or a table without provenance, treat it as a bug.

A single reconciliation table (the "funnel table") must be inserted in every report that cites multiple counts of the same concept — e.g., 1,715 (raw distinct governors) → 1,494 (with province) → 580 (DiD estimation sample) gets one row each.

## 5. Governor (巡撫) Definition

Canonical, from `0525/code/08_panel_summary.do` line 418:

```stata
gen is_governor = (regexm(履歷, "巡撫") & admin_level == 2 & isp == 1)
```

- **No rg threshold.** Adding `& rg <= 3` (or similar) silently shifts every downstream count.
- Any variant must be footnoted in the report AND re-traced through the funnel table.
- Acting governors (`acting_governors.dta`) are tracked separately in the 0527 pipeline; do not silently merge.

## 6. CS-DiD Control Group and Method

`csdid` (Stata package, friosavila/csdid_drdid) defaults to `control_group(never)`
(never-treated only). **Pass `control_group()` explicitly anyway** to be safe across
package versions:

```stata
csdid rank, ivar(group_id) time(year) gvar(gvar) method(reg) ///
    control_group(never)
```

**Method:** the canonical Paper-1 CS-DiD spec uses `method(reg)` (regression
adjustment with analytical standard errors), NOT the default `method(dripw)`
(doubly-robust). The reason is runtime: full-sample DR can take 30+ min and was
unstable on the staggered panel (260+ cohorts × 268 years). Any report sentence
saying "doubly-robust" is a bug unless the code is changed; the correct phrasing
is "regression-adjustment, analytical SEs".

If the report needs a DR robustness check, run it on the xunfu sample only
(small enough for `method(dripw)` to converge) and present it as a column in
the robustness table.

## 7. Vacancy-Duration Statistics

Two different statistics exist; always cite the one used and name the weight convention:

- **Weighted (system-wide):** `sum avg_vacancy [aweight=n_vacancy]` (see `05_vacancy_chains.do` line 388). High-turnover provinces dominate. ~1.4 years.
- **Unweighted (per-province mean):** simple `mean avg_vacancy` across 21 provinces (line 378). ~2.0 years.

If a report cites one, it must name the convention. If it cites the other, same.

## 8. Sample-Matched TWFE vs CS-DiD

When the report compares TWFE vs CS-DiD side-by-side (Table 15 in `did_analysis.tex`):

- TWFE on the **full sample** is the headline estimate
- A **second TWFE row on the same window the CS estimator uses** (`gvar ∈ [1760,1800]`, `year ∈ [1750,1810]`) is **required**, so the divergence between TWFE and CS is interpretable as estimator-driven, not sample-driven.
- The CS-DiD sample restriction is set in `0527/code/08_three_estimators.do` lines 259–261.

## 9. Report Compilation

```bash
# 0525 data-construction report
cd /Users/xiaoo/Desktop/0525/report && latexmk -xelatex -interaction=nonstopmode main.tex

# 0527 DiD analysis report
cd /Users/xiaoo/Desktop/0527/report && latexmk -xelatex -interaction=nonstopmode did_analysis.tex

# Executive summary
cd ~/Downloads && latexmk -xelatex -interaction=nonstopmode research_summary.tex
```

Each report uses standalone preamble inside the `.tex` (no `Preambles/header.tex` dependency — the dissertation slide preamble is for presentations, not reports).

## 10. What Lives Where

| Artifact | Path | Role |
|----------|------|------|
| Raw Academia Sinica IHP derivative | `/Users/xiaoo/Desktop/丁忧/dingyou_clean.dta` | Upstream input |
| 0525 pipeline | `/Users/xiaoo/Desktop/0525/code/` (Stata + R) | Data construction |
| 0525 report | `/Users/xiaoo/Desktop/0525/report/main.{tex,pdf}` | Methodology + descriptives |
| 0527 pipeline | `/Users/xiaoo/Desktop/0527/code/` (Stata + R) | DiD analysis |
| 0527 report | `/Users/xiaoo/Desktop/0527/report/did_analysis.{tex,pdf}` | Estimation + robustness |
| Executive summary | `~/Downloads/research_summary.{tex,pdf}` | 3-panel landscape consolidated |
| Coverage audit | `~/Downloads/coverage_audit.{tex,pdf}` | Standalone missing-data audit |

## 11. Quality Gates (Paper 1 specifics)

In addition to the rubric in `quality-gates.md`:

- **Critical (-100):** Any report cites a count without provenance → block commit
- **Critical (-50):** `csdid` call without explicit `control_group()` → block commit
- **Major (-20):** Vacancy duration cited without naming weighted vs unweighted
- **Major (-20):** TWFE vs CS-DiD comparison without matching-window TWFE row
- **Minor (-5):** Birth-year coverage cited without denominator (so 18.51% in 0525 and 30.6% in 0527 are always paired with `7,215/38,975` and `6,409/20,953`)

## 12. Re-run Discipline

The pipelines are large (~30 minutes for 0525 master, ~20 minutes for 0527 master). Do not re-run unless:

- An upstream `.do` or input `.dta` changed
- A reviewer requested a specific reproducibility check
- Tables/figures are stale (timestamps older than the source `.do` files)

A pure text-edit pass on a `.tex` report does **not** require re-running `master.do`; just `latexmk -xelatex`.

## 13. Treatment Indicator and Sample Definitions

**`post` indicator.** Defined as:

```stata
gen post = (k >= 3) if first_dy_year < .
```

So `post = 1` only at three-or-more years after the first dingyou (k ≥ 3).
**Years k = 0, 1, 2 are the on-leave window** (treated but transitional) and
are EXCLUDED from the post indicator — they appear only in the event-study
grey-band. Any table note claiming "post = at/after first dingyou spell"
is wrong; the correct wording is "post = three-or-more years after first
dingyou (k ≥ 3); k = 0–2 are on-leave grey-band, excluded".

**`ever_dingyou` unmatched-year handling.** Officials with `ever_dingyou == 1`
whose `first_dy_year` is unparseable from the raw `wy` field MUST be dropped
explicitly with a logged count in `01_prep_did.do`. Treating them as
never-treated controls contaminates the comparison group; silently dropping
them (current main-regression filter `gvar > 0 | ever_dingyou == 0` does this)
hides the loss. Use:

```stata
count if ever_dingyou == 1 & first_dy_year == .
local n_unparsed = r(N)
di "Dropping `n_unparsed' officials with ever_dingyou==1 but unparseable year"
drop if ever_dingyou == 1 & first_dy_year == .
```

## 14. Matched DiD Must Use Match Weights

`08_three_estimators.do` builds a matched panel with Mahalanobis k=3 nearest
neighbours via `psmatch2`, which writes `_weight`. The matched-DiD regression
MUST use those weights:

```stata
reghdfe rank post i.keju_bg [pw=match_w], ///
    absorb(group_id year) vce(cluster group_id)
```

Without `[pw=match_w]`, the estimator is "matched-support TWFE" (unweighted on
the restricted set), not "matched DiD". The report must use the weighted form
and label it accordingly.

## 15. R1/R3/R8 Robustness Must Be True DiD

R1 (narrow event window), R3 (pre-/post-Taiping split), R8 (alternative
windows) — and any future "subsample robustness" — MUST be true DiD with
sample restriction, never (a) event-time binning on the full sample (N
unchanged, defeats the purpose), nor (b) treated-only subsamples (drops
controls, no longer a DiD estimand). Correct pattern:

```stata
preserve
keep if <subsample-filter-for-treated> | ever_dingyou == 0
quietly reghdfe rank post i.keju_bg, ///
    absorb(group_id year) vce(cluster group_id)
local b_<name>  = _b[post]
local N_<name>  = e(N)
restore
```

N differs from baseline by construction. The robustness table note must say
"sample-restricted true DiD; never-treated controls retained; N differs from
headline".

## 16. Multi-Province Governor-General Jurisdictions

`07_location_extraction.do` collapses multi-province jurisdictions (兩廣,
兩江, 閩浙, 陝甘, 湖廣, 雲貴, 川陝, 東三省, 北洋大臣) into a single
`post_province` for backward compatibility. The original multi-province
text MUST be preserved as `post_jurisdiction` (string) and `is_multi_prov_jurisdiction`
(0/1) BEFORE the collapse, so governor / viceroy-level analyses that need the
true jurisdiction scope can recover it.

## 17. Concurrent Posting and the Highest-Rank Rule

`03_year_panel.do` deduplicates concurrent postings by keeping the highest-rg
record per (group_id, year) cell. This affects ~35% of cells. The flag

```stata
gen had_concurrent_posting = (n_concurrent_postings > 1)
```

MUST be persisted onto the analysis panel so DiD can run a `had_concurrent_posting == 0`
robustness column. Without it, the ATT cannot be defended against the
"we mechanically picked the highest rank during Taiping" criticism.

## 18. 0601-Specific Identification Conventions (Active Pipeline)

These supersede 0527 for any work under `/Users/xiaoo/Desktop/0601/`. They do
NOT apply retroactively to 0527 files, which remain frozen.

### 18.1 Five-Estimator Canonical Stack

Every headline staggered-DID exhibit reports five estimators, in this order:

1. **TWFE** — benchmark only. Susceptible to forbidden comparisons under
   heterogeneous timing. Demoted from main estimator in 0601.
2. **CS-DiD** (Callaway–Sant'Anna): `csdid ... , method(reg) control_group(never)`.
   Use `method(reg)` (regression adjustment + analytical SEs), NOT `method(dripw)`,
   for runtime reasons (see §6). Pass `control_group(never)` explicitly.
3. **Sun–Abraham**: `eventstudyinteract` with never-treated as the comparison cohort.
4. **BJS** (Borusyak–Jaravel–Spiess): `did_imputation`. Imputes untreated potential
   outcomes using never-treated cells AND treated officials' pre-treatment cells.
   It is NOT "never-treated only" — any report sentence saying that is wrong.
5. **dCDH** (de Chaisemartin–D'Haultfœuille): `did_multiplegt_dyn`.

Matched DiD (`psmatch2` + weighted `reghdfe`) is **demoted** to a 兼職-chapter
sensitivity check; it is no longer in the headline stack.

### 18.2 Outcome Rescaling

The 0601 outcome is

```stata
gen rank = -ln(rg)
```

NOT `14 - rg` (the 0527 convention). Higher values mean more senior positions.
A coefficient of 0.1 corresponds to roughly a 10 % rank improvement on a log
scale. Reports must label the outcome explicitly; legacy `rank_score` references
must be flagged.

### 18.3 Common-Support Window

The common-support table (`tab_common_support_estimators.tex`,
generated by `06b_common_support.do`) fixes:

- **Cohort window:** `gvar ∈ [1760, 1800]`
- **Calendar window:** `year ∈ [1750, 1810]`
- **Event window:** `k ∈ [-8, 10]`
- **Treated support:** observed at `k = -1` AND at least one `k ∈ [3, 10]`
- **Controls:** never-treated officials in the same calendar window

Purpose: separates estimator divergence from sample-window divergence. dCDH may
return `--` (estimator failure) on the strict window; this is documented in the
table footnote and is expected, not a code bug.

### 18.4 Identification-Walkthrough Order

DiD sections in 0601 reports follow this order, always:

1. Raw level means by group × period (treated vs. control, pre vs. post)
2. First difference per group (within-treated and within-control trajectories)
3. Raw second difference (the DiD operator), reported as a scalar with SE
4. Progressive regression specifications (year-FE only → official-FE → official-FE + controls)

`02_first_difference.do` produces `tab_first_diff.tex` and
`tab_first_diff_specs.tex` for this exhibit; column (i) of the specs table
**must** equal the raw second difference (0.0560 in the current build).

### 18.5 兼職 (Concurrent Positions) Standalone Chapter

Concurrent appointments are a substantive institutional margin, not a data
hygiene detail. The standalone 兼職 chapter contains four exhibits in this order:

1. Descriptive: prevalence of concurrent postings by sample (`tab_concurrent_descriptive.tex`)
2. Dedup-rule sensitivity: highest-rank / lowest-rank / mean-rank / all-rows-kept
   (`tab_concurrent_dedup.tex`, `fig_concurrent_dedup.pdf`)
3. `had_concurrent_posting == 0` robustness column (`tab_concurrent_robust.tex`)
4. Heterogeneity by `ever_concurrent` indicator (`tab_concurrent_het.tex`)

### 18.6 Report-Architecture Split (2026-06-02 onward)

The 0601 report is three files under `/Users/xiaoo/Desktop/0601/report/`:

| File | Role |
|------|------|
| `identification.tex` | Thin umbrella — abstract, central question, "two companion documents" roadmap, executive-summary table, bibliography. No tables/figures of its own. |
| `bureaucratic_resilience.tex` | **Module B (main paper)** — organizational/state-capacity story: institutional context, 巡撫 sample, vacancy duration, acting succession, markov transitions, coverage, who acts/comes/leaves. |
| `career_effects_appendix.tex` | **Module A (appendix)** — fragile-ATT story: first/second diff, 5-estimator stack, common-support comparison, 兼職 chapter. |

All three compile independently with `latexmk -xelatex`. Module A explicitly
acknowledges the modest, sample-sensitive ATT; Module B carries the load-bearing
thesis about how the Qing state absorbed mandatory personnel interruptions.

### 18.7 Verification Spot-Check

`code/_verify_headline.do` re-loads `did_panel.dta` and prints headline numbers
(sample N, raw 2nd diff, five-estimator ATTs from cached results). Run this
instead of full `master.do` when only confirming reproducibility — saves ~25 min.
