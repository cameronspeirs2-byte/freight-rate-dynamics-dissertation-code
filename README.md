# freight-rate-dynamics-dissertation-code
# Freight-Rate Dynamics: Far East Asia to US West Coast

Code for my undergraduate economics dissertation (University of Exeter, 2026) on what drives short-run movements in container freight rates on the Far East Asia to US West Coast route (January 2017 to December 2019), and how far those movements can be predicted.

**Dissertation:** [DISSERTATION FINAL.docx](https://github.com/user-attachments/files/33280121/DISSERTATION.FINAL.docx)

## Overview

The project combines freight-rate data with operational, weather, macroeconomic, cost and trade-policy variables, and uses two complementary approaches:

- **Monthly econometric analysis** (R): stationarity testing, dynamic time-series regressions with Newey-West standard errors, bootstrap checks, influence diagnostics, and ARIMA and regression-based forecast comparisons.
- **Daily machine-learning forecasting** (R): 3-, 5- and 7-day-ahead forecasts of freight returns, comparing a zero-change benchmark, a lag benchmark, Elastic Net and XGBoost in an expanding-window, one-forecast-at-a-time design. A separate set of scripts classifies large "stress moves" (unusually large repricing episodes) at the same three horizons, with the stress threshold estimated from the initial training sample only to avoid look-ahead bias.

## Repository contents

| File | Language | Purpose |
|------|----------|---------|
| `AIS_variable_creation.ipynb` | Python (Google Colab) | Processes daily AIS ship-tracking files and builds the operational variables: active ships, port arrivals, delay rate and average turnaround time |
| `ERA5 WEATHER FEATURE CREATION` | R | Builds daily route-level weather variables, including a weather stress index and a joint-storm indicator, from ERA5 wind and wave NetCDF files |
| `ECONOMETRICS SCRIPT` | R | Loads and merges the source series, then runs the monthly econometric analysis |
| `MASTER DATASET FOR ML` | R | Joins the daily series into the single dataset used by all machine-learning scripts |
| `3-DAY AHEAD FREIGHT FORECASTING`, `5-DAY ...`, `7-DAY ...` | R | Regression forecasts at each horizon |
| `STRESS-MOVE CLASSIFICATION - 3-DAY HORIZON`, `5-DAY ...`, `7-DAY ...` | R | Stress-move classification at each horizon |

## Data

The raw data are not included in this repository. The scripts expect the following files in the working directory:

| File | Contents | Source |
|------|----------|--------|
| `freight_daily.csv` | Daily spot freight rate, Far East Asia to US West Coast (2017-2019) | Xeneta Shipping Index by Compass (XSICFEUW), proprietary |
| `daily_data.csv` | Daily AIS-derived port variables: delay rate, active ships, port arrivals, average turnaround hours | Output of `AIS_variable_creation.ipynb`, built from [Marine Cadastre](https://marinecadastre.gov/) / US Coast Guard AIS vessel-traffic data |
| `weather_daily_route.csv` | Daily route-level weather stress index and joint-storm indicator | Output of the ERA5 script, built from ERA5 wind and wave data (Hersbach et al., 2020; Copernicus Climate Data Store) |
| `wind_raw.nc`, `wave_raw.nc` | ERA5 wind and wave fields | Copernicus Climate Data Store |
| `tariff_daily_filled_2017_2019.csv` | Daily tariff rate, share of goods tariffed and trade-war indicator | Peterson Institute for International Economics US-China tariff dataset (Bown, 2025) |
| `Industrial Production.csv` | US industrial production | Federal Reserve G.17 release |
| `PMI.csv` | China manufacturing PMI (official NBS series) | Trading Economics |
| `crude_oil_price.csv` | Brent crude oil price | Investing.com (Brent futures historical prices) |
| `DTWEXBGS.csv` | Broad trade-weighted US dollar index | FRED (loaded by the econometrics script; |

The AIS variables use five major US West Coast port areas: Los Angeles, Long Beach, Oakland, Seattle and Tacoma. The Xeneta index is a commercial dataset and cannot be redistributed, so it is not included. Please see the dissertation (Table S1 and Appendix B) for full variable definitions and construction rules.

## Requirements

**R** with the packages: `tidyverse`, `lubridate`, `readr`, `zoo`, `forecast`, `tseries`, `urca`, `moments`, `corrplot`, `lmtest`, `sandwich`, `car`, `broom`, `terra`, `slider`, `glmnet`, `xgboost`, `fastDummies`, `Metrics`, `pROC`.

**Python** (run in Google Colab): `pandas`, `numpy`, `tqdm`. The notebook mounts Google Drive and expects the raw AIS files in a folder named in the first cell.

## How to reproduce

1. Obtain the raw data listed above.
2. Run `AIS_variable_creation.ipynb` in Google Colab to produce `daily_data.csv`. Update the Drive paths in the first cell to match your own folders.
3. Run `ERA5 WEATHER FEATURE CREATION` in R to produce `weather_daily_route.csv`. Update the working directory near the top of the script first.
4. Run `ECONOMETRICS SCRIPT` to load and merge the series and produce the monthly results.
5. In the same R session, run `MASTER DATASET FOR ML` to build the daily dataset (`daily_ml_master_data.csv`).
6. Run the forecasting and stress-move scripts for each horizon. Each writes its results and performance tables to CSV files in the working directory.

Random seeds are set in the machine-learning scripts, so results should be reproducible given the same data and package versions.


## Contact

Cameron Speirs, cameronspeirs2@gmail.com
