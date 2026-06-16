# PS1 — Results

Two return-prediction tasks. In both, the data is synthetic (*signal + large idiosyncratic noise*),
and the lesson is the same: **regularized linear models win; added complexity doesn't pay.**

## Task 1 — Cross-Sectional Return Prediction
- **Data:** 3,500 train firms / 1,500 test firms; 5 standardized factors (`size, value, profit, invest,
  mom`) + credit `rating`. One row per firm, no time dimension.
- **Signal:** linear and weak — out-of-sample **R² ≈ 0.05 is the noise ceiling**.
- **Search:** {raw, interactions, poly-2, splines} × {ridge, lasso, elastic-net} + RF/boosting.
- **Result:** **ridge** with leave-one-out-tuned α, **OOS R² ≈ 0.054**. Feature expansions add only
  ~0.001 (paired *t* ≈ 0.7 → noise); tree/boosting models do worse.
- **Validation:** leave-one-out CV (closed-form, free for ridge) + repeated 10×5 k-fold to report.

## Task 2 — Panel Return Prediction (forecasting)
- **Data:** 100 firms × 200 days (2020 COVID crash → recovery). Test = the **future** 79 days, same
  firms, `ret` withheld.
- **Signal:** strong **momentum** (return autocorrelation 0.74) + a **market factor** (`macro2`,
  corr 0.63 contemporaneous / 0.66 lagged). `price` is a noisy decoy (corr 0.11 with `ret`).
- **Validation:** time-aware (last-30-day holdout); time-series models scored by a **recursive
  backtest** (predictions fed forward), not oracle lags.
- **Models** (realistic recursive R²):

  | model | R² |
  |---|---|
  | cross-sectional ridge (exogenous only) | 0.23 |
  | time-series AR-X (return lags) | 0.55 |
  | 5-day window | 0.57 |
  | **5-day window + lagged `macro2`** | **0.60** |

- **What didn't help:** firm-char lags, rolling means, momentum×macro interaction, firm fixed effects
  (all flat); nonlinear models (HistGBM/RF) do worse → linear DGP, near the noise ceiling (~0.64 oracle).

## Deliverables
- `task1/task1_predictions.csv` — `firm_id, y_hat` (1,500 rows)
- `task2/task2_predictions.csv` — `firm_id, date, y_hat` (7,900 rows)

*(Course-provided data and the theory PDF are not included in this repo.)*
