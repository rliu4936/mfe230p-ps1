# Task 1 — Cross-Sectional Return Prediction

Predict one-period stock returns from firm characteristics (size, value, profitability, investment,
momentum, credit rating) on a cross-section of firms (one row per firm, no time dimension).

## Approach
- **Preprocess:** standardize the numeric factors, one-hot encode `rating`.
- **Model:** ridge regression, penalty tuned by closed-form **leave-one-out CV**.
- **Validation:** repeated 10×5 k-fold (a `FAST` toggle drops to a quick 5×2 while iterating).
- **Searched broadly:** {raw, interactions, poly-2, splines} × {ridge, lasso, elastic-net} +
  ensembles/boosting, with a paired significance test on the leaders.

See [`../RESULTS.md`](../RESULTS.md) for findings.
