# MFE 230P · PS1 — Task 1: Cross-Sectional Return Prediction

Predicting one-period stock returns from firm characteristics (size, value, profitability,
investment, momentum, credit rating) on a cross-section of firms.

## Approach
- **Preprocess:** standardize the numeric factors, one-hot encode `rating`.
- **Model:** ridge regression with the penalty tuned by closed-form **leave-one-out CV**.
- **Validation:** repeated 10×5 k-fold (a `FAST` toggle drops to a quick 5×2 while iterating).
- **Searched broadly to justify the choice:**
  - linear grid — {raw, interactions, poly-2, splines} × {ridge, lasso, elastic-net}
  - ensembles & boosting — random forest, extra trees, AdaBoost, gradient boosting, hist-GBM
  - a bias–variance α sweep and a paired significance test.
