An ensemble of decision tree algorithms.

Can be used for both #classification and #regression problems

Extension of [[Bootstrap Aggregation (Bagging)]] (bagging) of decision trees. 

Trees used in ensemble are unpruned, making them slightly overfit to the training data set. 

**Regression**: Prediction is the average prediction across the decision trees.
**Classification**: Prediction is the majority vote class label predicted across the decision threes. 

# For Time Series

Time series data sets need to be [[Time Series Data Preparation#Transform in to supervisied learning problem|transformed in to a supervised learning problem]]. 

