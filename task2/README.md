# COSC2669 Individual Task 2 - Analysis

Arya Khamkar (s4152566), RMIT University

Part 2 of Individual Task 2 reflects on the Individual Task 1 Part 1 analysis
(`../s4152566-task1-analysis.ipynb`). It covers cross-validation, learning
curves, fairness assessment with Fairlearn, and deployment risks.

## Structure

```
task2/
├── task2-analysis.ipynb   # Part 2 notebook
├── data/                  # CSVs (not committed, see Data below)
├── figures/               # PNG plots for Overleaf
├── outputs/               # exported result tables (CSV)
├── screenshots/           # process evidence (AI use, errors, fixes)
├── overleaf/              # main.tex + references.bib skeleton
├── PROCESS_LOG.md         # AI use + issues/fixes log
└── requirements.txt
```

## Data

| File | Source |
|---|---|
| `data/dataset.csv` | NBA attendance, github.com/wyattowalsh/NBA-attendance-prediction (`data/processed/dataset.csv`) |
| `data/telecom_churn.csv` | Telecom churn, kaggle.com/datasets/barun2104/telecom-churn |

## Setup

Run this in the first cell of the notebook so packages install into the
kernel Jupyter is actually using:

```python
%pip install -r requirements.txt
```

Random seed: `RANDOM_STATE = 7` (same as Task 1).

## Commit as you go

```bash
cd ~/cosc2669_task1
git add task2
git commit -m "Task 2: <what you finished>"
git push
```
