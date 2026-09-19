# Micro-Project 1 — Preparing NVD Vulnerability (CVE) Data

**ANA500 Micro-Project Portfolio — Project 1 of 4**
**Data Science Process steps covered:** Problem Statement → Hypothesis → Acquire → Prepare
(this project stops at Prepare per the assignment scope)

## What this is

This project is the first of a four-part portfolio that uses publicly disclosed software
vulnerability data (CVE records from the National Vulnerability Database) to explore how
security teams can better prioritize patching and plan analyst workload. This notebook
acquires ~12 years of CVE data and cleans it into an analysis-ready dataset. Later
micro-projects in this portfolio will build on this same dataset for visualization
(Micro-Project 2), regression modeling of vulnerability severity (Micro-Project 3), and
deep-learning time-series forecasting of disclosure volume (Micro-Project 4).

## Data source

[`fkie-cad/nvd-json-data-feeds`](https://github.com/fkie-cad/nvd-json-data-feeds) — a
community-maintained reconstruction of NIST's National Vulnerability Database bulk JSON
feeds, built directly from the official NVD API 2.0 (NIST deprecated its own bulk
year-by-year downloads in December 2023). This is **not** a third-party scrape or a
pre-flattened mirror — it repackages the same official NVD data NIST used to provide
directly, refreshed every two hours.

## Running this notebook

```bash
pip install pandas numpy jupyter
jupyter nbconvert --to notebook --execute --inplace ANA500_MicroProject1_CVE_Analysis.ipynb
```

The notebook downloads whatever yearly archives aren't already present under `data/raw/`
and writes the cleaned dataset to `data/cve_clean.csv`. First run needs internet access;
subsequent runs skip re-downloading anything already present.

**Note:** the `data/` folder is intentionally excluded from version control (see
`.gitignore`) — the raw archives total ~85MB and the cleaned CSV is ~160MB, both well past
what's reasonable to commit to a git repo, and both are fully reproducible by re-running
this notebook against the public source above.

## Key results from this micro-project

- 321,240 raw CVE records parsed from 12 years (2015–2026) of nested JSON.
- 15,357 `Rejected` (withdrawn) CVE IDs removed — not real vulnerabilities.
- 3,011 additional records removed for having no CVSS score of any version yet.
- Final analysis-ready dataset: **302,872** scored vulnerability records, 37 columns,
  spanning CVSS versions 2.0 through 4.0, with parsed dates and a unified severity column.
