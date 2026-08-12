# Predicting Attendance and Churn: Two Views of the Fan Lifecycle

This project builds and compares machine learning models on two datasets — NBA game attendance and telecom customer churn — as part of a data science assignment exploring how predictive modelling supports decisions in a sports/marketing context (e.g. attendance forecasting and fan/member churn, both named responsibilities in the NBL Data Scientist graduate role).

## Datasets

**Dataset A — NBA game attendance**
Source: [wyattowalsh/NBA-attendance-prediction](https://github.com/wyattowalsh/NBA-attendance-prediction) (`data/processed/dataset.csv`)
30,546 games, January 1990 – December 2019, 16 attributes. Target: attendance. Features cover team form, game context (playoff, rivalry, day of week), historical attendance, and Google Trends search popularity.

**Dataset B — Telecom customer churn**
Source: [Kaggle — barun2104/telecom-churn](https://www.kaggle.com/datasets/barun2104/telecom-churn)
3,333 customers, 11 attributes. Target: churn (14.5% positive class). Features cover account details (tenure, contract renewal, plan), usage (data, minutes, calls), and cost (monthly charge, overage fee).

## Notebook

[`notebook.ipynb`](./notebook.ipynb) — full analysis, from baseline models through to feature importance and evaluation.

## Approach

For each dataset, a naive baseline was set first (predicting the training mean for attendance; predicting "no churn" for every customer), then two models were trained and compared: **XGBoost** and an **RBF-kernel SVM** (SVR for attendance, SVC for churn). For attendance, XGBoost was also trained on the same 5,000-row subsample used for SVR, to confirm its advantage wasn't just a function of having more training data.

## Results

**Attendance (regression)**

| Model | Train rows | MAE | RMSE | R² |
|---|---|---|---|---|
| Baseline (train mean) | 0 | 1,886 | 2,256 | 0.000 |
| SVR (RBF) | 5,000 | 1,247 | 1,713 | 0.357 |
| XGBoost | 5,000 | 1,056 | 1,518 | 0.495 |
| **XGBoost** | **24,239** | **958** | **1,373** | **0.587** |

XGBoost beat SVR even on identical training data, then improved further with the full dataset — its best model is off by only ~5.3% of average attendance. The strongest predictor by far is which team is playing at home (led by the Chicago Bulls), followed by attendance at the team's last game and day of week.

**Churn (classification)**

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC | PR-AUC |
|---|---|---|---|---|---|---|
| Baseline ("no one churns") | 0.855 | – | 0.000 | 0.000 | – | – |
| SVM (RBF) | 0.918 | 0.818 | 0.557 | 0.663 | 0.887 | 0.754 |
| **XGBoost** | 0.930 | 0.805 | 0.680 | 0.737 | 0.873 | 0.787 |

SVM edges XGBoost on ROC-AUC, but XGBoost wins on PR-AUC — the metric that matters more given the 14.5% class imbalance — as well as recall and F1. The strongest churn predictors are data plan status, contract renewal, and customer service calls; account tenure, initially expected to matter most, ranked lowest of all ten features. Targeting the riskiest 10% of customers by predicted probability catches churners at 6.36x the base rate (92.4% vs 14.5%), though this lift is likely inflated by the dataset being clean and semi-synthetic.

## Key takeaways

- XGBoost outperformed SVM on both problems, and the equal-data comparison on attendance confirms this is a genuine model advantage, not just a data-volume effect.
- ROC-AUC and PR-AUC disagreed on the better churn model — a reminder that metric choice matters under class imbalance.
- The two datasets are complementary rather than competing: one predicts demand for a single event, the other predicts whether an individual sticks around long-term — two different halves of the fan/customer lifecycle.
- Both models comfortably beat their baselines but have real limitations (attendance model overpredicts low-turnout games; churn model still misses roughly a third of churners at the default threshold).

## Requirements
pandas
numpy
scikit-learn
xgboost
matplotlib

## Author

Arya Khamkar
