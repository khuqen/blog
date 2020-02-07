---
category: 机器学习
tags:
  - Hands-on ML
date: 2020-01-28
title: The Machine Learning Landscape
vssue-title: The Machine Learning Landscape
---

《Hands-on Machine learning》 读书笔记, 第一章 The Machine Learning Landscape
<!-- more -->

# The Machine Learning Landscape


##  What Is Machine Learning

Machine Learning is the science (and art) of programming computers so they canlearn from data.



## Why Use Machine Learning

- Problems for which existing solutions require a lot of hand-tuning or long lists of rules: one Machine Learning algorithm can often simplify code and perform better.
- Complex problems for which there is no good solution at all using a traditional approach: the best Machine Learning techniques can find a solution.
- Fluctuating(波动) environments: a Machine Learning system can adapt to new data.
- Getting insights about complex problems and large amounts of data.



## Types of Machine Learning Systems

### Supervised/Unsupervised Learning

#### Supervised learning

In supervised learning, the training data you feed to the algorithm includes the desired solutions, called labels.

Here are some of the most important supervised learning algorithms.

- k-Nearest Neighbors
- Linear Regression
- Logistic Regression
- Support Vector Machines(SVMs)
- Decision Trees and Random Forests
- Neural networks

#### Unsupervised learning

In unsupervised learning, the training data is unlabeled. The system tries to learn without a teacher.

Here are some of the most important unsupervised learning algorithms.

- Clustering
  - K-Means
  - DBSCAN
  - Hierarchical Cluster Analysis (HCA)
- Anomaly(异常) detection and novelty(新奇) detection
  - One-class SVM
  - Isolation Forest
- Visualization and dimensionality reduction
  - Principal Component Analysis (PCA)
  - Kernel PCA
  - Locally-Linear Embedding (LLE)
  - t-distributed Stochastic Neighbor Embedding (t-SNE)
- Association rule learning
  - Apriori
  - Eclat

### Batch and Online Learning

#### Batch learning

In batch learning, the system is incapable of learning incrementally: it must be trained using **all** the available data. This will generally take a lot of time and computing resources, so it is typically done offline. 

#### Online learning

In online learning, you train the system incrementally by feeding it data instances sequentially, either individually or by small groups called *mini-batches*. Each learning step is fast and cheap, so the system can learn about new data on the fly, as it arrives.

### Instance-Based Versus Model-Based Learning

#### Instance-based learning

the system learns the examples by heart, then generalizes to new cases by comparing them to the learned examples (or a subset of them), using similarity  measure. 

#### Model-based learning

 build a model of these examples, then use that model to make *predictions*. 



## Testing and Validating

The only way to know how well a model will generalize to new cases is to actually try it out on new cases.

A better option is to split your data into two sets: the **training set** and the **test set**. As these names imply, you train your model using the training set, and you test it usingthe test set. 

The error rate on new cases is called the **generalization error**(泛化误差), and by evaluating your model on the test set, you get an estimate of this error. This value tells you how well your model will perform on instances it has never seen before.

If the training error is low (i.e., your model makes few mistakes on the training set) but the generalization error is high, it means that your model is **overfitting** the training data.



### Hyperparameter Tuning and Model Selection

Using a test set to  evaluate a model. 

Now suppose you are hesitating(犹豫) between two models (say a linear model and a polynomial model): how can you decide? One option is to train both and compare how well they generalize using the test set.

When you you measured the generalization error **multiple times** on the test set, and you **adapted** the model and hyperparameters to produce the best model for that **particular set**. This means that the model is unlikely to perform as well on new data.

A common solution to this problem is called holdout validation: you simply hold out part of the training set to evaluate several candidate models and select the best one. The new heldout set is called the **validation set**. 

More specifically, you train multiple models with various hyperparameters on the reduced training set (i.e., the full training set minus the validation set), and you select the model that performs best on the validation set. After this holdout validation process, you train the best model on the full training set (including the validation set), and this gives you the final model. Lastly, you evaluate this final model on the test set to get an estimate of the generalization error.

If the validation set is too small, then model evaluations will be imprecise: you may end up selecting a suboptimal model by mistake. Conversely, if the validation set is too large, then the remaining training set will be much smaller than the full training set. Why is this bad? Well, since the final model will be trained on the full training set, it is not ideal to compare candidate models trained on a much smaller training set.  One way to solve this problem is to perform repeated **cross-validation**, using many small validation sets. Each model is evaluated once per validation set, after it is trained on the rest of the data. By averaging out all the evaluations of a model, we get a much more accurate measure of its performance. However, there is a drawback: the training time is multiplied by the number of validation sets.

### Data Mismatch

In some cases, it is easy to get a large amount of data for training, but it is not perfectly representative of the data that will be used in production. 