```markdown
# Customer Churn Prediction — Iranian Telecom Dataset

Predicting which telecom customers are likely to churn, so retention efforts can be
prioritized toward the customers most worth saving.

## Business Problem

A telecommunications provider wants to reduce customer attrition by identifying subscribers
who are likely to leave in the near future. The goal isn't just an accurate model — it's a
tool that helps the business prioritize retention outreach for its most valuable at-risk
customers before they churn.

## Dataset

[Iranian Churn Dataset — UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/563/iranian+churn+dataset)

3,150 customers from an Iranian telecom company, aggregated over 9 months of activity, with
churn status observed at month 12. 13 features, no missing values.

| Column | Description |
|---|---|
| Call Failure | Number of call failures |
| Complaints | Binary (0 = no complaint, 1 = complaint) |
| Subscription Length | Total months subscribed |
| Charge Amount | Ordinal, 0 (lowest) – 9 (highest) |
| Seconds of Use | Total seconds of calls |
| Frequency of use | Total number of calls |
| Frequency of SMS | Total number of text messages |
| Distinct Called Numbers | Total distinct numbers called |
| Age Group | Ordinal, 1 (younger) – 5 (older) |
| Tariff Plan | Binary (1 = pay-as-you-go, 2 = contractual) |
| Status | Binary (1 = active, 2 = non-active) — *excluded from modeling, see note below* |
| Age | Customer age |
| Customer Value | Calculated monetary value of the customer |
| **Churn** | **Target: 1 = churned, 0 = retained** |

## Approach

1. **EDA** — checked for missing values (none), examined class balance (84% stay / 16% churn),
   and correlated every feature with churn.
2. **Preprocessing** — stratified 80/20 train/test split, feature scaling for the linear model.
3. **Baseline model** — Logistic Regression with balanced class weights.
4. **Advanced models** — Random Forest, XGBoost, LightGBM, compared against the baseline.
5. **Evaluation** — Precision, Recall, F1, ROC-AUC, and confusion matrix (accuracy alone is
   misleading on an imbalanced dataset like this one).
6. **Business analysis** — a Risk Score (`Churn Probability × Customer Value`) to rank
   customers by retention priority, and a discussion of decision-threshold tradeoffs based on
   the relative cost of false positives vs. false negatives.

### A note on data leakage

`Status` (active/non-active) correlates strongly with churn, but it's unclear whether it's
knowable *before* a customer churns or is effectively a record of having already left. To
avoid a model that looks good on paper but can't actually give early warning, it was **excluded**
from the final model. (Including it barely changed performance — proof it wasn't hiding much
signal beyond what the other features already capture.)

## Results

| Model | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|
| Logistic Regression | 0.45 | 0.84 | 0.58 | 0.92 |
| Random Forest | 0.88 | 0.85 | 0.87 | 0.99 |
| XGBoost | 0.86 | 0.91 | 0.88 | 0.99 |
| **LightGBM (final model)** | 0.87 | 0.90 | **0.89** | **0.99** |


**Top churn drivers** (Random Forest feature importance): Frequency of use, Seconds of Use,
Complaints, Customer Value, Subscription Length.

**Recommended decision threshold:** ~0.3–0.4 (lower than the default 0.5), since a missed
churner (false negative) is far costlier to the business than an unnecessary retention offer
(false positive).

```

