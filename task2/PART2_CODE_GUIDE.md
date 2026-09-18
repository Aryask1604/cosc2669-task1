# Part 2 Code Guide: Skeleton + Logic

COSC2669 Task 2, for Arya's `task2-analysis.ipynb`

**How to use this guide**

- `___` means a blank you fill in. `# TODO:` means lines you write yourself.
- Each block has **Logic** (why), a **Skeleton** (structure) and a **Check** (what correct looks like). Don't move on until the Check passes.
- Most of the code matches your Task 1 notebook, so open it side by side.
- **Declare this guide**: add a PROCESS_LOG row ("Claude gave a code skeleton with blanks and explanations; I wrote and ran the code") and include it in the Condition 3 declaration.
- Commit after each section: `git add task2 && git commit -m "Task 2: <section>" && git push`

**Facts I checked in your data (use them in Checks and write-up)**

| Fact | Value |
|---|---|
| Attendance rows / already sorted by `Time`? | 30,546 / **yes** (still sort, to be safe) |
| Rows before 2015-01-01 (Task 1 train) | 24,239 |
| Rows before 2005-01-01 (era split) | 13,836 |
| `H Pop` range | 0–100, heavily skewed (most games 1–5). Check the dataset repo README for what it measures |
| Churn customers with `CustServCalls >= 4` | 267 (8%). **Small group** |
| Churn customers with `DataPlan = 1` | 922 |

---

## Section 0: Setup

**Logic:** import everything once at the top so a marker can re-run the notebook from top to bottom.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

from sklearn.model_selection import (TimeSeriesSplit, StratifiedKFold,
                                     cross_validate, cross_val_predict,
                                     learning_curve, GridSearchCV)
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVR, SVC
from sklearn.metrics import (mean_absolute_error, mean_squared_error, r2_score,
                             precision_score, recall_score, f1_score)
from xgboost import XGBRegressor, XGBClassifier

RANDOM_STATE = ___       # same as Task 1
SUBSAMPLE_N  = ___       # same SVR subsample size as Task 1
```

**Check:** runs with no `ModuleNotFoundError`.

---

## Section 1: Data (same preprocessing as Task 1)

### 1.1 Attendance

**Logic**

- Copy Task 1 cells 3–4, with **three changes**:
  1. **Sort by `Time`.** TimeSeriesSplit assumes row order = time order.
  2. **Save a "meta" table** (`Time`, `H Pop`, `Attendance`) *before* dropping `Time`. Part 2(c) needs these for fairness groups.
  3. **Decide the CV scope.** Either use all 30,546 rows, or only the pre-2015 training rows so the 2015+ test set stays untouched. Write your reason in the log.
- **Keep `reset_index(drop=True)` after sorting.** Otherwise `.iloc` positions and index labels won't match later.

```python
nba = pd.read_csv("data/dataset.csv")
nba["Time"] = pd.to_datetime(nba["Time"], format="ISO8601")
# TODO: sort by Time, then reset the index (drop=True)

df = nba.drop(columns=[___])                           # leakage column from Task 1
meta_att = df[["Time", "H Pop", "Attendance"]].copy()  # for fairness later
train_mask = df["Time"] < "2015-01-01"

df = df.drop(columns=["Time"])
y = df.pop(___)
X = pd.get_dummies(df, columns=[___], drop_first=True).astype(float)

# DECISION: CV on all rows, or only train_mask rows?
X_cv    = ___
y_cv    = ___
meta_cv = ___
# TODO: if you use the subset, reset_index(drop=True) on all three so they line up

print(X.shape, X_cv.shape, nba["Time"].is_monotonic_increasing)
```

**Check:** `X.shape == (30546, 81)`, and `X_cv` has 30,546 or 24,239 rows. The last value must be `True`.

### 1.2 Churn

```python
churn = pd.read_csv("data/telecom_churn.csv")
yc = churn.pop(___)
Xc = churn
meta_churn = Xc[[___, ___, ___]].copy()   # the 3 columns you'll group by in 2(c)
print(Xc.shape, yc.mean().round(3))
```

**Check:** `(3333, 10)` and `0.145`.

### 1.3 Model "factories"

**Logic:** CV must train a **fresh, unfitted** model in every fold. A function that *returns* a new model makes this automatic and keeps your Task 1 hyperparameters in one place.

- SVR/SVC go inside a **Pipeline** so the scaler is fitted inside each fold. That's the leakage-safe version of what you did in Task 1.
- XGBoost doesn't need scaling.
- **Name your pipeline steps.** Those names are used in GridSearchCV later (e.g. `svc__C`).
- For the SVC in CV, leave out `probability=True`. `roc_auc` and `average_precision` then use `decision_function`, which gives the same ranking and runs about 5× faster. Mention this in your notes.

```python
def make_svr():
    return Pipeline([("scale", StandardScaler()),
                     ("svr", SVR(kernel="rbf", C=___, gamma="scale", epsilon=___))])

def make_xgb_reg():
    return XGBRegressor(___)   # TODO: copy exactly from Task 1 cell 8

def make_svc():
    return Pipeline([("scale", StandardScaler()),
                     ("svc", SVC(kernel="rbf", C=___, gamma="scale"))])

def make_xgb_clf():
    return XGBClassifier(___)  # TODO: copy exactly from Task 1 cell 28
```

**Check:** `make_svr()` prints a Pipeline, and calling it twice gives two separate objects.

---

## Section 2: Part 2(a) Cross-validation

### 2.1 Churn (do this first, it's fast)

**Logic**

- **StratifiedKFold(10, shuffle, seed):** each fold keeps about 14.5% churners. Without stratification, a fold could have very few churners and recall would jump around.
- `cross_validate` does fit and score for every fold in one call.
- `return_train_score=True` gives you train vs test scores, an early sign of overfitting.

```python
skf = StratifiedKFold(n_splits=___, shuffle=___, random_state=___)

# Check the folds BEFORE training
for k, (tr, te) in enumerate(skf.split(Xc, yc)):
    print(k, len(tr), len(te), round(yc.iloc[te].mean(), 3), int(yc.iloc[te].sum()))

scoring_c = [___]   # accuracy, precision, recall, f1, roc_auc, average_precision

cv_churn = {}
for name, make in [("SVM (RBF)", make_svc), ("XGBoost", make_xgb_clf)]:
    res = cross_validate(make(), Xc, yc, cv=skf, scoring=scoring_c,
                         return_train_score=True, n_jobs=-1)
    cv_churn[name] = pd.DataFrame(res)

# TODO: build a summary with one row per model and columns = mean and std of
#       each test_* metric. Hint: .filter(like="test_") then .agg(["mean", "std"])
```

**Check:** each fold has about 333 test rows and about 48 churners. The XGBoost mean F1 should be in the same ballpark as Task 1 (0.737); if it's wildly different, check your preprocessing.

### 2.2 Attendance (manual loop)

**Logic**

- **Why a manual loop?** `cross_validate` can't subsample inside each fold, but Task 1 trained SVR on 5,000 rows. To be a fair re-evaluation, CV has to repeat that per fold.
- **TimeSeriesSplit(5):** each fold trains on *earlier* games and tests on the *next* block, like predicting next season.
- **Fold sizes:** with all rows, the first fold trains on 5,091 rows. With the pre-2015 subset, the first fold trains on only **4,044**, fewer than 5,000. That's why you need `min(...)`. Log it as an issue and limitation.
- **Store the predictions** as you go. Part 2(c) fairness needs them, and `cross_val_predict` **doesn't work** with TimeSeriesSplit because the test blocks don't cover every row.

```python
tss = TimeSeriesSplit(n_splits=5)
rng = np.random.RandomState(RANDOM_STATE)
rows, pred_rows = [], []

for fold, (tr, te) in enumerate(tss.split(X_cv)):
    # TODO: print fold, len(tr), len(te), and the date range of train and test
    #       (use meta_cv["Time"].iloc[tr] / .iloc[te] with .min() and .max())

    X_tr, X_te = X_cv.iloc[tr], X_cv.iloc[te]
    y_tr, y_te = ___, ___

    n   = min(SUBSAMPLE_N, len(tr))
    sub = rng.choice(len(tr), n, replace=False)
    X_sub, y_sub = ___, ___               # .iloc with sub

    for model_name, make, X_fit, y_fit in [
        ("SVR (5k)",       make_svr,     X_sub, y_sub),
        ("XGBoost (5k)",   make_xgb_reg, X_sub, y_sub),
        ("XGBoost (full)", make_xgb_reg, ___,   ___),
    ]:
        model = make()
        # TODO: fit on X_fit, y_fit, then predict on X_te
        pred = ___

        rows.append({"fold": fold, "model": model_name, "train_rows": len(X_fit),
                     "MAE": ___, "RMSE": ___, "R2": ___})

        pred_rows.append(pd.DataFrame({"row": te, "fold": fold, "model": model_name,
                                       "actual": y_te.values, "pred": pred}))

cv_att    = pd.DataFrame(rows)
preds_att = pd.concat(pred_rows, ignore_index=True)   # used in 2(c)

# TODO: summary = mean and std of MAE/RMSE/R2 per model (groupby "model")
```

**Check:**

- Train sets grow each fold; test sets are all the same size.
- Train dates always end before test dates start.
- SVR takes roughly 30–90 seconds per fold; if it runs for more than 5 minutes, you're not using the subsample.

### 2.3 Comparison table with Task 1

**Logic:** the question is whether Task 1's single split was typical. If `|Task 1 − CV mean| ≤ CV std`, it was a typical split. If Task 1 is better than mean + std, it was lucky.

```python
task1 = pd.DataFrame([
    # TODO: type your Task 1 numbers from the report, e.g.
    # {"dataset": "Churn", "model": "XGBoost", "metric": "f1", "task1": 0.737},
    ___
])
# TODO: reshape your CV summaries to the same long format
#       (dataset, model, metric, cv_mean, cv_std)
# TODO: merge task1 with the CV table on [dataset, model, metric]
# TODO: add columns: diff = task1 - cv_mean ; within_1sd = abs(diff) <= cv_std
# TODO: save to outputs/cv_comparison.csv
```

**Write in the notes cell (your words):**

- Does the ranking change?
- Which metric has the biggest std, and why? Think about churners per fold.
- Do later attendance folds score worse, suggesting drift?
- What bias is still left in the evaluation (no tuning, subsample, one seed)?

### 2.4 Optional (top band): Nested CV for SVC

**Logic:** if you tune C and gamma using the same folds you report, the score is optimistically biased. Nested CV runs tuning *inside* each outer training fold, so outer test folds never influence tuning.

```python
inner = StratifiedKFold(n_splits=5, shuffle=True, random_state=RANDOM_STATE)
param_grid = {"svc__C": [___], "svc__gamma": [___]}   # step name must match your Pipeline
tuned = GridSearchCV(make_svc(), param_grid, cv=inner, scoring=___, n_jobs=-1)

nested = cross_validate(tuned, Xc, yc, cv=skf, scoring=scoring_c, return_estimator=True)
# TODO: compare nested mean scores vs your untuned CV scores
# TODO: print best_params_ from each outer fold. Are they stable?
```

---

## Section 3: Part 2(b) Learning curves

### 3.1 Plot helper (write once, use for all curves)

**Logic:** each score array is shaped *(n_sizes × n_folds)*. Take the mean and std across **folds** (axis=1). Draw training dashed and validation solid, in the same colour per model.

```python
def plot_lc(ax, sizes, train_scores, val_scores, label, color):
    # TODO: tr_mean, tr_std, va_mean, va_std  (axis=1)
    # TODO: ax.plot(sizes, tr_mean, "--", color=color, label=f"{label} train")
    # TODO: ax.plot(sizes, va_mean, "-",  color=color, label=f"{label} validation")
    # TODO: ax.fill_between(...) for both, alpha=0.15
    pass
```

### 3.2 Churn

```python
fig, ax = plt.subplots(figsize=(7, 4.5))
for name, make, color in [("SVM (RBF)", make_svc, "tab:blue"),
                          ("XGBoost", make_xgb_clf, "tab:orange")]:
    sizes, tr_sc, va_sc = learning_curve(make(), Xc, yc, cv=skf,
                                         train_sizes=np.linspace(___, ___, 8),
                                         scoring=___, n_jobs=-1)
    plot_lc(ax, sizes, tr_sc, va_sc, name, color)
# TODO: axis labels, title, legend, ylim if needed
# TODO: plt.savefig("figures/lc_churn.png", dpi=200, bbox_inches="tight")
```

**Check:** the x-axis goes from about 300 to about 3,000 customers.

### 3.3 Attendance (manual loop again)

**Logic**

- `learning_curve` with TimeSeriesSplit is limited by the **first (smallest)** fold and always takes the *oldest* rows, so it can't show XGBoost beyond about 5k.
- The manual version: for each fold and each size `n`, train on the **most recent n games** before the test block (`tr[-n:]`), which is how NBL would actually retrain.
- Skip a size if it's larger than that fold's training set.
- Only run SVR up to about 8k rows (speed); run XGBoost up to the full size.
- **This directly tests your Task 1 claim** that the XGB-5k vs XGB-full gap wasn't just a data effect.

```python
sizes_att = [___]   # e.g. several sizes from ~500 up to the largest fold's training size
lc_rows = []

for fold, (tr, te) in enumerate(tss.split(X_cv)):
    for n in sizes_att:
        if n > len(tr):
            continue
        use = tr[-n:]
        for model_name, make in [("SVR", make_svr), ("XGBoost", make_xgb_reg)]:
            if model_name == "SVR" and n > ___:
                continue
            model = make()
            # TODO: fit on X_cv.iloc[use], y_cv.iloc[use]
            # TODO: train_mae = MAE on the same rows; val_mae = MAE on te
            lc_rows.append({"fold": fold, "n": n, "model": model_name,
                            "train_MAE": ___, "val_MAE": ___})

lc_att = pd.DataFrame(lc_rows)
# TODO: groupby(["model", "n"]) -> mean and std of train_MAE, val_MAE
# TODO: plot (same style as plot_lc, but from this table)
# TODO: save figures/lc_attendance.png
```

**Check:** training MAE should be lower than validation MAE, and the gap should narrow as `n` grows.

**Interpret (your words):**

- **Big gap** = variance/overfitting.
- **Both high and flat** = bias.
- **Validation still falling at the right edge** = more data helps.
- For XGBoost, does validation MAE keep improving past 5k? Does that support, weaken or correct your Task 1 claim?

---

## Section 4: Part 2(c) Fairness with Fairlearn

### 4.0 Imports

```python
from functools import partial
from fairlearn.metrics import (MetricFrame, count, selection_rate,
                               false_positive_rate, false_negative_rate,
                               demographic_parity_difference, equalized_odds_difference)
```

### 4.1 Churn

**Logic**

- `cross_val_predict` gives an out-of-fold prediction for **all 3,333** customers. Each prediction comes from a model that never saw that customer, so the groups are about 5× bigger than the 667-row test set.
- **Proxy groups:** the dataset has no protected attributes (age, gender and so on), so you check groups the business acts on.
- **Always print the group's true churn rate next to the metrics.** If the churn rate differs by group, part of any gap comes from the *data* (different base rates), not only from the *model*. Separating these is the core of the analysis.

```python
yc_pred = cross_val_predict(make_xgb_clf(), Xc, yc, cv=skf, n_jobs=-1)
# (optional: repeat for make_svc() to compare models)

groups_c = {
    "DataPlan":     meta_churn["DataPlan"].map({0: ___, 1: ___}),
    "ServiceCalls": np.where(meta_churn["CustServCalls"] >= 4, ___, ___),
    "Tenure":       pd.qcut(meta_churn["AccountWeeks"], 4, labels=[___]),
}

metrics_c = {
    "n":              count,
    "churn_rate":     lambda y_t, y_p: np.mean(y_t),   # true base rate per group
    "selection_rate": selection_rate,
    "recall":         recall_score,
    "precision":      partial(precision_score, zero_division=0),
    "FNR":            false_negative_rate,
    "FPR":            false_positive_rate,
}

for gname, g in groups_c.items():
    mf = MetricFrame(metrics=metrics_c, y_true=yc, y_pred=___, sensitive_features=g)
    print(f"\n=== {gname} ===")
    display(mf.by_group.round(3))
    # TODO: print mf.difference()
    # TODO: print demographic_parity_difference(...) and equalized_odds_difference(...)
    #       (both need y_true, y_pred, sensitive_features=g)
    # TODO: save mf.by_group to outputs/fair_churn_<gname>.csv
```

**Check:** the group `n` values add up to 3,333. ServiceCalls "4+" should have n = 267.

**Write-up prompts:**

- Which group has the highest FNR (missed churners)? What does that mean for NBL, e.g. members who never get a retention offer?
- Is the gap from base rates or from the model?
- Is any group too small to trust?

### 4.2 Attendance

**Logic**

- Use `preds_att` from 2.2. Pick one model, e.g. "XGBoost (full)", and join its rows back to `meta_cv` using the `row` column.
- **Mean signed error** = mean(pred − actual). Positive means the model **over-predicts** that group, e.g. promo budget is wasted because demand looks higher than it is.
- **`H Pop` is skewed:** choose bins that give reasonable group sizes and check the counts. `pd.qcut(..., q=3, duplicates="drop")` or your own `pd.cut` edges both work; justify the choice.
- **Era:** check both pre-2005 and post-2005 actually appear in your test folds. If you used the pre-2015 subset, the earliest years are never in a test fold.
- **Attendance bins on the actual value:** errors are expected to look biased at the extremes (regression to the mean). Say this in your write-up rather than calling it unfairness outright.

```python
p = preds_att[preds_att["model"] == ___].copy()
p = p.join(meta_cv, on="row")           # adds Time, H Pop, Attendance

def mean_signed_error(y_true, y_pred):
    return ___                           # mean of (pred - actual)

groups_a = {
    "MarketSize": ___,                   # bins of p["H Pop"]
    "Era":        np.where(p["Time"].dt.year < 2005, ___, ___),
    "AttendanceBand": pd.qcut(p["actual"], 4, labels=[___]),
}

metrics_a = {"n": count, "MAE": mean_absolute_error, "signed_error": mean_signed_error}

for gname, g in groups_a.items():
    mf = MetricFrame(metrics=metrics_a, y_true=p["actual"], y_pred=p["pred"],
                     sensitive_features=g)
    # TODO: display by_group, difference(); save CSV
```

**Check:** `p["Attendance"]` should equal `p["actual"]`; this confirms the join lined up correctly.

### 4.3 Optional (top band): ThresholdOptimizer

**Logic:** post-processing picks **different decision thresholds per group** so that error rates (TPR/FPR) are equal across groups. It never retrains the model. Then measure what it costs in overall accuracy, recall and F1: the fairness–performance trade-off.

- It must be fitted on **training** data and evaluated on **test** data, so use the Task 1 80/20 stratified split here.

```python
from sklearn.model_selection import train_test_split
from fairlearn.postprocessing import ThresholdOptimizer

# TODO: same split as Task 1 (test_size, stratify, random_state); also split the group column
# TODO: fit make_xgb_clf() on the training part

to = ThresholdOptimizer(estimator=___, constraints="equalized_odds",
                        objective="balanced_accuracy_score",
                        prefit=True, predict_method="predict_proba")
to.fit(___, ___, sensitive_features=___)
pred_fair = to.predict(___, sensitive_features=___, random_state=RANDOM_STATE)

# TODO: table with overall accuracy/precision/recall/F1: original vs ThresholdOptimizer
# TODO: MetricFrame by group for both; compare equalized_odds_difference before/after
```

---

## Section 5: Part 2(d)

No code. Research notes only (Privacy Act 1988 / APPs, membership inference, data poisoning, targeted-marketing ethics).

---

## Section 6: Export for Overleaf

- **Figures:** `plt.savefig("figures/<name>.png", dpi=200, bbox_inches="tight")`, then upload to Overleaf's `figures/` folder.
- **Tables:** `df.to_csv("outputs/<name>.csv")`. For a LaTeX table, `print(df.to_latex(index=False, float_format="%.3f"))` gives the tabular body; add `\caption` and `\label` yourself.
- **Before submitting:** Kernel → **Restart & Run All**. It must run top to bottom with no errors. Commit and push.

---

## Common errors and what they mean

| Error / symptom | Likely cause |
|---|---|
| `ValueError: train_sizes ... must be within (0, n]` | `learning_curve` sizes are bigger than the smallest fold's training set |
| `Invalid parameter 'C' for estimator Pipeline` | Grid keys need the step name: `svc__C`, not `C` |
| `IndexError: positional indexers are out-of-bounds` | Used `.iloc` after filtering without `reset_index(drop=True)` |
| Fairness `n` doesn't add to the total | `NaN` groups, e.g. `map()` missed a value; check `g.isna().sum()` |
| `UndefinedMetricWarning` in precision | A group has zero predicted positives. Use `zero_division=0` and mention the group |
| SVR runs forever | You're fitting on the full fold, not `X_sub` |
| CV scores are much better than Task 1 | Check for leakage: `Capacity` still in X, or the scaler fitted outside the Pipeline |
