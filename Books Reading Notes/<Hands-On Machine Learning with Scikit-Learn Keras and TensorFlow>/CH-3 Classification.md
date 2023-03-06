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

# Training a Binary Classifier
- Only try to identify one digit (e.g. number 5)
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