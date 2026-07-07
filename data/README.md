# Dataset Access

## Overview

This project uses the dataset from the Kaggle competition **Carvana: Don't Get Kicked!**.

The task is to predict whether a vehicle purchased at auction is a bad buy. The target variable is:

```text
IsBadBuy
```

The dataset contains vehicle auction records with information about purchase date, auction provider, vehicle characteristics, odometer reading, acquisition cost, market price estimates, warranty cost, and other categorical and numerical attributes.

## Source

The dataset is available from the original Kaggle competition:

```text
https://www.kaggle.com/competitions/DontGetKicked
```

## Main Files

The original Kaggle dataset includes files such as:

```text
training.csv
test.csv
example_entry.csv
Carvana_Data_Dictionary.txt
```

## Target Variable

The target variable is:

```text
IsBadBuy
```

It identifies whether the purchased vehicle was a bad buy.

This is a binary classification problem:

```text
0 = not a bad buy
1 = bad buy
```

## Data Dictionary

The data dictionary is included in this repository:

```text
data/Carvana_Data_Dictionary.txt
```

It describes the main dataset fields, including:

```text
RefID
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
WheelType
VehOdo
Nationality
Size
TopThreeAmericanName
VehBCost
IsOnlineSale
WarrantyCost
IsBadBuy
```

## Temporal Split

The project uses a time-based split based on:

```text
PurchDate
```

The split is designed so that:

```text
train.PurchDate < valid.PurchDate < test.PurchDate
```

This setup better simulates a real-world scenario where the model is trained on historical purchases and evaluated on later purchases.

The split strategy is:

```text
First 33% of records  -> training set
Middle 33% of records -> validation set
Last 33% of records   -> test set
```

## Why the Full Dataset Is Not Included

The full CSV files are not stored directly in this repository.

The dataset should be downloaded from the original Kaggle competition page. This keeps the repository lightweight and avoids duplicating competition data.

## Expected Local Setup

After downloading the Kaggle files, place them in a local working directory as expected by the notebook.

Typical files:

```text
training.csv
test.csv
example_entry.csv
Carvana_Data_Dictionary.txt
```

The notebook documents the full workflow for loading, preprocessing, splitting, modeling, and evaluating the data.

## Notebook

The full machine learning workflow is available in:

```text
notebooks/carvana_tree_ensemble_workflow.ipynb
```