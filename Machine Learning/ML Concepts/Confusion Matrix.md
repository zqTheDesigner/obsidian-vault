- Rows in a Confusion Matrix Correspond to what the machine learning algorithm predicted
- Columns correspond to the known truth
- Confusion matrix is for measure [[Classification]] prediction quality
- 
![[Selection_024.png]]

- The size of the confusion matrix is determined by the number of things we want to predict
- A confusion matrix tell you what your machine learning algorithms did right and wrong


# Sensitivity
- Percentage of correctly identified positive cases. 
$$
Sensitivity = \frac{True Positives}{True Positives + False Negatives}
$$

# Specificity
- Percentage of correctly identified negative cases
$$
Specificity = \frac{True Negatives}{True Negatives + False Positives}
$$

- Both of sensitivity and specificity are metrics to identify the performance of the model (based on different cases.)