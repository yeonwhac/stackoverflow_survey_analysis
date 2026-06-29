# Stack Overflow 2024 Developer Survey — Analysis

Exploratory analysis of the Stack Overflow 2024 Developer Survey, covering data cleaning,
EDA, and a business-facing dashboard. Originally built while working through IBM's Data
Analyst Professional Certificate on Coursera; the structure and a subset of the EDA
questions follow the course material, with cleaning logic, an outlier-method comparison,
and one original research question added on top.

**Data source**: [Stack Overflow Developer Survey 2024](https://survey.stackoverflow.co/2024/)

## Contents

| File | Description |
|---|---|
| [`01_data_cleaning.ipynb`](./01_data_cleaning.ipynb) | Duplicate removal, missing-value handling, normalization, feature engineering |
| [`02_eda_and_insights.ipynb`](./02_eda_and_insights.ipynb) | EDA, outlier detection (3-sigma vs. IQR), correlation, and an original question on AI tool usage |
| [`03_visualizations.ipynb`](./03_visualizations.ipynb) | Additional charts comparing how different chart types fit different questions |
| [`slides/`](./slides) | Slide deck built on a companion Looker Studio dashboard |

## Approach

**Cleaning** (`01`): Duplicates are judged only on `ResponseId`, not response content, since two
respondents can legitimately give the same answers. Missing values are imputed differently by
column type (mode for categorical, group-median for skewed numeric columns like compensation).
Rows with missing compensation are flagged before imputation, so later notebooks can exclude them
when the actual reported distribution matters — including imputed rows in that kind of analysis
silently spikes the data at a single repeated value.

**EDA** (`02`): Covers experience vs. satisfaction, remote work by employment type, language
trends by country, and education vs. employment type. Outlier detection compares the 3-sigma rule
against IQR — IQR holds up better here since compensation is right-skewed rather than normal.

**Original question**: Does AI tool usage relate to job satisfaction or compensation?
The survey has two AI-related columns that are easy to mix up — `AISelect` asks directly whether
someone uses AI tools, while `AIToolCurrently Using` only asks *what for*, and is blank for anyone
who isn't shown that follow-up. Grouping by the wrong one understates non-users. Using `AISelect`:
medians are nearly identical between users and non-users (JobSat 7.0 / 7.0, compensation ~$65k both),
but a t-test on satisfaction comes back significant (p = 0.0016) — a reminder that statistical
significance and practical significance aren't the same thing at a sample size of 65,000+.

**Visualizations** (`03`): Same dataset, several chart types, with notes on where each one helps
or misleads (e.g. a histogram of unconverted multi-currency compensation is unreadable; log-scaling
fixes it).

## Dashboard

The `slides/` folder contains a presentation built from a Looker Studio dashboard covering language,
database, and framework trends, plus demographics. One slide (AI tool adoption vs. outcomes) pulls
directly from the analysis in `02_eda_and_insights.ipynb`.

## Notes

- `ConvertedCompYearly` (compensation, USD-converted) is heavily right-skewed and log-transformed
  for most comparisons; `CompTotal` is left in its original multi-currency form and only used to
  illustrate why conversion matters.
- Findings here are correlational, not causal.
