## Model Settings and Hyperparameter Tuning

### 1. Logistic Regression

Logistic Regression was used as the initial learned baseline model. L2 regularisation was applied with C=1.0, balanced class weights and a maximum of 800 iterations. Balanced class weights were used to account for the imbalance between readmitted and non-readmitted encounters.

### 2. Random Forest

The initial Random Forest model used 300 decision trees, a maximum depth of None, a minimum of 5 samples required for a split, a minimum of 2 samples per leaf, and square-root feature selection. The `balanced_subsample` class-weight setting was used to account for class imbalance.

### 3. Extra Trees

Extra Trees was configured with 300 trees, a maximum depth of None, a minimum split size of 5, a minimum leaf size of 2, square-root feature selection and balanced class weights. It was included to compare a second tree-ensemble approach with Random Forest.

### 4. Random Forest Hyperparameter Tuning

Hyperparameter tuning was performed on Random Forest to investigate whether alternative model configurations could improve predictive performance.

A smaller search was used to reduce computational time. Four configurations were evaluated using two-fold GroupKFold cross-validation, keeping encounters from the same patient in the same fold.

The search explored:

- Number of trees (`n_estimators`): 100 and 150.
- Maximum tree depth (`max_depth`): 15 and 25.
- Minimum samples per leaf (`min_samples_leaf`): 2.

Average Precision (PR-AUC) was used as the optimisation metric because the dataset is imbalanced, with only 11.16% positive readmission cases.

The best configuration was 150 trees, maximum depth of 15 and minimum leaf size of 2. Its best cross-validation PR-AUC was 0.2027.

### 5. Tuning Results and Interpretation

The tuned Random Forest achieved a test PR-AUC of 0.1988, compared with 0.2138 for the original Random Forest. Its ROC-AUC also decreased from 0.6797 to 0.6714.

However, default-threshold recall increased from 0.1269 to 0.5467, while F1-score increased from 0.1781 to 0.2647. This came at the cost of lower precision and accuracy.

Therefore, hyperparameter tuning did not improve the primary PR-AUC metric. The original Random Forest remained the strongest model according to PR-AUC and ROC-AUC.

The tuning exercise demonstrates that changing model parameters can improve some evaluation metrics while reducing others. It also highlights the importance of selecting evaluation metrics that match the project's objective.


### Error Analysis

The Random Forest model correctly classified 17,361 non-readmission cases and 273 readmission cases. However, it produced 641 false positives and 1,878 false negatives.

The high number of false negatives indicates that the model missed many actual 30-day readmissions. Its recall for the positive class at the default threshold was only 0.1269. Logistic Regression identified more actual readmissions (1,207), but generated substantially more false positives (6,011).

This demonstrates the trade-off between identifying readmissions and avoiding false alarms. The models performed less effectively on the minority readmission class than on the majority non-readmission class.



### Draft conclusion

The original Random Forest was the strongest model according to the primary PR-AUC metric, achieving 0.2138 compared with 0.2023 for Logistic Regression and 0.2049 for Extra Trees. It also achieved the highest ROC-AUC of 0.6797.

However, its recall at the required precision threshold of 0.60 was only 0.0079. This indicates that the model identifies very few actual readmissions when the precision constraint is imposed.

Hyperparameter tuning did not improve the primary test metric. The results demonstrate that model performance depends on the selected evaluation metric and classification threshold.

Limitations include class imbalance, substantial missing data, the historical nature of the dataset and the possibility that some features may not be available at the intended prediction time. In particular, discharge-related features require careful consideration if predictions are intended at admission.

Future work could investigate feature timing, alternative imbalance-handling approaches, additional validation and threshold selection using validation data.

This is an academic investigation and the models are not suitable for clinical decision-making.