# CAPM & Rolling Window Regression - Mercedes-Benz AG (MBG.DE)

An empirical financial analysis of Mercedes-Benz AG's market risk using the **Capital Asset Pricing Model (CAPM)** and **Rolling Window Regression** in R. The project estimates the expected market return from the perspective of a CFO, with a strong focus on sensitivity analysis and the time-varying behaviour of Beta.

---

## Overview

This project was developed as part of a quantitative finance course and covers the full analytical pipeline from raw data acquisition to interactive visualizations. Rather than relying on a single point estimate, the analysis applies multiple window sizes and return frequencies to assess how Beta evolves over time.

**Key questions addressed:**
- What is the estimated Beta (systematic risk) of Mercedes-Benz relative to the DAX?
- How does Beta change over time depending on the chosen window size and return frequency?
- When is the estimated Beta statistically significant — and when should it not be trusted?

---

## Methodology

### Data
| Variable | Source | Details |
|---|---|---|
| **Ri** — Stock Return | Yahoo Finance via `tidyquant` | MBG.DE Adjusted Close, 2020–2024 |
| **Rm** — Market Return | Yahoo Finance via `tidyquant` | DAX (^GDAXI) as reference market |
| **Rf** — Risk-Free Rate | Deutsche Bundesbank | Daily yield of current 5-year Federal notes |

Log returns are used throughout to satisfy the assumptions of linear regression.

### CAPM Model
The classical CAPM regression is estimated as:

$$R_i - R_f = \alpha + \beta (R_m - R_f) + \varepsilon$$

Where:
- $\beta$ (Beta) captures the sensitivity of Mercedes returns to the market
- $\alpha$ (Alpha) measures risk-adjusted excess return over the benchmark
- Excess returns are calculated for both monthly and daily frequencies

### Rolling Window Regression
Beta is estimated dynamically using sliding windows via the `slider` package:

| Frequency | Window Sizes |
|---|---|
| Monthly | 6, 12, 24 months |
| Daily | 60, 120, 250 trading days |

Each rolling window produces a Beta estimate with associated p-value and R², allowing significance to be tracked over time. Results are visualized interactively with `plotly`, including toggle buttons to switch between window sizes.

---

## Key Findings

- **Statistical significance** of Beta only emerges consistently at the **24-month** window for monthly returns shorter windows lack sufficient observations for reliable estimation.
- **Daily returns** yield significant Beta across all window sizes, but this is partly driven by noise rather than genuine systematic risk.
- Rolling Beta fluctuates considerably over time, indicating that Mercedes' market sensitivity is **not constant** underscoring the importance of dynamic rather than static risk estimation.
- The estimated Betas are benchmarked against **Damodaran's industry Beta of 1.2** for the European automotive sector (between the unlevered Beta of 0.83 and the levered Beta of 1.63).
- **Rolling Alpha** is rarely significant, suggesting Mercedes does not systematically outperform or underperform the market on a risk-adjusted basis.

---

## Repository Structure

```
project/
├── CAPM_Rolling_Window_Regression_MBG_Notebook.Rmd   # Main analysis notebook
├── risk_free_monthly_5 years.rds                      # Pre-processed monthly Rf data
├── risk_free_daily_5 years.rds                        # Pre-processed daily Rf data
├── MBG_DE_2020_2024.csv                               # MBG price data (CSV, data check)
├── GDAXI_2020_2024.csv                                # DAX price data (CSV, data check)
└── BBSSY.D.REN.EUR.A620.000000WT0505.A.csv            # Bundesbank yield data (raw)
```

---

## Setup & Usage

### Prerequisites
R (≥ 4.0) and the following packages:

```r
install.packages(c(
  "tidyquant", "tidyverse", "broom", "slider",
  "ggplot2", "plotly", "lubridate", "htmltools",
  "shiny", "zoo", "xts", "kableExtra", "timeDate"
))
```

### Running the Notebook
1. Clone this repository
2. Open `CAPM_Rolling_Window_Regression_MBG_Notebook.Rmd` in RStudio
3. Update the `root.dir` path in the setup chunk to point to your local project folder
4. Knit the document to HTML (`Knit → Knit to HTML`)

> All external data files (`.rds`, `.csv`) must be located in the same directory as the `.Rmd` file.

---

## Additional Metrics (Appendix)

Beyond Beta, the following rolling metrics are computed and visualized:

| Metric | Description |
|---|---|
| **Rolling Alpha** | Risk-adjusted excess return vs. market; significance flagged |
| **Rolling R²** | Proportion of return variance explained by the market |
| **Rolling Volatility** | Annualized standard deviation of excess returns (24-month window) |
| **Rolling Sharpe Ratio** | Annualized risk-adjusted return (24-month window) |

---

## Data Validation

A data integrity check compares the `tidyquant`-sourced data against manually downloaded Yahoo Finance CSVs for both MBG.DE and the DAX, following the methodology outlined in:

> Ang, C. S. (2021). *Analyzing Financial Data and Implementing Financial Models Using R* (2nd ed.). Springer Nature. https://doi.org/10.1007/978-3-030-64155-9

---

## Authors

Marleen Bünker · Denise Trinz · Eya Ben Romdhane · Franziska Meyndt

---

## References

- Damodaran, A. (2024). *Betas by Sector (Europe)*. NYU Stern. https://pages.stern.nyu.edu/~adamodar/New_Home_Page/datacurrent.html#discrate
- Deutsche Bundesbank. *Daily yields of current Federal securities*. https://www.bundesbank.de/en/statistics/money-and-capital-markets/interest-rates-and-yields/daily-yields-of-current-federal-securities-772220
- Ang, C. S. (2021). *Analyzing Financial Data and Implementing Financial Models Using R* (2nd ed.). Springer Nature.
