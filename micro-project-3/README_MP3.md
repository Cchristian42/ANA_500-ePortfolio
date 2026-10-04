# Micro-Project 3 — Predicting Vulnerability Severity with ML Regression

**ANA500 Micro-Project Portfolio — Project 3 of 4**
**Data Science Process steps covered:** Problem Statement → Hypothesis → Acquire → Prepare →
Analyze → Report → Act

## What this is

This project is the third of a four-part portfolio that uses publicly disclosed software
vulnerability data (CVE records from the National Vulnerability Database) to explore how
security teams can better prioritize patching. It asks whether a CVE's CVSS v3.x severity
score can be estimated from basic descriptive facts (vendor, product, weakness type (CWE),
publication year, and number of affected products), **without** using the score's own
ingredients (attack vector, exploitability and impact sub-scores), which would leak the answer.

Models: a mean-guessing baseline, linear regression, Ridge regression (tuned with 5-fold
GridSearchCV), and RBF-kernel Support Vector Regression (tuned with 5-fold GridSearchCV),
all built as scikit-learn pipelines and graded on a 25% held-out test set.

## Data source

[`fkie-cad/nvd-json-data-feeds`](https://github.com/fkie-cad/nvd-json-data-feeds), a
community-maintained reconstruction of NIST's NVD bulk JSON feeds, rebuilt from the official
NVD API 2.0. Same source and 2015–2026 window as Micro-Projects 1 and 2. This notebook
re-runs the download and the same extractor, so it is self-contained.

Changes from Micro-Project 1:
- CVEs with no score yet stay in the main cleaned dataset and are only excluded from the
  modeling subset.
- The model predicts only CVSS v3.0/v3.1 scores (one consistent scale), not a score mixed
  across CVSS versions.

## Running this notebook

```bash
pip install pandas numpy matplotlib scikit-learn jupyter
jupyter nbconvert --to notebook --execute --inplace ANA500_MicroProject3_CVE_Regression.ipynb
```

Runtime is roughly 4–5 minutes (most of it is the SVR). The `data/` folder (raw archives,
cleaned dataset, and the saved train/test split) is excluded from version control and is
fully reproducible by re-running the notebook. Charts and result tables are written to
`figures/`.

## Key results

- Modeling subset: **296,066** CVEs with a CVSS v3.x score; 75/25 split (222,049 train /
  74,017 test, `random_state=42`). The split is saved by CVE ID.
- Baseline (always guess the average): **1.42** points average error (MAE).
- Linear / Ridge regression: **1.10** MAE (23% below baseline), R² 0.31.
- SVR, RBF kernel (30,000-row sample): **1.06** MAE (26% below baseline), R² 0.31.
- No meaningful overfitting: train vs. test R² gap of 0.005 (linear) and 0.04 (SVR).
- Triage test: flagging CVEs predicted ≥ 7.0 means 77% of flagged CVEs are truly HIGH or
  CRITICAL (vs. 55% at random), and the top quarter of the ranked queue holds 59% of all
  CRITICAL CVEs.
- Limitation: 99.6% of CVEs still awaiting a score have no CWE yet, so the model cannot
  meaningfully rank them.
