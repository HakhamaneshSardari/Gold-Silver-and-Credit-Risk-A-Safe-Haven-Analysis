# Gold, Silver, and Credit Risk: A Safe-Haven Analysis

This project examines whether **gold** and **silver** behave as safe-haven assets against **U.S. sovereign credit risk**, as measured by changes in the 5-year U.S. CDS spread. The analysis controls for general market uncertainty (VIX) and currency effects (USD returns), using daily data from **January 2016 to December 2025**.

## Research Question

A safe-haven asset is one that maintains or increases in value during periods of financial stress (Baur & Lucey, 2010). This project tests whether gold and silver returns respond positively to rising perceived credit risk — which would support the safe-haven hypothesis — or whether they instead move with broader market stress.

## Data

| Variable | Source | Description |
|---|---|---|
| Silver return | Yahoo Finance (`SI=F`) | Daily % return, continuous futures |
| Gold return | Yahoo Finance (`GC=F`) | Daily % return, continuous futures |
| CDS change | Investing.com | Daily % change in U.S. 5-year CDS spread |
| VIX | FRED (`VIXCLS`) | CBOE Volatility Index |
| USD return | FRED (`DTWEXBGS`) | Daily % return, Trade Weighted U.S. Dollar Index |

Raw CDS data is stored in `data/us_cds.csv`; the other series are pulled live via `quantmod::getSymbols()`.

## Methodology

Two OLS specifications are estimated for each metal, using heteroskedasticity-robust standard errors (`estimatr::lm_robust`):

**Simple model:**

Return_t = α + β₁·CDSChange_t + ε_t

**Multiple model:**

Return_t = α + β₁·CDSChange_t + β₂·VIX_t + β₃·USDReturn_t + ε_t

β₁ is the coefficient of interest: positive → safe-haven behavior; negative → risk-sensitive behavior; near zero → no relationship.

**Robustness checks:**
- Lagged CDS change (tests for a delayed response)
- COVID-19 period dummy and interaction term (March 2020–Dec 2021)

## Key Results

**Silver:** CDS change has a negative, statistically significant coefficient in the simple model (β = −0.013, p<0.05), weakening to marginal significance (p<0.10) once controls are added. The effect disappears entirely with lagged CDS. USD return is by far the strongest predictor (β = −2.35, p<0.001). **No evidence of safe-haven behavior.**

**Gold:** CDS change is negative but not significant with contemporaneous values. With *lagged* CDS, the coefficient becomes positive and significant (β ≈ 0.009, p<0.05) — a more mixed picture than silver, though still not consistent, immediate safe-haven behavior. USD return again dominates (β = −1.14, p<0.001).

**Overall conclusion:** Neither gold nor silver shows robust, consistent safe-haven characteristics against U.S. credit risk in this sample. Dollar strength is a far more important driver of both metals' returns than credit risk is.

Full regression tables and discussion are in the notebook and in `output/*.docx`.

## Repository Structure

```
├── FRM_Project.Rmd          # Full analysis source
├── FRM_Project.html         # Rendered notebook output
├── data/
│   └── us_cds.csv           # U.S. 5Y CDS price data (Investing.com)
├── output/
│   ├── Silver_table.docx
│   ├── Gold_table.docx
│   ├── silver_lag_table.docx
│   └── gold_lag_table.docx
└── setup.R                  # Package installation script
```

## Reproducing the Analysis

1. Clone the repo and open `FRM_Project.Rmd` in RStudio.
2. Run `setup.R` (or the commented `install.packages()` calls at the top of the notebook) to install dependencies.
3. Knit the notebook. Regression tables will be written to `output/`.

**Required R packages:** `tidyverse`, `quantmod`, `lubridate`, `sandwich`, `lmtest`, `stargazer`, `dplyr`, `ggplot2`, `psych`, `corrplot`, `estimatr`, `modelsummary`

## References

- Baur, D. G., & Lucey, B. M. (2010). Is gold a hedge or a safe haven? An analysis of stocks, bonds and gold. *Financial Review*, 45(2), 217–229.
- Baur, D. G., & McDermott, T. K. (2010). Is gold a safe haven? International evidence. *Journal of Banking & Finance*, 34(8), 1886–1898.
- Wooldridge, J. M. (2013). *Introductory econometrics: A modern approach* (5th ed.). South-Western Cengage Learning.
- Federal Reserve Bank of St. Louis. VIX (VIXCLS) and Trade Weighted U.S. Dollar Index. https://fred.stlouisfed.org
- Investing.com. United States 5-Year Credit Default Swap (CDS). https://www.investing.com
- Yahoo Finance. Silver and Gold Futures Historical Data. https://finance.yahoo.com
