# CAISO Electricity Load Forecasting

Forecasting hourly electricity load (MW) for CAISO using historical load,
weather, and calendar features with a Linear Regression-based pipeline.

## Mission

Predict CAISO system load one hour ahead (with a day-ahead extension),
using time-based evaluation, lag/rolling features, weather drivers, and
calendar effects.

## Target Variable

Hourly CAISO system load (MW).

## Project Structure

- `data/raw/` - untouched downloaded data
- `data/interim/` - cleaned, merged hourly tables
- `data/processed/` - feature-engineered datasets
- `src/data/` - data download and cleaning code
- `src/features/` - feature engineering (lags, rolling, calendar, weather)
- `src/models/` - baselines and regression models
- `src/evaluation/` - metrics, backtesting, plots
- `src/utils/` - shared utilities
- `scripts/` - pipeline entry-point scripts
- `notebooks/` - exploratory analysis
- `reports/` - final report and figures
- `configs/` - project configuration
- `tests/` - unit tests

## Status

Project foundation phase - skeleton, configs, and package scaffolding.
No data downloaded or models trained yet.

## Roadmap

See `CAISO_PROJECT_ROADMAP.md` for the full phase-by-phase plan.
