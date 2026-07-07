# Modeling Approach

## Overview

This document describes the modeling strategy used in the Carvana Tree Ensemble Models project.

The project focuses on predicting whether a vehicle purchased at auction is a bad buy using tree-based machine learning models.

The target variable is:

```text
IsBadBuy
```

The main evaluation metric is:

```text
Gini score
```

## Modeling Objective

The objective is to build models that can rank vehicles by their probability of being a bad buy.

Instead of only predicting hard class labels, the project focuses on predicted probabilities because the Gini score is based on ranking quality.

A good model should assign higher probabilities to risky vehicles and lower probabilities to safer purchases.

## Validation Design

The project uses a time-based train, validation, and test split based on the `PurchDate` field.

The data is sorted by purchase date and split as follows:

```text
First 33% of records  -> training set
Middle 33% of records -> validation set
Last 33% of records   -> test set
```

This ensures:

```text
train.PurchDate < valid.PurchDate < test.PurchDate
```

This setup simulates a real-world production scenario where a model is trained on historical data and evaluated on future vehicle purchases.

## Leakage-Safe Preprocessing

The preprocessing pipeline is designed to avoid data leakage.

Categorical encoders and imputers are fitted only on the training data and then applied to validation and test data.

This prevents information from validation or test records from influencing the training process.

The preprocessing stage includes:

```text
Date sorting
Target separation
Numerical feature handling
Categorical feature encoding
Missing value handling
Train-only fitting of preprocessing objects
Validation and test transformation using fitted preprocessing objects
```

## Custom Decision Tree Classifier

The project includes a custom implementation of a Decision Tree Classifier.

The classifier supports:

```text
fit
predict_proba
predict
max_depth parameter
Gini impurity criterion
Recursive node splitting
```

The tree uses Gini impurity to select the best split at each node.

The goal is to divide the data into increasingly pure child nodes with respect to the target variable.

## Custom Node Design

A separate `Node` structure is used to represent each decision point in the tree.

Each node can store:

```text
Feature values
Target values
Split feature
Split threshold
Gini impurity
Left child node
Right child node
Leaf prediction
```

This design makes the tree implementation easier to understand and extend.

## Decision Tree Regressor

The project also includes a custom Decision Tree Regressor.

Instead of using Gini impurity, the regressor uses a variance-based criterion.

The regressor is useful for understanding how decision trees can be adapted from classification to regression tasks.

## Extra Randomized Trees

The project implements an Extra Randomized Tree approach.

Unlike a standard decision tree, which searches for the best split among many possible thresholds, an extra randomized tree introduces more randomness into the split selection process.

This can reduce variance and improve ensemble performance when multiple randomized trees are combined.

## Random Forest Classifier

The custom Random Forest Classifier combines multiple decision trees.

The main idea is to reduce the variance of a single tree by training many trees on different subsets of data and features.

The final prediction is obtained by averaging predicted probabilities across trees.

The Random Forest implementation supports:

```text
Multiple trees
Random feature selection
Bootstrap sampling
Fixed random seed
Probability averaging
```

Compared with a single tree, Random Forest usually provides more stable predictions and better validation performance.

## Gradient Boosting Decision Trees

The project includes a custom Gradient Boosting Decision Tree classifier.

The model is trained incrementally:

```text
Train an initial prediction
Compute the gradient of binary cross-entropy loss
Train the next tree on the residual signal
Update predictions
Repeat for multiple trees
```

This approach allows each new tree to correct errors made by the previous trees.

The custom GBDT implementation includes parameters such as:

```text
max_depth
number_of_trees
max_features
learning_rate
```

## Library-Based Models

The project also compares custom implementations with optimized machine learning libraries.

The main library-based models are:

```text
scikit-learn DecisionTreeClassifier
LightGBM
CatBoost
XGBoost
XGBoost DART
```

These libraries provide highly optimized implementations of tree-based learning algorithms and are commonly used for tabular machine learning problems.

## LightGBM

LightGBM is a gradient boosting framework designed for efficiency and strong performance on structured data.

It uses histogram-based learning and leaf-wise tree growth, which can improve accuracy while keeping training efficient.

In this project, LightGBM achieved the strongest validation Gini score.

## CatBoost

CatBoost is a gradient boosting library designed to handle categorical features effectively.

One of its key advantages is its native treatment of categorical variables, reducing the need for manual encoding in many use cases.

CatBoost is especially useful when datasets contain many categorical fields, such as vehicle make, model, trim, color, auction provider, and state.

## XGBoost and DART

XGBoost is a widely used gradient boosting library known for strong regularization and robust performance.

The project also evaluates XGBoost DART.

DART stands for Dropouts meet Multiple Additive Regression Trees. It applies a dropout-like strategy to boosting by randomly dropping trees during training.

This can help reduce overfitting in some boosting models.

## Model Comparison

Models are compared using validation Gini.

The validation set is used for model selection because it represents a later time period than the training set.

The test set is reserved for the final evaluation of the selected model.

This prevents the test set from being used during model selection.

## Final Model Selection

The final model is selected based on validation Gini.

In the final notebook, the best-performing model is:

```text
LightGBM
```

Reported scores:

```text
Train Gini:      0.6077
Validation Gini: 0.4813
Test Gini:       0.5003
```

The test score is slightly higher than the validation score, which suggests that the selected model generalizes reasonably well to the later time-based test split.

## Interpretation

The training score is higher than the validation score, which indicates a moderate train-validation gap.

This is expected for a high-capacity gradient boosting model.

However, the test score remains stable compared with validation, so the model does not show problematic degradation on the final temporal split.

## Key Takeaways

The modeling workflow shows that:

```text
Single decision trees provide interpretable baselines
Random forests improve stability over individual trees
Extra randomized trees can reduce variance through randomized splitting
Gradient boosting provides strong performance for tabular data
LightGBM, CatBoost, and XGBoost are powerful baselines for structured datasets
Temporal validation is important for realistic model evaluation
Gini score is suitable for ranking-based binary classification problems
```