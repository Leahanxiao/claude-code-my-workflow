---
paths:
  - "scripts/python/**/*.py"
  - "scripts/**/*.py"
---

# Python Code Standards

**Role:** Data pipeline and cleaning — not primary estimation (that's Stata)

---

## 1. Environment

- Pin all dependencies in `requirements.txt` or `pyproject.toml`
- Use a virtual environment (`.venv/`, gitignored)
- Python 3.10+ required

```
# requirements.txt example
pandas==2.1.0
numpy==1.26.0
pyarrow==14.0.0
stata-setup==0.1.0    # if using pystata
```

---

## 2. Reproducibility

```python
import random
import numpy as np

# At top of script — always set seeds before any stochastic operation
random.seed(20240101)
np.random.seed(20240101)
```

- Never modify data in-place if the original is still needed
- Raw data files are **immutable** — write to `data/clean/`, never overwrite `data/raw/`

---

## 3. Path Conventions

```python
from pathlib import Path

# Always resolve relative to project root
ROOT = Path(__file__).resolve().parents[2]  # adjust depth as needed
DATA_RAW  = ROOT / "data" / "raw"
DATA_CLEAN = ROOT / "data" / "clean"
DATA_INSTR = ROOT / "data" / "instruments"
OUTPUT_TABLES = ROOT / "output" / "tables"
OUTPUT_FIGS   = ROOT / "output" / "figures"
```

- No hardcoded strings like `"/Users/..."` or `"C:\\Users\\..."`
- All paths constructed via `pathlib.Path`

---

## 4. Data Integrity Logging

Log row/column counts before and after every merge or reshape:

```python
import logging
logging.basicConfig(level=logging.INFO, format="%(asctime)s %(message)s")

def log_shape(df, label: str) -> None:
    logging.info(f"{label}: {df.shape[0]:,} rows × {df.shape[1]} cols")

# Usage
log_shape(df_left, "before merge")
df_merged = df_left.merge(df_right, on=["city_code", "year"], how="left")
log_shape(df_merged, "after merge")

# Assert no unexpected duplicates
assert not df_merged.duplicated(subset=["city_code", "year"]).any(), \
    "Duplicate city-year rows after merge"
```

---

## 5. Output

### Cleaned Data

```python
# Prefer .dta for Stata compatibility, .parquet for large files
df_clean.to_stata(DATA_CLEAN / "panel_city_year.dta", write_index=False)
# or
df_clean.to_parquet(DATA_CLEAN / "panel_city_year.parquet", index=False)
```

### File Hashing (data integrity)

```python
import hashlib

def log_file_hash(path: Path) -> str:
    """Log SHA256 hash of output file for integrity checking."""
    h = hashlib.sha256(path.read_bytes()).hexdigest()[:12]
    logging.info(f"Wrote {path.name} | SHA256 prefix: {h}")
    return h
```

- Always call `log_file_hash()` after writing any output dataset
- Hashes are logged to console/log, not stored in git

---

## 6. Bartik / Shift-Share Checks

```python
# Assert weights sum to 1 per unit-year
weight_sums = df_bartik.groupby(["city_code", "year"])["weight"].sum()
assert (weight_sums - 1.0).abs().max() < 1e-6, \
    f"Bartik weights do not sum to 1; max deviation: {(weight_sums - 1.0).abs().max()}"
```

**In module/script docstring, document:**
1. Source of industry employment shares (which census wave)
2. Source of national-level demand shifts
3. Base year for shift-share construction
4. Any industries excluded and why

---

## 7. Function Documentation

```python
def build_bartik_instrument(
    emp_shares: pd.DataFrame,
    national_shifts: pd.DataFrame,
    base_year: int = 2000,
) -> pd.DataFrame:
    """
    Construct Bartik (shift-share) instrument for city-year panel.

    Parameters
    ----------
    emp_shares : DataFrame
        City-industry employment shares from base year census.
        Required columns: city_code, industry_code, emp_share
    national_shifts : DataFrame
        National industry-year employment growth rates.
        Required columns: industry_code, year, national_growth
    base_year : int
        Census year used for employment shares (default: 2000).

    Returns
    -------
    DataFrame with columns: city_code, year, bartik_iv
    """
```

---

## 8. Code Quality Checklist

```
[ ] requirements.txt / pyproject.toml with pinned versions
[ ] random.seed() + np.random.seed() at top
[ ] All paths via pathlib.Path relative to ROOT
[ ] log_shape() before and after each merge
[ ] assert no unexpected duplicates after merge
[ ] Raw data never overwritten
[ ] Output files hashed and logged
[ ] Bartik weights asserted to sum to 1
[ ] Functions have docstrings
[ ] No hardcoded absolute paths
```
