# 📈 Financial Market Regime Prediction & Risk Forecasting

**Identifying market regimes and forecasting high-risk conditions with machine learning** — combining unsupervised regime discovery with supervised forecasting to support financial risk monitoring and portfolio analysis.

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Wrangling-150458?logo=pandas&logoColor=white)
![Scikit--learn](https://img.shields.io/badge/Scikit--learn-Modeling-F7931E?logo=scikitlearn&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-Benchmarked-83a603?logo=xgboost&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-11557C)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

> ⭐ **Two-stage ML framework**: K-Means clustering discovers latent market regimes from 20 years of financial + macro data, then Logistic Regression, Random Forest, and XGBoost are benchmarked to predict the *next* regime — plus a separate volatility-forecasting model, all evaluated with strict chronological validation.

---

## 📖 Table of Contents

- [Problem](#-business-problem)
- [Objectives](#-objectives)
- [Dataset](#-dataset)
- [Project Workflow](#-project-workflow)
- [Models Benchmarked](#-models-benchmarked)
- [Results](#-results)
- [Why Logistic Regression Won](#-why-logistic-regression-won)
- [Key Insights](#-key-insights)
- [Limitations](#-limitations)
- [Repo Structure](#-repo-structure)
- [How to Run](#-how-to-run)
- [Tech Stack](#-tech-stack)
- [Future Work](#-future-work)

---

## 📌 Business Problem

Financial markets behave differently across periods of low volatility, strong growth, high uncertainty, and financial stress. Identifying these market regimes and anticipating transitions toward higher-risk conditions can support financial risk monitoring and portfolio analysis.

## 🎯 Objectives

| Goal | Approach |
|---|---|
| 🔍 Discover market regimes | Unsupervised clustering (K-Means) |
| 📊 Understand regime behavior | Profiling & temporal analysis of regime characteristics |
| 🔮 Predict future regimes | Supervised classification (next-day regime forecast) |
| ⚠️ Forecast risk | Regression model for next-day volatility |

## 🗂 Dataset

| Attribute | Value |
|---|---|
| Period | January 2005 – December 2025 |
| Sources | FRED, Yahoo Finance, Kenneth French Data Library |
| Coverage | Interest rates, financial stress index, equities, commodities, currencies, volatility (VIX), Fama-French factors |
| Target (classification) | `Next_Regime` (Regime 0 / Regime 1, shifted +1 trading day) |
| Target (regression) | Next-day S&P 500 volatility |
| Problem type | Clustering → Classification → Regression |

Data spans several contrasting market environments, including the 2008 Global Financial Crisis, the 2020 COVID-19 shock, and the 2022 inflation/monetary-tightening cycle.

**Sources:**
- [FRED — Federal Reserve Economic Data](https://fred.stlouisfed.org/)
- [Yahoo Finance](https://finance.yahoo.com/)
- [Kenneth French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html)

A high-yield credit spread series (BAMLH0A0HYM2) was excluded — its history only starts in 2023, far short of the project's 2005 start.

## 🔧 Project Workflow

This notebook follows a 24-phase, end-to-end pipeline:

```
1. Business Problem              → 9.  Next-Regime Target          → 17. XGBoost
2. Objective & Data Scope        → 10. Define X/y - Time Split     → 18. Baseline Comparison
3. Importing Libraries           → 11. Train/Test Split            → 19. Overfitting Check
4. Data Understanding            → 12. Time-Series CV              → 20. Final Model Training
5. Data Cleaning                 → 13. Feature Scaling             → 21. EDA
6. Data Alignment                → 14. Logistic Regression         → 22. Risk Forecasting
7. Feature Engineering           → 15. Random Forest               → 23. Final Interpretation
8. Regime Target (K-Means)       → 16. (baseline comparison)       → 24. Limitations & Conclusion
```

| Phase | What was done |
|---|---|
| Data Cleaning | Handled holiday gaps in Treasury yields, interpolated CPI/UNRATE, stripped Fama-French footer rows, resolved Yahoo Finance's duplicate column naming |
| Data Alignment | Merged all sources onto a common daily timeline (2005–2025), forward-mapping lower-frequency series to avoid look-ahead bias |
| Feature Engineering | Returns, rolling volatility, momentum, interest-rate changes, macroeconomic and market-stress features |
| Regime Discovery | K-Means clustering (k = 2–5 tested via silhouette score) |
| Modeling | Chronological 80/20 split with an untouched final holdout, time-series CV, dedicated 2020 supervised validation window |
| Risk Forecasting | Linear Regression vs. naive persistence baseline for next-day volatility |

## 🤖 Models Benchmarked

| # | Model | Task |
|---|---|---|
| — | K-Means | Regime discovery (unsupervised) |
| 1 | **Logistic Regression** ⭐ | Next-regime classification — *final model* |
| 2 | Random Forest | Next-regime classification |
| 3 | XGBoost | Next-regime classification |
| 4 | Linear Regression | Next-day volatility forecast |
| — | Naive Persistence | Volatility forecast baseline |

## 📊 Results

**Next-Regime Classification — 2020 validation period**

| Model | Balanced Accuracy | Macro F1 | Regime-1 Recall | Regime-1 F1 |
|---|---:|---:|---:|---:|
| **Logistic Regression** ⭐ | **96.86%** | **97.54%** | **94%** | **96%** |
| XGBoost | 75.96% | 81.22% | 52% | 68% |
| Random Forest | — | — | 46% | 63% |

Logistic Regression correctly identified 49 of 52 high-stress observations with only one false positive. On the untouched final holdout (1,029 observations, of which only 1 was true Regime-1), the model correctly classified that observation plus 1,012 of 1,028 Regime-0 observations.

**Risk Forecasting — next-day S&P 500 volatility**

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Naive Persistence | 0.000379 | 0.000789 | 0.9731 |
| **Linear Regression** | 0.000443 | **0.000769** | **0.9745** |

<!-- Add exported chart images here, e.g.: -->
<!-- ![Regime Timeline](images/regime_timeline.png) -->
<!-- ![Model Comparison](images/model_comparison.png) -->

## 🏆 Why Logistic Regression Won

Across all three classifiers evaluated on the 2020 validation period, Logistic Regression achieved the highest balanced accuracy, macro F1, and — most importantly for this problem — the highest recall on the rare, high-stress Regime 1. Random Forest and XGBoost were both more conservative, missing more than half of the high-stress observations despite producing few false positives. Since the goal is *catching* high-risk conditions rather than avoiding false alarms, Logistic Regression's higher recall made it the better choice, even though it's the simplest model of the three.

## 📈 Key Insights

- **Two regimes emerge clearly**: a dominant Normal/Low-Stress regime (~96% of days) and a rare High-Stress/Crisis regime (~4%), concentrated around 2008–2009 and 2020 rather than randomly distributed.
- **Volatility, VIX, and return dispersion** — not the return level itself — best separate the two regimes; Regime 1's median return is close to zero, but its spread is far wider.
- **A simpler model won**: Logistic Regression outperformed both tree-based ensembles at recalling the minority high-stress class.
- **Volatility is highly persistent**: a naive "tomorrow = today" baseline is nearly as strong as a trained regression model, meaning the engineered features add only limited incremental value for short-term volatility forecasting.

## ⚠️ Limitations

- Regime 1 is rare and concentrated in specific historical periods; the final holdout contains only one such observation, limiting how representative its crisis-detection metrics are.
- Regimes were discovered via K-Means rather than predefined economic labels, so interpretation rests on observed characteristics, not ground truth.
- Financial relationships can shift over time — historical performance doesn't guarantee future performance.
- The analysis identifies associations, not causal relationships.
- Next-day volatility forecasting shows limited improvement over a naive persistence baseline.

## 🗂️ Repo Structure

```
financial-market-regime-risk-forecasting/
│
├── data/                          # Dataset documentation
│   └── README.md
│
├── images/
│   ├── regime_timeline.png
│   ├── model_comparison.png
│   └── volatility_by_regime.png   # Project visualizations
│   └── vix_by_regime.png
├── financial-market-regime-prediction-risk-forecasting.ipynb    # Complete ML workflow
│
├── requirements.txt               # Python dependencies
├── LICENSE                        # MIT License
└── README.md                      # Project documentation
```

## ▶️ How to Run

```bash
git clone https://github.com/<your-username>/financial-market-regime-risk-forecasting.git
cd financial-market-regime-risk-forecasting
pip install -r requirements.txt
jupyter notebook market-regime-risk-ml.ipynb
```

## 🛠️ Tech Stack

`Python` · `Pandas` · `NumPy` · `Scikit-learn` · `XGBoost` · `Matplotlib`

## 🚀 Future Work

- Extend regime detection to more than two clusters to capture transitional/intermediate states
- Test alternative resampling or class-weighting strategies to improve tree-based model recall on Regime 1
- Explore additional features (e.g., credit spreads once sufficient history is available) for both classification and volatility forecasting
- Validate the framework on out-of-sample, more recent data as it becomes available

---

⭐ If you found this useful, consider starring the repo!
