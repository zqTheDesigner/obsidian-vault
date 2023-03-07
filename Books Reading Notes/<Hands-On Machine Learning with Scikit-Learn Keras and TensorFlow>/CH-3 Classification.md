c---
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