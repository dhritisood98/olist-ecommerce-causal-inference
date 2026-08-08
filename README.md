# E-commerce Marketing Causal Inference & A/B Testing

Causal inference and experiment-design methodology applied to the [Olist Brazilian E-commerce dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (~99K orders), examining whether promotional voucher redemption drives customer retention.

## Objective

Estimate the causal effect of promotional incentives (voucher usage) on customer conversion/retention, and design a properly powered A/B test to validate the finding prospectively — using propensity score matching to control for selection bias, and sensitivity analysis to test how robust the result actually is.

## Data

Olist's public multi-table e-commerce dataset: orders, order items, payments, reviews, products, customers, and sellers (2016–2018, ~99,441 orders). Raw CSVs are not included in this repo (see `.gitignore`) — download them from Kaggle and place them in `data/raw/` to reproduce.

## Methodology

1. **Data pipeline** — merge 9 raw tables into an order-level analytical dataset; clean types, handle nulls.
2. **Treatment definition** — `used_voucher`: whether an order's payment included a redeemed promotional voucher (3,866 of 99,441 orders, 3.9%).
3. **Outcome definition** — `repeat_purchase`: whether the customer placed another order afterward (retention/conversion proxy, since every row is already a completed order — there's no browse-vs-buy funnel in this dataset to measure conversion against directly).
4. **Power analysis** — sample size required to detect a 3-percentage-point minimum detectable effect (MDE) at 95% confidence, 80% power, using `statsmodels`.
5. **Propensity score matching (PSM)** — logistic regression on confounders (product category, customer/seller state, price, freight, installments, order size) to estimate propensity scores, then 1:1 nearest-neighbor matching with bias adjustment via the `causalinference` package. Covariate balance is checked before matching.
6. **Sensitivity analysis** — Rosenbaum bounds to quantify how much hidden (unobserved) bias would be needed to overturn the result.

## Key Findings

| Metric | Result |
|---|---|
| Baseline repeat-purchase rate | 3.36% |
| Required sample size (3% MDE, 95% CI, 80% power) | 1,576 orders total — comfortably met by historical volume |
| ATT of voucher usage on repeat purchase | +1.0pp (≈+30% relative), **p = 0.068** |
| ATE of voucher usage on repeat purchase | +0.5pp, p = 0.393 (not significant) |
| Effect on review score (secondary) | ATT not significant after matching |
| Rosenbaum bounds | Result loses significance at Γ ≈ 1.10 (fragile to mild hidden bias) |



