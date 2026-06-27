# Does Heat Still Drive Ice Cream Output?

A 10-year analysis of U.S. ice cream production, seasonal patterns, and the predictive power of temperature.

---

## Dataset

| Variable | Source | ID |
|---|---|---|
| Ice Cream Production Index | FRED | `IPN31152N` |
| Avg U.S. Temperature | NOAA Climate Data Online | Monthly (°F) |

Period: January 2015 to December 2024 (120 observations, no missing values). Data is fetched at runtime inside the notebook, no manual download needed.

---


## Modeling Notes

A few decisions that are not obvious from the slide deck:

- **Why not plain ARIMA?** ADF and KPSS conflict at every differencing level. This is typical when strong deterministic seasonality is layered on a structural trend shift (the post-2020 recovery). Seasonal differencing `D=1, s=12` is fixed across all 72 grid-search combinations for this reason.

- **Why test both Lag-0 and Lag-1 in SARIMAX?** CCF peaks at lag-1, which aligns with supply chain logic. Lag-0 was included because same-month temperature is available to planners in real time. Final selection was by test RMSE, not CCF rank.

- **Why does Prophet underperform?** Temperature is itself highly seasonal. When added as an additive regressor, it interferes with Prophet's internal yearly seasonality component rather than supplementing it. This is visible in the component plots inside the notebook.

- **Caveat on the winning model:** The seasonal MA term in SARIMAX(Temp_Lag0) sits at the invertibility boundary (coefficient = -1.0, SE = 1377.8). The RMSE win is real, but coefficient estimates are unreliable. See the Limitations section in the notebook for the full discussion.

---

## Results

| Rank | Model | RMSE | MAE | MAPE |
|---|---|---|---|---|
| 1 | SARIMAX (Temp_Lag0) | 3.7465 | 3.2108 | 2.99% |
| 2 | SARIMA(1,1,2)x(0,1,1)[12] | 3.8174 | 3.2688 | 3.04% |
| 3 | SARIMAX (Temp_Lag1) | 4.2440 | 3.6257 | 3.36% |
| 4 | Seasonal Naive | 4.8202 | 4.2562 | 4.02% |
| 5 | Prophet Univariate | 5.5255 | 4.2003 | 4.03% |
| 6 | Prophet + Temp_Lag1 | 6.7509 | 5.5284 | 5.28% |
| 7 | Mean | 14.4125 | 12.5545 | 11.77% |
| 8 | Naive (Random Walk) | 29.8273 | 26.2326 | 22.42% |

---

## Author

**Az-Zukhrufu Fi Silmi Suwondo**
[LinkedIn](https://linkedin.com/in/az-zukhrufu-fi-silmi-suwondo) | [GitHub](https://github.com/afsilmis)
