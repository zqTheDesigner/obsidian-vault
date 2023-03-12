---
tags: classification
---

- Load mnist data
```python
from sklearn.datasets import fetch_openml
mnist = fetch_openml('mnist_784', version=1)
mnist.keys()
```

- Datasets loaded by Scikit-Learn have similar dictionary structure
	- A **DESCR** key describing the dataset
	- A **data** key containing the features
	- A **target** key containing the labels 
- Plot image

```python
some_digit = X.loc[0].values
some_digit_image = some_digit.reshape(28, 28)
plt.imshow(some_digit_image, cmap='binary')
```
- Most ML algorithms expect numbers
`y = y.astype(np.uint8)`

# Training a Binary Classifier
- Only try to identify one digit (e.g. number 5)
- [[Stochastic Gradient Descent (SGD)]]
```python
# True for all 5s and false for all other digits
y_train_5 = (y_train == 5)
y_test_5 = (y_test == 5)

from sklearn.linear_model import SGDClassifier

sgd_clf = SGDClassifier(random_state=42)
sgd_clf.fit(X_train, y_train_5)

sgd_clf.predict([some_digit])

```

# Performance Measures

## Measuring Accuracy Using Cross-Validation
- Evaluate the model performance
- Customised k-fold `skfolds = StratifiedKFold(n_splits=3, random_state=42, shuffle=True)`

## [[Confusion Matrix]]
- Evaluate the performance of a classifier
- Count the number of times instances of class A are classified as class B
- Use `cross_val_predict` to get "clean" prediction of the training data
- Each row in a confusion matrix represent an *actual* class
- Each column represent a *predicted* class
- In the binary case, we can extract true positives, etc as follows:
	- `tn, fp, fn, tp = confusion_matrix([0, 1, 0, 1], [1, 1, 1, 0]).ravel()`
- sklearn's confusion matrix started with true negative
- [[Confusion Matrix#Precision|Precision]] - Accuracy of the positive predictions
$$
	precision=\frac{TP}{TP+FP}
$$
- [[Confusion Matrix#Sensitivity/Recall|Recall]] - Percentage of positive cases that are correctly detected
$$
	recall=\frac{TP}{TP+FN}
$$
```python
from sklearn.metrics import precision_score, recall_score

precision_score(y_train_5, y_train_pred)
recall_score(y_train_5, y_train_pred)
```
### $F_1$ Score
-  Combine precision and recall in to a single metric
- Harmonic mean of precision and recall (harmonic mean gives more weight to low values)
- The classifier will only get a high F1 score if both recall and precision are high
$$
F_1 = \frac2{
{\frac1{prevision} + 
{\frac1{recall}}}
} = 2\times \frac{prexision \times recall}{precision + recall}
= \frac{TP}{TP + \frac {FN + FP}2}
$$

```python
from sklearn.metrics import f1_score
f1_score(y_train_5, y_train_pred)
```

## Precision / Recall Trade-off

- For SGDClassifier, lowering the threshold increases recall and reduces precision 
```python
from sklearn.metrics import precision_recall_curve

precisions, recalls, threshold = precision_recall_curve(y_train_5, y_scores)

def plot_precision_recall_vs_threshold(precisions, recalls, threshold):
    plt.plot(threshold, precisions[:-1], 'b--', label='Precision')
    plt.plot(threshold, recalls[:-1], 'g-', label='Recall')
    
plot_precision_recall_vs_threshold(precisions, recalls, threshold)
plt.show()
```

- To find the thresholds to use for a specific precision
```python
# Get a lost of sgd_clf scores with cross val prediction (with training data)
y_scores = cross_val_predict(sgd_clf, X_train, y_train_5, cv=3, method='decision_function')

precisions, recalls, thresholds = precision_recall_curve(y_train_5, y_scores)

threshold_90_precision = thresholds[np.argmax(precisions >= 0.9)]

# Re-label the training set based on new threshold 
y_train_pred_90 = (y_scores >= threshold_90_precision)
```

# The [[ROC (Receiver Operator Characteristics)|ROC]] Curve
- Receiver Operating Characteristic Curve
- Used by binary classifiers
- Similar to precision/recall curve, plots true positive rate ([[Confusion Matrix#Recall|Recall]]) against false positive rate (FPR)
- FPR is 1 - TNR (True negative rate, [[Confusion Matrix#Specificity|Specificity]])

```python
from sklearn.metrics import roc_curve

fpr, tpr, thresholds = roc_curve(y_train_5, y_scores)

plt.plot(fpr, tpr, linewidth=2, label=['fpr', 'tpr'])
plt.plot([0,1],[0,1], 'k--')
```

- The higher recall (TPR), the more false positives (FPR) the classifier produces


## [[AUC (Area under the curve)]]

- Compare classifier's ROC
- A perfect classifier will have a ROC AUC equals to 1, a purely random classifier will have the ROC AUC equals to 0.5
```python
from sklearn.metrics import roc_auc_score
roc_auc_score(y_train_5, y_scores)
```

- Use (Precision/recall)PR curve when the positive calss is rare, or when care more about the false positives than false negatives. Otherwise use ROC curve. 
- 

### Train a RandomForestClassifier

```python
from sklearn.ensemble import RandomForestClassifier

forest_clf = RandomForestClassifier(random_state=42)

# predict_proba method will return a pair of positive, negative probability
y_probas_forest = cross_val_predict(forest_clf, X_train, y_train_5, cv=3, method='predict_proba')

# Take the positive class's probability as the score
y_score_forest = y_probas_forest[:, 1]

fpr_forest, tpr_forest, thresholds_forest = roc_curve(y_train_5, y_score_forest)

plt.plot(fpr, tpr, 'b:', label='SGD')
plt.plot(fpr_forest, tpr_forest, label='Random Forest')
plt.plot([0,1], [0,1])
plt.legend(loc='lower right')
```

# Multiclass Classification
- Multinomial Classifiers, distinguish between more than two classes
- [[Stochastic Gradient Descent (SGD)|SGD classifiers]], [[Random Forest]] classifiers and [[naive Bayes]] classifiers are capable of handling multiple classes   
- [[Logistic Regression]] or [[Support Vector Machine]] classifiers are strictly binary classifiers
- Multiclass classification can be performed with multiple binary classifiers

### OvR strategy
- One-versus-the-rest
- Train 10 binary classifiers, one for each digit, get the decision score from each classifier and the output is the highest score

### OvO strategy
- One-versus-one
- Train a binary classifier of every pair of digits. 
- If there are N classes , you need to train N x (N-1) /2 classifiers
- Only needs to be trained on the part of the training set for the two classes that it must distinguish

Scikie-Learn will automatically runs OvR or OvO depending on the algorithm

```python
from sklearn.svm import SVC

svm_clf = SVC()
svm_clf.fit(X_train, y_train)

svm_clf.predict([some_digit])

some_digit_scores = svm_clf.decision_function([some_digit])

# Show list of trained classes
svm_clf.classes_
```

- To force Scikit-Learn to use one-versus-one or one-versus-the-rest, use OneVsOneClassifier or OneVsRestClassifier
```python
from sklearn.multiclass import OneVsRestClassifier
ovr_clf = OneVsRestClassifier(SVC())
ovr_clf.fit(X_train, y_train)
```


```python
from sklearn.ensemble import RandomForestClassifier
rf_clf = RandomForestClassifier()
rf_clf.fit(X_train, y_train)

from sklearn.model_selection import cross_val_score

cross_val_score(rf_clf, X_train, y_train, cv=3, scoring='accuracy')
```

```python
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit_transform(X_train.astype(np.float64))
```

# Error Analysis

- Make predictions using the cross_val_predict()
- Then call the confusion_matrix()

```python

from sklearn.model_selection import cross_val_predict
y_train_pred = cross_val_predict(rf_clf, X_train, y_train,cv=3 )

from sklearn.metrics import confusion_matrix
conf_mx= confusion_matrix(y_train, y_train_pred)

plt.matshow(conf_mx, cmap=plt.cm.gray)
```

- If most images are on the main diagonal, the confusion matrix consider good. 

- Focus the plot on the errors
	- Divide each value in the confusion matrix by the number of images in the corresponding class
	- Compare the error rate instead of absolute number of errors

```python
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx/row_sums
# Fill the diagonal with zeros to focus only on errors
np.fill_diagonal(norm_conf_mx, 0)
plt.matshow(norm_conf_mx, cmap=plt.cm.gray)
```

- Plot digits
```python

import matplotlib
# EXTRA
def plot_digits(instances, images_per_row=10, **options):
    size = 28
    images_per_row = min(len(instances), images_per_row)
    images = [np.array(instances.iloc[i]).reshape(size, size) for i in range(instances.shape[0])]
    n_rows = (len(instances) - 1) // images_per_row + 1
    row_images = []
    n_empty = n_rows * images_per_row - len(instances)
    images.append(np.zeros((size, size * n_empty)))
    for row in range(n_rows):
        rimages = images[row * images_per_row : (row + 1) * images_per_row]
        row_images.append(np.concatenate(rimages, axis=1))
    image = np.concatenate(row_images, axis=0)
    plt.imshow(image, cmap = matplotlib.cm.binary, **options)
    plt.axis("off")
    
cl_a, cl_b = 3, 5

X_aa = X_train[(y_train == cl_a) & (y_train_pred == cl_a)]
X_ab = X_train[(y_train == cl_a) & (y_train_pred == cl_b)]
X_ba = X_train[(y_train == cl_b) & (y_train_pred == cl_a)]
X_bb = X_train[(y_train == cl_b) & (y_train_pred == cl_b)]

plt.figure(figsize=(8,8))

plt.subplot(221); plot_digits(X_aa[:25], images_per_row = 5)
plt.subplot(222); plot_digits(X_ab[:25], images_per_row = 5)


```

# Multilabel Classification
- Classification system that outputs multiple binary tags. 
- KNeighborsClassifier() supports multi label classification
``` python
from sklearn.neighbors import KNeighborsClassifier
knn_clf = KNeighborsClassifier()
knn_clf.fit(X_train, y_multilabel)

knn_clf.predict([some_digit])
```
- To evaluate a multilabel classifier
```python
from sklearn.model_selection import cross_val_predict
from sklearn.metrics import f1_score

y_train_knn_pred = cross_val_predict(knn_clf, X_train, y_multilabel, cv=3)
f1_score(y_multilabel, y_train_knn_pred, average='macro')
```

# Multioutput Classification
- A generalization of multilabel classification where each label can be multi class (have more than two possible values)
- 