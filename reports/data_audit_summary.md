Data Audit - Diabetes 30 Day Readmission Prediction

1. Dataset

Dataset Rows Columns
diabetic_data.csv 101766 50
IDS_mapping.csv 67 2

There are 101766 hospital encounters and 50 variables in the main dataset. The variables cover demographics, diagnosis, medications, inpatient details and whether they have been readmitted.

2. Variable Types

Data Type Count
Int64 13
Object 37
Total 50

Numerical variables include time_in_hospital, num_lab_procedures and num_medications. Categorical variables include race, gender, age, medical_specialty and medication variables.

There are some variables which should be treated as categorical even though they are integers. This includes admission_type_id, discharge_disposition_id and admission_source_id. Variables such as encounter_id and patient_nbr should not be used as regular predictor variables.

3. Missing Data

Missing data was indicated by '?'. This was treated as missing data.

Variable Missing Count % Missing
weight 98569 96.86%
max_glu_serum 96420 94.75%
A1Cresult 84748 83.28%
medical_specialty 49949 49.08%
payer_code 40256 39.56%
race 2273 2.23%
diag_3 1423 1.40%
diag_2 358 0.35%
diag_1 21 0.02%

Note: weight, max_glu_serum and A1Cresult have a very high amount of missing data. It would be worth removing weight from the model since it has 96.86% missing values. Any variables that are kept should have their missing data addressed in preprocessing.

4. Duplicates

Check Result
Duplicate Rows 0
Duplicate Encounter ID 0
Duplicate Patient ID 30248

There are no duplicate rows or encounter IDs. Some patients have had multiple encounters. To avoid data leakage, we should keep all encounters from the same patient together when splitting into train/test.

5. Target Variable

The original target variable has 3 classes.

Target Variable Count %
NO 54864 53.91%
>30 35545 34.93%

<30 11357 11.16%
Total 101766 100.00%
However, we will convert this to binary:
If readmitted == '<30' then readmitted_30d == 1
If readmitted == 'NO' or '>30' then readmitted_30d == 0.
Binary Target Count %
Not readmitted within 30 days (0) 90409 88.84%
Readmitted within 30 days (1) 11357 11.16%
Total 101766 100.00%
Note: We have an imbalance in our binary target variable. Only 11.16% of cases are positive. This means accuracy may be misleading. We should consider looking at precision, recall, F1-score, PR-AUC and ROC-AUC.

6. Obvious Outliers
We can look at potential outliers by calculating the inter-quartile range (IQR) of numerical count/measure variables. We will not consider identifier or categorical variables with integer encoding.
Variable Max Value Count of IQR Outliers
time_in_hospital 14 2252
num_lab_procedures 132 143
num_procedures 6 4954
num_medications 81 2557
number_outpatient 42 16739
number_emergency 76 11383
number_inpatient 21 7049
number_diagnoses 16 281
Note: number_outpatient, number_emergency and number_inpatient have quite a high amount of IQR outliers. However, these could just be real values where patients have visited these services many times. We should investigate these values.

7. Summary & Recommendations
Audit Area Findings
Dataset Size 101766 rows x 50 columns
Variable Types 13 Int64, 37 Objects
Missing Data Some variables have a high amount of missing data
Duplicate Rows No duplicates
Duplicate Encounter ID No duplicates
Duplicate Patient ID 30248
Target Variable Imbalanced (11.16% positive)
Outliers Several numerical count variables have outliers.




