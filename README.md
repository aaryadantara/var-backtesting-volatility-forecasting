# VaR Backtesting and Volatility Forecasting

> **Headline:** `<one sentence with real numbers, e.g. "GARCH-t passed the Kupiec test at 99% on all three assets; EWMA failed the independence test in 2020.">`

Daily 1-day 99% Value-at-Risk forecasts from three volatility models, backtested out-of-sample across the 2008 crisis, March 2020 and the 2022 rate shock. The aim is to test whether each model's VaR can be trusted, not only to compute it.

## Question

Which volatility model produces VaR forecasts that hold up in stress periods, and where do the others break down?

## Data

- Daily adjusted closes from Yahoo Finance via `yfinance`, 2004 to present
- Assets: `<SPY>`, `<Euro Stoxx 50 ETF>`, `<IEF>`
- Log returns, no look-ahead: the forecast for day *t* uses returns through *t-1* only
- Public data only; raw downloads are cached locally and not committed

## Method

**Models**

| Model | Description |
|---|---|
| Historical | Rolling empirical quantile of past returns |
| EWMA | RiskMetrics recursion, lambda = 0.94 |
| GARCH(1,1) | One-step-ahead volatility forecast, refit every 20 days; normal and Student-t quantiles |

**Backtest**

- Rolling 1,000-day estimation window, 99% confidence (alpha = 0.01)
- VaR is reported as a positive loss; an exception is a return below -VaR
- Kupiec proportion-of-failures test for coverage
- Christoffersen test for independence, to catch exceptions that cluster
- Basel traffic-light zones on 250-day windows (green 0-4, yellow 5-9, red 10+)

Window length and lambda were fixed before looking at results and not tuned on the reporting period.

## Results



| Asset | Model | Obs. | Expected exceptions | Actual exceptions | Kupiec p | Christoffersen p | Green / Yellow / Red |
|---|---|---|---|---|---|---|---|
| `<>` | Historical | | | | | | |
| `<>` | EWMA | | | | | | |
| `<>` | GARCH-t | | | | | | |

![VaR vs returns](figures/var_vs_returns.png)

![Exceptions per year](figures/exceptions_per_year.png)

**Stress periods:** `<2-3 sentences on 2008-09, March 2020 and 2022: which model reacted fastest, which clustered, and why.>`

## Limitations

- Several models assume normal or Student-t returns, which understates tail risk in a crisis
- Results depend on the 1,000-day window; shorter windows react faster but are noisier
- Daily data only, so no intraday risk or liquidity effects
- Three assets is a small sample; conclusions may not generalise to other markets

## Project structure

```
.
├── src/
│   ├── data.py        # download, cache, log returns
│   ├── models.py      # historical, EWMA, GARCH VaR
│   └── backtest.py    # rolling forecasts, Kupiec, Christoffersen, traffic light
├── notebooks/         # walkthrough notebook (imports from src/)
├── tests/             # unit tests, including a no-leakage check
├── figures/
└── requirements.txt
```

## How to run

```bash
pip install -r requirements.txt
pytest
jupyter notebook notebooks/walkthrough.ipynb
```

## Tools

Python, `pandas`, `numpy`, `scipy`, `arch`, `matplotlib`, `yfinance`, `pytest`
