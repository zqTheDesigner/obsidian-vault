- Combines the predictions from many [[Decision Tree]].
- An ensemble created from [[Decision Tree|decision trees]] fit on different samples of a data set
- Other high-variance machine learning algorithms can be used (e.g. KNN algorithm with a low k value)
- For low-variance model that already perform well. bagging can result in a decrease in model performance

# Bagging Ensemble Algorithm
- [[Bootstrap sample|Bootstrap]] Aggregation - ensemble machine learning algorithm
- Predictions are made for regression problems by averaging the prediction across the decision tree
- Predictions are made for classification problems by taking the majority vote prediction

# Bagging Extensions
- Modification and extensions to bagging algorithms in an effort to improve the performance of the approach - e.g. random forest

## Bootstrap Ensemble
> E.g. Training set [1, 2, 3, 4, 5, 6] => [1, 2, 2, 3, 5, 5], [1, 2, 3, 3, 4, 6] ??? 

## Pasting Ensemble 
- Fitting ensemble members based on random samples of the training dataset instead of bootstrap samples.
- Use smaller sample sizes than the training dataset incase where the training dataset does not fit into the memory.
- Take small pieces of the data, grow the predictor on each small piece and then paste these predictors together. 

- Set "bootstrap" argument to "False" and set the number of samples used in the training set via "max_samples" to a modest value - '0.5', 50% of the training dataset size.

> E.g. Training set : [1, 2, 3, 4, 5, 6] => [1, 3, 5, 6], [1, 3,4,5], [1,2,4,5] ...

## Random Subspaces Ensemble
- Fitting ensemble members based on datasets constructed from random subsets of the features in the training dataset
- Similar to the random forest but data samples are random rather than bootstrap 
- Subset of features selected for the entire decision tree rather than at each split point in the tree

- Set 'bootstrap' argument to 'False' and set the number of "max_features" to a modest value
> E.g. Training set's feature :['A', 'B', 'C', 'D'] => ['A', 'B', 'D'], ['A', 'C', 'D']

## Random Patches Ensemble
- Fitting ensemble members based on datasets constructed from random subset of rows (samples) and columns (features) of the training dataset
- Does not use bootstrap samples.
- Ensemble that combines both the [[Bagging Ensemble#Pasting Ensemble|Pasting ensemble]] and [[Bagging Ensemble#Random Subspaces Ensemble |Random Subspace ensembles]]
- 
