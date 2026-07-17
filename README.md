# Stock Market Time Series Analysis

## Introduction

Stock prices, at first glance, appear to move randomly. However, when examined over a long enough time horizon, price movements can be understood as a combination of several underlying forces operating at different time scales. The objective of this analysis is to decompose the historical price data of three major companies — **Amazon, Tesla, and Apple** — into three interpretable components:

1. **Trend** — the long-term direction in which a stock is moving
2. **Seasonal** — a pattern that repeats at a fixed, predictable interval (in this case, yearly)
3. **Cyclic** — irregular, longer-term fluctuations that do not repeat at a fixed interval, generally associated with broader economic conditions such as recessions or market corrections

Isolating these components allows for a more structured interpretation of price behavior than is possible from the raw series alone, where genuine long-term growth, recurring seasonal effects, and cycle-driven volatility are all superimposed on one another.

## Datasets

| Stock  | File                      | Time Period |
|--------|---------------------------|-------------|
| Amazon | Amazon.csv                | 1997 – 2021 |
| Tesla  | TSLA.csv                  | 2015 – 2024 |
| Apple  | AAPL Historical Data.csv  | 2012 – 2022 |

Each dataset consists of daily historical closing prices, sourced from Kaggle.

## Methodology

**Noise reduction:** Daily closing prices exhibit considerable short-term noise arising from routine trading activity. To reduce this noise and reveal the underlying structure more clearly, the data was resampled from daily to **weekly averages**.

**Decomposition:** The weekly series was decomposed using **additive seasonal decomposition** (`statsmodels.seasonal_decompose`, with a period of 52, corresponding to one year of weekly observations). This method expresses the series as:

```
Price(t) = Trend(t) + Seasonal(t) + Residual(t)
```

The Trend component is obtained through a centered moving average, which smooths short-term fluctuations to reveal the underlying long-run direction. The Seasonal component is estimated by averaging deviations at each corresponding position within the yearly cycle across all years in the dataset. The Residual component captures whatever remains after the Trend and Seasonal components have been removed — this includes both random noise and any genuine cyclic behavior.

**Isolating the cyclic component:** A limitation of classical decomposition is that it does not distinguish cyclic behavior from irregular noise; both are contained within the Residual. To approximate the cyclic component specifically, a **26-week centered rolling average** was applied to the Residual. This has the effect of filtering out short-term random noise while retaining the slower, longer-duration swings characteristic of cyclic market behavior.

## Observations

**Trend:**
All three stocks exhibit a sustained upward trend over their respective observation periods, consistent with each company's overall growth trajectory. Apple and Amazon display comparatively steady, gradual trends, whereas Tesla's trend is considerably steeper and more volatile in its rate of change — reflecting a more speculative growth phase, particularly evident between 2020 and 2021.

**Seasonal:**
Each stock displays a measurable yearly seasonal pattern; however, its magnitude is small relative to the overall price level, particularly in later periods when absolute prices are substantially higher. This suggests that seasonal effects, while present, are not a primary driver of price behavior for these stocks.

**Cyclic:**
The cyclic component reveals the most economically meaningful structure among the three. Distinct cyclic swings coincide with recognizable market-wide events:
- The **2020 COVID-19 market crash**, appearing as a pronounced negative cyclic deviation across all three stocks
- The **2021–2022 post-pandemic volatility and subsequent correction**, appearing as a substantial positive-to-negative swing
- For Amazon, whose data extends further back, cyclic activity is also evident around the **dot-com collapse (2000)** and the **2008 financial crisis**

The amplitude of cyclic fluctuation is markedly larger for Tesla, consistent with its comparatively higher volatility, while Apple's cyclic component remains relatively subdued, reflecting its established reputation for greater price stability.

## Conclusion

This analysis indicates that although all three stocks are governed by a dominant long-term upward trend, the substantial deviations typically associated with "market volatility" are attributable primarily to the **cyclic component** rather than seasonal effects. Seasonality, while measurable, plays a comparatively minor role at this time scale. This finding is consistent with a broader characteristic of financial time series: unlike series such as retail sales or energy demand, which are strongly shaped by calendar-based seasonality, stock price movement over short-to-medium horizons is predominantly driven by broader economic and cyclic conditions rather than time-of-year effects.

## Output

For each stock, four graphs were generated and are available in the `outputs` folder:
- Original Time Series
- Trend Component
- Seasonal Component
- Cyclic Component
```
