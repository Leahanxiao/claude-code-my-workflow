---
paths:
  - "scripts/stata/**/*.do"
  - "scripts/**/*.do"
---

# Stata Code Standards

**Standard:** PhD-level empirical work — reproducible, well-documented, defensible

---

## 1. Reproducibility Header

Every do-file must begin with:

```stata
version 17
set more off
set seed 20240101       // YYYYMMDD format; omit if no stochastic steps
capture log close
log using "$root/output/logs/my_analysis.log", replace text
```

---

## 2. Path Conventions

**Globals only — no hardcoded paths:**

```stata
// At top of master.do (sourced by all other do-files)
global root    "."
global data    "$root/data"
global raw     "$data/raw"
global clean   "$data/clean"
global instr   "$data/instruments"
global output  "$root/output"
global tables  "$output/tables"
global figures "$output/figures"
global logs    "$output/logs"
```

- Never use absolute paths (e.g., `/Users/...` or `C:\Users\...`)
- All scripts source globals from `scripts/stata/globals.do` or define at top

---

## 3. Panel Setup

```stata
// Always set panel before any XT commands
xtset city_code year

// Verify balance and coverage
xtdescribe
```

- Panel ID: `city_code` (numeric, authoritative crosswalk verified)
- Time ID: `year`
- Always run `xtdescribe` after `xtset` to confirm panel structure

---

## 4. Estimation

### HDFE Regressions

```stata
// Use reghdfe for high-dimensional fixed effects
reghdfe outcome regressor controls, absorb(city_code year) vce(cluster city_code)
```

### IV / Bartik

```stata
// Always use ivreg2 or ivregress 2sls for IV
ivreg2 outcome controls (regressor = instrument), ///
    absorb(city_code year) cluster(city_code) first

// Or with reghdfe + ivreg2 (via ftools):
ivreghdfe outcome controls (regressor = instrument), ///
    absorb(city_code year) cluster(city_code) first
```

**First-stage reporting is mandatory:**
- Report F-stat from first stage
- Rule-of-thumb: F > 10 (Stock-Yogo); prefer Montiel-Pflueger critical values
- Document exclusion restriction argument in script header comments

---

## 5. Clustering

- Default: cluster at **city level** (`vce(cluster city_code)`)
- If deviating (e.g., clustering at province), document reason in comment above regression
- Never run unclustered SE in panel regressions without explicit justification

---

## 6. Output

### Regression Tables

```stata
// Store estimates
eststo model1: reghdfe outcome regressor, absorb(city_code year) vce(cluster city_code)
eststo model2: reghdfe outcome regressor controls, absorb(city_code year) vce(cluster city_code)

// Export to LaTeX
esttab model1 model2 using "$tables/main_results.tex", ///
    replace label booktabs se star(* 0.10 ** 0.05 *** 0.01) ///
    title("Main Results") stats(N r2 F, labels("Obs" "R2" "F-stat"))
```

- All tables exported as `.tex` to `output/tables/`
- Keep `.log` files in `output/logs/`
- Use `estimates store` + `esttab` pattern consistently

---

## 7. IV / Bartik Checks

```stata
// After first stage: check instrument strength
weakivtest          // Montiel-Pflueger test (requires weakivtest package)
// Or: estat firststage (after ivregress)

// Assert weights sum to 1 per unit-year (in cleaning script)
bys city_code year: egen weight_sum = total(bartik_weight)
assert abs(weight_sum - 1) < 1e-6
```

**In script header, document:**
1. Source of shift-share weights (which census year, which industry classification)
2. Source of national-level shifts
3. Exclusion restriction argument (why instrument is valid)

---

## 8. China-Specific Pitfalls

| Pitfall | Impact | Prevention |
|---------|--------|------------|
| Inconsistent city codes across data sources | Panel breaks / spurious merges | Always verify `_merge==3` rate; use authoritative crosswalk |
| 4T stimulus assignment endogeneity | Biased OLS | Never use stimulus allocation as regressor without IV |
| CFPS attrition | Selection bias | Report attrition rates; test covariate balance on attritors |
| Prefecture vs county level mismatch | Aggregation error | Always verify geographic level before merge with `codebook city_code` |
| Year-of-census discontinuities | Spurious trends | Add census-year dummies or check breaks with `xtbreak` |

---

## 9. Code Quality Checklist

```
[ ] version 17 + set more off at top
[ ] log using before any estimation
[ ] All paths via globals (no hardcoded strings)
[ ] xtset before any XT commands
[ ] xtdescribe run to verify panel
[ ] Clustering documented at city level (or deviation justified)
[ ] First-stage F-stat reported for all IV
[ ] esttab output to output/tables/
[ ] quietly on verbose display commands
[ ] log close at end of script
```
