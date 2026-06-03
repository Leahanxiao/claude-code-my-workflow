# AGENTS.MD -- PhD Dissertation Project (Codex mirror of CLAUDE.md)

> **Note:** This file mirrors `CLAUDE.md` for Codex compatibility. Edit `CLAUDE.md` first, then sync the same change here. Do not maintain the two files independently — they will drift. Last sync: 2026-06-02.

---

# CLAUDE.MD -- PhD Dissertation Project with Claude Code

**Project:** Distortions as Social Reproduction Mechanisms — A Three-Paper Dissertation
**Author:** Xiao Han
**Institution:** University of Chicago, Department of Economics
**Year:** 2nd year PhD (2026)
**Branch:** main

**Unifying thesis:** Distortions are not merely efficiency losses; they are social reproduction mechanisms.
Macro distortions shape micro decisions, and micro decisions reproduce macro inequalities across generations.

**Three papers:**
| # | Title | Wedge | Domain | Status |
|---|-------|-------|--------|--------|
| 1 | *Dingyou*, Bureaucratic Careers, and State Capacity in Imperial China | Institutional talent wedge | Nation/State | Data in hand; do-files planned |
| 2 | Misallocation, Educational Involution, and the Fertility Trap | Capital wedge τ (4T stimulus) | Household | Draft framework complete |
| 3 | Tariff Policy, Firm–State Rent-Seeking, and the Fiscal State | Political tariff wedge | Firm/State | Proposal stage |

**Framework proposal:** `~/Downloads/proposal.pdf` and `~/Desktop/proposal.pdf` (LaTeX source: `proposal.tex` at same paths)

---

## Core Principles

- **Plan first** -- enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
- **Verify after** -- run code and confirm output at the end of every task
- **Data never in git** -- all data lives outside the repo in `data/` (gitignored); see `data-manifest.md`
- **Stata is primary estimator** -- panel regressions, IV, HDFE done in `.do` files
- **Reproducibility via do-files** -- every regression result must be reproducible from a do-file
- **Quality gates** -- nothing ships below 80/100
- **[LEARN] tags** -- when corrected, save `[LEARN:category] wrong → right` to MEMORY.md

---

## Folder Structure

```
dissertation/
├── CLAUDE.md                    # This file
├── data-manifest.md             # Tracked: documents external data sources
├── .claude/                     # Rules, skills, hooks
├── data/                        # GITIGNORED — never commit data
│   ├── raw/                     # Immutable raw data
│   ├── clean/                   # Processed datasets (.dta, .parquet)
│   └── instruments/             # Bartik weights, IV files
├── scripts/
│   ├── stata/                   # .do files (primary analysis)
│   ├── python/                  # Data pipeline / cleaning
│   └── r/                       # Figures, tables
├── output/
│   ├── tables/                  # .tex regression tables
│   └── figures/                 # .pdf/.png figures
├── paper/                       # LaTeX paper draft
├── Figures/                     # Slide figures (existing)
├── Slides/                      # Beamer .tex (existing, for presentations)
├── Preambles/                   # header.tex (existing)
├── Bibliography_base.bib        # Centralized bibliography
├── explorations/                # Research sandbox (see rules)
├── quality_reports/             # Plans, session logs, merge reports
└── templates/                   # Session log, quality report templates
```

---

## Commands

```bash
# Stata (run do-file)
stata -b do scripts/stata/my_analysis.do

# Python (data pipeline)
python scripts/python/build_panel.py

# R (figures/tables)
Rscript scripts/r/figures.R

# LaTeX (3-pass, XeLaTeX only — for presentation slides)
cd Slides && TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode file.tex
BIBINPUTS=..:$BIBINPUTS bibtex file
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode file.tex
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode file.tex
```

---

## Quality Thresholds

| Score | Gate | Meaning |
|-------|------|---------|
| 80 | Commit | Good enough to save |
| 90 | PR | Ready for deployment |
| 95 | Excellence | Aspirational |

---

## Skills Quick Reference

| Command | What It Does |
|---------|-------------|
| `/data-analysis` | End-to-end R data analysis workflow |
| `/review-r [file]` | R code quality review |
| `/review-paper [file]` | Study a source paper |
| `/lit-review [topic]` | Literature search + synthesis |
| `/research-ideation [topic]` | Generate research questions and hypotheses |
| `/interview-me` | Formalize a research idea into a structured spec |
| `/proofread [file]` | Grammar/typo/overflow review |
| `/compile-latex [file]` | 3-pass XeLaTeX + bibtex (for presentation slides) |
| `/slide-excellence [file]` | Combined multi-agent slide review |
| `/validate-bib` | Cross-reference citations |
| `/commit [msg]` | Stage, commit, PR, merge |
| `/learn [skill-name]` | Extract discovery into persistent skill |
| `/context-status` | Show session health + context usage |
| `/deep-audit` | Repository-wide consistency audit |
| `apsr_gender_scraper.py` | `~/Downloads/` — collect all APSR gender papers → Excel + PDF |

---

## Theoretical Toolkit

Key background papers and their connection to the dissertation:

| Paper | Mechanism | Dissertation Link |
|-------|-----------|------------------|
| Young (2015), "The Evolution of Social Norms", *AREcon* 7:359–87 | Evolutionary game theory → norm dynamics (persistence, tipping, compression) | Fertility norms channel: social multiplier amplifies economic squeeze on fertility; also: male-breadwinner norm tipping under commercialization (Paper 2) |
| Acemoglu & Robinson (2012), *Why Nations Fail* | Institutional persistence | Capital misallocation via institutional constraints; also: patrilineal property rights as amplifier of gender shocks (Paper 2) |
| Bisin & Verdier (2001), "The Economics of Cultural Transmission", *JEcTheory* | Endogenous vertical/horizontal norm transmission | Paper 2: commercialization raises Δ_m → q* rises → stable patriarchal norm equilibrium |
| Boserup (1970), *Woman's Role in Economic Development* | Technology → gendered division of labor | Paper 2: baseline gender wage gap channel amplified by commercialization |
| Federici (2004), *Caliban and the Witch* | Historical narrative: reproductive labor devalued under capitalism | Paper 2: the formal economic mechanism behind Federici's argument |

**Norms → Fertility channel:** Social norms on family size are coordination equilibria. A housing price shock → economic incentives shift → can trigger a tipping point if the norm is near threshold. Young's stochastic stability framework formalizes this.

---

## Slides Convention

- **Reading-note slides** (external papers): self-contained `.tex` in `~/Downloads/`, compile with `xelatex` in place, output `.pdf` alongside
- **Dissertation slides** (presentations): `Slides/` directory, use `Preambles/header.tex`, compile with `TEXINPUTS=../Preambles:$TEXINPUTS xelatex`

---

## Data Manifest

All external data sources are documented in `data-manifest.md` (tracked in git).
The `data/` directory itself is gitignored — never commit raw or processed data.

Key datasets:
- **Economic census:** City-level firm data; used for misallocation measures
- **Housing prices:** City-year panel; key regressor
- **CFPS:** China Family Panel Studies; fertility outcomes
- **Bartik instrument:** Shift-share IV for housing price variation

See `data-manifest.md` for full details (paths, dates, variables).

---

## Working Papers

| Paper | Title | Status | Key Data / IV |
|-------|-------|--------|---------------|
| **Paper 1** | *Dingyou*, Bureaucratic Careers, and State Capacity in Imperial China | **Active** — data construction + DiD reports at `/Users/xiaoo/Desktop/{0525,0527}/`; identification-strategy reconfiguration in `/Users/xiaoo/Desktop/0601/` (2026-06-01) | Parental mortality shock; CGED-Q bureaucratic records; Qing ethnic variation |
| **Paper 2** | Misallocation, Educational Involution, and the Fertility Trap | **Active** — APSR gender lit review underway (2026-05); framework drafted | Bartik shift-share (pre-2008 industry × 4T stimulus); CFPS fertility; city housing price panel |
| **Paper 3** | Tariff Policy, Firm–State Rent-Seeking, and the Fiscal State | Proposal stage | WTO accession tariff schedule; firm political embeddedness (ownership, party membership) |

**Unifying framework proposal:** `~/Downloads/proposal.pdf` + `~/Desktop/proposal.pdf`
LaTeX source: `~/Downloads/proposal.tex` + `~/Desktop/proposal.tex`

### Paper 1 Working Locations

**0601 is the primary active pipeline as of 2026-06-02.** 0525 (upstream data construction) and 0527 (frozen prior cut) are referenced for comparison only.

| Artifact | Path | Reproducer |
|----------|------|-----------|
| **Identification pipeline (active, 2026-06-02)** | `/Users/xiaoo/Desktop/0601/code/` (14 `.do` + 10 `.R` + `master.do`) | `cd /Users/xiaoo/Desktop/0601 && stata -b do code/master.do` |
| **Umbrella report** | `/Users/xiaoo/Desktop/0601/report/identification.tex` → `identification.pdf` | `cd /Users/xiaoo/Desktop/0601/report && latexmk -xelatex identification.tex` |
| **Main paper (Module B)** | `/Users/xiaoo/Desktop/0601/report/bureaucratic_resilience.tex` → `bureaucratic_resilience.pdf` | `cd /Users/xiaoo/Desktop/0601/report && latexmk -xelatex bureaucratic_resilience.tex` |
| **Appendix (Module A)** | `/Users/xiaoo/Desktop/0601/report/career_effects_appendix.tex` → `career_effects_appendix.pdf` | `cd /Users/xiaoo/Desktop/0601/report && latexmk -xelatex career_effects_appendix.tex` |
| Raw cleaned input (CGED-Q derivative) | `/Users/xiaoo/Desktop/丁忧/dingyou_clean.dta` | — (upstream) |
| Data construction pipeline (upstream) | `/Users/xiaoo/Desktop/0525/code/` (8 `.do` + 4 `.R` + `master.do`) | `cd /Users/xiaoo/Desktop/0525 && stata -b do code/master.do` |
| Data construction report (upstream) | `/Users/xiaoo/Desktop/0525/report/main.tex` → `main.pdf` | `cd /Users/xiaoo/Desktop/0525/report && latexmk -xelatex main.tex` |
| DiD analysis pipeline (frozen prior cut) | `/Users/xiaoo/Desktop/0527/code/` (8 `.do` + 6 `.R` + `master.do`) | `cd /Users/xiaoo/Desktop/0527 && stata -b do code/master.do` |
| DiD analysis report (frozen prior cut) | `/Users/xiaoo/Desktop/0527/report/did_analysis.tex` → `did_analysis.pdf` | `cd /Users/xiaoo/Desktop/0527/report && latexmk -xelatex did_analysis.tex` |
| Executive summary (3-panel landscape) | `/Users/xiaoo/Downloads/research_summary.tex` → `research_summary.pdf` | `cd ~/Downloads && latexmk -xelatex research_summary.tex` |
| Coverage audit (standalone) | `/Users/xiaoo/Downloads/coverage_audit.tex` → `coverage_audit.pdf` | `cd ~/Downloads && latexmk -xelatex coverage_audit.tex` |

See `data-manifest.md` §0/§6/§7/§8 for dataset detail and `.claude/rules/dingyou-pipeline-conventions.md` for project-specific conventions (panel ID = `group_id`, governor definition, `csdid` control-group convention, weighted vs unweighted vacancy duration, five-estimator stack, common-support window).

The 0601 pipeline supersedes 0527 for identification work. Outcome is `rank = -ln(rg)`; five staggered-DiD estimators are stacked (TWFE, CS-DiD, Sun-Abraham, BJS, dCDH); 兼職 (concurrent positions) gets a standalone chapter. The report has been split into a thin umbrella + two companion documents: a fragile-ATT **appendix** (Module A) and a bureaucratic-resilience **main paper** (Module B). 0527 stays as the frozen prior cut for comparison.

**Methodology across papers:** Sufficient statistics (Chetty 2009) + direct approach (Atkin–Donaldson 2022)
Wedge framework: Hsieh–Klenow 2009; Baqaee–Farhi 2020; Bergquist–Lashkari–Verhoogen 2026
Welfare cost = $\mathcal{L}^{\text{static}} + \mathcal{L}^{\text{repro}}$ — the reproduction term is the new contribution

---

## Current Project State

| File/Directory | Status | Content |
|----------------|--------|---------|
| `scripts/stata/` | Active (external: `/Users/xiaoo/Desktop/0601/code/` primary; `/Users/xiaoo/Desktop/{0525,0527}/code/` upstream + frozen) | Paper 1 do-files. 0601 has 14 `.do` + master.do (5-estimator stack, common-support, 兼職 chapter); 0525 has 8 (data construction); 0527 has 8 (frozen DiD analysis). |
| `scripts/python/` | Active | Data cleaning pipeline; APSR scraper at `~/Downloads/apsr_gender_scraper.py` |
| `scripts/r/` | Active (external: `/Users/xiaoo/Desktop/0601/code/` primary; 0525/0527 supplementary) | Paper 1 R figure scripts. 0601 has 10 (`fig_first_diff*`, `fig_es_combined`, `fig_es_grid`, `fig_concurrent_dedup`, `fig_rank_distribution`, `fig_qing_map`, `fig_xunfu_lifecycle/network`, `fig_vacancy`). |
| `output/` | Active (external: `/Users/xiaoo/Desktop/0601/output/` primary; 0525/0527 supplementary) | Tables (.tex) and figures (.pdf/.png/.csv) per pipeline. 0601 has 26 tables + 60+ figures + audit CSVs. |
| `report/` (external) | Active | **0601 primary:** `identification.tex` (umbrella) + `bureaucratic_resilience.tex` (Module B main paper) + `career_effects_appendix.tex` (Module A appendix). Upstream: `0525/report/main.tex` (data construction). Frozen: `0527/report/did_analysis.tex`. Executive summary at `~/Downloads/research_summary.tex`. |
| `paper/` (in-repo) | Planned | Full LaTeX paper draft (when ready) |
| `data-manifest.md` | Active | External data source documentation (incl. CGED-Q + 0525/0527/0601 derivatives as of 2026-06-02) |
| `Slides/` | Active | Beamer presentation slides |
| `Preambles/header.tex` | Active | Metropolis theme, custom environments |
| `Bibliography_base.bib` | Active | Bibliography entries |
