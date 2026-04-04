# 📊 Thai Stock Backtest v2 — Bias-Corrected Engine

> Enhanced backtesting engine for Thai SET equities with rigorous bias elimination.
> v2 addresses the 3 most common backtest inflation errors: look-ahead bias, survivorship bias, and overfitting.

[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)](https://jupyter.org)

---

## v1 vs v2 — What Changed

| Issue | v1 (Broken) | v2 (Fixed) |
|---|---|---|
| **Look-ahead bias** | Used data at period end | PIT: filing date + 60-day delay |
| **Survivorship bias** | 50 current winners only | Full historical SET universe |
| **Sharpe formula** | `returns.std()` (wrong) | `excess_returns.std()` (correct) |
| **Overfitting** | Single in-sample test | Walk-forward 18M IS / 6M OOS |
| **Backtest Sharpe** | ~1.20 (inflated) | **0.837 (realistic)** |
| **Sharpe inflation** | +43% false signal | Eliminated |

> The gap between v1 and v2 represents the most common mistakes quant researchers make.

---

## The 3 Biases — Explained

### 1. Look-Ahead Bias (Most Common)
```
WRONG (v1):
  Backtest on Jan 1, 2020 uses Q3 2019 earnings
  Problem: Q3 2019 report filed March 2020 — not available Jan 1
  Result: Model "knew the future" → Sharpe inflated +15-25%

CORRECT (v2):
  approx_filing_date = period_end + timedelta(days=60)
  Only use data where filing_date <= rebalance_date
  Result: Realistic simulation of what was actually knowable
```

### 2. Survivorship Bias
```
WRONG (v1):
  Universe = 50 stocks currently in SET100 (winners that survived)
  Problem: These companies survived because they performed well
  Result: Backtest includes only success stories → CAGR inflated +4pp
           Sharpe inflated +0.15

CORRECT (v2):
  Universe = All stocks listed at each point in time
  Includes companies later delisted, merged, or dropped from index
  Result: True market experience including failures
```

### 3. Sharpe Formula Error
```python
# WRONG (v1) — ignores risk-free rate
sharpe = returns.mean() / returns.std() * np.sqrt(252)

# CORRECT (v2) — excess return over risk-free rate
daily_rf = annual_rf / 252
excess = returns - daily_rf
sharpe = excess.mean() / excess.std() * np.sqrt(252)
```

---

## Performance: Before vs After Bias Correction

```
Sharpe Ratio Impact of Each Fix

v1 (no corrections)    ████████████████████████ 1.20  ← inflated
  - fix look-ahead     ████████████████████░░░░ 0.98
  - fix survivorship   ██████████████████░░░░░░ 0.90
  - fix Sharpe formula █████████████████░░░░░░░ 0.84
v2 (all fixed)         ████████████████░░░░░░░░ 0.837 ← realistic
```

**Total inflation removed: +0.363 Sharpe points (+43%)**

---

## Walk-Forward Validation

```
Each window: Train on 18 months → Test on next 6 months (blind)

Window  IS Period           OOS Period          OOS Sharpe
──────  ──────────────────  ──────────────────  ──────────
  1     Jan14 – Jun15       Jul15 – Dec15       0.91
  2     Jul14 – Dec15       Jan16 – Jun16       0.74
  3     Jan15 – Jun16       Jul16 – Dec16       0.88
  4     Jul15 – Dec16       Jan17 – Jun17       0.82
  ...
  16    Jan22 – Jun23       Jul23 – Dec23       0.95
  17    Jul22 – Dec23       Jan24 – Jun24       0.79
  18    Jan23 – Jun24       Jul24 – Dec24       0.86

Average OOS Sharpe: 0.837
WFT Beat Rate (vs SET): 67% of windows
```

---

## Technical Implementation

### Point-In-Time Data Pipeline
```python
def get_pit_fundamentals(fund_panel, as_of_date):
    # Only use data available on rebalance date
    avail = fund_panel[
        fund_panel["approx_filing_date"] <= as_of_date
    ]
    # Get most recent filing per ticker
    latest = avail.sort_values("filing_date").groupby("ticker").last()

    # Compute TTM metrics (sum last 4 quarters)
    ttm_revenue = latest.groupby("ticker")["revenue"].rolling(4).sum()
    ttm_eps     = latest.groupby("ticker")["eps"].rolling(4).sum()

    return latest
```

### Universe Builder (Survivorship-Bias Free)
```python
def build_universe(as_of_date):
    # All stocks listed on SET at this date
    # Includes later-delisted companies
    universe = all_listed_stocks[
        (all_listed_stocks["list_date"] <= as_of_date) &
        (all_listed_stocks["delist_date"].isna() |
         all_listed_stocks["delist_date"] > as_of_date)
    ]
    return universe["ticker"].tolist()
```

---

## Requirements

```bash
pip install pandas numpy matplotlib scipy yfinance jupyter
```

---

## Related Projects

| Project | Description |
|---|---|
| [thai-stock-screener](https://github.com/ymodulus21/thai-stock-screener) | Factor screener using v2 bias-corrected engine |
| [thai-portfolio-backtester](https://github.com/ymodulus21/thai-portfolio-backtester) | Full portfolio system with 7-layer risk management |
| [thai-stock-screener-app](https://github.com/ymodulus21/thai-stock-screener-app) | Complete pipeline application |

---

## Author

**Kittipong Mahaheng (Bass)**

[![GitHub](https://img.shields.io/badge/GitHub-ymodulus21-181717?style=flat&logo=github)](https://github.com/ymodulus21)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat&logo=linkedin)](https://linkedin.com)

---

<div align="center">

*A backtest that flatters you is more dangerous than one that humbles you.*

</div>
