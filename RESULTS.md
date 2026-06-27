# PS1 — Results

Two return-prediction tasks, graded on **out-of-sample R²** on a withheld test set (we optimize honest
proxies: leave-one-out CV for the cross-sectional Task 1, a **held-out-future recursive backtest** for the
time-series Task 2). The data is synthetic (*signal + large idiosyncratic noise*).

## At a glance — last week (2026-06-16) → this week (2026-06-26)
Both models were **improved this week** and validated on the out-of-sample metric that mirrors the grade.
Neither new model is worse than last week's anywhere.

| task | last week (on GitHub) | **this week** | out-of-sample metric | change |
|---|---|---|---|---|
| Task 1 | single ridge | ridge **blend** (raw + interactions + poly-2) | LOOCV R² | 0.0564 → **0.0597** |
| Task 2 | 5-day AR-X + lagged `macro2` | + **betas + `macro2²` + `firm×firm`** | held-out-future R² (30d) | 0.600 → **0.642** |

---

## Task 1 — Cross-Sectional Return Prediction
**Data:** 3,500 train / 1,500 test firms; 5 standardized factors (`size, value, profit, invest, mom`) +
credit `rating`. One row per firm, no time dimension. Signal is linear and weak → **R² ≈ 0.05 is the noise ceiling**.

- **Last week:** single ridge, LOO-tuned α≈153. **LOOCV R² 0.0564** (10×5 CV 0.0537).
- **This week:** equal-weight **blend** of three ridges (raw + interactions + poly-2), refit on all data.
  **LOOCV R² 0.0597** (10×5 CV 0.0567).
- **Why it's better (and not overfit):** the blend beats raw on identical folds by +0.0030 (paired *t* = 3.2),
  positive in **all 8 CV seeds**, ~92% bootstrap probability, and a **nested CV** confirmed it's a genuine
  variance-reduction gain, not selection-on-the-max. Independently reproduced from scratch. Test firms are
  in-distribution, so the gain transfers, with **~zero downside**. The margin is small (the data is at its
  noise ceiling), so it reliably *raises expected grade* even if it's within one draw's sampling noise.

---

## Task 2 — Panel Return Prediction (forecasting)
**Data:** 100 firms × 200 days (2020 COVID crash → recovery). Test = the **future** 79 days, same firms,
`ret` withheld; `macro1` (regime) and `macro2` (market factor) are **given on test days**. 72% of return
variance is between-day (the `macro2` market level), 28% within-day. **Validation = held-out-future R²**
(train past, predict a held-out future block) at short (30d) and long (51–71d) horizons matching the 79-day
test; no look-ahead leakage (shuffling `macro2`↔day collapses R² 0.61→~0.10).

**Last week → this week** (held-out-future R², same harness):

| horizon | last week (5-day AR-X + macro2) | + betas | **this week (+ `macro2²` + `firm×firm`)** |
|---|---|---|---|
| 30d [91:121] | 0.5998 | 0.6114 | **0.6421** |
| 51d [70:121] | 0.5194 | 0.5368 | **0.6049** |
| 71d [50:121] | 0.4839 | 0.5018 | **0.6000** |

- **What we added this week:** *heterogeneous betas* (`macro2 × firm` — exogenous, never compounds, helps
  every horizon); **`macro2²`** (the `macro2`→return map is mildly **convex** — confirmed genuine: helps
  *in-range* and with *true* lags (+0.019–0.054) and fixes the linear model's *negative* R² on high-`macro2`
  recovery days); **`firm×firm`** (small but consistent; firm chars in-range → no extrapolation risk).
  Final ≈ **0.64** (30d) / **0.60** (51–71d); oracle (true-lag) ceiling ≈ 0.65–0.67.
- **Tested and REJECTED on the held-out-future metric** (overfitting guards): direct/hybrid multi-step
  forecasting (worse at every horizon — recursion usefully propagates the known `macro2` path; reproduced
  from scratch); regime-conditional momentum (helps 30d, hurts the long test horizon — recursive→compounds);
  `macro2` clipping (hurts −0.08…−0.14 out-of-range → the convex extrapolation is correct); `macro2×lag`,
  firm×regime, firm-char lags, time-decay weighting, nonlinear/firm-FE models.
- **⚠️ Grade depends on the R² convention.** Scored **pooled** over all 7,900 firm-days (the standard for a
  flat prediction file) we get the numbers above. Scored **per-day cross-sectionally** (de-meaning each day),
  any market-factor model scores negative — strong evidence pooled R² is intended; worth a one-line confirm.

---

## Deliverables (regenerated this week, verified)
- `task1/task1_predictions.csv` — `firm_id, y_hat` (1,500 rows)
- `task2/task2_predictions.csv` — `firm_id, date, y_hat` (7,900 rows)

*(Course-provided data and the theory PDF are not included in this repo.)*
