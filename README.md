# Predicting Diabetes from CDC BRFSS Health Indicators

*Comparing logistic regression and random forest models, tuned for high recall in screening*

## Question
Can self-reported health indicators predict whether a person has diabetes or prediabetes?
In screening, a missed case (false negative) is the costliest error, because the person's
condition goes untreated. This project therefore prioritizes **recall** over accuracy.

## Data
- **Source:** CDC Behavioral Risk Factor Surveillance System (BRFSS) 2015, via the UCI
  Machine Learning Repository ([DOI: 10.24432/C53919](https://doi.org/10.24432/C53919))
- **Size:** 253,680 survey respondents, 21 features (health conditions, lifestyle, demographics)
- **Target:** 0 = no diabetes, 1 = prediabetes or diabetes
- **Class balance:** 13.9% positive (imbalanced)

## Methods
- **Preprocessing:** stratified 75/25 train-test split; features standardized for logistic regression
- **Models:**
  1. Logistic regression
  2. Random forest (default settings)
  3. Random forest with `min_samples_leaf=50` to reduce overfitting
- **Class imbalance:** `class_weight='balanced'` weights missed diabetics about 6x more during training
- **Threshold tuning:** decision thresholds from 0.3 to 0.7 compared on the final model
- **Interpretation:** random forest feature importance

## Results

| Model | Recall (class 1) | Precision (class 1) | ROC-AUC |
|---|---|---|---|
| Logistic regression | 0.76 | 0.31 | 0.82 |
| Random forest (default) | 0.16 | 0.46 | 0.80 |
| **Random forest (min_samples_leaf=50)** | **0.78** | **0.31** | **0.83** |

Accuracy is not reported as a main metric: a model predicting "no diabetes" for everyone
would be 86% accurate with 0% recall.

**Threshold tuning (final model):**

| Threshold | Recall | Precision |
|---|---|---|
| **0.3** | **0.93** | **0.23** |
| 0.4 | 0.87 | 0.27 |
| 0.5 (default) | 0.78 | 0.31 |
| 0.6 | 0.64 | 0.36 |
| 0.7 | 0.46 | 0.43 |

**Chosen threshold: 0.3.** It catches 93% of diabetics. Lower precision is acceptable for
screening: a false alarm leads to an inexpensive follow-up blood test, while a missed
diagnosis can mean years of untreated disease.

**Comparison with published work:** Xie et al. (2019) built diabetes prediction models on
BRFSS 2014 data. Their best ROC-AUC was 0.79 and their highest sensitivity (recall) was 0.52.
This project's higher recall comes mainly from class weighting and threshold tuning. Because
the survey year, variables, and target definition differ, this is a reference point, not a
direct comparison.

## Key Findings
- **A simpler model held its own:** the default random forest overfit and caught only 16% of
  diabetics. Limiting leaf size raised recall to 78%, only slightly above logistic regression.
- **Top features:** GenHlth (self-rated general health), HighBP, and BMI.
- **Predictive is not causal:** HighBP and BMI are established diabetes risk factors.
  GenHlth is a strong correlate but likely reflects reverse causation: people with diabetes
  may rate their health as poor *because* of the disease.

## Limitations
- **Cross-sectional design:** each person was surveyed once, so the data cannot show whether
  a factor came before diabetes. Feature importance does not imply causation.
- **Self-reported data:** answers are subject to recall bias and reporting bias.
- **Threshold chosen on the test set:** a separate validation set would give an unbiased estimate.
- **Generalizability:** results reflect US adults surveyed in 2015 and may not apply to other
  populations.

## References
1. CDC Diabetes Health Indicators [Dataset]. (2017). UCI Machine Learning Repository.
   https://doi.org/10.24432/C53919
2. Xie Z, Nikolayeva O, Luo J, Li D. Building Risk Prediction Models for Type 2 Diabetes
   Using Machine Learning Techniques. *Prev Chronic Dis* 2019;16:190109.
   https://doi.org/10.5888/pcd16.190109

## How to Run
Open [`diabetes_risk_prediction.ipynb`](diabetes_risk_prediction.ipynb) in Google Colab and
select **Runtime → Run all**.
