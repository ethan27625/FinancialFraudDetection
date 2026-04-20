# Financial Fraud Detection
Detecting fraudulent bank transactions is a critical challenge in the financial industry. While most transactions are legitimate, a model that classifies everything as non-fraudulent will miss every single fraud case. This project builds a machine learning classifier to identify fraudulent activity within customer-facing bank accounts at Caishen, a NYC-based international bank. 

---

# Intro
This project focuses on building a classification model to detect fraudulent transactions from a synthetic dataset of bank transactions. The dataset contains 6,362,620 transactions with 11 features including transaction type, amount, and account balances before and after each transaction. The target varibale is isFraud which indicated whether a transaction was actually fraudulent. Only 8,213 transactions are fraudulent making this a heavily imbalanced classification problem. 

--- 

# Project Overview 
This project followed a standard machine learning pipeline with four main phases:
- Exploratory Data Analysis (EDA)
- Data Cleaning & Preprocessing
- Model Creation & Hyperparameter Tuning 
- Final Report 

---

# Modules/Libraries
- Scikit-learn
- Matplotlib
- Numpy
- Pandas
- Seaborn
- Scipy

---

# Exploration

I began the exploration phase by examining the class distribution and found the dataset is heavily imbalanced with fraud making up only 0.13% of all transactions. A critical finding was that fraud only occurs in TRANSFER and CASH_OUT transactions. I also found that 98.1% of fraudulent transactions drain the origin account balance to zero. The existing isFlaggedFraud system only caught 16 out of 8,213 actual fraud cases.

---

# Data Cleaning

The dataset had no missing values or duplicates. The following steps were taken during cleaning:

- Dropped nameOrig and nameDest columns which were unique identifiers that would not generalize
- Dropped isFlaggedFraud which was the existing naive flag that caught only 16 out of 8,213 fraught cases
- Engineered new features: orig_balance_drained, orig_balance_error, best_balance_error
- One-hot encoded the transaction type column

---

# Model Creation

I built a RandomForestClassifier with class_weight set to balanced to handle the severe class imbalance. Since the full dataset has over 6 million rows a stratified sample was used for training. After training a baseline model I used RandomizedSearchCV to find optimal hyperparameters.

---

# Results

- Baseline F1 Score: 0.9985
- Tuned F1 Score: 0.9985
- ROC AUC: 0.9990
- Top feature: orig_balance_error 

The tuned model achieved near-perfect performance in detecting fraud. The feature importance analysis confirmed that balance-related features identified during EDA are the strongest predictors of fraudulent activity. 