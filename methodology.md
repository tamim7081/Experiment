# methodology

# Methodology

## 1. Refined research question

Using the IBM Telco Customer Churn dataset, can a gradient-boosted model (XGBoost) rank customers by churn risk better than a Logistic Regression baseline and a Random Forest, when class imbalance is handled with class weights and the features are extended with tenure, spend and service-count variables? Which features drive churn most?

## 2. Dataset description

- **Source:** IBM Telco Customer Churn, a public sample dataset (CSV).
- **Size:** 7,043 customers (rows), 21 columns.
- **Target:** `Churn` (Yes/No). About a quarter of customers churn, so the classes are imbalanced.
- **Features:**
    - Demographics: `gender`, `SeniorCitizen`, `Partner`, `Dependents`
    - Account: `tenure` (months), `Contract`, `PaperlessBilling`, `PaymentMethod`, `MonthlyCharges`, `TotalCharges`
    - Services: `PhoneService`, `MultipleLines`, `InternetService`, `OnlineSecurity`, `OnlineBackup`, `DeviceProtection`, `TechSupport`, `StreamingTV`, `StreamingMovies`
    - `customerID` is the one that will not be used.
- **Limitations:**
    - It is a single snapshot with no dates, so I cannot do a time-based split or test drift over time.
    - It is a sample dataset published by IBM and may not represent real customers; results may not transfer to a real company.
    - Only about 7,000 rows, so estimates of model performance have noticeable variance.
    - No information on why customers left, on cost of retention offers, or on customer lifetime value, so I cannot compute true business profit.
    - `TotalCharges` has a few blank values.

## 3. Data cleaning plan

1. Convert `TotalCharges` from text to numeric; blanks become missing.
2. Fill those missing values with 0, since they belong to customers with tenure 0 (new customers, nothing billed yet).
3. Drop `customerID`.
4. Encode the target as 1 (churn) and 0 (stay).
5. Merge “No internet service” and “No phone service” into “No”, as they duplicate `InternetService` and `PhoneService`.
6. Check for duplicate rows and remaining missing values.

## 4. Feature engineering plan

All features are computed from the customer’s own row, so they do not use the target.

- `tenure_group`: bins of 0-12, 13-24, 25-48, 49-72 months (early-life customers churn differently)
- `num_services`: number of add-on services (security, backup, protection, support, streaming)
- `avg_monthly_spend`: `TotalCharges / tenure` (0 if tenure is 0)
- `charge_gap`: `MonthlyCharges - avg_monthly_spend`, a signal of recent price increases
- `is_month_to_month` and `auto_pay`: flags for contract flexibility and automatic payment
- Numeric features are standardized and categoricals one-hot encoded inside a scikit-learn `Pipeline`, so nothing is fit on test data.

## 5. Models and why

| Model | Why |
| --- | --- |
| Logistic Regression | Simple, interpretable baseline; tells me how much the harder models add |
| Random Forest | Captures non-linear effects and interactions with little tuning |
| XGBoost | Most often the top performer on tabular churn data in the literature |

Class imbalance is handled with class weights (`class_weight` / `scale_pos_weight`), not resampling, which keeps the data unchanged and avoids leakage during cross-validation.

**Validation:** stratified 80/20 train/test split; 5-fold stratified cross-validation on the training set to compare models; the test set is used once for final evaluation. The decision threshold is chosen on out-of-fold training predictions, not on the test set.

## 6. Evaluation metrics and why

- **ROC-AUC (main metric):** measures how well the model ranks churners above non-churners regardless of threshold. Suitable for comparing models.
- **PR-AUC (average precision):** more informative than ROC-AUC when the positive class is a minority.
- **Recall on churners:** the share of customers who leave that the model catches. Missing a churner usually costs more than a wasted retention offer.
- **Precision on churners:** how many flagged customers really leave, which controls the cost of false alarms.
- **F1:** a single balance of precision and recall, used to choose the threshold.
- **Accuracy is not used as a headline metric:** a model that predicts “stay” for everyone would already be right about three times in four.