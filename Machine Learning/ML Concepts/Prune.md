- Prevent overfitting with the training data, so the tree can do a better job with a testing data
- 

# Prune Regression Tree

## Cost Complexity Pruning (Weakest Link Pruning)

- Remove some of the leaves and replace with the average number of a larger observations 
- Calculate the Sum or Squared [[Residuals]] (SSR) for all the observations
- Each time remove a leaf, the Sum of Squared Residuals will gets larger, this is because the whole idea was for the pruned trees to NOT fit the Training Data as well as the full sized tree

### Tree Score
- Tree Score = SSR + $\alpha T$ 
- $\alpha T$ is a function of the number of leaves (Terminal nodes) in the tree or sub-tree
- $\alpha T$ - Tree Complexity Penalty, The more leaves, the larger the penalty
- $\alpha$ is a tuning parameter that we finding using Cross Validation
- T is the total number of leaves
- Pick the sub tree with the lowest tree score

### Find the best value for $\alpha$
- Use all data to build a full sized tree (with both training data and testing data)
- 