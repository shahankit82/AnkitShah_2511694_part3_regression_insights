# Model Equations & Dummy Variable Explanation

## Dependent Variable
**monthly_sales** — Total monthly sales revenue (Rs) for each store-month observation.

---

## Simple Regression Equations

### M1: Marketing Spend Model
```
monthly_sales = 566,166 + 2.1296 × marketing_spend
```
| Term | Value | Meaning |
|---|---|---|
| Intercept | Rs 566,166 | Expected sales if marketing spend = Rs 0 |
| marketing_spend coefficient | 2.1296 | Each additional Rs 1 in marketing → Rs 2.13 more in sales |
| R² | 0.1672 | Marketing alone explains 17% of sales variation |
| p-value | < 0.001 | Statistically significant |

**Business interpretation**: Every Rs 10,000 in additional marketing is associated with Rs 21,296 in extra monthly sales — a 2.1× revenue multiplier. However, this model explains only 17% of sales variation, meaning other factors dominate.

---

### M2: Footfall Model
```
monthly_sales = 124,296 + 35.6780 × footfall
```
| Term | Value | Meaning |
|---|---|---|
| Intercept | Rs 124,296 | Fixed base sales (overhead/anchor revenue) |
| footfall coefficient | 35.6780 | Each additional visitor → Rs 35.68 more in sales |
| R² | 0.7363 | Footfall alone explains 74% of sales variation |
| p-value | < 0.001 | Highly significant |

**Business interpretation**: Getting 1,000 more visitors per month is associated with approximately Rs 35,680 in incremental sales. This is the strongest single predictor in the dataset.

---

### M3: Average Discount % Model
```
monthly_sales = 707,282 − 138,730 × avg_discount_pct
```
| Term | Value | Meaning |
|---|---|---|
| Intercept | Rs 707,282 | Expected sales at 0% discount |
| avg_discount_pct coefficient | −138,730 | Each 1-unit (1%) increase in discount → Rs 138,730 less in sales |
| R² | 0.0083 | Discount alone explains < 1% of variation |
| p-value | 0.104 | NOT significant at 5% level |

**Business interpretation**: Not useful as a standalone predictor. The negative sign is directionally expected (deeper discounts erode net revenue), but the relationship is too confounded by store type and region to be meaningful alone.

---

### M4: Inventory Availability Model
```
monthly_sales = 486,568 + 2,217.83 × inventory_availability_pct
```
| Term | Value | Meaning |
|---|---|---|
| Intercept | Rs 486,568 | Expected sales at 0% inventory availability |
| inventory_availability_pct coefficient | 2,217.83 | Each 1% improvement in stock availability → Rs 2,218 more in sales |
| R² | 0.0131 | Explains only 1.3% alone |
| p-value | 0.041 | Marginally significant |

**Business interpretation**: Weak alone, but the coefficient strengthens meaningfully in the full model (Rs 3,062, p < 0.001), confirming that inventory management is a real lever when other factors are controlled.

---

### M5: Customer Rating Model
```
monthly_sales = 710,296 − 5,446 × customer_rating
```
| Term | Value | Meaning |
|---|---|---|
| Intercept | Rs 710,296 | Expected sales at rating = 0 |
| customer_rating coefficient | −5,446 | Negative sign (counter-intuitive) |
| R² | 0.0007 | Explains essentially nothing alone |
| p-value | 0.627 | NOT significant |

**Business interpretation**: Do not use this equation for decisions. The negative sign is caused by confounding — Airport stores (which have the highest ratings) also have the highest baseline sales due to location, not rating. In the full model, rating is correctly positive (+Rs 12,439, p = 0.006).

---

## Multiple Regression Equation (M6: Final Selected Model)

```
monthly_sales = 86,603
  + 1.2097 × marketing_spend
  + 27.3346 × footfall
  − 41,272 × avg_discount_pct         [not significant]
  + 3,062.1 × inventory_availability_pct
  + 12,439 × customer_rating
  + 3,509.2 × staff_count
  − 3,438.3 × competitor_distance_km
  + 15,174 × holiday_flag
  + 9,949 × region_North              [not significant]
  + 21,082 × region_South
  + 25,292 × region_West
  − 24,476 × store_type_High Street
  − 11,853 × store_type_Mall          [not significant]
  − 44,693 × store_type_Residential
```

**R-squared: 0.8569 | Adjusted R-squared: 0.8504 | F-statistic p-value: < 0.00000001**

---

## Coefficient Explanations (M6)

| Variable | Coefficient | Significance | Business Meaning |
|---|---|---|---|
| Intercept | +86,603 | (ns, p=0.055) | Theoretical baseline; not directly interpretable |
| marketing_spend | +1.2097 | *** | Rs 1.21 in sales per Rs 1 marketing. In full model, marketing competes with footfall and operational factors for explanatory power, so the coefficient is lower than in the simple model. |
| footfall | +27.33 | *** | Still the dominant driver; each visitor worth Rs 27.33 in the full model |
| avg_discount_pct | −41,272 | ns (p=0.22) | Discounting reduces revenue per sale; but not significant — pricing strategy may be driven by store type/region decisions already captured |
| inventory_availability_pct | +3,062 | *** | Each 1% improvement in stock availability → Rs 3,062 in monthly sales |
| customer_rating | +12,439 | ** | A 0.1-point improvement in average rating → Rs 1,244 in monthly sales |
| staff_count | +3,509 | ** | Each additional staff member → Rs 3,509 in monthly sales |
| competitor_distance_km | −3,438 | *** | Closer competitors are associated with higher sales — likely because both the store and its competitors are located in high-footfall areas; interpretation requires caution |
| holiday_flag | +15,174 | * | Holiday months generate Rs 15,174 more than typical months |

---

## Dummy Variable Explanation

### Why Dummy Variables?
`region` and `store_type` are categorical variables — they cannot be entered directly into regression as numbers. Dummy variables convert each category into a binary 0/1 indicator.

### Reference Categories
- **Region reference: East** — All region effects are measured relative to East region stores.
- **Store type reference: Airport** — All store type effects are measured relative to Airport stores.

These reference categories were chosen because:
1. They avoid the dummy variable trap (perfect multicollinearity that would prevent model estimation)
2. Airport stores are the premium store type and serve as a natural benchmark
3. East is a neutral mid-performing region, making comparisons interpretable

### Dummy Variable Coefficients

| Dummy Variable | Coefficient | Meaning |
|---|---|---|
| region_North | +9,949 (ns) | North earns Rs 9,949 more per month than East on average — but NOT statistically significant at 5% |
| region_South | +21,082 ** | South earns Rs 21,082 more per month than East — significant |
| region_West | +25,292 *** | West earns Rs 25,292 more per month than East — highly significant |
| store_type_High Street | −24,476 ** | High Street earns Rs 24,476 LESS per month than Airport — significant |
| store_type_Mall | −11,853 (ns) | Mall earns Rs 11,853 less than Airport — NOT significant |
| store_type_Residential | −44,693 *** | Residential earns Rs 44,693 LESS per month than Airport — highly significant |

### What Happened to the Dropped Categories?
`region_East` and `store_type_Airport` were dropped from the regression. Their effect is contained in the **intercept**. When all region and store_type dummies are 0, the store is an East-region Airport store.

---

## Final Model Selection

**Model M6 (Full Multiple Regression) was selected** as the final model for the following reasons:

1. **Highest R²**: 85.7% of monthly sales variation explained — vs 74% for the best simple model
2. **All key predictors jointly significant**: F-statistic p-value < 0.00000001
3. **Correct signs on all variables**: In simple regressions, customer_rating had a counterintuitive negative sign. In M6, with proper controls, it is correctly positive
4. **Actionable across multiple business functions**: Marketing (marketing_spend), Operations (inventory, staff), Location strategy (region, store_type), and Pricing (discount) are all represented
5. **Adjusted R² confirms no overfitting**: Adjusted R² = 0.850, very close to R² = 0.857, confirming that the additional variables are genuine contributors rather than noise

### Prediction Example using M6
For a West-region Mall store with:
- marketing_spend = Rs 50,000
- footfall = 18,000
- avg_discount_pct = 0.15
- inventory_availability_pct = 85%
- customer_rating = 4.2
- staff_count = 12
- competitor_distance_km = 2.0
- holiday_flag = 0

```
Predicted monthly_sales = 86,603
  + 1.2097 × 50,000    = 60,485
  + 27.3346 × 18,000   = 492,023
  − 41,272 × 0.15      = −6,191
  + 3,062 × 85         = 260,270
  + 12,439 × 4.2       = 52,244
  + 3,509 × 12         = 42,108
  − 3,438 × 2.0        = −6,876
  + 15,174 × 0         = 0
  + 25,292 (West)      = 25,292
  − 11,853 (Mall)      = −11,853

Total predicted = Rs 993,105 (approx Rs 9.9 lakh)
```
