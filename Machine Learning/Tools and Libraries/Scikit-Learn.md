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

### Custom Transformers

```python
from sklearn.base import BaseEstimator, TransformerMixin

rooms_ix, bedrooms_ix, population_ix, households_ix = 3, 4, 5, 6

class CombineAttributesAdder(BaseEstimator, TransformerMixin):
    # hyperparameters all goes inside __init__ function
    def __init__(self, add_bedrooms_per_room = True):
        self.add_bedrooms_per_room = add_bedrooms_per_room
        
    def fit(self, X, y=None):
        return self # Nothing else to do
        
    def transform(self, X):
        rooms_per_household = X[:, rooms_ix] / X[:, households_ix]
        population_per_household = X[:, population_ix]/X[:, households_ix]
        if self.add_bedrooms_per_room:
            bedrooms_per_room = X[:, bedrooms_ix] / X[:, rooms_ix]
            return np.c_[X, rooms_per_household, population_per_household, bedrooms_per_room]
        else:
            return np.c_[X, rooms_per_household, population_per_household]
        
        
```


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