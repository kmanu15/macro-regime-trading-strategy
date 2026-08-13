# Macro Regime Asset Allocation

A rule-based asset allocation strategy that shifts portfolio weights across U.S. business-cycle regimes, backtested against passive benchmarks.

## Objective

Determine whether classifying the economy into macro regimes — using only publicly available data — can improve risk-adjusted returns relative to buy-and-hold investing.

## Research Questions

1. Can publicly available macroeconomic indicators identify meaningful business-cycle regimes?
2. Do different macro regimes favor different asset classes?
3. Does a macro-driven allocation improve risk-adjusted returns compared with passive investing?

## Methodology

Macro regimes are defined on a 2x2 grid of trailing 12-month CPI inflation vs. trailing 12-month industrial production growth, split around 2% thresholds:

| Regime | Inflation | Growth |
|---|---|---|
| Goldilocks | Below threshold | Above threshold |
| Inflationary Expansion | Above threshold | Above threshold |
| Stagflation | Above threshold | Below threshold |
| Slowdown | Below threshold | Below threshold |

The regime label is shifted forward one month so each month's allocation only uses information that would have been available at the time.

Pipeline: download macro data → classify regime → assign portfolio weights → download ETF prices → backtest → evaluate performance.

## Asset Universe

| Asset Class | ETF | Purpose |
|---|---|---|
| U.S. Equity | SPY | Broad market exposure |
| Growth Equity | QQQ | Technology and growth exposure |
| Long-Term Treasuries | TLT | Defensive asset during recessions |
| Gold | GLD | Inflation hedge |
| Energy | XLE | Commodity/inflation exposure |
| Cash | BIL | Short-term Treasury bills |

Benchmarked against SPY buy-and-hold and a static 60/40 SPY/AGG portfolio.

## Macroeconomic Data (FRED)

- CPI (`CPIAUCSL`) — inflation
- Unemployment rate (`UNRATE`)
- Industrial production (`INDPRO`) — growth
- Federal funds rate (`FEDFUNDS`)
- NBER recession indicator (`USREC`)
- 3-month T-bill rate (`TB3MS`) — risk-free rate

## Strategy Versions

**Version I — Theoretical Static Weights**
Fixed target weights assigned to each regime by hand, applied over the full sample. A sanity check on whether the regime framework has any signal at all before testing it out of sample.

**Version II — Expanding Window Walk-Forward**
At each annual re-estimation date, regime weights are computed from all data up to that point, then held fixed until the next re-estimation. Weights are only ever informed by data available at the time.

**Version III — Rolling Window Walk-Forward**
Same walk-forward structure as Version II, but each re-estimation only looks back a fixed 10-year window, letting weights adapt to more recent regime dynamics rather than being anchored to the full history.

**Transaction Costs**
Applied to Version III (the best-performing version). Cost is 10bps per unit of monthly turnover, where turnover is the month-over-month change in portfolio weights.

## Evaluation Metrics

Each version is scored on annualized volatility, Sharpe ratio, max drawdown, and total return, and compared against SPY buy-and-hold and a 60/40 SPY/AGG benchmark.

## Repo Contents

- `macro_regime_trading_strat_clean.ipynb` — full analysis: data pipeline, regime classification, all three backtest versions, transaction cost analysis, and model comparison.

## Requirements

```
pandas
numpy
yfinance
pandas-datareader
matplotlib
plotly
```

## Notes

- Data sources: Yahoo Finance (ETF prices) and FRED (macro series), both pulled live via API at notebook run time.
- Regime labels are shifted forward one month to avoid look-ahead bias.
