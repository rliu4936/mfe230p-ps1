# Task 2 — Panel Return Prediction

Predict daily stock returns for a panel of 100 firms. The test set is the **future** (same firms,
later days, returns withheld), so this is a forecasting problem.

## Approach
- **Explore:** panel structure, distributions, and signal — strong momentum + a market factor.
- **Validation:** time-aware (last-30-day holdout); time-series models scored by a **recursive
  backtest** (predictions fed forward), since lagged returns aren't in the test set.
- **Models:** cross-sectional ridge → time-series AR-X (return lags) → 5/10/15-day window, plus
  lagged `macro2`; final forecast rolls the best model forward recursively across the test days.

See [`../RESULTS.md`](../RESULTS.md) for findings.
