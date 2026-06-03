# Project Memory

Corrections and learned facts that persist across sessions.
When a mistake is corrected, append a `[LEARN:category]` entry below.

---

<!-- Append new entries below. Most recent at bottom. -->

## Workflow Patterns

[LEARN:workflow] Requirements specification phase catches ambiguity before planning → reduces rework 30-50%. Use spec-then-plan for complex/ambiguous tasks (>1 hour or >3 files).

[LEARN:workflow] Spec-then-plan protocol: AskUserQuestion (3-5 questions) → create `quality_reports/specs/YYYY-MM-DD_description.md` with MUST/SHOULD/MAY requirements → declare clarity status (CLEAR/ASSUMED/BLOCKED) → get approval → then draft plan.

[LEARN:workflow] Context survival before compression: (1) Update MEMORY.md with [LEARN] entries, (2) Ensure session log current (last 10 min), (3) Active plan saved to disk, (4) Open questions documented. The pre-compact hook displays checklist.

[LEARN:workflow] Plans, specs, and session logs must live on disk (not just in conversation) to survive compression and session boundaries. Quality reports only at merge time.

## Documentation Standards

[LEARN:documentation] When adding new features, update BOTH README and guide immediately to prevent documentation drift. Stale docs break user trust.

[LEARN:documentation] Always document new templates in README's "What's Included" section with purpose description. Template inventory must be complete and accurate.

[LEARN:documentation] Guide must be generic (framework-oriented) not prescriptive. Provide templates with examples for multiple workflows (LaTeX, R, Python, Jupyter), let users customize. No "thou shalt" rules.

[LEARN:documentation] Date fields in frontmatter and README must reflect latest significant changes. Users check dates to assess currency.

## Design Philosophy

[LEARN:design] Framework-oriented > Prescriptive rules. Constitutional governance works as a TEMPLATE with examples users customize to their domain. Same for requirements specs.

[LEARN:design] Quality standard for guide additions: useful + pedagogically strong + drives usage + leaves great impression + improves upon starting fresh + no redundancy + not slow. All 7 criteria must hold.

[LEARN:design] Generic means working for any academic workflow: pure LaTeX (no Quarto), pure R (no LaTeX), Python/Jupyter, any domain (not just econometrics). Test recommendations across use cases.

## File Organization

[LEARN:files] Specifications go in `quality_reports/specs/YYYY-MM-DD_description.md`, not scattered in root or other directories. Maintains structure.

[LEARN:files] Templates belong in `templates/` directory with descriptive names. Currently have: session-log.md, quality-report.md, exploration-readme.md, archive-readme.md, requirements-spec.md, constitutional-governance.md.

## Constitutional Governance

[LEARN:governance] Constitutional articles distinguish immutable principles (non-negotiable for quality/reproducibility) from flexible user preferences. Keep to 3-7 articles max.

[LEARN:governance] Example articles: Primary Artifact (which file is authoritative), Plan-First Threshold (when to plan), Quality Gate (minimum score), Verification Standard (what must pass), File Organization (where files live).

[LEARN:governance] Amendment process: Ask user if deviating from article is "amending Article X (permanent)" or "overriding for this task (one-time exception)". Preserves institutional memory.

## Skill Creation

[LEARN:skills] Effective skill descriptions use trigger phrases users actually say: "check citations", "format results", "validate protocol" → Claude knows when to load skill.

[LEARN:skills] Skills need 3 sections minimum: Instructions (step-by-step), Examples (concrete scenarios), Troubleshooting (common errors) → users can debug independently.

[LEARN:skills] Domain-specific examples beat generic ones: citation checker (psychology), protocol validator (biology), regression formatter (economics) → shows adaptability.

## Memory System

[LEARN:memory] Two-tier memory solves template vs working project tension: MEMORY.md (generic patterns, committed), personal-memory.md (machine-specific, gitignored) → cross-machine sync + local privacy.

[LEARN:memory] Post-merge hooks prompt reflection, don't auto-append → user maintains control while building habit.

## Meta-Governance

[LEARN:meta] Repository dual nature requires explicit governance: what's generic (commit) vs specific (gitignore) → prevents template pollution.

[LEARN:meta] Dogfooding principles must be enforced: plan-first, spec-then-plan, quality gates, session logs → we follow our own guide.

[LEARN:meta] Template development work (building infrastructure, docs) doesn't create session logs in quality_reports/ → those are for user work (slides, analysis), not meta-work. Keeps template clean for users who fork.

## Project Setup

[LEARN:project-setup] Dissertation project (UChicago, China misallocation/fertility) configured 2026-04-11: Stata primary estimator, Python data pipeline, R figures. Key rules added: stata-code-conventions.md, python-code-conventions.md. Data always gitignored (data/ dir); data-manifest.md tracks sources. Quality gates extended to .do and .py files. City code crosswalk and CFPS weight choices are common pitfalls.

## User Profile

[LEARN:user] Xiao Han is a 2nd-year UChicago PhD economist (2026), post-quals. Academic taste: Becker, Lucas, North, Acemoglu. Strong intuition and economic facts; actively building formal theory and identification skills. Writes in AER-register English: measured, concise, powerful, no excess. Slide writing: one point per slide, sparse bullets, minimal equations (intuition first), keybox/highlightbox environments. Paper: capital misallocation → two channels → fertility decline (Double Squeeze framework).

[LEARN:slides] Seminar idea pre-slides: prioritize intuition over formulas. Only ONE equation as anchor (the fertility decomposition rule). Use keybox for main takeaways, highlightbox for qualifications, definitionbox for formal concepts. Each slide makes exactly one point. Metropolis theme with mDarkTeal/mLightBrown palette. Self-contained .tex file compiles with xelatex in Downloads.

## Paper 1: Dingyou Pipeline

[LEARN:dingyou-data] Count cascade is the #1 trust failure in any dingyou report. A single concept ("巡撫" governor) yields ~1,715 → 1,494 → 1,613 → 1,035 → 883 → 580 records across raw / classified-with-province / governor-panel / spell / network-sample / DiD-estimation datasets. Provincial breakdowns also differ across stages (e.g., Anhui = 159 distinct in `03_panel.dta` vs 67 in `did_panel.dta`). **Mandatory rule:** every report citing a count must include a funnel-reconciliation table mapping each N to its source `.dta` and filter chain. Numbers without provenance are bugs.

[LEARN:dingyou-data] Governor (巡撫) definition is text-match dependent. `/Users/xiaoo/Desktop/0525/code/08_panel_summary.do` line 418 defines `is_governor = regexm(履歷,"巡撫") & admin_level==2 & isp==1` — **no rg threshold**. Adding one (e.g., `rg<=3`) silently shifts every count downstream. Any variant must be footnoted in the report and re-verified against the funnel table.

[LEARN:dingyou-did] `csdid` defaults to `control_group(notyet)`, not `never`. `/Users/xiaoo/Desktop/0527/code/08_three_estimators.do` line 276 calls `csdid rank, ivar(group_id) time(year) gvar(gvar) method(reg)` with no explicit `control_group()`, so Callaway–Sant'Anna uses not-yet-treated. Any report sentence saying "never-treated comparison group" is wrong unless `control_group(never)` is added explicitly. Always pass the option explicitly in future `csdid` calls.

[LEARN:dingyou-did] Vacancy duration: 1.4 years vs 2.0 years are both correct but different statistics, not an error. 1.4 = `sum avg_vacancy [aweight=n_vacancy]` from `05_vacancy_chains.do` line 388 (weighted by vacancies per province — high-turnover provinces dominate). 2.0 = simple `mean avg_vacancy` across 21 provinces from line 378 (unweighted). Always cite both with the weight convention named.

[LEARN:dingyou-did] TWFE and CS-DiD are not on the same sample. TWFE in `02_did_full.do` absorbs all cohorts and all years; CS-DiD in `08_three_estimators.do` restricts to `gvar ∈ [1760, 1800]` and `year ∈ [1750, 1810]` (lines 259–261). Any side-by-side estimator comparison (Table 15) must add a same-window TWFE row before the divergence can be interpreted — otherwise the comparison mixes estimator differences with sample differences.

## Paper 1: Dingyou Method Decisions (2026-05-31)

[LEARN:dingyou-did] `post = 1 iff k ≥ 3` only. k = 0, 1, 2 are the on-leave window (treated but transitional) and excluded from the post indicator — they only appear in the event-study grey-band. Any table note saying "post = at/after first dingyou spell" is inconsistent and must be rewritten as "post = three-or-more years after first dingyou (k ≥ 3); k=0–2 are on-leave grey-band, excluded".

[LEARN:dingyou-did] Matched DiD must run with `[pw=match_w]` (Mahalanobis k=3 weights from `psmatch2`). Earlier code (`08_three_estimators.do` line 262) ran TWFE unweighted on the matched-support panel, which is "matched-support DiD" not "matched DiD". Report wording must say "**weighted** TWFE DiD on the matched panel".

[LEARN:dingyou-did] CS-DiD uses `method(reg)` (regression adjustment with analytical SEs), NOT doubly-robust. Doubly-robust (`method(dripw)` default) is slower on full sample. Any report wording must say "regression-adjustment, analytical SEs"; the phrase "doubly-robust" is wrong unless the code is changed.

[LEARN:dingyou-did] R1 (narrow event-window), R3 (pre-/post-Taiping), and R8 (alternative window) robustness checks must be **true DiD with sample restriction**, NOT (a) event-time binning on the full sample (which leaves N unchanged) or (b) treated-only subsamples (which drop never-treated controls and yield non-DiD estimands). Correct pattern: `keep if <sample-filter> | ever_dingyou == 0` then `reghdfe rank post i.keju_bg, absorb(group_id year) vce(cluster group_id)`. N differs from baseline by construction.

[LEARN:dingyou-data] When the user's analysis identifies a code-vs-report inconsistency, ALWAYS rewrite the code to be the source of truth and update the report wording to match. Never the reverse. This caught: stale 935/580/883 N narrative, "doubly-robust" mislabel, unweighted matched DiD, ever_dingyou-unparsed silent drop, admin_level==9 catches after the variable was already set, multi-province governor-general jurisdiction silently collapsed to single province in `07_location_extraction.do`.

[LEARN:dingyou-conventions] Multi-province governor-general jurisdictions (兩廣, 兩江, 閩浙, 陝甘, 湖廣, 雲貴, 川陝, 東三省, 北洋大臣) must be preserved in a separate variable `post_jurisdiction` BEFORE collapsing to single `post_province`. `is_multi_prov_jurisdiction = 1` flag enables governor/viceroy-level analyses that need the original multi-province scope.

[LEARN:dingyou-conventions] Concurrent-posting deduplication ("highest rank wins") affects ~35% of official-year cells in `03_year_panel.do`. The flag `had_concurrent_posting = (n_concurrent_postings > 1)` MUST be persisted onto the analysis panel so DiD can run a `concurrent==0` robustness column. Without it, the headline ATT cannot be defended against the "we mechanically picked the highest rank" criticism.

## Paper 1: Dingyou Method Decisions (2026-06-01, 0601 pipeline)

[LEARN:dingyou-method] Primary outcome is `rank = -ln(rg)` (defined in `/Users/xiaoo/Desktop/0601/code/01_prep_did.do`), NOT `14 - rg`. Higher = more senior, same direction as before, but the log scale gives unit-free promotion semantics: a coefficient of 0.1 ≈ a 10% upgrade in rank position, comparable across rank tiers. The `14 - rg` cardinalisation treated a 13→12 step (knight→county) as equal to a 3→2 step (governor→viceroy), which over-weights senior-tier movement. Old `rank = 14 - rg` results are preserved in `/Users/xiaoo/Desktop/0527/` for comparison.

[LEARN:dingyou-method] Staggered-DiD master table stacks FIVE estimators per sample: TWFE, Callaway–Sant'Anna (`csdid`, `control_group(never)`, `method(reg)`), Sun–Abraham (`eventstudyinteract`, `control_cohort(ever_dingyou==0)`), Borusyak–Jaravel–Spiess (`did_imputation`), and de Chaisemartin–D'Haultfœuille (`did_multiplegt_dyn`). Matched DiD is no longer in the master table (moved to a sensitivity column in the 兼職 chapter). All five share reference period k=-1 and grey out k∈{0,1,2} on event-study plots.

[LEARN:dingyou-method] Identification sections must walk the reader through the DiD operator before showing regressions: (1) raw level means by group×period, (2) first difference within each group (treated and control separately), (3) second difference (raw DiD without controls), (4) progressive specifications adding year FE → individual FE → controls. The `02_first_difference.do` exhibit is now standard structure for any DiD section in this project — never start with the TWFE table.

[LEARN:dingyou-method] 兼職 (concurrent positions) gets a standalone chapter with four sub-analyses: (i) institutional context, (ii) dedup-rule sensitivity (highest-rank vs lowest-rank vs mean vs all-rows), (iii) `had_concurrent_posting==0` robustness, (iv) treatment-effect heterogeneity by `ever_concurrent`. Dropping this as a footnote was an old practice; concurrent posting is institutionally meaningful enough to warrant a chapter.

## Paper 1: 0601 Architecture (2026-06-02)

[LEARN:workflow] Selective rerun + a focused `_verify_headline.do` is dramatically faster than a full `master.do` rerun when audit confirms outputs match scripts. The 433 MB `did_panel.dta` rebuild + 5-estimator stack takes ~30 min; the spot-check is ~2 min and produces an unambiguous PASS/FAIL on the headline numbers (raw 2nd diff, sample N, cached 5-estimator × 5-sample ATTs). Before re-running `master.do`, compare timestamps of `.do` vs `output/`; if outputs are current, run only the verify script.

[LEARN:reports] Splitting a contested-finding report into Module A (fragile-ATT appendix) + Module B (organizational primary paper) lets the strong story carry the headline without throwing away the careful negative evidence. When the individual-career ATT is honest but modest and sample-sensitive, burying it as the headline weakens the project; relegating it to a fully developed appendix preserves the careful negative evidence while letting the organizational story carry the load. Use this pattern whenever the evidence base has both a strong descriptive/organizational story and a weak/contested causal estimand.

[LEARN:planning] When strategic critique asks for new analytical pieces, scope them OUT of the first session so the narrative pivot can land cleanly first. Mixing structural restructure with new analytical work compounds risk — if the new code has a bug, the structural pivot is also at risk. Defer new analytical work to a named next session, with the work items listed in the report itself ("Planned extensions") so the handoff is unambiguous.

[LEARN:latex] `\texttt{...}` with CJK characters fails silently under XeLaTeX with the lmmono fallback — characters are dropped from the PDF without an error. The lmmono12-regular monospace font has no CJK glyphs and the fontspec setup typically does not define a CJK monospace fallback. Keep CJK strings outside `\texttt{}` blocks. If a CJK identifier must appear in monospace, define a CJK-aware monospace font via `\setmonofont{...}[FallbackFonts]`.

[LEARN:reports] When a user gives second-round clarifications on a freshly restructured report, default to text/structural revisions (not new code or new analyses). The existing data exhibits usually already satisfy the request; the asking is usually for explicit narrative framing of properties the code already enforces (e.g., `k = -1` anchor, dingyou-blank discipline) and for explicit lists/structure of things that were previously prose-buried (e.g., bulleted controls list, separate subsection for the control-group mixture). Re-running the pipeline is rarely needed; a single verify-headline spot-check confirms the data are unchanged. Page count can go up even on a "make it more concise" pass if the user is asking for added structure (controls list, subfigure blocks, numbered recommendation paragraphs) — that growth is fine because it adds usability, not bloat.
