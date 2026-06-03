---
paths:
  - "scripts/**/*.R"
  - "scripts/**/*.do"
  - "scripts/**/*.py"
  - "explorations/**"
  - "Figures/**/*.R"
---

# Research Project Orchestrator (Simplified)

**For R scripts, Stata do-files, Python scripts, and data analysis** -- use this simplified loop instead of the full multi-agent orchestrator.

## The Simple Loop

```
Plan approved → orchestrator activates
  │
  Step 1: IMPLEMENT — Execute plan steps
  │
  Step 2: VERIFY — Run code, check outputs
  │         R scripts: Rscript runs without error
  │         Stata do-files: stata -b do runs without error; log created
  │         Python scripts: python runs without error; output hashed
  │         Simulations: set.seed / set seed reproducibility
  │         Plots: PDF/PNG created, correct format
  │         If verification fails → fix → re-verify
  │
  Step 3: SCORE — Apply quality-gates rubric
  │
  └── Score >= 80?
        YES → Done (commit when user signals)
        NO  → Fix blocking issues, re-verify, re-score
```

**No 5-round loops. No multi-agent reviews. Just: write, test, done.**

## Verification Checklist

- [ ] Script runs without errors
- [ ] All packages / globals loaded at top
- [ ] No hardcoded absolute paths
- [ ] `set.seed()` / `set seed` once at top if stochastic
- [ ] Output files created at expected paths
- [ ] **Stata:** log file created; first-stage F reported for IV
- [ ] **Python:** output file hashes logged; Bartik weights assert passes
- [ ] Tolerance checks pass (if applicable)
- [ ] Quality score >= 80
