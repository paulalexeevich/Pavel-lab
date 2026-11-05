---
slug: timestral-magnit-forecast
name: Timestral Magnit Forecast
status: active
type: data science experiment / forecasting benchmark
stack:
  - Python (pandas, numpy, matplotlib)
  - mistralai/timestral (Timestral-123M, zero-shot time-series foundation model)
infra:
  - local only — reads a cached local parquet snapshot of the retail-analytics `sales_stock` Supabase table
repo: local only — not yet pushed to GitHub
url:
added: 2026-11-05
updated: 2026-11-05
---

# Timestral Magnit Forecast

> One-shot test of Mistral's [Timestral](https://github.com/mistralai/timestral) time-series
> foundation model against real Magnit daily sales data: forecast month 3 of a store/SKU series from
> its first two months, compare against actuals.

## What It Does

- Pulls daily `sale_qnty` for 1 store (`whs_id=25891`) and its 3 best-selling SKUs out of
  [[retail-analytics]]'s locally cached `sales_stock_mapped_universe.parquet` (same Supabase-backed
  Magnit data that project already has read access to) — 89 days of real history
  (2026-05-01 .. 2026-07-28; the table only has ~3 months of data, not the 4 assumed going in).
- Runs `mistralai/Timestral-123M` zero-shot: first 60 days as context, forecasts the remaining 29 days,
  compares the median forecast against actuals (MAE/RMSE/MAPE) and against a naive "predict the
  2-month average" baseline.
- Plots context/actual/forecast + 10-90% quantile band per SKU to `forecast_vs_actual.png`.

**Result**: Timestral's quantile band is reasonably well calibrated (most actuals fall inside it), but
its median forecast is close to flat and only beats the naive-mean baseline on 1 of 3 SKUs — these
daily series are close to noise around a stable mean with no visible trend/seasonality, a
low-information setting for any zero-shot univariate model with no covariates. See the repo's
`README.md`/`results.csv` for full numbers and the suggested covariate follow-up.

## Stack & Infra

- **Code**: `forecast.py` (single script), data extraction done ad hoc against retail-analytics' cached parquet
- **Deploy**: none — local script, not a service
- **Repo**: not yet created/pushed — exists only at `~/Desktop/timestral-magnit-forecast` (1 local commit)
- **Live**: not deployed

## Status

**ACTIVE** — first experiment run and committed locally 2026-11-05. Not yet pushed to GitHub (pending
a decision on repo visibility, since `data/store_25891_daily_sales.csv` contains real Magnit sales
figures).

## Key Decisions

- Sourced data from [[retail-analytics]]'s existing local `sales_stock` parquet cache instead of
  querying the live Supabase table directly — the live table has no index beyond its primary key, and
  this avoids load on production credentials for what's a one-off exploratory script.
- Picked the single store with full 89/89-day coverage and its top-3 SKUs by volume, rather than a
  random sample, so the time series would actually be forecastable (dense, no large gaps).

## Sessions

| Date | What happened |
|------|---------------|
| 2026-11-05 | Initial experiment: extracted data, ran Timestral-123M forecast, compared vs. naive baseline, wrote up results. Local repo only, 1 commit, not yet pushed. |

## Links

- GitHub: —
- Live: —
- Handoff: —
