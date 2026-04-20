## Financial Fraud Dection - Final Report

---

# Which insights did you gain from your EDA? 

During my exploratoyr data analysis I found several important patterns in the data. 
The dataset contains 6,362,620 transactions and is heavily imbalanced. Only 8,213 transactions are fraudulent which is a fraud rate of 0.13%. This means a model that simply predicts "not fraud" for everything would still have 99.87% accuracy but would miss evcery single fraud case. 

I discovered that fraud only occurs in two transaction type: CASH_out (4,116 cases) and TRANSFER(4,097 cases). There were zero fraude cases in PAYMENT, CASH_IN, or DEBIT transactions. This makes sense because fraud involves moving money out of a victim's account. 

I also found that 98.1% of fraudulent transactions drain the origin account balance completely to zero. This became an important feature that I engineered during preprocessing.

The existing fraud detection system (isFlaggedFraud) was essentially useless. It only flagged 16 out of 8,213 actual fraud cases which confirms why the bank needs a better model.

The Mann-Whitney U test confirmed a statistically significant difference in transaction amounts between fraud and non-fraud groups (p < 0.05). Fraudulent transactions had a mean amount of $1.47M compared to $178K for non-fraud. The Chi-Square test also showed a statistically significant relationship between transaction type and fraud (Chi-Square = 22,082.54, p < 0.05).

---

# How did you determine which columns to drop or keep? 

I dropped three columns based on my EDA findings:
nameOrig was dropped because it had 6,353,307 unique values out of 6,362,620 total rows. It is essentially a unique identifier that would not generalize to new transactions and would cause overfitting.
nameDest was dropped for the same reason. It had 2,722,362 unique values which makes it too specific to be useful as a predictive feature.
isFlaggedFraud was dropped because the existing naive flag only caught 16 out of 8,213 actual fraud cases. It provides almost no predictive value and including it would add noise rather than useful signal.
I kept all other columns because the EDA showed they contain useful patterns. The transaction type showed a clear relationship with fraud. The balance columns showed that fraud transactions drain accounts to zero. The amount column showed significant differences between fraud and non-fraud groups.

--- 

# Which hyperparameter tuning strategy did you use? Grid-search or random-search? Why?

I used RandomizedSearchCV for hyperparameter tuning. I chose this over GridSearchCV for a few reasons.

The hyperparameter space I wanted to search was fairly large. I was tuning n_estimators, max_depth, min_samples_split, min_samples_leaf, max_features, and class_weight. A full grid search over all combinations would have been very slow especially with cross-validation on a large dataset.

RandomizedSearchCV samples a fixed number of parameter combinations from the defined distributions which means I can cover a wide search space without testing every single combination. Research has shown that random search often finds good hyperparameters just as well as grid search but in much less time.
I used 15 iterations with 3-fold cross-validation scoring on F1 since that balances precision and recall which is what we care about for fraud detection.

# How did your model's performance change after discovering optimal hyperparameters?

The baseline Random Forest model with default parameters (100 trees, class_weight='balanced') achieved an F1 score of 0.9985 on the test set for the fraud class. This is already very strong because the engineered features like orig_balance_drained and orig_balance_error captured the fraud patterns well.
After hyperparameter tuning the best parameters found were n_estimators=50, min_samples_split=2, min_samples_leaf=2, max_features='log2', max_depth=None, and class_weight='balanced_subsample'. The tuned model achieved the same F1 score of 0.9985 on the test set.
The cross-validated F1 score during tuning was 0.9979 which is a more honest estimate of real-world performance since it is averaged across multiple folds. The fact that both models achieved nearly identical performance suggests that the fraud patterns in this dataset are strong and clearly separable. The tuned model was more efficient though using only 50 trees instead of 100 while maintaining the same accuracy.

The feature importance analysis showed that orig_balance_error (0.389) was by far the most important feature followed by oldbalanceOrg (0.132) and orig_balance_drained (0.117). This confirms that the balance-related patterns identified in the EDA are the key signals for detecting fraud.

# What is your final F1 Score? 

My final F1 Score for the fraud class was 0.9985. 

This was achieved using a RandomForestClassifier with the following optimal hyperparameters found through RandomizedSearchCV: 

- n_estimators: 50
- max_depth: None
- min_samples_split: 2
- min_samples_leaf: 2 
- max_features: log2
- class_weight: balanced_subsample
- ROC AUC: 0.9990
- PR AUC: near 1.0