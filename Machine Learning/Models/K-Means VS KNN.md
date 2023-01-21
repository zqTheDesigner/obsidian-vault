## What's KNN imputation? 

KNN imputation will find the K amount of nearest neighbours from the dataset based on the observed features (the missing feature will be ignored first) and fill up the missing feature by calculating the mean value of the nearest K neighbours.

Here, we talking about "nearest" distance by calculating the [[Euclidean Distance]] between the data points. (Other ways of distance calculation may be used, but not as popular as Euclidean Distance)

**For example**: we have feature A, B, C in the dataset, and one of the row missed feature C,
We'll first find the K neighbours based on observed feature A and B, then use the mean value of the observed C to calculated the missed C. 

KNN imputation and KNN classification are different. 
- KNN classification uses the features to predict the labels. 
- KNN imputation uses the observed features to compute the missed feature

Because KNN is based on the distance between data points, outliers will affect the result a lot, data normalisation is important. (I think sklearn don't normalise the data automatically, the normalisation is a necessary step found from a few tutorials.)

## What is K-Means imputation?

What's the difference between K-means and KNN?