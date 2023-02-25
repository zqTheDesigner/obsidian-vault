
- Decision trees work great with the data used to create them, but they are not flexible when it comes to classifying new samples. 

- An ensemble of [[Decision Tree]] algorithms.
- Can be used for both #classification and #regression problems
- Extension of [[Bootstrap Aggregation (Bagging)]] (bagging) of decision trees. 
- Trees used in ensemble are unpruned, making them slightly overfit to the training data set. 

**Regression**: Prediction is the average prediction across the decision trees.
**Classification**: Prediction is the majority vote class label predicted across the decision threes. 

# For Time Series

- Time series data sets need to be [[Time Series Data Prepariation#Transform in to supervisied learning problem|transformed in to a supervised learning problem]]. 
- The model must be trained on the past and predict the future.
	- Methods that randomize the dataset cannot be used (like k-fold cross-validation). 
	- We must use [[Walk-forward validation]] instead

# Steps of create a Random Forest
### Make a bootstrap dataset 
- Randomly select samples from the data set, and the same sample can be selected more than once. 

### Make a decision tree using the bootstrapped dataset
- But only use a random subset of variables (columns) at each step 

# Evaluate the Random Forest's performance
- When doing bootstrap data aggregation, the data that not been selected (because there are duplicated entries) is the 'Out Of Bag Dataset'
- The proportion of Out-Of-Bag samples that were incorrectly classified is the "Out-Of-Bag Error"