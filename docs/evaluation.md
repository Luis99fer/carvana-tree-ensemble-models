# Evaluation

## Overview

This document describes the evaluation strategy used in the Carvana Tree Ensemble Models project.

The project predicts whether a vehicle purchased at auction is a bad buy. Since the goal is to rank vehicles by risk, the main evaluation metric is the Gini score.

## Target Variable

The target variable is:

```text
IsBadBuy
```

This is a binary target:

```text
0 = not a bad buy
1 = bad buy
```

The models predict the probability that each vehicle belongs to the bad-buy class.

## Main Metric

The main evaluation metric is:

```text
Gini score
```

The Gini score is derived from ROC AUC:

```text
Gini = 2 × AUC - 1
```

This means:

```text
AUC = (Gini + 1) / 2
```

## Metric Interpretation

The Gini score measures how well the model separates risky vehicles from safer vehicles.

General interpretation:

```text
Gini close to 0  -> weak ranking, close to random
Positive Gini    -> useful ranking signal
Higher Gini      -> better separation between classes
```

In this project, Gini is more appropriate than accuracy because the task is focused on ranking bad-buy risk rather than only predicting hard class labels.

## Temporal Evaluation Strategy

The dataset is split using the purchase date field:

```text
PurchDate
```

The split follows a time-based structure:

```text
First 33% of records  -> training set
Middle 33% of records -> validation set
Last 33% of records   -> test set
```

This ensures:

```text
train.PurchDate < valid.PurchDate < test.PurchDate
```

This evaluation design simulates a real-world deployment scenario where a model is trained on historical data and tested on future purchases.

## Why Time-Based Split Matters

A random split could mix past and future records across training, validation, and test sets.

That may produce overly optimistic results because the validation or test set could contain patterns from the same time period as the training set.

The time-based split is stricter and more realistic because it evaluates whether the model generalizes to later vehicle purchases.

## Model Selection

The validation set is used for model comparison and model selection.

The test set is reserved for the final evaluation of the selected model.

This prevents the test data from influencing modeling decisions.

The model selection process follows this logic:

```text
Train models on the training split
Evaluate models on the validation split
Select the best model by validation Gini
Evaluate the selected model on the test split
```

## Compared Models

The project compares custom implementations and library-based models.

Custom models include:

```text
Custom Decision Tree Classifier
Custom Random Forest Classifier
Custom Extra Randomized Trees
Custom Gradient Boosting Decision Trees
```

Library-based models include:

```text
scikit-learn DecisionTreeClassifier
LightGBM
CatBoost
XGBoost
XGBoost DART
```

## Validation Results

The validation results show that gradient boosting models provide the strongest performance.

Representative validation scores from the notebook:

```text
Custom Decision Tree Gini:      0.4509
Custom Random Forest Gini:      0.4582
Custom GBDT Gini:               0.4629
Custom Extra Trees Gini:        0.4648
LightGBM Gini:                  0.4813
CatBoost Gini:                  0.4805
XGBoost DART Gini:              0.4798
```

The best validation score is achieved by:

```text
LightGBM
```

## Final Model Performance

The final selected model is LightGBM.

Final reported scores:

```text
Train Gini:      0.6077
Validation Gini: 0.4813
Test Gini:       0.5003
```

Approximate AUC values:

```text
Train AUC:      0.8039
Validation AUC: 0.7407
Test AUC:       0.7502
```

## Generalization Analysis

The training Gini is higher than the validation Gini:

```text
Train - Validation Gini gap: 0.1264
```

This indicates a moderate train-validation gap, which is expected for a high-capacity gradient boosting model.

However, the test Gini is slightly higher than the validation Gini:

```text
Validation Gini: 0.4813
Test Gini:       0.5003
```

This suggests that the model does not show problematic degradation on the final time-based test split.

## Final Interpretation

The LightGBM model provides the strongest validation performance and remains stable on the later test period.

Although the model performs better on training data than validation data, the validation and test scores are consistent enough to support reasonable generalization.

The final test Gini of approximately 0.5003 corresponds to an approximate AUC of 0.7502, which indicates useful ranking ability for identifying bad vehicle purchases.

## Key Takeaways

The evaluation shows that:

```text
Tree ensembles outperform a single decision tree
Gradient boosting models provide the strongest validation performance
LightGBM gives the best validation Gini in this workflow
The time-based test score remains stable
The model shows useful ranking ability for bad-buy risk prediction
```

## Notes

The reported scores may vary slightly depending on preprocessing details, random seed behavior, library versions, and model hyperparameters.

The notebook focuses on a transparent and reproducible comparison of tree-based models under a realistic time-based evaluation setup.