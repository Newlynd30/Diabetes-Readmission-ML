# Preprocessing Pipeline and Train/Test Split Design

## 1. Objective

This document describes the preprocessing workflow and evaluation split
used for the Diabetes 30-Day Readmission Prediction project.

The dataset contains multiple hospital encounters for some patients.
Therefore, the split was designed at the patient level to reduce the
risk of data leakage between training and testing data.

## 2. Target Preparation

The original target variable is `readmitted`, with three possible
values: `NO`, `>30`, and `<30`.

A binary target named `readmitted_30d` was created:

-   `1` when `readmitted == '<30'` (readmitted within 30 days).
-   `0` when `readmitted` is `NO` or `>30`.

The binary target distribution across the full dataset was:

  Binary target                              Records    Percentage
  ------------------------------------ ------------- -------------
  0 -- Not readmitted within 30 days          90,409        88.84%
  1 -- Readmitted within 30 days              11,357        11.16%
  **Total**                              **101,766**   **100.00%**

The target is imbalanced, with the positive class representing 11.16% of
all encounters.

## 3. Feature Preparation

The following preparation decisions were applied:

-   The `?` placeholder was treated as a missing value.
-   `encounter_id` and `patient_nbr` were excluded from predictive
    features because they are identifiers. `patient_nbr` was retained
    separately for grouping the split.
-   `weight` was excluded from modelling because 98,569 values (96.86%)
    were missing.
-   Integer-coded fields such as `admission_type_id`,
    `discharge_disposition_id`, and `admission_source_id` were treated
    as categorical variables rather than continuous numerical
    measurements.
-   The modelling data contained 8 numerical features and 38 categorical
    features before preprocessing.

## 4. Preprocessing Pipeline

Preprocessing was implemented using Scikit-learn transformers within a
modelling pipeline. This ensures that imputation, scaling, and encoding
are fitted on the training data and then applied to the test data.

  ---------------------------------------------------------------------------
  Feature type            Preprocessing method        Purpose
  ----------------------- --------------------------- -----------------------
  Numerical               Median imputation           Fill missing numerical
                                                      values

  Numerical               StandardScaler              Standardize numerical
                                                      feature scales

  Categorical             Most-frequent imputation    Fill missing
                                                      categorical values

  Categorical             OneHotEncoder               Convert categories into
                                                      numeric indicator
                                                      features

  Categorical encoding    `handle_unknown="ignore"`   Handle categories
  setting                                             present in test data
                                                      but not seen during
                                                      training

  Categorical encoding    `min_frequency=10`          Group infrequent
  setting                                             categories according to
                                                      the encoder's frequency
                                                      setting
  ---------------------------------------------------------------------------

The numerical and categorical transformations were combined using a
`ColumnTransformer`.

**Data leakage prevention:** The preprocessing pipeline was fitted on
the training data only. The fitted transformations were then applied to
the test data. The test data was not used to calculate imputation
values, scaling parameters, or category encodings.

## 5. Train/Test Split Design

### Splitting method

The data was divided using Scikit-learn's `GroupShuffleSplit` with an
80:20 training/testing split.

The grouping variable was `patient_nbr`. All encounters belonging to a
given patient were assigned to either the training set or the test set,
rather than being distributed independently between both sets.

### Why patient-level grouping was selected

A patient can have multiple encounters in the dataset. With a
conventional random row-level split, encounters from the same patient
could appear in both the training and test sets. This could allow the
model to benefit from patient-specific patterns seen during training and
lead to an overly optimistic evaluation.

Grouping by patient reduces this risk and evaluates performance on
patients not represented in the training set.

### Split configuration

  Setting               Value
  --------------------- ---------------------
  Splitting method      `GroupShuffleSplit`
  Training proportion   80%
  Testing proportion    20%
  Grouping variable     `patient_nbr`
  Random state          42

### Actual split results

  Metric                           Training set   Test set
  ------------------------------ -------------- ----------
  Number of encounters                   81,613     20,153
  Positive-class rate                    11.28%     10.67%
  Patient overlap between sets                0          0

The positive-class rates in the two sets are reasonably close, but they
are not guaranteed to be identical. `GroupShuffleSplit` enforces group
separation; it does not perform stratification by target class.

## 6. Cross-Validation Design

A single 80:20 patient-grouped holdout split was used for this baseline
experiment. Cross-validation was **not** performed at this stage.

If cross-validation is introduced in later experiments, the folds should
also be group-aware so that encounters from the same patient do not
appear in both the training and validation portions of a fold. Any model
selection or threshold tuning should be performed without using the
final test set.

## 7. Summary

The preprocessing and evaluation design addresses several important
properties of the dataset:

1.  Missing-value placeholders are handled explicitly.
2.  Highly incomplete `weight` data is excluded from the baseline
    features.
3.  Identifier columns are excluded from predictive features.
4.  Integer-coded categorical fields are treated as categorical.
5.  Numerical and categorical preprocessing is fitted on training data
    only.
6.  Patient-level grouping prevents the same patient's encounters from
    appearing in both training and test sets.

**Conclusion:** The baseline uses a patient-grouped 80:20 holdout split
and a training-only preprocessing pipeline. This provides a more
appropriate evaluation than a simple random row-level split for a
dataset containing repeated encounters per patient.
