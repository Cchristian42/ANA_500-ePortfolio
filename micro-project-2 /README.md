# Micro-Project 2 — Visualizing NVD Vulnerability (CVE) Data

**ANA500 Micro-Project Portfolio — Project 2 of 4**
**Data Science Process steps covered:** Analyze data -> Report
(this project builds on Micro-Project 1's cleaned dataset and stops at Report per the
assignment scope)

## What this is

This project is the second of a four-part portfolio that uses publicly disclosed
software vulnerability data (CVE records from the National Vulnerability Database) to
explore how security teams can better prioritize patching and plan analyst workload.
This notebook takes the cleaned dataset produced in Micro-Project 1 and visualizes it
with Matplotlib and Plotly, organized around six chart categories: distributions,
category comparisons, relationships, change over time, part-to-whole, and a
correlation heatmap. Micro-Project 3 will use these same patterns to build a
regression model of vulnerability severity, and Micro-Project 4 will forecast
disclosure volume with a deep-learning time-series model.

## Data source

[`fkie-cad/nvd-json-data-feeds`](https://github.com/fkie-cad/nvd-json-data-feeds), a
community-maintained reconstruction of NIST's National Vulnerability Database bulk
JSON feeds, built from the official NVD API 2.0. See `micro-project-1/README.md` for
full acquisition and cleaning detail; this notebook re-runs that same pipeline to
refresh the dataset (304,115 cleaned records, 37 columns, as of this run).

## Running this notebook

```bash
pip install pandas numpy matplotlib seaborn plotly kaleido jupyter
jupyter nbconvert --to notebook --execute --inplace ANA500_MicroProject2_CVE_Visualization.ipynb
```

This notebook expects `data/cve_clean.csv` to already exist (produced by running
`micro-project-1/ANA500_MicroProject1_CVE_Analysis.ipynb` first). As with Micro-Project
1, the `data/` folder is excluded from version control since it is large and fully
reproducible from the public source above.

## Key results from this micro-project

- Annual CVE disclosures have grown roughly twelve-fold since 2015 and are still
  accelerating; monthly data shows a sharp, sustained surge starting in early 2026.
- MEDIUM and HIGH severity CVEs make up over 80% of the dataset; the CRITICAL share of
  disclosures has generally grown by year, not shrunk.
- CVEs reachable over a Network are both the most common attack-vector group and the
  easiest to exploit by CVSS's own scoring, supporting the hypothesis that structural
  CVSS traits relate to severity.
- Overall CVSS score correlates strongly with the Impact sub-score (0.76) and
  moderately with Exploitability (0.40); the two sub-scores are only weakly related to
  each other (-0.08), meaning both carry independent signal for the regression model
  planned in Micro-Project 3.
