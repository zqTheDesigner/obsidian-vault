- The ROC graph summarizes all of the confusion matrices that each threshold produced

- Y-axis shows the true positive rate ([[Confusion Matrix#Sensitivity/Recall|Sensitivity]])
- X-axis shows the false positive rate ([[Confusion Matrix#Specificity|Specificity]])
$$
{FalsePositiveRate} = (1 - Specificity) = \frac{FalsePositives}{FalsePositives + TrueNegatives}
$$

- Point 1,1 means even we correctly captured ALL positive cases, we also incorrectly captured all negative cases (Because everything we captured as positive)

- [[AUC (Area under the curve)]] Makes it easier to compare one ROC curve with another ROC curve