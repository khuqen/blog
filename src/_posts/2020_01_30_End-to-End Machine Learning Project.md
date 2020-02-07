---
category: 机器学习
tags:
  - Hands-on ML
date: 2020-01-30
title: End-to-End Machine Learning Project
vssue-title: End-to-End Machine Learning Project
---

《Hands-on Machine learning》 读书笔记, 第二章 End-to-End Machine Learning Project
<!-- more -->

# End-to-End Machine Learning Project


## Look at the Big Picture

The first task is to build a model of housing prices in California using the California census data. The model should learn from this data and be able to predict the median housing price in any district, given all the other metrics.

### Frame the Problem

It is important to know what exactly is the business objective,  it will determine how you frame the problem, wha algorithms you will select, what performance measure you will use to evaluate your model, and how much effort you should spend tweaking(调整) it.

#### Pipelines

> A sequence of data processing **components** is called a data **pipeline**. Pipelines are very common in Machine Learning systems, since there is a lot of data to manipulate and many data transformations to apply.

### Select a Performance Measure

A typical performance measure for regression problems is the **Root Mean Square Error** (RMSE). It gives an idea of how much error the system typically makes in its predictions, with a **higher weight for large errors**. 
$$
RMSE(\bold X, h) = \sqrt{\frac1m\sum_{i=1}^{m}{(h(\bold x^{(i)})-y^{(i)})^2}}
$$
Suppose that there are many outlier districts. In that case, you may consider using the **Mean Absolute Error**.
$$
MAE(\bold X, h) = \frac1m\sum_{i=1}^m|h(x^{(i)})-y^{(i)}|
$$
Both the RMSE and the MAE are ways to measure the distance between two vectors:the vector of predictions and the vector of target values. Various distance measures,or **norms**, are possible:

- Computing the root of a sum of squares (RMSE) corresponds to the **Euclidean norm**: it is the notion of distance you are familiar with. It is also called the ℓ2 norm, noted $\left\|\cdot\right\|_2$ (or just $\left\|\cdot\right\|$) 

- Computing the sum of absolutes (MAE) corresponds to the ℓ1 *norm*, noted $\left\|\cdot\right\|_1$. It is sometimes called the **Manhattan norm** because it measures the distance between two points in a city if you can only travel along orthogonal city blocks.

- More generally, the $ℓ_k$ *norm* of a vector $\bold v$ containing $n$ elements is defined as
  $$
  \left\| v\right\|_k=(|v_0|^k+|v_1|^k+\cdots+|v_n|^k)^{\frac1k}
  $$
  $ℓ_0$ just gives the number of non-zero elements in the vector, and $ℓ_\infty$ gives the maximum absolute value in the vector.

- The higher the norm index, the more it focuses on large values and neglects small ones. This is why the RMSE is more sensitive to outliers than the MAE. But when outliers are exponentially rare (like in a bell-shaped curve), the RMSE performs very well and is generally preferred.

## Get the Data

### Create a Test Set

Creating a test set is theoretically quite simple: just pick some instances randomly, typically 20% of the dataset (or less if your dataset is very large), and set them aside:

```python
import numpy as np
def split_train_test(data, test_ratio):
 shuffled_indices = np.random.permutation(len(data))
 test_set_size = int(len(data) * test_ratio)
 test_indices = shuffled_indices[:test_set_size]
 train_indices = shuffled_indices[test_set_size:]
 return data.iloc[train_indices], data.iloc[test_indices]
```

or

```python
from sklearn.model_selection import train_test_split
train_set, test_set = train_test_split(housing, test_size=0.2, random_state=42)
```



## Prepare the Data for Machine Learning Algorithms

### Feature Scaling

#### Min-max scaling

values are shifted and rescaled so that they end up ranging from 0 to 1. We do this by subtracting the min value and dividing by the max minus the min.  Scikit-Learn provides a transformer called **MinMaxScaler** for this. It has a feature_range hyperparameter that lets you change the range if you don’t want 0–1 for some reason.

#### Standardization

first it subtracts the mean value (so standardized values always have a zero mean), and then it divides by the standard deviation so that the resulting distribution has unit variance. 

Standardization does not bound values to a specific range, it is much less affected by outliers. Scikit-Learn provides a transformer called **StandardScaler** for standardization.



## Select and Train a Model

### Training and Evaluating on the Training Set

 for example, train a Linear Regression model

```python
from sklearn.linear_model import LinearRegression
lin_reg = LinearRegression()
lin_reg.fit(housing_prepared, housing_labels)
```

### Better Evaluation Using Cross-Validation

A great alternative is to use Scikit-Learn’s **K-fold cross-validation** feature. 

```python
from sklearn.model_selection import cross_val_score
scores = cross_val_score(lin_reg, housing_prepared, housing_labels,
 scoring="neg_mean_squared_error", cv=10)
tree_rmse_scores = np.sqrt(-scores)
```

Scikit-Learn’s cross-validation features expect a utility function (greater is better) rather than a cost function (lower is better).

look at the results:

```python
def display_scores(scores):
    print("Scores:", scores)
    print("Mean:", scores.mean())
    print("Standard deviation:", scores.std())
    
display_scores(tree_rmse_scores)
```



## Fine-Tune Your Model

### Grid Search

you should get Scikit-Learn’s **GridSearchCV** to search for you. All you need to do is tell it which hyperparameters you want it to experiment with, and what values to try out, and it will evaluate all the possible combinations of hyperparameter values, using cross-validation.

```python
from sklearn.model_selection import GridSearchCV
param_grid = [ {'n_estimators': [3, 10, 30], 'max_features': [2, 4, 6, 8]}, {'bootstrap': [False], 'n_estimators': [3, 10], 'max_features': [2, 3, 4]}, ]
forest_reg = RandomForestRegressor()
grid_search = GridSearchCV(forest_reg, param_grid, cv=5, scoring='neg_mean_squared_error',return_train_score=True)
grid_search.fit(housing_prepared, housing_labels)
```

you can get the best combination of parameters like this:

```python
>>> grid_search.best_params_
{'max_features': 8, 'n_estimators': 30}
```

You can also get the best estimator directly:

```python
>>>  grid_search.best_estimator_
RandomForestRegressor(bootstrap=True, criterion='mse', max_depth=None,
 max_features=8, max_leaf_nodes=None, min_impurity_decrease=0.0,
 min_impurity_split=None, min_samples_leaf=1,
 min_samples_split=2, min_weight_fraction_leaf=0.0,
 n_estimators=30, n_jobs=None, oob_score=False, random_state=None,
 verbose=0, warm_start=False)
```

 the evaluation scores are also available:

```python
>>> cvres = grid_search.cv_results_
>>> for mean_score, params in zip(cvres["mean_test_score"], cvres["params"]):
    	print(np.sqrt(-mean_score), params)
63669.05791727153 {'max_features': 2, 'n_estimators': 3}
55627.16171305252 {'max_features': 2, 'n_estimators': 10}
53384.57867637289 {'max_features': 2, 'n_estimators': 30}
...
```

### Randomized Search

The grid search approach is fine when you are exploring relatively few combinations, like in the previous example, but when the hyperparameter search space is large, it is often preferable to use **RandomizedSearchCV** instead. This class can be used in much the same way as the GridSearchCV class, but instead of trying out all possible combinations, it evaluates a given number of random combinations by selecting a random value for each hyperparameter at every iteration. 