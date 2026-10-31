# Preprocessing Pipeline and Train/Test Split Design

## 1. Overview

This section outlines the preprocessing steps and train/test split design for the Diabetes 30-Day Readmission Prediction project.

As the data contains multiple instances per patient, the train/test split has been designed to ensure we're splitting at the patient level to prevent any information leakage.

## 2. Target Variable Cleaning

Original target variable:
readmitted
- NO
- >30
- <30
We have transformed this into a binary variable:

readmitted_30d

- 1 = readmitted == '<30'
- 0 = readmitted == 'NO' or '>30'

Binary variable distribution:
Binary variable | Instances | %
--------------- | --------- | ---

0 = Not readmitted within 30 days | 90409 | 88.84%
1 = Readmitted within 30 days | 11357 | 11.16%
Total | 101766 | 100.00%

The binary variable is an imbalanced target variable with the positive class representing 11.16% of the dataset.

## 3. Feature Variable Cleaning

The following transformations/decisions have been made:

- The '?' value has been treated as nulls.
- The encounter_id and patient_nbr variables were removed from the predictive variables due to being an identifier. However, patient_nbr has been retained to use in the grouping.

- The weight variable was removed due to having a large amount of missing data (96.86% of instances were null).
- Categorical variables were identified by looking at the integer encoded variables. These include admission_type_id, discharge_disposition_id, admission_source_id.

- We have 8 numerical variables and 38 categorical variables in the modelling dataset after cleaning.

## 4. Preprocessing Pipeline
All preprocessing has been done using Scikit-learn Transformers inside a modelling pipeline to ensure the transformations are fit on the training data and transformed on the test data.
Feature Type | Transformation | Reason
------------ | -------------- | ------
Numerical | Impute missing values using the median |
Numerical | Standardise using StandardScaler |
Categorical | Impute missing values using the most frequent value |
Categorical | Encode using OneHotEncoder |
Encoding | handle_unknown="ignore" | To handle unknown categories in the test data.
Encoding | min_frequency=10 | To group together categories with low frequency.
We have used a ColumnTransformer to combine these transformations.
Information leakage has been prevented by fitting the preprocessing pipeline on the training data and transforming the test data.

## 5. Train/Test Split Design
### Splitting
The data has been split using Scikit-learns GroupShuffleSplit method. An 80/20 split was used.
The data has been grouped by patient_nbr meaning all instances for a given patient will be in the training or test sets.

### Why Group by Patient
Some patients will have multiple instances in the dataset. If we had used a random row based split we could have some instances for the same patient in both the training and test sets. This would mean the model could utilise the patterns specific to that patient causing an overly optimistic evaluation.
By splitting at the patient level we reduce the chances of this happening and are evaluating our model on new patients.

### Parameters
Parameter | Value
--------- | -----
Splitting Method | GroupShuffleSplit
Training Proportion | 80%
Test Proportion | 20%
Grouping | patient_nbr
Random State | 42

### Results
Metric | Training Data | Test Data
------ | ------------- | -------
Instances | 81613      | 20153
Positive Class Proportion | 11.28% | 10.67%
Patients duplicated between sets | 0 | 0
The positive class proportions are quite similar although may not be exactly the same. GroupShuffleSplit forces separation between groups so there is no stratification based on the target variable.

## 6. Cross Validation Design
An 80/20 holdout split has been used with the data grouped by patient. We haven't performed any cross validation.
If we introduce cross validation in future experiments we need to ensure the splits are grouped. This means we won't have any instances for the same patient in both the training and validation sets. Model selection and threshold tuning shouldn't be performed using the final test set.

## 7. Summary
The preprocessing and evaluation has considered several key aspects of the data:
- '?' values have been considered.
- The weight variable has been removed due to missing a large amount of data.
- Identifier variables have been removed.
- Integer encoded categorical variables have been considered as categorical.
- Transformations have only been fit on the training data.
- Grouping by patient to ensure we don't have any instances for the same patient in the training and test sets.
The baseline has been designed with an 80/20 holdout with grouping by patient and a preprocessing pipeline fit only on the training data. This gives us a more appropriate evaluation for data with multiple instances per patient.