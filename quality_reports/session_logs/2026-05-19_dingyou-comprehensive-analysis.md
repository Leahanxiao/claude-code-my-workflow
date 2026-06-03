# Session Log: Dingyou Paper — Comprehensive Empirical Analysis
**Date:** 2026-05-19
**Project:** Paper 1 — *Dingyou*, Bureaucratic Careers, and State Capacity in Imperial China
**Data:** `/Users/xiaoo/Desktop/丁忧/dingyou_clean.dta` → `/Users/xiaoo/Desktop/report/`

---

## Session Goal
Build out the full empirical infrastructure for the dingyou natural experiment paper: event study figures, panel balance, central/local transitions, NYT DiD design, data quality analysis.

---

## Major Work Completed

### Do Files Written (all in `/Users/xiaoo/Desktop/report/do_files/`)

| File | Purpose | Status |
|------|---------|--------|
| `15_var_identification.do` | Variable identification rates, variation plots, exogeneity | ✅ Done |
| `16_event_study_v2.do` | Polished event study (coefplot, x-axis fix) | ✅ Done |
| `17_panel_balance.do` | Year×official panel balance diagnostics | ✅ Done |
| `18_transitions.do` | Career transitions C↔L, prefecture, province mobility | ✅ Done |
| `19_event_study_v3.do` | Manual twoway plot: k=-1 diamond, k=0 on x-axis | ✅ Done |
| `20_csdid_nyt.do` | Callaway-Sant'Anna NYT design (csdid + fallback DiD) | ✅ Done |
| `21_posting_classify_v2.do` | Enhanced central/local regex dictionary | ✅ Done |
| `22_robustness_checks.do` | Liuren placebo, birth cohort ES, age-at-dingyou test | ✅ Done |
| `23_missingness_detail.do` | Detailed analysis of why postings are unidentified | ✅ Done |
| `24_data_gap_1750.do` | 1740–1760 CGED-Q coverage gap documentation | ✅ Done |
| `25_stacked_did.do` | Stacked DiD implementation (NYT design, 119 cohorts) | ✅ Done |

### Reports Generated
- `0519all.pdf` — 50-page comprehensive report
- `updated_did.pdf` — 13-page standalone NYT DiD chapter

---

## Key Empirical Findings

### 1. Variable Identification
- Rank (rg): 61.5% identified. Non-identified = blank (7.4%), non-posting (8.7%), unmatched text (22.4%)
- Unmatched text: mainly acting/署 positions, geographic prefix variants, candidate/waiting states
- **Central/local "unclassified" ≈ same records as rank-unclassified**: not an additional failure

### 2. 1740–1760 Data Gap
- 1740s: 281 total postings; 1750s: 36 total postings (vs. 42,000 in 1760s)
- **CGED-Q coverage gap**, not a historical phenomenon
- Corresponds to 乾隆5–25年; jump in 1761 when systematic archive compilation began

### 3. Event Study v3 (manual twoway)
- **k=-1 shown as filled diamond** at y=0 (reference, coef=0 by construction)
- **k=0 labeled on x-axis** with red dashed vertical line ("Dingyou mourning leave, excluded")
- Pre-trend F = 11.57 (polynomial) vs 15.79 (binned deciles) → pre-trend is GENUINE, not polynomial artifact

### 4. Stacked DiD / NYT Design
- 119 cohort years, 1,693 treated officials, 12,228 NYT control officials
- Pre-trend F = 3.78 (p=0.004) — reduced 67% vs within-person but still significant
- Post-mourning coefficients (l=+1 to +5): **not significant** (−0.054 to −0.135)
- **Key contrast**: within-person k=1 = −0.58***, NYT l=1 = −0.054 (ns)
- Suggests within-person effect largely lifecycle, not pure mourning shock

### 5. Career Track Structure
- 京官 ↔ 外官: largely integrated (rotation intentional)
- 武职 ↔ 文官: mostly separate (文武分途); Banner officials exception
- Implication: should restrict ES to pure civil officials (type 1 or 2) for cleanest results

---

## Open Questions / Next Steps

1. **Pre-trend in NYT is still significant (p=0.004)**: next step = match on career stage (cpos) within stacks
2. **武职 contamination**: restrict to civil officials only in main specifications
3. **Restrict "prime career" mourning**: age 35–55 at first dingyou for most exogenous sample
4. **Cohort-specific ATT**: heterogeneity by mourning year (war periods vs. peace)
5. **Install drdid** for full Callaway-Sant'Anna estimator: `ssc install drdid, replace`

---

## Conceptual Clarifications Made

- **Within-person Control**: same official's pre-mourning trajectory (not a separate control group)
- **NYT Control**: officials who WILL also mourn but haven't yet in period t
- **Life cycle diagnostic**: polynomial vs binned deciles test whether pre-trend is functional-form artifact
- **Career track paths**: 京官↔外官 打通; 武职↔文官 基本隔离 (旗人 exception)
- **Central/local "unclassified"**: shares ≈90% overlap with rank-unidentified records; not independent failure

---

## Files Updated
- `/Users/xiaoo/Desktop/report/reports/0519all.tex` + `0519all.pdf` (50 pages)
- `/Users/xiaoo/Desktop/report/reports/updated_did.tex` + `updated_did.pdf` (13 pages)
- `/Users/xiaoo/Desktop/report/reports/updated_did.tex` + `updated_did.pdf`


---
**Context compaction (auto) at 10:59**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 14:15**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 15:21**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 15:50**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 19:40**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 22:56**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 08:16**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 09:49**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 21:59**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 08:17**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 09:38**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 09:56**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 11:02**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 11:34**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 13:37**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 14:56**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 17:55**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 18:49**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 19:17**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 20:03**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 10:13**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 10:49**
Check git log and quality_reports/plans/ for current state.


---
**Context compaction (auto) at 11:48**
Check git log and quality_reports/plans/ for current state.
