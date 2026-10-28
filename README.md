# Manufacturing Wage Forecasting in Mexico Using Holt-Winters

## Project Overview
This academic project analyzes **120 monthly observations of average hourly compensation** in Mexico's manufacturing sector (January 2012–December 2021) and produces forecasts for **2022–2024**. The objective is to understand historical trends, recurring patterns, and what the projections could imply for wage policy.

## Research Questions
- Has hourly compensation increased or decreased over time?
- Is there a repeating pattern, and how often does it occur?
- How accurately can Holt-Winters forecast hourly compensation?
- What should policymakers consider when interpreting the forecasts?

## Data and Workflow
1. Load the Excel dataset with **Pandas**, parse the `Periodo` column, and set the time index to monthly-start frequency (`asfreq("MS")`).
2. Plot the historical series and inspect its trend and recurring spikes.
3. Explore autocorrelation (ACF), stationarity diagnostics, and a periodogram.
4. Use a **chronological 80/20 split**: 96 months (2012–2019) for training and 24 months (2020–2021) for testing.
5. Fit Holt-Winters models with additive trend and either additive or multiplicative seasonality, using `seasonal_periods=12`.
6. Compare out-of-sample errors, select the better-performing model, and refit it using all 120 observations to forecast the next 36 months.

## Concepts Explained

| Concept | What it means in this project |
| --- | --- |
| **Time series** | Observations recorded in chronological order: one hourly-compensation value for each month. |
| **Monthly frequency (`MS`)** | Dates are indexed at the **start of each month**. This is about the spacing and labeling of observations, not the seasonal cycle. |
| **Trend** | The longer-term direction of compensation, which generally rises in the historical series. |
| **Seasonality** | A pattern that repeats at regular intervals, such as a recurring annual peak. |
| **Autocorrelation (ACF)** | Measures how closely a month's compensation relates to values a given number of months earlier. Peaks at lags 12, 24, etc. suggest an annual pattern. |
| **Lag** | The number of months separating two observations. Lag 12 compares values one year apart. |
| **Periodogram** | Shows the strength of different cyclical frequencies in the series. |
| **Frequency** | The number of cycles per month. A 12-month cycle has frequency `1/12 = 0.08333` cycles/month; a three-month cycle has frequency `1/3 = 0.33333` cycles/month. **0.08333 is not an 8.33% wage increase.** |
| **Seasonal period (`L = 12`)** | Tells Holt-Winters to model a full seasonal cycle spanning 12 monthly observations. Unlike `MS`, this controls the modeled repeating pattern. |
| **Holt-Winters exponential smoothing** | A forecasting approach that estimates the series' level, trend, and seasonality, giving more weight to recent information. |
| **Additive seasonality** | Seasonal effects are modeled as approximately fixed amounts added to or subtracted from the baseline. |
| **Multiplicative seasonality** | Seasonal effects are modeled as proportions of the baseline; their absolute size changes with the level. |
| **Alpha (α)** | Smoothing parameter for the estimated level. |
| **Beta (β)** | Smoothing parameter for the estimated trend. |
| **Gamma (γ)** | Smoothing parameter for updating seasonal factors. An estimated γ of zero does **not** mean there is no seasonality. |
| **Train/test split (80/20)** | The first 80% of months are used to fit candidate models; the last 20% are held out to assess forecasting performance. No random shuffling is used. |
| **RMSE** | Root mean squared error: measures prediction errors in the original compensation units and penalizes larger errors more heavily. Lower is better. |
| **MAPE** | Mean absolute percentage error: the average absolute percentage deviation between predictions and observed values. Lower is better. |
| **MPE** | Mean percentage error: retains the sign of errors. With the convention `(actual - predicted)/actual`, positive MPE indicates underprediction on average. |
| **Fitted values vs. forecasts** | Fitted values are model estimates for observed dates; forecasts estimate future or held-out dates. |

### Why use 12 months if the periodogram peaks at 3 months?
The strongest periodogram peak corresponds to a **three-month** period. However, the historical plot and ACF also show an important **annual repeating pattern**. A sharply peaked annual pattern can produce higher-frequency harmonics. We therefore modeled a 12-month seasonal cycle. This is a modeling choice supported by the annual structure, **not proof that 12 months is optimal**; comparing `L=3` and `L=12` on a holdout set would provide stronger evidence.

## Model Evaluation
Both candidates used an additive trend and a 12-month seasonal period.

| Candidate | Test MAPE |
| --- | ---: |
| Holt-Winters, additive seasonality | **~1.47%** |
| Holt-Winters, multiplicative seasonality | ~1.57% |

The **additive seasonal model** had the lower test MAPE and was selected for the final 36-month forecast. These test errors describe performance on 2020–2021 data; they do not guarantee the same accuracy in later years.

## Forecast Results

| Year | Forecast average compensation (MXN/hour) |
| --- | ---: |
| 2022 | 105.84 |
| 2023 | 106.91 |
| 2024 | 107.98 |

The model projects a gradual increase in **nominal hourly compensation**, alongside seasonal fluctuations. These figures are **not monthly salaries** and are **not adjusted for inflation**.

## Policy Interpretation
The forecasts suggest gradual nominal wage growth, but policymakers should examine **inflation and real purchasing power** before concluding that workers will be better off. Forecasts should be updated with more recent data and considered alongside productivity, employment, and broader economic conditions.

## Technologies
Python · Pandas · NumPy · Matplotlib · Statsmodels · Scikit-learn · SciPy · Google Colab · Excel

## Files and Reproducibility
- `Quiz_Holt_Winters.ipynb`: the analysis and forecasting notebook.
- `Ejercicio_Salarios.xlsx`: the course dataset required to rerun the notebook. **Not included here**; place it in the Colab working directory before running.

## Context
Academic time-series forecasting exercise connecting statistical modeling with an economic-policy question.
