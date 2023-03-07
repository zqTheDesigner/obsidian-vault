- Rows in a Confusion Matrix Correspond to what the machine learning algorithm predicted
- Columns correspond to the known truth
- Confusion matrix is for measure [[Classification]] prediction quality
- 
![[Selection_024.png]]

- The size of the confusion matrix is determined by the number of things we want to predict
- A confusion matrix tell you what your machine learning algorithms did right and wrong

![[Pasted image 20230307160013.png]]


# Sensitivity/Recall
- Percentage of positive cases that are correctly detected 
- Works together with precision
- (Horizontally compare)
- TP + FN is the over all data for this particular label
- "it detect {recall score} percent of the {label}"
- **Higher recall means will capture more positive cases, also means more negative cases may captures as positive (宁滥勿缺)**

> For example, if a classifier is to detect faulty product, high recall means more faulty product will be detected, however, the chance of a good product been detected as faulty also higher. 

> If there is 10 faulty product out of 100 product, 100% recall means identified n (n >= 10) faulty products, and all 10 actual faulty product been captured with in the n captured faulty products
$$
Sensitivity = \frac{True Positives}{True Positives + False Negatives}
$$

# Specificity
- Percentage of correctly identified negative cases
$$
Specificity = \frac{True Negatives}{True Negatives + False Positives}
$$

- Both of sensitivity and specificity are metrics to identify the performance of the model (based on different cases.)

# Precision
- Accuracy of the positive predictions
- TP + FP = Total Positive (Source of truth)
- (Vertically compare)
- **Higher precision means less negative cases will be falsely predict as positive case, however, also means some positive cases may be predicted as negative cases. (宁缺勿滥)**

- Precision does not include the number of True Negatives in it's calculation, and it is not affected by the imbalance

> For example, if a classifier to identify faulty product, higher precision means while capturing most if not all faulty products, the amount of good product been classified as faulty product is lower. 

> If out of 100 product, 10 product are actual faulty, n been classified by the model as faulty product, a higher precision means with in the n identified fault product, the percentage of actual faulty product is high. But probably not all 10 faulty product been captured with in n, those missed product will lower down the recall value. 
$$
precision=\frac{TP}{TP+FP}
$$