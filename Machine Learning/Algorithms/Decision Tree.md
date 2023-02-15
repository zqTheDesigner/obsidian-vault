---
tags: classification, decision-tree
---

A decision tree makes a **statement**, and then makes a decision based on whether or not the statement is True or False (True will go left, False will go right)

Different data types can be used in the same tree (e.g. numeric and boolean). Same questions can be asked multiple times with different numeric thresholds. 

Final classification can be repeated. 

Numeric thresholds (If the value it bigger or small than certain value, split to group a, else split to group b) can be different for the same data. 

# Classification Tree
When decision tree classifies things into categories

When a leave has the impurity of 0 (all data contains the same label), we won't split the leaf any more. And the output value can be assigned to the leave (the category has the most values.)

# Regression Tree
When a decision tree predicts numeric values (E.g. Between x and y value, larger than x or below y)
- Start from the average value between first and 2nd value, on the left, there is only 1 value (1st value), so the average is the value itself
- On the right, there are all the rest of values (except the 1st value), we calculate the average.
- On the right, the residual (value between predicted value and actual value) is higher
- We calculate the squared residual on each average value between 2 points
- And split the data into two groups by finding the threshold that gave us the smallest sum of squared residuals.
- For multivariable prediction, each feature's residual will be calculated and compared, the feature with lowest residual will be choose as the root node. 

# Impurity of a node
If the leave contains more than one value, it is impure.

Quantify the impurity of leaves to identify which feature can do a better prediction job.

Leave Gini impurity = 1 - (probability of "Yes") ** 2 - (probability of "No") ** 2

Total Gini Impurity = Weighted average of Gini impurities for the leaves

Even a leaf is impure, still need an output value - the majority of the leaf.

## Gini impurity for numeric data
- Sort the data by ascending order
- Calculate the average value between each sibling values (so we can always clearly draw the line to split the numeric data)
- For each average value, use it as the statement to split the leaves, and calculate the Gini Impurity for each average value
- If two value are tied, can pick up either one 

![[Selection_001.png]]

# Prevent [[Overfit| overfitting]]
If there is only very little data made to a leaf, it is hard to have confidence that it will do a great job on making the predictions. 
Decision trees have the downside of often being overfit. 

Require each split to make a large reduction in impurity helps a tree from being over fit. 

Create a threshold such that the impurity reduction has to be large enough to make a big difference. 

## Prune

## Put limits on how trees grow
E.g. by request 3 or more people per leaf. 
Use [[Cross Validation]] to decide which value of limits works the best. 

# Feature Selection
When splitting each node, the impurity with not splitting and splitting will always be compared (The reduction of impurity when splitting), if the impurity of not splitting is lower, then the feature will not be used in this node to split the leaves (so the leaf will become the Yes or No branch of the node). 

If a feature doesn't used in any branch, it will be automatically ignored - automatic feature selection.

# Missing data
- Use the most common value for categorical data
- Find the highest correlation feature and refer to the high correlation feature
- Replace value with mean or median
- Find the highest correlation feature, and do a linear regression