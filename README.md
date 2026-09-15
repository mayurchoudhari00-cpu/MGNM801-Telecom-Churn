# MGNM801 — Telecom Customer Churn Analysis

## Project Title
Descriptive and Predictive Analysis of Telecom Customer Churn

## Business Problem
Telecom operators compete in a market with low switching costs, so customer
retention — not just acquisition — drives long-term profitability. This
project identifies the customer, service, contract and billing
characteristics associated with churn, and builds a classification model to
predict which active customers are at the highest risk of churning, so a
telecom company can run a targeted, proactive retention programme instead of
a broad, reactive one.

## Dataset
- **Name:** IBM Telco Customer Churn (public sample dataset)
- **Source URL:** https://raw.githubusercontent.com/IBM/telco-customer-churn-on-icp4d/master/data/Telco-Customer-Churn.csv
- **Size:** 7,043 customers, 21 raw variables (verified programmatically on download)
- **Collected for this project:** September 2026

The dataset is downloaded automatically by the script — no manual upload is required.

## Methodology
1. **Data cleaning:** `TotalCharges` (read as text) is coerced to numeric; 11
   blank values, all belonging to zero-tenure customers, are transparently
   imputed as `MonthlyCharges x tenure` (= $0) rather than deleted or guessed.
   Duplicate rows/IDs and negative-value checks are also run (none found).
2. **Feature engineering:** TenureGroup, MonthlyChargeGroup, Total_Services,
   Premium_Service_Count, Has_Streaming, Has_Internet, Household_Category,
   Payment_Auto, and the binary target Churn_Flag.
3. **Descriptive analysis:** churn rate by contract, tenure group, internet
   service, payment method, monthly-charge band, demographics, and support
   add-ons; 9 Matplotlib figures.
4. **Predictive modelling:** Logistic Regression and Random Forest, trained on
   21 leakage-checked predictors (5 numeric + 16 categorical) via a
   scikit-learn `Pipeline`/`ColumnTransformer` (StandardScaler + OneHotEncoder),
   with an 80/20 stratified train/test split (`random_state=42`).

## Models & Results (test set, n = 1,409)

| Metric | Logistic Regression | Random Forest |
|---|---|---|
| Accuracy | 0.8055 | 0.7594 |
| Precision | 0.6572 | 0.5316 |
| Recall | 0.5588 | **0.7861** |
| F1-score | 0.6040 | **0.6343** |
| ROC-AUC | 0.8421 | **0.8437** |

**Champion model: Random Forest**, selected on a recall-first business rule
(a missed churner costs a fully lost customer; an unnecessary retention offer
to a false positive is comparatively cheap).

## Key Findings
- Overall churn rate: **26.54%** (1,869 of 7,043 customers)
- Contract type is the strongest driver: Month-to-month **42.71%** churn vs
  Two-year **2.83%**
- Churn is concentrated in year one of tenure (**47.44%**), falling to
  **6.61%** after five years
- Electronic-check payers churn at **45.29%**, ~3x the automatic-payment rate
- Customers without TechSupport/OnlineSecurity churn at ~**41.6–41.8%**,
  ~3x the rate of subscribers
- Top predictive features (Random Forest): Contract type, tenure, TotalCharges,
  OnlineSecurity, TechSupport, MonthlyCharges

## How to Run
1. Open `MGNM801_Telecom_Churn_Analysis.ipynb` in Google Colab
   (**File → Upload notebook**), or paste `MGNM801_Telecom_Churn_Analysis.py`
   into a single Colab code cell.
2. Run all cells (**Runtime → Run all**) — no manual file upload is required,
   the dataset downloads automatically from the URL above.
3. Outputs (cleaned dataset, descriptive-statistics CSVs, model comparison,
   test-set predictions, feature importance, confusion matrix, and all 9
   chart PNGs) are written to `outputs/` (created automatically).

```bash
python MGNM801_Telecom_Churn_Analysis.py
```

## Limitations
- Single cross-sectional snapshot — no churn-reason, support-interaction, or
  network-quality data, and no time-series/seasonal view of churn.
- Data comes from one (unnamed) operator/geography and may not generalise to
  every market.
- Predictive associations reflect historical, observational data and should
  not be read as proof of causation (see report Section 8.10).
- Recall-optimised champion model trades precision for recall (53.2%
  precision), so roughly one unnecessary retention offer should be expected
  per genuine at-risk customer identified — an explicit, documented trade-off.

## Repository Structure
```
MGNM801-Telecom-Churn/
├── MGNM801_Telecom_Churn_Analysis.py     (runnable, Colab-ready script)
├── MGNM801_Telecom_Churn_Analysis.ipynb  (same workflow as a notebook)
├── README.md
├── outputs/                              (generated on each run)
│   └── charts/                           (9 PNG figures)
└── report/
    └── MGNM801_Telecom_Churn_Business_Analytics_Project.docx
```
