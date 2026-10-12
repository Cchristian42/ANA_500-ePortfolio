# Micro-Project 4: Predicting Vulnerability Severity with Deep Learning Regression

**ANA500 Micro-Project Portfolio, Project 4 of 4**
**Data Science Process steps covered:** Problem Statement, Hypothesis, Acquire, Prepare,
Analyze, Report, Act

## What this is

This is the last of a four-part portfolio that uses publicly disclosed software
vulnerability data (CVE records from the National Vulnerability Database) to explore how
security teams can better prioritize patching. It asks whether deep learning, and in
particular a recurrent neural network (LSTM) that reads each CVE's written description,
can estimate the CVSS v3.x score of **newly published** CVEs better than the Micro-Project 3
regression models, including when the weakness type (CWE) has not been assigned yet.

Every neural network is compared with the Micro-Project 3 models using the **same baseline
(always guess the average) and the same metrics** (MAE, RMSE, R-squared, training vs. test
R-squared), plus severity-band precision, recall, F1, and confusion matrices.

Models: baseline, Ridge regression and RBF SVR (Micro-Project 3 settings), a feed-forward
neural network (MLP) on the same five descriptive facts, an LSTM on the description text,
and a combined LSTM + facts network, built with scikit-learn and TensorFlow/Keras.

## Test design

- **Random split** (Micro-Project 3's exact 75/25 split), kept as a continuity check.
- **Chronological split** (main test): oldest 75% of CVEs for training, newest 25% for
  testing, so every test CVE was published after every training CVE.
- **Day-one test:** the chronological test set graded again with the weakness type
  replaced by "Unknown", the way a not-yet-analyzed CVE looks.

## Data source

[`fkie-cad/nvd-json-data-feeds`](https://github.com/fkie-cad/nvd-json-data-feeds), a
community-maintained reconstruction of NIST's NVD bulk JSON feeds, rebuilt from the official
NVD API 2.0. Same source and 2015 to 2026 window as Micro-Projects 1 to 3. The notebook
re-runs the download and the same extractor, so it is self-contained. The feed is live, so
record counts drift upward slightly between runs.

## Running this notebook

```bash
pip install pandas numpy matplotlib scikit-learn tensorflow jupyter
jupyter nbconvert --to notebook --execute --inplace ANA500_MicroProject4_CVE_DeepLearning.ipynb
```

Runtime is roughly 30 minutes on a 2-core CPU, most of it training the two LSTM models.
The `data/` folder (raw archives and cleaned dataset) is excluded from version control and
is fully reproducible by re-running the notebook. Charts and result tables are written to
`figures/`.

## Key results (chronological split, 74,685 newest CVEs)

| Model | Test MAE | Test R-squared |
|---|---|---|
| Baseline (guess average) | 1.40 | 0.00 |
| Ridge (Micro-Project 3) | 1.27 | 0.12 |
| SVR (Micro-Project 3, 30k sample) | 1.22 | 0.15 |
| MLP on the same five facts | 1.26 | 0.12 |
| LSTM on the description | 1.15 | 0.25 |
| **LSTM + five facts** | **1.11** | **0.29** |

- Ridge reproduces Micro-Project 3 on the random split (R-squared 0.31) but falls to 0.12 on
  newer CVEs.
- The MLP beat Ridge on the random split but only matched it on newer CVEs: complexity
  alone did not help.
- With the weakness type unknown, Ridge and the MLP do worse than the baseline; the LSTM
  models stay well ahead of it.
- CRITICAL recall (as a band): 15% for LSTM + five facts vs. 3% for Ridge. Flagging every
  CVE predicted 7.0 or higher catches 80% of truly CRITICAL CVEs (Ridge: 72%).
