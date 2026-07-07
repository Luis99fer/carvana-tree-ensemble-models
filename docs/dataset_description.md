# Dataset Description

## Overview

This project uses the dataset from the Kaggle competition **Carvana: Don't Get Kicked!**.

The dataset contains vehicle auction records and is designed for a binary classification task. The goal is to predict whether a purchased vehicle is likely to become a bad buy.

The target variable is:

```text
IsBadBuy
```

## Source

The dataset comes from the original Kaggle competition:

```text
https://www.kaggle.com/competitions/DontGetKicked
```

## Problem Type

This is a supervised binary classification problem.

```text
Input: Vehicle auction and vehicle profile features
Output: Probability of bad purchase
Target variable: IsBadBuy
Metric: Gini score
```

The target is interpreted as:

```text
0 = not a bad buy
1 = bad buy
```

## Business Context

Carvana and similar used-car businesses purchase vehicles from auctions. Some vehicles later turn out to be problematic purchases due to quality issues, warranty costs, resale risk, or other hidden problems.

The objective of the model is to identify risky vehicles before or during the acquisition decision process.

## Main Dataset Fields

The dataset includes vehicle, auction, pricing, and warranty information.

Important fields include:

```text
RefID
IsBadBuy
PurchDate
Auction
VehYear
VehicleAge
Make
Model
Trim
SubModel
Color
Transmission
WheelTypeID
WheelType
VehOdo
Nationality
Size
TopThreeAmericanName
VehBCost
IsOnlineSale
WarrantyCost
```

The dataset also includes multiple MMR price reference fields, such as:

```text
MMRAcquisitionAuctionAveragePrice
MMRAcquisitionAuctionCleanPrice
MMRAcquisitionRetailAveragePrice
MMRAcquisitonRetailCleanPrice
MMRCurrentAuctionAveragePrice
MMRCurrentAuctionCleanPrice
MMRCurrentRetailAveragePrice
MMRCurrentRetailCleanPrice
```

These variables provide market reference values for the vehicle at acquisition time and current time.

## Data Dictionary

The full data dictionary is included in this repository:

```text
data/Carvana_Data_Dictionary.txt
```

It provides descriptions for each dataset field.

## Target Variable

The target variable is:

```text
IsBadBuy
```

This variable identifies whether the vehicle was a bad purchase.

The project models this as a binary classification problem and predicts the probability that each vehicle belongs to the bad-buy class.

## Temporal Feature

The dataset includes a purchase date field:

```text
PurchDate
```

This field is used to create a time-based split.

Instead of randomly splitting the data, records are sorted by purchase date and divided into train, validation, and test subsets.

This is important because in real-world applications, models are trained on historical data and evaluated on future records.

## Split Strategy

The split follows this logic:

```text
First 33% of records  -> training set
Middle 33% of records -> validation set
Last 33% of records   -> test set
```

The resulting time order is:

```text
train.PurchDate < valid.PurchDate < test.PurchDate
```

This helps avoid temporal leakage and provides a more realistic evaluation setup.

## Feature Types

The dataset contains both numerical and categorical features.

Examples of numerical features:

```text
VehicleAge
VehOdo
VehBCost
WarrantyCost
MMR price variables
```

Examples of categorical features:

```text
Auction
Make
Model
Trim
SubModel
Color
Transmission
WheelType
Nationality
Size
TopThreeAmericanName
PRIMEUNIT
AUCGUART
VNST
```

## Preprocessing Considerations

The project applies leakage-safe preprocessing.

Categorical encoders are fitted only on the training set and then applied to validation and test sets.

This is important because validation and test information should not influence training transformations.

The preprocessing workflow handles:

```text
Missing values
Categorical variables
Numerical variables
Date-based splitting
Unseen categories in validation and test data
```

## Evaluation Metric

The main evaluation metric is the Gini score.

The Gini score is derived from ROC AUC:

```text
Gini = 2 × AUC - 1
```

Interpretation:

```text
Gini close to 0 -> weak or random ranking
Higher Gini     -> better separation between good and bad buys
```

## Dataset Usage in This Repository

The full CSV files are not included in this repository.

The dataset should be downloaded from Kaggle and used with the notebook:

```text
notebooks/carvana_tree_ensemble_workflow.ipynb
```

The included data dictionary provides the necessary field descriptions for understanding the dataset structure.

## Notes

This dataset is well suited for tabular machine learning because it includes mixed feature types, missing values, market price variables, categorical vehicle attributes, and a time-based purchase structure.

These properties make it appropriate for evaluating decision trees, random forests, extra trees, gradient boosting, and modern GBDT libraries.