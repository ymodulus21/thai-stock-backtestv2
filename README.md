# Thai Stock Backtest: Momentum, Low Volatility, and Equal Weight Strategies

[![Python Version](https://img.shields.io/badge/python-3.9%2B-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Last Commit](https://img.shields.io/github/last-commit/your-username/thai-stock-backtest)](https://github.com/your-username/thai-stock-backtest)

A quantitative backtesting framework for Thai stocks (SET) that compares **Equal Weight**, **Momentum**, and **Low Volatility** strategies over 2019–2026. Built with Python, `yfinance`, and rigorous risk metrics aligned with CFA Level 3 standards.

## 📌 Key Features
- **Look‑ahead bias free** – weights computed using only past data.
- **Transaction costs** – configurable commission and turnover tracking.
- **Advanced metrics** – Sharpe, Sortino, Calmar, VaR, CVaR, Beta, Alpha, Information Ratio, Up/Down Capture.
- **Robustness testing** – varies lookback periods and top‑N picks.
- **Stress testing** – COVID‑19 period analysis.
- **Visualization** – equity curves, drawdown, rolling Sharpe, correlation heatmap.

## 📊 Results Summary
| Strategy          | CAGR (%) | Sharpe | Max DD (%) | Calmar | Turnover (Annual) |
|-------------------|----------|--------|------------|--------|-------------------|
| Equal Weight      | 29.19    | 1.36   | -12.12     | 2.41   | 0.00%             |
| Momentum (12m)    | 7.00     | 0.35   | -37.72     | 0.19   | 74.79%            |
| Low Volatility (3m)| 8.42    | 0.41   | -23.79     | 0.35   | 54.66%            |

*(Full results in `results/metrics_summary.csv`)*

## 🧪 Stress Test – COVID‑19 (2020)
- **Low Volatility** held up best with **-11.25%** return and **-23.79%** max drawdown.
- Momentum and Equal Weight suffered deeper drawdowns (> -35%).

## 📈 Visuals
![Equity Curves](results/figures/equity_curves.png)
![Drawdown](results/figures/drawdown.png)
![Rolling Sharpe](results/figures/rolling_sharpe.png)

## 🛠️ Installation & Usage
```bash
git clone https://github.com/your-username/thai-stock-backtest.git
cd thai-stock-backtest
pip install -r requirements.txt
python notebooks/backtest_analysis.ipynb   # หรือเปิดใน Jupyter