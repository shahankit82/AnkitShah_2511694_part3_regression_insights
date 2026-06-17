# Model Comparison — Monthly Sales Regression Analysis

## Overview

Six regression models were estimated with `monthly_sales` as the dependent variable. Five were simple regressions (one predictor each); one was a full multiple regression model including numerical and dummy variables.

---

## Model Summary Table

| Model | Variables Used | R-squared | Adj R-squared | Sig. Variables | Business Useful? |
|---|---|---|---|---|---|
| M1: Marketing Only | marketing_spend | 0.1672 | 0.1645 | marketing_spend (***) | Partial |
| M2: Footfall Only | footfall | 0.7363 | 0.7355 | footfall (***) | Strong (single) |
| M3: Discount Only | avg_discount_pct | 0.0083 | 0.0052 | None (p=0.10) | No |
| M4: Inventory Only | inventory_availability_pct | 0.0131 | 0.0100 | inventory (*) | Weak |
| M5: Rating Only | customer_rating | 0.0007 | −0.0025 | None (p=0.63) | No |
| **M6: Full Multiple ✔** | All 8 numeric + 6 dummies | **0.8569** | **0.8504** | **9 variables** | **✔ Selected** |

---

## Individual Model Analysis

### M1: Marketing Spend → Monthly Sales
- **Equation**: monthly_sales = 566,166 + 2.13 × marketing_spend
- **R-squared**: 0.1672 — marketing alone explains only 17% of sales variation
- **P-value**: < 0.001 — statistically significant
- **Business usefulness**: Marketing is a real driver but not the dominant one in isolation. A store spending more on marketing does sell more, but footfall and store type matter far more.
- **Limitation**: Omits footfall, store type, region, and operational variables. Using this model for decisions would overstate the impact of marketing.

### M2: Footfall → Monthly Sales
- **Equation**: monthly_sales = 124,296 + 35.68 × footfall
- **R-squared**: 0.7363 — footfall alone explains 74% of sales variation
- **P-value**: < 0.001 — highly significant
- **Business usefulness**: The strongest single predictor by a wide margin. Every additional visitor is associated with Rs 35.68 more in monthly sales.
- **Limitation**: Footfall may itself be influenced by marketing, location, and store type — meaning it is partly a mediator rather than a pure independent lever. Also provides no guidance on what else leadership can do.

### M3: Average Discount % → Monthly Sales
- **Equation**: monthly_sales = 707,282 − 138,730 × avg_discount_pct
- **R-squared**: 0.0083 — practically zero explanatory power alone
- **P-value**: 0.104 — not significant at 5%
- **Business usefulness**: No. The discount-sales relationship is too confounded by region and store type to be meaningful in isolation.
- **Limitation**: High-performing Airport stores may apply lower discounts; Residential stores may apply higher discounts. This distorts the relationship when estimated without controls.

### M4: Inventory Availability → Monthly Sales
- **Equation**: monthly_sales = 486,568 + 2,218 × inventory_availability_pct
- **R-squared**: 0.0131 — very weak alone
- **P-value**: 0.041 — borderline significant
- **Business usefulness**: Weak as a standalone predictor. However, the coefficient becomes stronger and more significant in the full model (coef = 3,062, p < 0.001), suggesting its effect is real but masked by other factors when used alone.
- **Limitation**: Too narrow for decision-making on its own.

### M5: Customer Rating → Monthly Sales
- **Equation**: monthly_sales = 710,296 − 5,446 × customer_rating
- **R-squared**: 0.0007 — essentially zero
- **P-value**: 0.627 — completely non-significant alone
- **Business usefulness**: No. The negative sign is counter-intuitive and reflects confounding. In the full model, customer rating is actually positive and significant (coef = +12,439, p = 0.006).
- **Limitation**: Rating is confounded by store type; Airport stores likely have higher ratings and also different sales levels, distorting this simple model.

### M6: Full Multiple Regression ✔ (Selected Model)
- **R-squared**: 0.8569 — model explains 85.7% of monthly sales variation
- **Adjusted R-squared**: 0.8504 — adjusted for number of predictors; still very high
- **F-statistic p-value**: < 0.00000001 — model is highly significant overall
- **Significant variables**: footfall (***), marketing_spend (***), inventory_availability_pct (***), competitor_distance_km (***), region_West (***), store_type_Residential (***), customer_rating (**), staff_count (**), region_South (**), store_type_High Street (**), holiday_flag (*)
- **Non-significant variables**: avg_discount_pct (ns), region_North (ns), store_type_Mall (ns)
- **Business usefulness**: ✔ Best model. Provides actionable insights on multiple levers simultaneously.
- **Limitation**: Cross-sectional data — cannot prove causality. No time-series dimension to model trends. Omits some potential confounders (e.g. store age, catchment demographics).

---

## R-squared Comparison

| Model | R² | Improvement over previous |
|---|---|---|
| M5: Rating | 0.0007 | Baseline worst |
| M3: Discount | 0.0083 | +0.0076 |
| M4: Inventory | 0.0131 | +0.0048 |
| M1: Marketing | 0.1672 | +0.154 |
| M2: Footfall | 0.7363 | +0.569 |
| **M6: Full Model** | **0.8569** | **+0.121** |

The jump from M2 (footfall alone, R²=0.74) to M6 (full model, R²=0.86) demonstrates that operational and locational variables add meaningful explanatory power beyond visitor volume alone.

---

## Why M6 is Selected as the Final Model

1. **Highest explanatory power**: 85.7% of sales variation explained — far above any simple model.
2. **Multiple actionable levers**: Provides guidance on marketing, inventory, staffing, and store type strategy simultaneously.
3. **Correctly signs customer_rating**: In isolation, rating appeared negative (confounding). The full model reveals the true positive relationship.
4. **Regional and store-type effects quantified**: Leadership can see exactly how much West/South regions and Airport store types outperform.
5. **Adjusted R² penalty accepted**: The adjusted R² of 0.850 confirms the additional variables are earning their place — not just inflating R² through overfitting.

---

## Limitations of All Models

- **No causality**: All models show association, not proven cause-and-effect. A controlled experiment would be needed to prove, for example, that increasing marketing spend causes a specific sales uplift.
- **Cross-sectional data**: The dataset represents a snapshot. Stores observed once cannot account for time-varying effects (seasonality, macro trends).
- **Omitted variable bias**: Variables like store age, local population density, competitor quality, and product mix are absent and may explain some residual variation.
- **Multicollinearity risk**: Footfall and marketing_spend may be correlated (marketing drives footfall), which can affect coefficient stability. VIF analysis is recommended for a production model.
- **Outliers**: A small number of stores have residuals exceeding Rs 100,000 — these may have unique characteristics not captured by the current predictors.
