---
tags: knn
source: https://mulberry-flat-420.notion.site/KNN-Interview-Questions-KNN-Algorithm-Questions-to-Test-Your-Skills-8e2d45f8890b4a1789b7b572a29163bd
---

KNN - K nearest neighbour 

A [[Supervised Learning]] and [[Parametric & Non-parametric Algorithms#Non-parametric algorithms|non-parametric]] algorithm. 
KNN model parameters grow with the training data by consider each training case as a parameter of the model. 

Used to solve both [[Classification]] and [[Regression]] problem statements

It uses data in which there is a target column present (labelled data) to model a function to product an output for the unseen data. 

It uses the [[Euclidean Distance]] formula to compute the distance between data points for classification or prediction. (Different articles says differently, some mentioned that other distance calculation method also can be used.)

> Main objective: Similar data points must be close to each other.

KNN algorithm known as lazy learner, it does not immediately learn a model, it just **stores** the data, and use the training data when it actually needs to do some prediction on the unseen datasets.  

KNN is sensitive to noises present in the dataset. 

KNN is the only algorithm that can be used for the imputation of both categorical and continuous variables. 

# How to choose K 
Odd value of K is preferred over even value to ensure there are no ties in the voting. 

[[Hyperparameter Tuning]]

The optimum value of K for KNN is highly dependent on the data itself. 
- **Square root method**: Take square root of the number of samples in the training dataset and assign it to K value. 
- **Cross-Validation Method**: Start with minimum value of K (1) and run cross validation, measure the [[Measure Accuracy|accuracy]] and keep repeating till result become consistent. ([[Elbow Method]])

Too small K - noise will influence the result, lead to large [[Variance|variance]] in the predictions
Too large K - model will be under-fitted, [[Bias|bias]] will increases

# How KNN predicts on the unseen dataset

- Calculate the distance of test point to all points in the training set and store them.
- Sort the calculated distances in increasing order.
- Store the k nearest points from out training dataset
- Calculate the proportions of each class.
- Assign the class with highest proportion. 

# [[Feature Scaling]]

Feature scaling is required to get the better performance of the KNN algorithm.

If a variable having a higher magnitude, the variable will be given a higher weightage during the prediction.

# Space and time complexity
O(N3 log N)
All the pairwise distances are stored and sorted in memory. 

## For large datasets
source: https://www.vldb.org/conf/2001/P421.pdf
In the above paper, 100k and 50 dimensional clustered dataset been tested. 
So assume any data that is smaller than 100k and 50 dimensional are suitable for KNN?

# Further Reading & Practices





