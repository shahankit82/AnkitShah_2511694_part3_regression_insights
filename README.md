# Part 3: Regression-Based Business Insights & Model Interpretation

## Business Problem Summary

A retail chain leadership team wants to understand which factors drive monthly sales performance across stores, and whether business levers such as marketing spend, inventory management, staffing, and store type strategy can be used to improve performance. This analysis uses regression modelling to quantify those relationships and produce actionable recommendations.

---

## Dataset Description

| Attribute | Value |
|---|---|
| File | `business_regression_data.xlsx` |
| Rows | 320 (80 stores × 4 months) |
| Columns | 14 |
| Observations per store | 4 monthly snapshots |

### Variables

**Dependent Variable:**
- `monthly_sales` — Total monthly sales revenue (Rs) per store-month

**Independent Variables (numerical):**
| Variable | Type | Notes |
|---|---|---|
| marketing_spend | Continuous | Rs amount per month |
| footfall | Integer | Number of visitors |
| avg_discount_pct | Continuous | Average discount applied |
| staff_count | Integer | Number of staff |
| inventory_availability_pct | Continuous | % of SKUs in stock |
| competitor_distance_km | Continuous | 6 missing values → filled with median |
| holiday_flag | Binary (0/1) | 1 = holiday month |
| customer_rating | Continuous | 8 missing values → filled with mean |

**Independent Variables (categorical → dummy coded):**
| Variable | Categories | Reference Category |
|---|---|---|
| region | East, North, South, West | East |
| store_type | Airport, High Street, Mall, Residential | Airport |

**Excluded from regression:**
- `store_id` — Identifier only
- `month` — Only 4 time points; not enough for time-series regression
- `monthly_profit` — Outcome derived from sales; including it would create data leakage

---

## Regression Approach

1. **Data cleaning**: Filled 6 missing `competitor_distance_km` with median; 8 missing `customer_rating` with mean. No rows dropped.
2. **Dummy variable creation**: Created 6 dummies from `region` (3) and `store_type` (3) after dropping reference categories.
3. **Simple regressions (M1–M5)**: Ran 5 simple linear regressions with one predictor each against `monthly_sales`.
4. **Multiple regression (M6)**: Ran full model with 8 numerical predictors + 6 dummy variables. Estimated using OLS (Ordinary Least Squares).
5. **Residual analysis**: Calculated predicted values and residuals; identified top 5 positive and negative outliers.
6. **Model comparison**: Compared all 6 models on R², adjusted R², significance, and business usefulness.

---

## Dummy Variable Approach

Categorical variables (`region`, `store_type`) were converted to binary dummy variables using one-hot encoding with **one category dropped per variable** to avoid the dummy variable trap (perfect multicollinearity).

- **Reference for region**: `East` — all region coefficients are relative to East
- **Reference for store_type**: `Airport` — all store type coefficients are relative to Airport stores

**Dummies created:**
- `region_North`, `region_South`, `region_West`
- `store_type_High Street`, `store_type_Mall`, `store_type_Residential`

Full explanation in: `outputs/model_equations.md`

---

## Simple Regression Results Summary

| Model | Predictor | R² | p-value | Coefficient | Useful? |
|---|---|---|---|---|---|
| M1 | marketing_spend | 0.1672 | <0.001 | +2.13 per Rs 1 | Partial |
| M2 | footfall | **0.7363** | <0.001 | +35.68 per visitor | ✔ Strong |
| M3 | avg_discount_pct | 0.0083 | 0.104 | −138,730 per unit | No |
| M4 | inventory_availability_pct | 0.0131 | 0.041 | +2,218 per 1% | Weak |
| M5 | customer_rating | 0.0007 | 0.627 | −5,446 (confounded) | No |

---

## Model Comparison Summary

| Model | R² | Adj R² | Significant Vars | Selected? |
|---|---|---|---|---|
| M1: Marketing Only | 0.1672 | 0.1645 | marketing_spend | No |
| M2: Footfall Only | 0.7363 | 0.7355 | footfall | No (single lever) |
| M3: Discount Only | 0.0083 | 0.0052 | None | No |
| M4: Inventory Only | 0.0131 | 0.0100 | inventory | No |
| M5: Rating Only | 0.0007 | −0.0025 | None | No |
| **M6: Full Multiple** | **0.8569** | **0.8504** | **9 variables** | **✔ YES** |

---

## Final Model Selected: M6 (Full Multiple Regression)

**Why M6?**
- Highest R² (0.857) and Adjusted R² (0.850)
- 9 statistically significant predictors across operational, locational, and commercial dimensions
- Correctly identifies customer_rating as positively associated with sales (reversed sign in simple model was due to confounding)
- Provides actionable guidance across multiple business functions simultaneously

**Significant predictors in M6:**
footfall (***), marketing_spend (***), inventory_availability_pct (***), competitor_distance_km (***), region_West (***), store_type_Residential (***), customer_rating (**), staff_count (**), region_South (**), store_type_High Street (**), holiday_flag (*)

**Not significant:** avg_discount_pct, region_North, store_type_Mall

---

## Business Recommendation Summary

1. **Inventory first**: Fix stockout issues — each 1% improvement → Rs 3,062/month/store
2. **Staff-to-footfall alignment**: Under-staffed high-traffic stores lose sales (+Rs 3,509/staff)
3. **Customer rating investment**: 0.5-point improvement → +Rs 6,200/month
4. **Marketing to West/South**: These regions carry significant positive coefficients
5. **Airport-first expansion**: Rs 44,693 premium over Residential; Rs 24,476 over High Street
6. **Investigate East outliers**: Top positive residuals are all East stores — find the playbook

Full recommendation: `outputs/final_recommendation.md`

---

## Assumptions and Limitations

- **OLS assumptions**: Linearity, homoscedasticity, and independence of errors are assumed but not fully tested (residuals show some heteroscedasticity risk in West stores)
- **Causality**: Regression shows association only — not causal proof. Controlled experiments needed before large investments
- **Cross-sectional data**: 4 months is insufficient for seasonality modelling
- **Missing variables**: Store age, catchment demographics, anchor tenant, parking availability not included
- **Multicollinearity**: Marketing spend and footfall may be correlated; VIF analysis recommended for production use

---

## Screenshots Included

| File | Shows |
|---|---|
| `screenshots/simple_regression_output.png` | Footfall scatter plot with regression line + M1 vs M2 comparison table |
| `screenshots/multiple_regression_output.png` | Coefficient forest plot + full M6 coefficients table |
| `screenshots/residuals_preview.png` | Actual vs predicted scatter + top 5 positive/negative residuals |
| `screenshots/model_comparison_preview.png` | R-squared bar chart across all models + comparison table |

---

## Repository Structure

```
part3_regression_insights/
├── data/
│   └── business_regression_data.xlsx
├── analysis/
│   ├── regression_workbook.xlsx     ← 7-sheet workbook: raw data, cleaned, dummies,
│   │                                    simple regressions, multiple regression,
│   │                                    predictions/residuals, model comparison
│   ├── model_comparison.md          ← Detailed model-by-model comparison
│   └── residual_analysis.md         ← Residual patterns and business interpretation
├── outputs/
│   ├── regression_summary.xlsx      ← Clean comparison table + coefficients detail
│   ├── model_equations.md           ← All regression equations with explanations
│   └── final_recommendation.md      ← Full business recommendation memo
├── screenshots/
│   ├── simple_regression_output.png
│   ├── multiple_regression_output.png
│   ├── residuals_preview.png
│   └── model_comparison_preview.png
└── README.md
```
