- How much error the system typically makes in its predictions. 

$$ 
 RMSE(X,h) 
 =
 \sqrt { 
 \frac1m
 \sum_{i=1}^m
 (h(X^{(i)})-y^{(i)})^2
 } 
$$


- m is the number of instances in the dataset you are measuring the RMSE on
- $x^{(i)}$ is a vector of all the feature values (excluding the label) if the $i^{th}$ instance in the dataset, and $y^{(i)}$ is the label
- X is the matrix containing all the feature values of all instances in the dataset. $i^{th}$ row is equals to the transpose of $(X^{(i)})^T$ 
- h is the system's prediction function - hypothesis. $\hat{y}^{(i)} = h(x^{(i)})$ 

- RMSE corresponds to [[Euclidean Distance|Euclidean]] norm 
