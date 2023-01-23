---
tags: iterative-imputation
source: https://machinelearningmastery.com/iterative-imputation-for-missing-values-in-machine-learning/
---
# What's iterative [[Missing Data Imputation | Imputation]]? 
Defining a model to predict each missing feature as a function of all other features  and to repeat this process of estimating feature values multiple times. 
	**e.g:**
	1. Provided feature A, B, C and D is missed, an arbitrary value will be assigned to D (e.g. the mean of non-missing values for feature D in other rows of the data set). 
	2. Then use feature D to estimate the relationships between feature A, B, C and D, we'll have `f(A, B, C) = D`
	3. Use the `f()` to calculate the missed feature D, replace the value in step 1, now we have a full set of feature D where the value is more accurate than mean.
	4. Repeat between step 2 and 3 till converged to a stable solution, or reached the maximum iteration count.  

Use the refined estimated values for other features to be used as input in subsequent iterations of predicting missing values. 

Iterative imputation refers to a process where each feature is modelled as a function for the other features.

Different [[Regression Algorithms]] can be used to estimate the missing values for each feature. ([[Regression Algorithms#Linear Regression|Linear methods]] are often used for simplicity) 


# What does "iterative" mean here?
The process is repeated multiple times, allowing ever improved estimates of missing values to be calculated as missing values across all features are estimated. 
	e.g: If on another row the value A is missed instead, we'll have `f2(B, C, D) = A`

This approach may be generally referred as fully conditional specification (FSC) or [[Missing Data Imputation#Multivariate Imputation by Chained Equations (MCIE)|multivariate imputation by chained equations (MCIE)]]

