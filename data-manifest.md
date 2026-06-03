# Data Manifest

**Purpose:** Document all external data sources used in this dissertation.
This file is tracked in git. The `data/` directory is gitignored — never commit data.

**Last Updated:** 2026-06-02

---

## Data Directory Structure

```
data/                    # GITIGNORED (in-repo placeholder; live data lives outside the repo)
├── raw/                 # Immutable raw data — never overwrite
├── clean/               # Processed datasets (.dta, .parquet)
└── instruments/         # Bartik weights, IV construction files
```

Note: Active Paper 1 (dingyou) datasets currently live at `/Users/xiaoo/Desktop/丁忧/`,
`/Users/xiaoo/Desktop/0525/data/`, `/Users/xiaoo/Desktop/0527/data/`, and `/Users/xiaoo/Desktop/0601/data/` (see §0, §6, §7, §8).
Active Paper 2 (fertility) datasets live under the in-repo `data/raw/...` layout above.

---

## Data Sources

### 0. CGED-Q (China Government Employee Database — Qing) — *Paper 1 primary input*

| Field | Value |
|-------|-------|
| **Source** | Lee–Campbell research group, HKUST (compiled from 履歷 / Qing personnel records, including 大清縉紳全書 and related serials) |
| **Coverage** | All recorded Qing officials, 1644–1912 |
| **Local path (raw clean)** | `/Users/xiaoo/Desktop/丁忧/dingyou_clean.dta` |
| **Pipeline derivatives** | See §6 (0525 data construction) and §7 (0527 DiD analysis) |
| **Date last refreshed** | 2026-05-28 (last `master.do` run; see `/Users/xiaoo/Desktop/0527/master.log`) |
| **Key variables** | `obs_id`, `group_id` (official identifier), `wy` (record year), `履歷` (career-history text, traditional Chinese), `rg` (rank grade 1–13), `admin_level` (0–9), `isp` (posting-candidate flag), `is_governor` (巡撫 indicator), `post_province`, `post_circuit`, `post_prefecture`, `keju_bg` (exam background), `first_dy_year` (first dingyou year), `ever_dingyou` |
| **Scale** | ~38,815 distinct officials; ~2,684 dingyou-treated; ~20,953 in DiD panel after filters |
| **Notes** | Text encoded in traditional Chinese. Rank dictionary is maintained externally — do not redefine in code. Year imputation uses 5 tiers (see `01_id_and_cleaning.do` and `03_year_panel.do`); imputation tier matters for pre-trend interpretation. Coverage is non-random across provinces and eras — see `coverage_audit.tex`. Database is academically licensed; cite Lee/Campbell/Chen series. |

---

### 1. Economic Census (经济普查) — *Paper 2*

| Field | Value |
|-------|-------|
| **Source** | National Bureau of Statistics of China |
| **Coverage** | City-level firm data |
| **Waves** | 2004, 2008, 2013, 2018 |
| **Access** | [TBD — please fill: e.g., direct purchase, university license] |
| **Local path** | `data/raw/economic_census/` |
| **Date downloaded** | [TBD — please fill] |
| **Key variables** | city_code, industry_code, num_firms, total_employment, output, capital |
| **Notes** | Industry classification changes between waves — use crosswalk |

---

### 2. Housing Prices — *Paper 2*

| Field | Value |
|-------|-------|
| **Source** | [TBD — please fill: e.g., China Real Estate Index System / Wind / local government statistics] |
| **Coverage** | City-year panel |
| **Time span** | [TBD — please fill: e.g., 2000–2020] |
| **Access** | [TBD — please fill] |
| **Local path** | `data/raw/housing_prices/` |
| **Date downloaded** | [TBD — please fill] |
| **Key variables** | city_code, year, avg_price_rmb_sqm |
| **Notes** | Deflate with CPI before use. CPI data at `data/raw/cpi/` |

---

### 3. CFPS (China Family Panel Studies / 中国家庭追踪调查) — *Paper 2*

| Field | Value |
|-------|-------|
| **Source** | Peking University Institute of Social Science Survey |
| **Coverage** | Individual/household panel, nationally representative |
| **Waves** | 2010, 2012, 2014, 2016, 2018, 2020 |
| **Access** | Registration required at https://www.isss.pku.edu.cn/cfps/ |
| **Local path** | `data/raw/cfps/` |
| **Date downloaded** | [TBD — please fill] |
| **Key variables** | individual_id, household_id, year, num_children, fertility_intent, city_code, weight_indiv, weight_hh |
| **Notes** | Use individual weights for individual-level outcomes; household weights for household outcomes. Check attrition patterns before analysis. |

---

### 4. Bartik Instrument — *Paper 2*

| Field | Value |
|-------|-------|
| **Source** | Constructed from Economic Census + National Industry Statistics |
| **Type** | Shift-share IV for housing price variation |
| **Local path** | `data/instruments/bartik_weights.dta` |
| **Construction script** | `scripts/python/build_bartik.py` |
| **Base year** | [TBD — please fill: e.g., 2000] |
| **Industry classification** | [TBD — please fill: e.g., GB/T 4754-2002] |
| **Key variables** | city_code, year, bartik_iv, weight_sum_check |
| **Notes** | Weights must sum to 1 per city-year. Assertion in build script. Document exclusion restriction: national industry growth shifts uncorrelated with local fertility conditional on controls. |

---

### 5. CPI (Consumer Price Index) — *Paper 2*

| Field | Value |
|-------|-------|
| **Source** | National Bureau of Statistics of China |
| **Coverage** | Province-year or city-year |
| **Local path** | `data/raw/cpi/` |
| **Date downloaded** | [TBD — please fill] |
| **Key variables** | city_code (or province_code), year, cpi_index |
| **Notes** | Used to deflate housing prices to real values |

---

### 6. Paper 1 derived datasets — 0525 pipeline (data construction)

| Field | Value |
|-------|-------|
| **Source** | Built from §0 CGED-Q via `/Users/xiaoo/Desktop/0525/code/master.do` |
| **Local path** | `/Users/xiaoo/Desktop/0525/data/` |
| **Date last refreshed** | 2026-05-28 |
| **Reproducer** | `cd /Users/xiaoo/Desktop/0525 && stata -b do code/master.do` |
| **Pipeline order** | `01_id_and_cleaning.do` → `02_title_classification.do` → `07_location_extraction.do` → `03_year_panel.do` → `04_summary_analysis.do` → `05_jpe_tables.do` → `06_jpe_figures.do` → `08_panel_summary.do` → R figure scripts (fig3, fig6, fig7, fig8) |
| **Report** | `/Users/xiaoo/Desktop/0525/report/main.tex` → `main.pdf` (~2.0 MB, last built 2026-05-28 11:53) |
| **Key derivative `.dta` files** | `01_cleaned.dta` (~745 MB; parsed years, posting flags), `02_classified.dta` (~755 MB; rank + admin_level + location), `03_panel.dta` (~712 MB; official-year panel), `03_panel_level.dta` (~29 KB; level aggregates), `03_admin_level_counts.dta` (~3.5 KB) |
| **Key output tables (.tex)** | `tab8`–`tab11_*.tex` (panel summary), `tab_governors_summary.tex`, `tab_concurrent_posting.tex`, `tab_match_audit_era/admin.tex`, `tab_keju.tex` (all under `output/tables/`) |
| **Key output figures (.pdf/.png)** | `fig1`–`fig8*.{pdf,png}` (under `output/figures/`); audit CSVs under `output/audit/` (e.g., `governor_records.csv`) |
| **Notes** | Panel unit is `group_id` (official); time is `year`. Pipeline gitignored within this repo (`data/` excluded). Counts cascade across stages — see §6.8 of report and `[LEARN:dingyou-data]` count cascade in MEMORY.md. |

---

### 7. Paper 1 derived datasets — 0527 pipeline (DiD analysis)

| Field | Value |
|-------|-------|
| **Source** | Built from §6 derivatives via `/Users/xiaoo/Desktop/0527/code/master.do` |
| **Local path** | `/Users/xiaoo/Desktop/0527/data/` |
| **Date last refreshed** | 2026-05-28 |
| **Reproducer** | `cd /Users/xiaoo/Desktop/0527 && stata -b do code/master.do` |
| **Pipeline order** | `00_setup.do` → `01_prep_did.do` → `02_did_full.do` → `03_did_subsamples.do` → `04_did_xunfu.do` → `05_vacancy_chains.do` → `06_robustness.do` → `07_het_interactions.do` → `08_three_estimators.do` → R figure scripts (`fig_combined_eventstudy.R`, `fig_cs_eventstudy.R`, `fig_qing_map.R`, `fig_vacancy.R`, `fig_xunfu_lifecycle.R`, `fig_xunfu_network.R`) |
| **Report** | `/Users/xiaoo/Desktop/0527/report/did_analysis.tex` → `did_analysis.pdf` (~5.0 MB, last built 2026-05-28 11:53). Bibliography at `report/refs.bib`. |
| **Key derivative `.dta` files** | `did_panel.dta` (~422 MB), `xunfu_sample.dta` (~29 MB), `position_spells_xunfu.dta` (~50 KB), `vacancy_chains_xunfu.dta` (~90 KB), `treat_timing.dta` (~38 KB), `acting_governors.dta` (~91 KB), `coverage_by_province.dta` (~99 KB), `markov_transitions.dta`, `placebo_betas.dta`; transient `_tmp_match_*.dta` |
| **Key output tables (.tex)** | `tab_desc_full.tex` (balance), `tab_did_*.tex` (TWFE), `tab_vacancy_xunfu.tex`, `tab_robust_compare.tex`, `tab_three_est.tex`, `tab_het_*.tex`, `tab_coverage.tex` (all under `output/tables/`) |
| **Key output figures (.png/.pdf)** | `fig_combined_eventstudy*`, `fig_cs_eventstudy*`, `fig_qing_map_coverage`, `fig_qing_map_vacancy`, `fig_vacancy_duration`, `fig_xunfu_network`, `fig_xunfu_lifecycle`, `fig_placebo_distribution` (under `output/figures/`); event-study CSVs at `output/es_coefs_*.csv` and `output/cs_atts.csv` |
| **Consolidated executive summary** | `/Users/xiaoo/Downloads/research_summary.tex` → `research_summary.pdf` (3-panel landscape; rebuilt as part of any report-update cycle) |
| **Notes** | TWFE uses full sample; CS-DiD restricts to `gvar ∈ [1760, 1800]` and `year ∈ [1750, 1810]` — they are NOT on the same sample (see `[LEARN:dingyou-did]` in MEMORY.md). Default `csdid` control group is `notyet`, not `never`. Always cite weighted vs unweighted vacancy duration. |

---

### 8. Paper 1 derived datasets — 0601 pipeline (identification reconfiguration)

| Field | Value |
|-------|-------|
| **Source** | Built from §6 derivatives via `/Users/xiaoo/Desktop/0601/code/master.do`. Reads `03_panel.dta` and `02_classified.dta` from the 0525 pipeline; does NOT rebuild upstream. |
| **Local path** | `/Users/xiaoo/Desktop/0601/data/` |
| **Date last refreshed** | 2026-06-02 (reproducibility confirmed via `_verify_headline.do` selective spot-check) |
| **Reproducer** | `cd /Users/xiaoo/Desktop/0601 && stata -b do code/master.do` (full pipeline, ~30 min) OR `stata -b do code/_verify_headline.do` (headline-stats spot-check, ~5 min) |
| **Pipeline order** | `00_setup.do` → `01_prep_did.do` → `02_first_difference.do` → `02b_summary_stats.do` → `02c_first_diff_by_estimator.do` → `03_did_full.do` → `04_did_subsamples.do` → `05_did_xunfu.do` → `06_staggered_all.do` → `06b_common_support.do` → `07_concurrent.do` → `08_robustness.do` → `09_heterogeneity.do` → `10_vacancy_chains.do` → R figure scripts (`fig_rank_distribution.R`, `fig_first_diff.R`, `fig_first_diff_by_estimator.R`, `fig_es_combined.R`, `fig_es_grid.R`, `fig_concurrent_dedup.R`, `fig_qing_map.R`, `fig_xunfu_lifecycle.R`, `fig_xunfu_network.R`, `fig_vacancy.R`) |
| **Report** | Three files under `/Users/xiaoo/Desktop/0601/report/`: `identification.tex` (thin umbrella) + `bureaucratic_resilience.tex` (main paper, Module B) + `career_effects_appendix.tex` (fragile-ATT appendix, Module A). Bibliography at `report/refs.bib` (carried from 0527). |
| **Key derivative `.dta` files** | `did_panel.dta` (rank = -ln(rg) outcome, ~433 MB), `xunfu_sample.dta` (~50 MB), `treat_timing.dta`, `position_spells_xunfu.dta`, `vacancy_chains_xunfu.dta`, `acting_governors.dta`, `coverage_by_province.dta`, `markov_transitions.dta`, `placebo_betas.dta`, transient `_tmp_*.dta` |
| **Key output tables (.tex)** | `tab_first_diff.tex` (raw 2nd diff = 0.0560), `tab_first_diff_specs.tex` (progressive specs), `tab_staggered_all.tex` (5 estimators × 5 samples), `tab_common_support_estimators.tex` (cohort/calendar/event window enforced), `tab_concurrent_descriptive/dedup/robust/het.tex` (兼職 chapter), `tab_desc_*.tex` and `tab_twfe_*.tex` (per-sample), `tab_robust_compare.tex`, `tab_vacancy_xunfu.tex`, `tab_coverage.tex`, `tab_het_subgroups/parenttype/xunfu.tex`, `tab_lifecycle_xunfu.tex`, `tab_summary_stats.tex` |
| **Key output figures (.pdf/.png)** | `fig_first_diff*.pdf`, `fig_es_combined_*.pdf` (per-sample 5-estimator overlays), `fig_es_grid_csdid.pdf`, `fig_es_twfe_*.pdf`, `fig_concurrent_dedup.pdf`, `fig_vacancy_duration.pdf`, `fig_who_acts/comes/leaves.pdf`, `fig_xunfu_lifecycle/network/region_flows.pdf`, `fig_qing_map_*.pdf`, `fig_markov_transitions.pdf`, `fig_placebo_distribution.pdf`, `fig_coverage_timeline.pdf`, `fig_rank_distribution.pdf`. Event-study CSVs at `output/audit/es_coefs_*` and aggregate ATTs at `output/audit/agg_atts_*`. |
| **Notes** | **Supersedes 0527** for identification work. Primary outcome is `rank = -ln(rg)` (NOT `14 - rg`). Five staggered-DiD estimators stacked: TWFE, CS-DiD (`control_group(never)`, `method(reg)`), Sun-Abraham, BJS (`did_imputation`), dCDH (`did_multiplegt_dyn`). Common-support window: `gvar ∈ [1760, 1800]`, `year ∈ [1750, 1810]`, event window `k ∈ [-8, 10]`, treated must be observed at `k=-1` AND ≥1 of `k ∈ [3,10]`. Identification walkthrough: raw means → first diff → second diff → progressive specs (`02_first_difference.do` is the standard exhibit). 兼職 (concurrent positions) gets a standalone chapter. Report split into Module A (career_effects_appendix.tex — fragile, sample-sensitive ATT) + Module B (bureaucratic_resilience.tex — organizational resilience, the main paper). 0527 is frozen as the prior cut for comparison. |

---

## City Code Crosswalk — *Paper 2*

| Field | Value |
|-------|-------|
| **Purpose** | Harmonize city codes across different data sources and census waves |
| **Local path** | `data/raw/crosswalks/city_code_crosswalk.dta` |
| **Source** | [TBD — please fill: e.g., constructed following Asher & Novosad (2020) methodology] |
| **Notes** | Always merge other datasets to this crosswalk first. Check `_merge==3` rate. |

---

## Data Access Notes

- CFPS data requires signing a data use agreement
- Economic census data access should be documented [TBD — institution/license number]
- All raw data should be backed up to [TBD — e.g., university secure storage / external drive]
- Data last verified complete: [TBD — please fill]
- CGED-Q derivatives are reproducible from `/Users/xiaoo/Desktop/丁忧/dingyou_clean.dta` via the 0525/0527 master.do pipelines; do not rebuild from scratch unless raw upstream changes.
