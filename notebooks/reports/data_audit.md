# Milestone 2 — Data Audit Report

## Dataset overview
- Rows: 101,766
- Columns: 50 original columns plus the derived target
- Exact duplicate rows: 0
- Duplicate encounter IDs: 0
- Repeated patient IDs: 30,248

## Variable types
str      37
int64    14

## Missing values
| Feature | Missing count | Missing percentage |
|---|---:|---:|
| weight | 98,569 | 96.86% |
| max_glu_serum | 96,420 | 94.75% |
| A1Cresult | 84,748 | 83.28% |
| medical_specialty | 49,949 | 49.08% |
| payer_code | 40,256 | 39.56% |
| race | 2,273 | 2.23% |
| diag_3 | 1,423 | 1.40% |
| diag_2 | 358 | 0.35% |
| diag_1 | 21 | 0.02% |

## Target distribution
| Target | Meaning | Count | Percentage |
|---:|---|---:|---:|
| 0 | Not readmitted within 30 days | 90,409 | 88.84% |
| 1 | Readmitted within 30 days | 11,357 | 11.16% |

## Outlier review
Potential outliers were flagged using the IQR rule. They were not automatically removed. Categorical administrative IDs were excluded from numerical outlier interpretation.

## Initial preprocessing considerations
- Convert '?' placeholders to missing values.
- Exclude encounter_id and patient_nbr from model predictors.
- Use patient_nbr for grouped splitting only.
- Consider excluding weight because 96.86% of its values are missing.
- Fit imputation, scaling, and encoding on training data only.
- Treat administrative ID-coded variables as categorical.

## Limitation
This is a historical educational dataset. Findings do not establish clinical effectiveness or generalization.