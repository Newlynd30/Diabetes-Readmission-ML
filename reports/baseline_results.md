# Baseline Model Results -- Diabetes 30-Day Readmission Prediction

## 1. Objective

The objective of this baseline experiment is to predict whether a
patient will be readmitted to the hospital within 30 days of an
encounter.

The original `readmitted` target contains three categories:

-   `NO`
-   `>30`
-   `<30`

For this binary classification task, the target was defined as:

-   `readmitted_30d = 1` when `readmitted == '<30'`.
-   `readmitted_30d = 0` when `readmitted` is `NO` or `>30`.

The positive class therefore represents readmission within 30 days.

## 2. Models Evaluated

Two models were evaluated:

### Dummy Classifier

A `DummyClassifier` using `strategy="most_frequent"` was used as a
simple reference baseline. It always predicts the majority class in the
training data and provides a benchmark that a useful model should
improve upon.

### Logistic Regression

Logistic Regression was used as the initial machine-learning baseline
with the following configuration:

  Parameter        Setting
  ---------------- ---------------------
  Model            Logistic Regression
  Penalty          L2 (default)
  `C`              1.0
  `class_weight`   `balanced`
  `max_iter`       800

The preprocessing steps were included in the modelling pipeline so that
they were fitted using the training data only.

## 3. Evaluation Metrics

Because the positive class represents approximately 11% of encounters,
the dataset is imbalanced. Accuracy alone would not adequately describe
model performance.

The proposed evaluation metrics were:

-   **PR-AUC (Average Precision):** Primary metric. It summarizes the
    precision-recall trade-off and is informative when the positive
    class is relatively uncommon.
-   **Recall at precision ≥ 0.60:** Measures the proportion of actual
    positive cases identified while requiring precision to be at least
    0.60. If no threshold achieves the required precision, recall is
    reported as not achieved.
-   **ROC-AUC:** Secondary metric. It measures the model's ability to
    rank positive cases above negative cases across classification
    thresholds.

The positive-class prevalence in the test set was 0.1067 (10.67%),
providing a useful reference for interpreting PR-AUC.

## 4. Baseline Results

  Metric                         Dummy Classifier   Logistic Regression
  ---------------------------- ------------------ ---------------------
  PR-AUC (Average Precision)               0.1067            **0.2023**
  ROC-AUC                                  0.5000            **0.6578**
  Recall at precision ≥ 0.60         Not achieved                0.0023

## 5. Results Interpretation

Logistic Regression achieved a PR-AUC of **0.2023**, compared with
**0.1067** for the Dummy Classifier and a test-set positive-class
prevalence of **0.1067**. This indicates that Logistic Regression
provides better precision-recall ranking performance than the
majority-class baseline.

The Logistic Regression ROC-AUC was **0.6578**, compared with **0.5000**
for the Dummy Classifier, indicating better-than-random ranking ability.

However, recall at precision ≥ 0.60 was only **0.0023 (0.23%)** for
Logistic Regression. This means that, at a threshold satisfying the
specified precision requirement, the model identified only a very small
fraction of actual 30-day readmissions. The Dummy Classifier did not
achieve the required precision threshold.

Overall, Logistic Regression improves on the simple Dummy Classifier,
but its performance remains modest. In particular, the low recall at the
required precision level shows that the baseline is not yet effective at
identifying a substantial proportion of positive cases under that
constraint.

## 6. Conclusion and Next Steps

The baseline experiment establishes a reference point for future model
development. Logistic Regression improves PR-AUC and ROC-AUC over the
majority-class Dummy Classifier, but recall at the required precision
threshold remains very low.

Possible next steps include:

1.  Investigate model thresholds using the training data or a separate
    validation set rather than tuning on the test set.
2.  Compare additional models while retaining the same patient-level
    evaluation design.
3.  Review feature availability at the intended prediction time to avoid
    using information that would not be available when a prediction is
    made.
4.  Consider further validation on a separate time period or external
    dataset.

**Limitation:** These results are from a single patient-grouped holdout
test set. They do not establish clinical usefulness or generalization to
other hospitals or time periods. This is an educational machine-learning
experiment, not a clinical decision-support system.