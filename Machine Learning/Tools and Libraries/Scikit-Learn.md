# Estimators
- Any object that can estimate some parameters (based on a dataset)
- Estimation it self is performed by the fit() method
- fit() method only takes a dataset as parameter, or 2 datasets for supervised learning algorithms, the second dataset contains the label
- Any other parameter needed to guide the estimation process is considered a hyperparameter, must be set as an instance variable (via constructor parameter)
```python
# Set the hyperparameter
imputer = SimpleImputer(strategy='median')

# Perform the estimation
imputer.fit(housing_num)
```

# Transformers 
- Some estimators can also transform a dataset
- The transformation is performed by the transform() method, with the dataset to transform as a parameter
- It returns the transformed dataset
- fit_transform() is equivalent to calling fit() and then transform() with the same dataset

# Predictors
- Some estimators, given a dataset, and capable of making predictions
- Use the predict() method (takes a dataset of new instances)
- Return a dataset of corresponding predictions
- use score() method to measure the quality of predictions (given a test set)

# Inspection
- All estimator's hyperparameters can be accessed via public instance variables (e.g. `imputer.strategy`)
- All estimator's learned parameters can accessed via public instance variables with an underscore suffix (e.g. `imputer.statistics_`)

# Nonproliferation of classes
- Datasets are represented as NumPy array or SciPy sparse matrices
- Hyperparameters are just regular Python strings or numbers