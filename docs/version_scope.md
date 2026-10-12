# Project Version Scope

This document records the intentional split between the first release and the second release of the CAISO electricity load forecasting project.

The main reason for this split is to keep the first version achievable while still preserving the bigger forecasting vision.

## Version 1: Load-Only Forecasting Baseline

Version 1 focuses on building a clean, reproducible forecasting system using historical CAISO load and calendar features.

The goal of v1 is not to build the best possible load forecast. The goal is to build a correct baseline system that avoids leakage, uses time-based evaluation, and creates a trustworthy foundation.

### V1 Inputs

- Historical CAISO load/demand.
- Lagged load values.
- Rolling load statistics.
- Calendar features.
- Holiday indicators.
- Daylight Saving Time indicators.

### V1 Models

- Previous-hour baseline.
- Previous-day baseline.
- Previous-week baseline.
- Linear Regression.
- Ridge Regression or Lasso Regression.

### V1 Evaluation

- Time-based train/validation/test splits.
- No random shuffling.
- MAE.
- RMSE.
- MAPE.
- Comparison against simple baselines.

### V1 Deliverable

A working CAISO load forecasting project that predicts future hourly load from historical load patterns and calendar context.

V1 should answer:

> How well can we forecast CAISO load using only past load behavior and calendar structure?

## Version 2: Weather-Enhanced Forecasting

Version 2 adds weather features after the v1 pipeline is stable.

Weather is expected to be important, especially during extreme heat and cold periods. However, adding it too early can complicate data access, timestamp alignment, and leakage rules. That is why it belongs in v2.

### V2 Additional Inputs

- Temperature.
- Dew point.
- Wind speed.
- Cooling Degree Days.
- Heating Degree Days.
- Optional weather-hour interaction features.

Preferred weather source:

- Open-Meteo historical weather API, because it can be used without an API key for non-commercial work.

### V2 Models

- Reuse v1 models with weather features added.
- Compare Linear Regression, Ridge, and Lasso with and without weather.
- Optional later models can be added only after the linear-model comparison is clear.

### V2 Evaluation

V2 must be evaluated against v1 using the same split periods and metrics.

The key question is:

> How much does weather improve the forecast compared with the load-only v1 system?

Special attention should be paid to:

- Hot days.
- Cold days.
- Summer peaks.
- Winter peaks.
- Holidays.
- Periods where v1 underpredicts or overpredicts badly.

### V2 Deliverable

A weather-enhanced CAISO forecasting system with a clear before/after comparison against v1.

## Rule for Future Work

Do not mix v2 weather work into v1 unless the v1 data pipeline, baseline models, Linear Regression model, and time-based evaluation are already working.

The correct order is:

1. Build v1.
2. Measure v1.
3. Add weather for v2.
4. Measure improvement over v1.