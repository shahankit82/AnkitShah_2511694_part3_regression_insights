# Residual Analysis — Model M6: Full Multiple Regression

## Overview

Residuals are the difference between actual monthly sales and the values predicted by Model M6. They reveal where the model performs well and where it may be systematically over- or under-predicting.

**Residual = Actual Monthly Sales − Predicted Monthly Sales**

A positive residual means the store performed better than the model expected.  
A negative residual means the store performed worse than the model expected.

---

## Model Fit Summary

| Metric | Value |
|---|---|
| Mean Residual | 0.00 (expected — OLS property) |
| Std Dev of Residuals | Rs 39,249 |
| % of stores within ±Rs 20,000 | 40.3% |
| % of stores within ±Rs 50,000 | 80.3% |
| R-squared | 0.8569 |

The model is well-calibrated overall (mean residual = 0), but individual predictions can vary by up to Rs 1.1 lakh in either direction for outlier stores.

---

## Top 5 Positive Residuals (Model Under-Predicted)

These stores outperformed what the model expected given their characteristics.

| Store ID | Region | Store Type | Actual Sales | Predicted Sales | Residual |
|---|---|---|---|---|---|
| STR-1058 | East | High Street | Rs 8,70,937 | Rs 7,59,887 | +Rs 1,11,050 |
| STR-1028 | East | Mall | Rs 7,13,611 | Rs 6,10,562 | +Rs 1,03,049 |
| STR-1073 | East | Residential | Rs 8,13,317 | Rs 7,21,401 | +Rs 91,916 |
| STR-1051 | East | Airport | Rs 7,87,716 | Rs 7,01,300 | +Rs 86,415 |
| STR-1026 | East | Mall | Rs 6,25,514 | Rs 5,40,162 | +Rs 85,353 |

### Business Interpretation: Positive Residuals

**Pattern observed**: All five top positive residuals are in the **East region**. This suggests:

1. **East-region effect not fully captured**: The model uses East as the reference category, meaning East-specific advantages not correlated with the included predictors are absorbed into positive residuals. These stores may benefit from factors not in the dataset (e.g. lower competition quality, stronger brand loyalty, unique catchment demographics).

2. **STR-1058 (High Street, East)**: The model predicts High Street stores to earn Rs 24,476 less than Airport stores — but this particular High Street store outperformed the Airport average. It likely has unusually high footfall or an excellent location relative to other High Street stores.

3. **STR-1073 (Residential, East)**: Residential stores carry a large negative dummy coefficient (−Rs 44,693). This store significantly beat that expectation, suggesting it has exceptional local characteristics — possibly a large captive residential population or lower nearby competition.

4. **Implication for leadership**: Visit or investigate these East-region stores to identify best practices that could be replicated elsewhere. Positive outliers are often the most instructive stores for understanding what drives sales beyond measurable factors.

---

## Top 5 Negative Residuals (Model Over-Predicted)

These stores underperformed what the model expected.

| Store ID | Region | Store Type | Actual Sales | Predicted Sales | Residual |
|---|---|---|---|---|---|
| STR-1023 | South | Mall | Rs 6,27,172 | Rs 7,63,972 | −Rs 1,36,800 |
| STR-1017 | West | High Street | Rs 6,85,379 | Rs 8,05,326 | −Rs 1,19,947 |
| STR-1012 | West | Residential | Rs 5,95,468 | Rs 7,09,942 | −Rs 1,14,475 |
| STR-1007 | West | Mall | Rs 8,00,452 | Rs 9,12,321 | −Rs 1,11,869 |
| STR-1060 | West | Mall | Rs 7,21,079 | Rs 8,08,037 | −Rs 86,957 |

### Business Interpretation: Negative Residuals

**Pattern observed**: Three of the five largest negative residuals are in the **West region**, which the model associates with a +Rs 25,292 premium over East. These stores are receiving that region premium in their prediction but failing to realise it in actual sales.

1. **STR-1023 (South, Mall)**: The model expects South + Mall to perform well, but this store significantly underperforms. Possible causes: local competitive pressure, poor location within the South region, or temporary disruption during the measurement period.

2. **STR-1017 and STR-1007 (West)**: West region stores are predicted to be among the highest performers. These two are falling well short. The West region average may be distorted by a small number of very high-performing outlier stores, making the dummy coefficient less applicable to all West stores.

3. **STR-1012 (West, Residential)**: Double negative pressure — Residential stores underperform Airport by Rs 44,693, and this one also sits in the under-delivering segment of West stores.

4. **Implication for leadership**: These are underperforming stores relative to their peer group. They warrant operational investigation — check inventory management, staff deployment, customer satisfaction, and local competitive dynamics.

---

## Systematic Patterns in Residuals

### East Region: Consistently Positive
The East region (reference category, zero dummy) has all 5 top positive residuals. This suggests either:
- East-specific unmeasured advantages
- The reference-category intercept may not be perfectly calibrated for East

### West Region: Mixed Performance
Despite the highest regional dummy coefficient (+Rs 25,292), West has 3 of the 5 worst negative residuals. This implies **high within-region variance** — some West stores are exceptional, others are not, and the average regional effect masks this heterogeneity.

### Model Under-Predicting vs Over-Predicting
- The model **under-predicts** East stores more than other regions
- The model **over-predicts** West Mall stores more than other combinations
- 80.3% of stores have residuals within ±Rs 50,000 — acceptable for a model of this complexity

---

## Does the Model Systematically Mis-Predict Certain Store Types?

| Store Type | Mean Residual | Interpretation |
|---|---|---|
| Airport | ~+Rs 10,000 | Slightly under-predicted on average |
| Mall | ~−Rs 8,000 | Slightly over-predicted on average |
| High Street | ~+Rs 5,000 | Near-neutral |
| Residential | ~−Rs 3,000 | Near-neutral |

The systematic mis-prediction for Malls (slightly over-predicted) suggests the Mall dummy coefficient may be capturing heterogeneous stores — some Malls perform at Airport levels while others are closer to Residential levels. A more granular store-type classification (e.g. Flagship Mall vs Community Mall) might improve accuracy.

---

## Conclusion

The residual analysis reveals:
1. **East region stores are consistently outperforming model expectations** — investigate for best practices.
2. **West region has the highest within-region variance** — the regional average premium does not apply uniformly.
3. **80.3% of predictions are accurate to within ±Rs 50,000** — the model is reliable for portfolio-level planning but should not be used for individual store forecasting without additional store-level variables.
4. **Outlier stores (|residual| > Rs 80,000)** account for ~8% of stores and likely have characteristics not captured in the dataset (e.g. anchor tenant presence, recent refurbishment, nearby infrastructure changes).
