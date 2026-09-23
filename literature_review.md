# Literature Review: Customer Churn Prediction

Five sources on churn prediction with machine learning, mostly in telecom. I read the abstracts and available text of each; details such as exact metrics should be checked against the full papers before being cited formally.

## 1. Imani, Hashmi & Verma (2025). *Evaluating the Performance of Random Forest and XGBoost with Gaussian Noise Upsampling Technique for Customer Churn Prediction*
Preprint (Stockholm University, not peer reviewed). https://www.preprints.org/frontend/manuscript/4c892466c8665040c9be4272c7682f9e/download_pub

The authors compare XGBoost and Random Forest on a public telecom dataset, with and without a Gaussian-noise upsampling step that addresses class imbalance. XGBoost beats Random Forest on most metrics before resampling. Upsampling raises recall on churners for both models, but it lowers precision and overall accuracy, so the choice depends on what a missed churner costs compared with a false alarm.

## 2. Explainable AI-driven customer churn prediction: a multi-model ensemble approach with SHAP-based feature analysis (2026). *Frontiers in Artificial Intelligence*
https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2026.1748799/full

This study uses the same Telco Customer Churn dataset (7,043 customers) that I use. It combines feature engineering, SMOTE oversampling, and seven models (including XGBoost, Random Forest and a multi-layer perceptron), then uses SHAP values and customer segmentation to explain which factors drive churn. Its main lesson for my project is that a churn model is more useful to a business when it explains why customers leave, not only who is likely to.

## 3. Ahmad, Jafar & Aljoumaa (2019). *Customer churn prediction in telecom using machine learning in big data platform*. Journal of Big Data
https://link.springer.com/article/10.1186/s40537-019-0191-6

The authors compare tree-based models (Decision Tree, Random Forest, Gradient Boosting Machine, XGBoost) on large telecom data, after building their own preparation, feature engineering and feature selection steps. XGBoost ranks first by AUC, followed by GBM, Random Forest and Decision Tree. When they tested the model on data from a different period, performance dropped, which they attribute to customer behaviour changing over time, so they recommend retraining regularly.

## 4. Saleh & Abd-Alsabour (2024). *Improved Decision Tree, Random Forest, and XGBoost Algorithms for Predicting Client Churn in the Telecommunications Industry*. IJACSA, Vol. 15, No. 12
https://thesai.org/Downloads/Volume15No12/Paper_68-Improved_Decision_Tree_Random_Forest_and_XGBoost_Algorithms.pdf

This paper tackles high-dimensional, imbalanced telecom data (the Cell2Cell dataset) by combining careful preprocessing with grid-search tuning of Decision Tree, Random Forest and XGBoost models. XGBoost comes out on top with an accuracy of about 0.82. The paper argues that tree models need proper hyperparameter tuning and standardized preprocessing to reach their potential.

## 5. *Application Design of Customer Churn Prediction Using Random Forest and XGBoost Algorithms for Telecommunication Industry in Indonesia* (Zenodo record)
https://zenodo.org/records/17636453

This work builds a churn prediction application for Indonesian internet service providers using Random Forest and XGBoost. For class imbalance it combines cluster-based undersampling with cost-sensitive learning, which weights mistakes on the minority class more heavily instead of altering the data. It is useful to me as an example of turning a churn model into a working tool, and as support for handling imbalance through weighting.

## Takeaways for this project
- Gradient boosting (XGBoost) is the most common top performer, so it should be my main model, with Logistic Regression and Random Forest as comparisons.
- Class imbalance is central. Resampling raises recall but costs precision, so I will use class weights and choose the decision threshold deliberately.
- Accuracy is misleading on imbalanced data; ROC-AUC, PR-AUC, recall and precision are more informative.
- Explainability (feature importance or SHAP) makes the model more valuable to a business.
- Churn behaviour drifts over time, so a real product needs periodic retraining. This dataset has no dates, which limits what I can test.
