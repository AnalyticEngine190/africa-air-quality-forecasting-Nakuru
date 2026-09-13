# 🌍 Forecasting Air Quality in Nakuru: Rebuilding an Old Project

When I first started learning data science, I built a baseline time-series model to forecast PM2.5 air pollution in Nakuru, Kenya. Looking back at that early notebook, I realized how much my approach to data engineering and machine learning had grown. 

I decided to rebuild the entire project from scratch—not just to improve the final prediction score, but to replace brute-force shortcuts with proper statistical modeling and production-ready code.

### 📊 What Changed in Version 2.0?

In my original project, I ran into classic beginner roadblocks:
* I dropped `temperature` and `humidity` to simplify the problem, focusing only on `P2` (PM2.5).
* I averaged readings across the entire city, which mixed different microclimates together and created artificial noise.
* To capture the 24-hour day/night cycle, I forced a high-order `ARIMA(24, 0, 2)` model. This forced the computer to calculate 26 individual parameters on a small dataset, which caused dozens of convergence warnings.

**How I rebuilt it:**
1. **Multi-Variate Data:** Particulate matter doesn't move in a vacuum. I reshaped the dataset to keep localized temperature and humidity alongside PM2.5.
2. **Station Isolation:** Instead of blending readings across Nakuru, I isolated the primary sensor station (Location 4000) to model real localized atmospheric dynamics.
3. **Proper Seasonality:** Guided by PACF plots, I replaced the heavy 26-parameter ARIMA with a clean Seasonal SARIMAX: `(1, 0, 1) x (1, 0, 1, 24)`. This dropped the parameter count from 26 down to 4, completely eliminated optimization errors, and allowed the model to use weather data as exogenous features.
4. **Machine Learning Benchmark:** I benchmarked classical SARIMAX against a modern tree-based regressor (LightGBM) using engineered lag and rolling window features.

### 🏆 Model Comparison & Benchmarks

All models were evaluated on unseen holdout test data using chronological splits and Walk-Forward Validation (WFV).

| Model | Test MAE | Training / Run Time | Key Characteristic |
| **Naive Baseline (Mean)** | ~17.18 | Instant | Simple average of training data |
| **Seasonal SARIMAX (1,0,1)x(1,0,1)[24]** | **16.49** | ~5m 40s (144-step WFV) | Best overall accuracy & physical explainability |
| **LightGBM Regressor** | **16.79** | < 0.05 seconds | Ultra-fast inference with engineered rolling features |

#### The Engineering Trade-off
* **SARIMAX** was the winner for accuracy and interpretability. Its coefficients proved real atmospheric physics: temperature showed a statistically significant negative correlation ($p = 0.000$, heat disperses particles), while humidity showed a positive correlation ($p = 0.000$, moisture traps particles).
* **LightGBM** was only 0.30 MAE behind, but finished in milliseconds rather than minutes. For a real-time IoT or edge device streaming sensor data, LightGBM is the obvious production choice.

### 🔍 Project Walkthrough

1. **Data Cleaning:** Loaded raw sensor telemetry (`Nakuru.csv`), parsed European number formatting (semicolon separators and comma decimals), removed corrupt strings, and filtered out sensor errors (> 500 µg/m³).
2. **Time-Series Structuring:** Converted timestamps to local time (`Africa/Nairobi`) and resampled to 1-hour intervals using time-weighted interpolation for smooth sensor gaps.
3. **Exploratory Analysis:** Created hourly boxplots and ACF/PACF autocorrelation charts to prove the presence of the 24-hour diurnal cycle (morning traffic spikes and evening cooking patterns).
4. **Walk-Forward Validation:** Evaluated SARIMAX over a 144-hour rolling window to test how the model behaves when forecasting one hour ahead in the real world.
5. **Feature Engineering:** Built 1-hour lags, 24-hour seasonal lags, and 6-hour rolling means/standard deviations to train and benchmark LightGBM.

### 🛠️ Tech Stack

* **Language:** Python 3.10+
* **Data Manipulation:** pandas, numpy
* **Statistical Modeling:** statsmodels (SARIMAX, ACF/PACF)
* **Machine Learning:** LightGBM, scikit-learn
* **Data Visualization:** matplotlib, seaborn
* **Utilities:** tqdm

---
