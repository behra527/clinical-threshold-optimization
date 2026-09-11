# Clinical Threshold Optimization

A clinical machine learning project focused on systematic classification threshold optimization using calibrated probabilities. The project evaluates the trade-off between Precision, Recall, F1-Score, and Specificity and selects a threshold based on clinical risk priorities.

## Project Objective

The main objective is to systematically vary the classification threshold from **0.00 to 1.00** and determine how different thresholds affect model performance.

The project focuses on:

* Precision–Recall trade-offs
* Probability calibration
* Clinical threshold optimization
* False Positive and False Negative costs
* Threshold stability
* Statistical validation
* Final holdout evaluation

## Dataset

The project uses a clinical dataset containing **280,985 records and 39 columns**.

The target variable is:

* `Normal` → 0
* `Abnormal` → 1

After removing exact duplicate feature rows, **278,701 records** were used for modeling.

Leakage-prone and unnecessary fields were removed, including:

* `composite_key`
* `sublabel`
* `disease_flags`
* `source_dataset`

## Machine Learning Workflow

The project follows this workflow:

1. Data loading and quality analysis
2. Duplicate detection and removal
3. Leakage analysis
4. Feature preprocessing
5. Stratified Train/Validation/Test split
6. Logistic Regression baseline model
7. Probability calibration
8. Threshold sweeping
9. Precision, Recall, F1, and Specificity analysis
10. Clinical threshold optimization
11. Cost-sensitive analysis
12. Bootstrap confidence intervals
13. Threshold stability analysis
14. Threshold sensitivity analysis
15. Final threshold locking
16. Final holdout evaluation

## Preprocessing

Numerical features were standardized using `StandardScaler`.

Categorical features were encoded using `OneHotEncoder` with:

```text
handle_unknown="ignore"
```

The preprocessing pipeline was fitted only on the training data to prevent data leakage.

## Probability Calibration

Two calibration methods were evaluated:

* Sigmoid (Platt Scaling)
* Isotonic Regression

The calibration methods were compared using Brier Score and Log Loss.

### Calibration Results

| Method       | Brier Score | Log Loss |
| ------------ | ----------: | -------: |
| Isotonic     |      0.0870 |   0.2608 |
| Sigmoid      |      0.0929 |   0.2830 |
| Uncalibrated |      0.0929 |   0.2830 |

**Isotonic calibration** achieved the best validation calibration performance.

## Threshold Optimization

Classification thresholds from **0.00 to 1.00** were evaluated with a step size of **0.01**.

For every threshold, the following metrics were calculated:

* True Positives
* True Negatives
* False Positives
* False Negatives
* Precision
* Recall
* F1-Score
* Specificity
* False Positive Rate
* False Negative Rate

## Clinical Optimization

Several threshold-selection strategies were compared.

| Objective                    | Threshold | Precision | Recall |     F1 |
| ---------------------------- | --------: | --------: | -----: | -----: |
| Default / Recall-Constrained |      0.50 |    0.8622 | 0.9502 | 0.9041 |
| Maximum F1                   |      0.49 |    0.8612 | 0.9518 | 0.9042 |
| Precision-Constrained        |      0.70 |    0.9063 | 0.8429 | 0.8735 |
| Cost-Sensitive               |      0.08 |    0.7734 | 0.9951 | 0.8704 |

For the clinical screening objective, **Recall was prioritized** because missing a positive case can be more costly than generating additional false positives.

The final threshold was locked at **0.50** based only on validation data.

## Threshold Stability

The threshold region from **0.45 to 0.50** provided stable performance while maintaining:

* Recall ≥ 95%
* F1-Score ≥ 90%

The threshold **0.50** was selected because it was the highest threshold in the robust region while still satisfying the validation Recall requirement.

## Statistical Validation

Bootstrap validation was performed using **1,000 iterations** with 95% percentile confidence intervals.

At threshold 0.50:

| Metric      | Estimate |        95% CI |
| ----------- | -------: | ------------: |
| Precision   |   0.8622 | 0.8588–0.8655 |
| Recall      |   0.9503 | 0.9482–0.9525 |
| F1-Score    |   0.9041 | 0.9019–0.9063 |
| Specificity |   0.7475 | 0.7414–0.7535 |
| ROC-AUC     |   0.9457 | 0.9441–0.9474 |
| PR-AUC      |   0.9661 | 0.9651–0.9672 |

## Final Test Results

The final threshold of **0.50** was applied once to the untouched test set.

| Metric      | Test Result |
| ----------- | ----------: |
| Accuracy    |  **86.00%** |
| Precision   |  **89.09%** |
| Recall      |  **88.41%** |
| F1-Score    |  **88.75%** |
| Specificity |  **82.00%** |
| ROC-AUC     |  **94.51%** |
| PR-AUC      |  **96.69%** |

### Final Confusion Matrix

```text
[[17162  3768]
 [ 4036 30775]]
```

Where:

* TN = 17,162
* FP = 3,768
* FN = 4,036
* TP = 30,775

## Validation vs Test

The locked threshold achieved **95.02% Recall** on validation but **88.41% Recall** on the final test set.

This indicates that the selected 95% clinical Recall target was not maintained on unseen data.

However, ROC-AUC remained very similar:

* Validation ROC-AUC: **94.57%**
* Test ROC-AUC: **94.51%**

This suggests that the model maintained strong overall discrimination, while the selected operating point showed some variation between validation and test data.

No post-hoc threshold tuning was performed using the test set.

## Limitations

* The model was evaluated on a single dataset.
* External validation was not performed.
* Prospective clinical validation was not performed.
* The 95% Recall target was not maintained on the final test set.
* Threshold selection should be revalidated for a new clinical population.
* This model should not be treated as a standalone diagnostic system without appropriate clinical validation and oversight.

## Conclusion

This project demonstrates a complete, leakage-aware workflow for clinical threshold optimization.

The final threshold of **0.50** was selected using validation data and then frozen before evaluating the untouched test set.

The final test results showed:

* **86.00% Accuracy**
* **88.41% Recall**
* **88.75% F1-Score**
* **94.51% ROC-AUC**
* **96.69% PR-AUC**

The project demonstrates how classification thresholds can be selected using clinical priorities rather than relying only on the default 0.50 threshold.
