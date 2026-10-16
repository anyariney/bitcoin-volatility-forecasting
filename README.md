# Forecasting Bitcoin Prices and Volatility

I wanted to know two things: can past prices forecast where Bitcoin goes next, and can its volatility be predicted?

## Data
Daily BTC-USD closing prices from January 2019 to October 2026, pulled from Yahoo Finance with `quantmod`.

## What I did
I fit ARIMA, ETS, drift, naive, and combination models on log prices, then tested them two ways: on a 90-day holdout and across 31 rolling cross-validation folds. I checked the residuals with Ljung-Box and McLeod-Li tests. For volatility, I fit a GARCH(1,1) model with Student-t errors and simulated 2,000 price paths to build forecast intervals.

## What I found
Nothing beat a random walk. Across the 31 folds, ARIMA's RMSE landed within 0.2% of the naive forecast. Drift looked like the winner on the single holdout, with a 20% lower CRPS than naive, but it came in last once tested across all the folds. The holdout just happened to catch a trending stretch.

Volatility told a different story. ARIMA's residuals passed the Ljung-Box test (p = 0.11), so the direction of daily moves looks like noise. The squared residuals failed badly (McLeod-Li p < 0.001), which means big moves cluster together. GARCH picked this up, with persistence near 1 and very fat tails. Its 90-day variance forecast still lost to a plain constant variance on the test window, though, so clustering in the past didn't translate into a better forecast here.

## Limitations
The holdout is one 90-day window, so I treat the cross-validation as the main evidence. GARCH persistence this close to 1 may come partly from regime shifts in the sample, like the 2021 boom and the 2022 crash. Every model's 95% interval covered 100% of the test prices at the 90-day horizon, which says the intervals were too wide rather than accurate, so I compared models with CRPS instead.

## Running it
Open `bitcoin_forecast.Rmd` in RStudio and knit. Packages: tidyverse, tsibble, fable, feasts, scales, quantmod, rugarch, forecast, slider.

[Full analysis](bitcoin_forecast.md)
