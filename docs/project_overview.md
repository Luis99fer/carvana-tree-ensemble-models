# Project Overview

## Project Name

Carvana Tree Ensemble Models

## Overview

This project is a machine learning case study focused on predicting bad vehicle purchases using auction data from the Kaggle competition **Carvana: Don't Get Kicked!**.

The objective is to build and evaluate tree-based classification models for the binary target variable:

```text
IsBadBuy
```

The project combines custom implementations of decision-tree-based algorithms with industry-standard gradient boosting libraries.

## Business Problem

Vehicle auction purchases involve risk. Some vehicles may later become bad buys due to hidden defects, poor resale potential, warranty issues, or other risk factors.

The goal of this project is to predict whether a purchased vehicle is likely to be a bad buy using historical auction and vehicle information.

This type of model can support decision-making in:

```text
Auction risk analysis
Vehicle acquisition strategy
Used car portfolio management
Fraud and quality screening
Pricing and warranty risk assessment
```

## Machine Learning Task

The task is a binary classification problem.

```text
Input: Vehicle auction record
Output: Probability that the vehicle is a bad buy
Target: IsBadBuy
Metric: Gini score
```

The Gini score is derived from ROC AUC:

```text
Gini = 2 × AUC - 1
```

A higher Gini score means the model is better at separating bad purchases from good purchases.

## Dataset

The dataset comes from the Kaggle competition:

```text
https://www.kaggle.com/competitions/DontGetKicked
```

The data includes vehicle auction information such as:

```text
Purchase date
Auction provider
Vehicle year
Vehicle age
Manufacturer
Model
Trim
Color
Transmission
Odometer reading
Vehicle cost
Market reference prices
Warranty cost
Online sale flag
Bad buy target
```

The full dataset is not included in this repository. Dataset access instructions are documented in:

```text
data/README.md
```

The data dictionary is included in:

```text
data/Carvana_Data_Dictionary.txt
```

## Validation Strategy

The project uses a time-based split based on the `PurchDate` field.

The split is designed as:

```text
First 33% of records  -> training set
Middle 33% of records -> validation set
Last 33% of records   -> test set
```

This ensures that:

```text
train.PurchDate < valid.PurchDate < test.PurchDate
```

This approach is more realistic than a random split because the model is evaluated on future data rather than randomly mixed historical records.

## Modeling Scope

The project evaluates multiple tree-based approaches.

Custom implementations include:

```text
Decision Tree Classifier
Decision Tree Regressor
Random Forest Classifier
Extra Randomized Trees
Gradient Boosting Decision Trees
```

Library-based models include:

```text
scikit-learn Decision Tree
LightGBM
CatBoost
XGBoost
XGBoost DART
```

## Why Tree-Based Models

Tree-based models are well suited for tabular data because they can handle:

```text
Nonlinear relationships
Feature interactions
Mixed numerical and categorical features
Missing values
Risk segmentation
Complex decision boundaries
```

Gradient boosting models such as LightGBM, CatBoost, and XGBoost are especially strong for structured tabular problems.

## Main Workflow

The notebook follows this workflow:

```text
Load Carvana auction data
Sort records by purchase date
Create time-based train, validation, and test splits
Preprocess categorical and numerical features
Implement custom tree-based algorithms
Train baseline and ensemble models
Compare models using validation Gini
Select the best model
Evaluate final performance on train, validation, and test splits
Interpret generalization behavior
```

## Final Model

The strongest validation performance was achieved by:

```text
LightGBM
```

Final reported scores:

```text
Train Gini:      0.6077
Validation Gini: 0.4813
Test Gini:       0.5003
```

The test score remains stable compared with validation, suggesting reasonable generalization to the later time-based split.

## Skills Demonstrated

This project demonstrates practical skills in:

```text
Tabular machine learning
Binary classification
Tree-based model design
Custom algorithm implementation
Random Forests
Extra Trees
Gradient Boosting
LightGBM
CatBoost
XGBoost
Temporal validation
Leakage-safe preprocessing
Gini-based model evaluation
Model comparison and interpretation
```

## Repository Purpose

This repository presents a portfolio-ready machine learning project that combines algorithmic understanding with applied model evaluation.

It demonstrates not only how to use advanced machine learning libraries, but also how tree-based models work internally through custom implementations.