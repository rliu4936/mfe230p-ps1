# Task 2 — Panel Return Prediction

Predict daily stock returns for a panel of 100 firms. The test set is the **future** (same firms,
later days, returns withheld), so this is a forecasting problem.

## Approach
- **Explore:** panel structure and signal — a dominant **market factor** (`macro2`, 72% of return
  variance, given on test) + firm-level momentum.
- **Validation:** every candidate judged ONLY by **held-out-future R²** (train on past days, predict a
  held-out future block — the forecasting analog of cross-validation), across short (30-day) and long
  (51–71-day) horizons matching the 79-day test; time-series models scored by a **recursive backtest**
  (predictions fed forward), since lagged returns aren't in the test set.
- **Final model:** 5-day AR-X + lagged `macro2` + exogenous firm/price/regime + **heterogeneous betas**
  (`macro2×firm`) + **`macro2²`** (genuine market-factor convexity) + **`firm×firm`**, rolled forward
  recursively across the 79 test days. Direct/hybrid forecasting, `macro2` clipping, and regime-conditional
  momentum were tested and **rejected** on the held-out-future metric.

See [`../RESULTS.md`](../RESULTS.md) for findings.
