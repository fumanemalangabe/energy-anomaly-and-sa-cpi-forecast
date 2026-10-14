# Energy anomaly detection and South African inflation forecast

Two questions: can faulty hourly electricity meter readings be flagged automatically, and where is South African headline inflation heading in 2026?

## Part 1: Anomaly detection

**Data.** The Kaggle Large-scale Energy Anomaly Detection (LEAD) subset: 1,749,494 labelled hourly readings from 200 training buildings and 1,800,567 readings from 206 test buildings, covering 2016.

**Method.** 24 features per building-hour, including a run-length feature for readings that stop changing. Isolation Forest (unsupervised) against LightGBM (supervised), compared with five-fold cross-validation grouped by building so no building appears in both training and validation. Hyperparameters were tuned with nested Bayesian search, and the decision threshold was chosen at maximum F1 on out-of-fold scores.

**Results.**

| Model | Mean PR-AUC | Mean ROC-AUC |
|---|---|---|
| LightGBM | 0.7765 | 0.9542 |
| Isolation Forest | 0.5086 | 0.8994 |
| Random ranking | 0.0227 | 0.5000 |

- At a threshold of 0.9795, LightGBM reaches precision 0.8603, recall 0.6421 and F1 0.7353.
- The run-length feature and its flag carry 41.5% of LightGBM's total gain: most labelled anomalies are meters stuck on one value.
- The final model flags 1.43% of test rows.

## Part 2: Inflation forecast

**Data.** Stats SA headline CPI index (CPS00000, all urban areas), January 2015 to December 2025, 132 months.

**Method.** Six ARIMA and SARIMA specifications against an RBF support vector regression (tuned with Optuna) and a Random Forest, all modelling monthly log changes. Models were scored on 48 one-month forecasts and on 12 quarterly origins with horizons of one to twelve months (144 forecasts each). The best model was refitted on all 132 months to forecast January to December 2026 with 95% prediction intervals.

**Results.**

| Model | Pooled MAE, horizons 1 to 12 (index points) |
|---|---|
| SARIMA(1,1,2)(1,0,1)[12] | 0.9316 |
| Random Forest | 1.2477 |
| SVR | 1.7483 |

- SARIMA ranked first on every metric and at every horizon; its twelve-month error is 25.3% lower than Random Forest's.
- Forecast 2026 year-on-year inflation averages 3.51%, falling to 3.13% in July and ending the year at 3.98%.
- The December 95% interval runs from 1.49% to 6.52%.

## Files

| File | Contents |
|---|---|
| `q1_anomaly_detection.ipynb` | Part 1 notebook with outputs |
| `q2_cpi_inflation_forecast.ipynb` | Part 2 notebook with outputs |
| `reports/` | Written reports for both parts (PDF) |
| `outputs/anomaly_submission.csv` | Binary predictions for every test row |
| `outputs/2026_inflation_forecast.xlsx` | Monthly 2026 forecast and model comparison |

## How to run

Open a notebook in Google Colab. Part 1 needs `train.csv` and `test.csv` from the [LEAD competition](https://www.kaggle.com/competitions/energy-anomaly-detection). Part 2 needs the Stats SA CPI (COICOP) Excel workbook from the [Stats SA time-series page](https://www.statssa.gov.za/?page_id=1847). Update the file paths in the first cells.

## Team

EDAB6808 Assignment 2, BCom Honours in Business Analytics, University of the Free State, 2026. Group project by Fumane Malangabe and Lwazi Nhlapo.
