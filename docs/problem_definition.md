# CAISO Electricity Load Forecasting Problem Definition

## 1. Project Goal

The goal of this project is to build a reproducible machine learning pipeline for forecasting CAISO electricity demand using historical load, calendar features, and eventually weather features.

The first release should focus on a clean, reliable load-only forecasting system. Weather features are intentionally reserved for version 2, after the load data pipeline, baseline models, and time-based evaluation are working correctly.

See `docs/version_scope.md` for the project split between v1 and v2.

## 2. Region

Initial region:

- CAISO, the California Independent System Operator balancing area.

Future extension:

- Add other US regions such as PJM, ERCOT, NYISO, ISO-NE, MISO, SPP, or EIA-930 balancing authorities after the CAISO pipeline is stable.

## 3. Target Variable

The target variable is hourly CAISO system demand/load measured in megawatts (MW).

Target column name:

- `load_mw`

Expected timestamp level:

- Hourly timestamps.

Target interpretation:

- For each timestamp, predict the CAISO system load in MW for that hour.

## 4. Initial Forecasting Task

The first practical forecasting task is:

> Predict CAISO hourly system load one hour ahead.

In notation:

- At time `t`, predict `load_mw` for time `t + 1 hour`.

This is the main v1 task because it is simple enough to build quickly but still forces correct time-series thinking.

## 5. Baseline Tasks

Before training machine learning models, the project should evaluate simple forecasting baselines.

Baseline forecasts:

- Previous hour baseline: predict `load(t)` using `load(t-1h)`.
- Previous day baseline: predict `load(t)` using `load(t-24h)`.
- Previous week baseline: predict `load(t)` using `load(t-168h)`.

These baselines are important because electricity load has strong daily and weekly seasonality. A Linear Regression model is only meaningful if it can compete with or improve on these simple references.

## 6. Future Forecasting Extensions

After the v1 pipeline works, the project can add:

- 24-hour ahead forecasting.
- Day-ahead hourly load forecasting.
- Weather-enhanced forecasting.
- Multi-region forecasting.
- Model comparison beyond Linear Regression.

These are not required for the first release.

## 7. Modeling Granularity

The project uses hourly observations.

If raw data is available at 5-minute resolution, it should be aggregated or sampled into hourly records before modeling. The aggregation method must be documented before use.

Recommended v1 approach:

- Prefer official hourly CAISO actual demand data if available.
- If using 5-minute Today's Outlook demand data, convert to hourly data using a clearly documented rule such as hourly mean.

## 8. Historical Window

Target historical window for v1:

- 2018-01-01 through the latest available complete year, if available without special credentials.

Fallback historical window:

- Use the largest continuous public date range available without paid access or special approval.

The final date range must be recorded in the data download metadata once Phase 2 begins.

## 9. Data Access Assumptions

Initial load data source:

- CAISO public demand data through OASIS or Today's Outlook CSV feeds.

Credential rule:

- v1 should use data sources that are free and do not require paid access.
- v1 should avoid sources that require AWS requester-pays downloads, market participant certificates, or approval-gated accounts.

Weather source for version 2:

- Open-Meteo historical weather API is preferred for v2 weather features because it can be used without an API key for non-commercial use.

Holiday/calendar source:

- Calendar and US federal holiday features should be generated locally in code.

## 10. Allowed Information at Prediction Time

The model must only use information that would be known at the time of prediction.

Allowed for one-hour-ahead prediction:

- Historical load values up to time `t`.
- Calendar features for the forecast timestamp `t + 1 hour`.
- Holiday indicators for the forecast timestamp.
- Daylight Saving Time indicators for the forecast timestamp.
- Weather observations available up to time `t`, once weather is added.
- Weather forecasts for `t + 1 hour`, if a future phase explicitly uses weather forecast data.

Not allowed:

- Actual load at the forecast timestamp.
- Rolling features that include the forecast timestamp.
- Future weather observations if the prediction is supposed to be made before they occur.
- Random train/test splits that mix future observations into training.
- Any preprocessing step fitted on validation or test data.

## 11. Feature Families

Required v1 feature families:

- Lagged load features:
  - `load_lag_1h`
  - `load_lag_24h`
  - `load_lag_168h`
- Rolling load features:
  - 3-hour trailing mean.
  - 24-hour trailing mean.
- Calendar features:
  - Hour of day.
  - Day of week.
  - Month.
  - Weekend flag.
  - Holiday flag.
  - Daylight Saving Time flag.

Version 2 weather features:

- Temperature.
- Dew point.
- Wind speed.
- Cooling Degree Days.
- Heating Degree Days.
- Weather and hour interaction features.

## 12. Model Scope

Required v1 models:

- Previous-hour baseline.
- Previous-day baseline.
- Previous-week baseline.
- Linear Regression.
- Ridge Regression or Lasso Regression.

Optional later models:

- Elastic Net.
- Random Forest.
- Gradient Boosting.
- More advanced time-series models.

The first project version should stay centered on Linear Regression and regularized linear models.

## 13. Evaluation Strategy

Evaluation must use chronological splits only.

No random shuffling is allowed.

Recommended initial split:

- Training set: earliest 70 percent of the timeline.
- Validation set: next 15 percent of the timeline.
- Test set: final 15 percent of the timeline.

Preferred final split after the exact date range is known:

- Train on earlier years.
- Validate on a later full period.
- Test on the final full period.

Example if data covers 2018-2025:

- Train: 2018-2022.
- Validation: 2023.
- Test: 2024-2025.

This example should be adjusted after the real available data range is confirmed.

## 14. Metrics

Primary metrics:

- MAE: Mean Absolute Error.
- RMSE: Root Mean Squared Error.
- MAPE: Mean Absolute Percentage Error.

Interpretation:

- MAE is the main business-friendly error metric in MW.
- RMSE penalizes large errors more strongly.
- MAPE gives a percentage error, but it should be interpreted carefully when load values are low.

## 15. Success Criteria for Version 1

Version 1 is successful when:

- CAISO load data can be collected and cleaned reproducibly.
- The target column is clearly defined.
- Time-based train, validation, and test splits are created.
- Simple baselines are implemented and evaluated.
- Linear Regression is trained and evaluated.
- Ridge or Lasso is trained and evaluated.
- The model results are compared against baselines.
- The project avoids target leakage.
- The README explains how to reproduce the results.

## 16. Known Risks

Data access risk:

- Some historical CAISO download paths may require AWS credentials or requester-pays access. v1 should avoid depending on those paths.

Timestamp risk:

- Electricity data can be tricky around time zones and Daylight Saving Time transitions. Timestamp handling must be tested carefully.

Leakage risk:

- Lag and rolling features can accidentally include future values. Feature generation must shift data before creating rolling statistics.

Weather risk:

- Weather alignment can become complicated if multiple stations or forecast-vs-observation timing is introduced too early.

Version-scope risk:

- Weather is valuable, but adding it before the load-only baseline works can hide whether the basic forecasting pipeline is correct. Keep v1 load-only and use v2 to measure the improvement from weather.

## 17. Phase 1 Definition of Done

Phase 1 is complete when:

- The target variable is unambiguous.
- The initial forecast horizon is written down.
- The modeling granularity is defined.
- The initial historical window assumption is documented.
- The evaluation strategy is time-based.
- The document explains what information is allowed at prediction time.

This document satisfies the Phase 1 definition of done and should be updated only when actual data availability forces a change.
