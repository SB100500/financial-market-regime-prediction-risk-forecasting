# 🗂 Data

This directory contains the raw source files used in the [Financial Market Regime Prediction & Risk Forecasting](../README.md) project. All files are combined and aligned to a common daily timeline (2005–2025) in the notebook's Data Alignment step.

## 📖 Contents

- [Overview](#-overview)
- [FRED — Macroeconomic & Rate Data](#-fred--macroeconomic--rate-data)
- [Yahoo Finance — Market Data](#-yahoo-finance--market-data)
- [Kenneth French Data Library — Factor Data](#-kenneth-french-data-library--factor-data)
- [Notes on Usage](#-notes-on-usage)

## 📌 Overview

| File | Source | Frequency | Rows | Coverage used in project |
|---|---|---|---|---|
| `FEDFUNDS.csv` | FRED | Monthly | 866 | 2005–2025 |
| `STLFSI4.csv` | FRED | Weekly | 1,703 | 2005–2025 |
| `DGS10.csv` | FRED | Daily | 5,479 | 2005–2025 |
| `DGS2.csv` | FRED | Daily | 5,479 | 2005–2025 |
| `DGS30.csv` | FRED | Daily | 5,479 | 2005–2025 |
| `CPIAUCSL.csv` | FRED | Monthly | 956 | 2005–2025 |
| `UNRATE.csv` | FRED | Monthly | 944 | 2005–2025 |
| `market_data.csv` | Yahoo Finance | Daily | 5,639 | 2005–2025 |
| `F-F_Research_Data_5_Factors_2x3_daily.csv` | Kenneth French Data Library | Daily | 15,861 | 2005–2025 |
| `F-F_Momentum_Factor_daily.csv` | Kenneth French Data Library | Daily | 26,189 | 2005–2025 |

Row counts above are for the raw files as downloaded — most contain history well outside the project's 2005–2025 window and are filtered down during preprocessing.

## 🏦 FRED — Macroeconomic & Rate Data

Downloaded from the [Federal Reserve Economic Data (FRED)](https://fred.stlouisfed.org/) database. Each file has two columns: `observation_date` and the series value.

| File | Series | Description |
|---|---|---|
| `FEDFUNDS.csv` | Federal Funds Effective Rate | The interest rate at which banks lend reserves to each other overnight |
| `STLFSI4.csv` | St. Louis Fed Financial Stress Index | Composite measure of stress in financial markets |
| `DGS2.csv` | 2-Year Treasury Yield | Constant-maturity Treasury rate |
| `DGS10.csv` | 10-Year Treasury Yield | Constant-maturity Treasury rate |
| `DGS30.csv` | 30-Year Treasury Yield | Constant-maturity Treasury rate |
| `CPIAUCSL.csv` | CPI for All Urban Consumers | Headline inflation measure |
| `UNRATE.csv` | Unemployment Rate | Civilian unemployment rate |

**Known data quality notes:**
- Treasury yield series (`DGS2`, `DGS10`, `DGS30`) have gaps corresponding to U.S. market holidays.
- `CPIAUCSL` and `UNRATE` have isolated missing observations, handled via linear interpolation in preprocessing.

## 📈 Yahoo Finance — Market Data

`market_data.csv` — daily OHLCV (Open, High, Low, Close, Adj Close, Volume) data pulled from Yahoo Finance for 8 tickers, in a wide multi-index format (each field repeated once per ticker):

| Ticker | Instrument |
|---|---|
| `^GSPC` | S&P 500 Index |
| `^DJI` | Dow Jones Industrial Average |
| `^IXIC` | NASDAQ Composite |
| `^VIX` | CBOE Volatility Index |
| `CL=F` | Crude Oil Futures |
| `GC=F` | Gold Futures |
| `EURUSD=X` | EUR/USD Exchange Rate |
| `JPY=X` | USD/JPY Exchange Rate |

**Known data quality notes:**
- The raw file uses duplicated generic column headers (`Adj Close`, `Adj Close.1`, etc.); the ticker each column belongs to is given in the second header row.
- Only **Adjusted Close** prices are used downstream — the project focuses on regime identification and risk forecasting rather than intraday trading, so Open/High/Low/Volume are not carried into feature engineering.
- Missing values are mainly due to differing trading calendars across equities, commodities, currencies, and volatility instruments; these are resolved during date alignment.

## 📊 Kenneth French Data Library — Factor Data

Downloaded from the [Kenneth French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html), maintained by Fama and French (CRSP database).

| File | Contents |
|---|---|
| `F-F_Research_Data_5_Factors_2x3_daily.csv` | Daily Fama-French 5-Factor data: `Mkt-RF`, `SMB`, `HML`, `RMW`, `CMA`, `RF` (risk-free rate) |
| `F-F_Momentum_Factor_daily.csv` | Daily momentum factor (`Mom`) |

**Known data quality notes:**
- Both files include descriptive header text and a copyright footer row, which are stripped during cleaning.
- Dates are stored as `YYYYMMDD` integers rather than a standard date format.

## 📝 Notes on Usage

- A high-yield credit spread series (`BAMLH0A0HYM2`, FRED) was considered but **excluded** from this dataset — its available history only begins in 2023, which would have significantly reduced the usable 2005–2025 training window.
- All files are combined and restricted to the common **2005–2025** analysis period in the notebook's Data Alignment step. Daily series are aligned directly; weekly/monthly series (e.g. `STLFSI4`, `CPIAUCSL`, `UNRATE`, `FEDFUNDS`) are mapped forward using the most recently available observation to avoid look-ahead bias.
- See the [project README](../README.md) for how these files feed into feature engineering and modeling.
