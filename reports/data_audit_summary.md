# Data Audit Summary - Diabetes 30-Day Readmission Prediction

# 1. Dataset Overview

  Dataset                    Rows   Columns
  --------------------- --------- ---------
  `diabetic_data.csv`     101,766        50
  `IDS_mapping.csv`            67         2

The main dataset contains 101,766 hospital encounters and 50 variables
covering patient demographics, diagnoses, medications, hospital stay
details, and readmission status.

# 2. Variable Types

  Data type             Number of columns
  ------------------- -------------------
  Integer (`int64`)                    13
  Object (`object`)                    37
  **Total**                        **50**

Examples of numerical variables include `time_in_hospital`,
`num_lab_procedures`, and `num_medications`. Categorical variables
include `race`, `gender`, `age`, `medical_specialty`, and
medication-related fields.

Some integer-coded fields, such as `admission_type_id`,
`discharge_disposition_id`, and `admission_source_id`, represent
categories rather than continuous measurements. Identifier fields such
as `encounter_id` and `patient_nbr` should not be used as ordinary
predictive features.

# 3. Missing Values

The dataset uses `?` to represent missing values. These placeholders
were treated as missing values for the audit.

  Variable                Missing values   Missing percentage
  --------------------- ---------------- --------------------
  `weight`                        98,569               96.86%
  `max_glu_serum`                 96,420               94.75%
  `A1Cresult`                     84,748               83.28%
  `medical_specialty`             49,949               49.08%
  `payer_code`                    40,256               39.56%
  `race`                           2,273                2.23%
  `diag_3`                         1,423                1.40%
  `diag_2`                           358                0.35%
  `diag_1`                            21                0.02%

**Observation:** `weight`, `max_glu_serum`, and `A1Cresult` have very
high missingness. The `weight` variable should be considered for
exclusion because approximately 96.86% of its values are missing.
Missing values in retained variables should be handled during
preprocessing.

# 4. Duplicate Records

  Check                       Result
  ------------------------- --------
  Exact duplicate rows             0
  Duplicate encounter IDs          0
  Repeated patient IDs        30,248

There are no exact duplicate rows or duplicate encounter IDs. Some
patients have multiple encounters. To reduce data leakage, encounters
belonging to the same patient should be kept together when splitting the
data into training and test sets.

# 5. Target Distribution

The original target variable is `readmitted`, with three categories.

  Original target         Records    Percentage
  ----------------- ------------- -------------
  `NO`                     54,864        53.91%
  `>30`                    35,545        34.93%
  `<30`                    11,357        11.16%
  **Total**           **101,766**   **100.00%**

For the binary prediction task, the target is defined as:

-   `readmitted_30d = 1` when `readmitted == '<30'`.
-   `readmitted_30d = 0` when `readmitted` is `NO` or `>30`.

  Binary target                              Records    Percentage
  ------------------------------------ ------------- -------------
  0 -- Not readmitted within 30 days          90,409        88.84%
  1 -- Readmitted within 30 days              11,357        11.16%
  **Total**                              **101,766**   **100.00%**

**Observation:** The binary target is imbalanced, with 11.16% positive
cases. Accuracy alone may therefore be misleading. Precision, recall,
F1-score, PR-AUC, and ROC-AUC should also be considered when evaluating
models.

# 6. Obvious Outliers

Potential outliers were inspected using the Interquartile Range (IQR)
method on numerical count/measurement variables. Identifier fields and
integer-coded categorical fields were excluded from this check.

  Variable                 Maximum observed value   IQR outlier count
  ---------------------- ------------------------ -------------------
  `time_in_hospital`                           14               2,252
  `num_lab_procedures`                        132                 143
  `num_procedures`                              6               4,954
  `num_medications`                            81               2,557
  `number_outpatient`                          42              16,739
  `number_emergency`                           76              11,383
  `number_inpatient`                           21               7,049
  `number_diagnoses`                           16                 281

**Observation:** The outpatient, emergency, and inpatient visit-count
variables have relatively high numbers of IQR outliers. These values may
reflect genuine frequent healthcare use rather than data errors. They
should be investigated and should not be removed automatically.

## 7. Summary and Recommendations

  Audit area                Finding
  ------------------------- ----------------------------------------------
  Dataset size              101,766 rows × 50 columns
  Variable types            13 integer and 37 object columns
  Missing values            Very high missingness in selected variables
  Exact duplicate rows      None
  Duplicate encounter IDs   None
  Repeated patient IDs      30,248
  Target distribution       Imbalanced; 11.16% positive class
  Outliers                  Present in several numerical count variables




