# Baseline Model Results - Predicting Diabetes Patients Readmission Within 30 Days
## 1. Objective

The aim of the baseline is to determine if patients will be readmitted
within 30 days.

The original variable was called 'readmitted’ and had 3 classes:

- NO

- >30

- <30

We can simplify this to a binary variable by defining a new variable:

- readmitted_30d = 1 if readmitted == '<30'
- readmitted_30d = 0 if readmitted == 'NO’ or '>30’

This way we can define our positive class as being readmitted within 30
days.

## 2. Models Tested

### Dummy Classifier

A DummyClassifier with strategy="most_frequent" was used as a basic
baseline. This will always predict the most frequent class in the training
data.

### Logistic Regression

Logistic Regression was used as a first pass baseline machine learning
model. The parameters used were:

| Parameter | Value |

|----------|-------|

| Model | Logistic Regression |

| Penalty | L2 (default) |

| C | 1.0 |

| class_weight | balanced |

| max_iter | 800 |

The preprocessing was included in the pipeline so that the model is only

fit on the training data.

## 3. Metrics Used

As we have approximately 11% of encounters in the positive class the

data is imbalanced and accuracy alone would not give an accurate view of

our models.

The metrics chosen were:

- PR-AUC (Average Precision): This is our primary metric. It gives us
information about our model based on the trade-off between precision and
recall. It is a useful metric due to the low number of positives.

- Recall: This is the proportion of true positives predicted. We are measuring this at thresholds where precision is >= 0.60. If there are no thresholds with a precision of 0.60 this will be marked as not achieved.

- ROC-AUC: This is our secondary metric. It gives us information about how well our model can rank the positives over the negatives.

We had 10.67% of positives in the test set (0.1067).

## 4. Baseline Results

| Metric | Dummy Classifier | Logistic Regression |

|-------|------------------|---------------------|

| PR-AUC (Average Precision) | 0.1067 | 0.2023 |

| ROC-AUC | 0.5000 | 0.6578 |

| Recall (Precision >= 0.60) | Not Achieved | 0.0023 |

## 5. Interpreting Results

Our Logistic Regression model has a PR-AUC of 0.2023 which compares to
0.1067 for our Dummy Classifier as well as a positive class prevalence in
the test set of 0.1067. This shows our model has better ranking power in
terms of precision and recall than our majority class baseline.
Our Logistic Regression also has a higher ROC-AUC of 0.6578 than our Dummy
Classifier of 0.5000. This means our model has better ranking ability than random guessing.
Our model only had a recall of 0.0023 (0.23%) at a threshold of precision
>= 0.60. This means the model only correctly predicted a small proportion
of true 30 day readmissions. Our Dummy Classifier also did not achieve
the precision threshold.
Overall our Logistic Regression model shows improvement over our simple
Dummy Classifier, however it is still not very good. As the recall at the
precision threshold is very low this shows our baseline is not currently
effective at predicting a large proportion of positives.

## 6. Conclusion and Next Steps

Our baseline has provided us with a starting point for our models. Our
Logistic Regression model performed better in terms of PR-AUC and ROC-AUC
than our Dummy Classifier, however the recall at the precision threshold
was very low.
Next steps could be to:

1. Look into different thresholds for our model using the training data
or validation set, instead of using the test set to tune.

2. Test more models.

3. Check what features we have available at the time we want to make our predictions to ensure we are not using data that will not be available.

4. Further validate by using a separate time period or external data.

These results are only based on one holdout test set for the patient group.
They do not show the model will be clinically useful or generalise to other
hospitals or times. This is an educational ML experiment and not a clinical decision support tool.

