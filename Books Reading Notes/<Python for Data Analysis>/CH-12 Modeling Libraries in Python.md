---
tags: statsmodels, scikit-learn
---

# 12.1 Interfacing between pandas and Model Code

A common workflow for model development is to use pandas for data loading and cleaning before switching over to a modelling library to buildthe model itself. 

Feature engineering
Any data transformation or analytics that extract information from a raw data set that may be useful in a modelling context. 
Data aggregation and GroupBy tools are often used in feature engineering context. 

```python
# Turn a DataFrame to a NumPy array
data = pd.DataFrame({
	'x0': [1, 2, 3, 4, 5],
	'x1': [0.01, -0.01, 0.25, -4.1, 0.],
	'y': [-1.5, 0., 3.6, 1.3, -2.]
})

data.to_numpy()

# To convert dataframe back to NumPy Array
df2 = pd.DataFrame(data.to_numpy(), columns=['one', 'two', 'three'])

df2

# heterpgeneous data will be converted an ndarray of Python Objects
df3 = data.copy()
df3['strings'] = ['a', 'b', 'c', 'd', 'e']

df3.to_numpy()

# to select subset of columns, use loc
model_cols = ['x0', 'x1']

data.loc[:, model_cols].to_numpy()

# Create a categorical column
data['category'] = pd.Categorical(['a', 'b', 'a', 'a', 'b'], categories=['a', 'b'])

# Replace the 'category' column with dummy variables
dummies = pd.get_dummies(data.category, prefix='category')

data_with_dummies = data.drop('category', axis=1).join(dummies)

data_with_dummies
```


# 12.2 Creating Model Descriptions with Patsy

A python library for describing statistical models (especially linear models) with a string-based 'formula syntax'.

The description here is to describe the [[formula-based models]] like linear regression or logistic regression. Other models like decision tree or neural networks are not represented by a single formula. 

`y ~ x0 + x1`

a + b means terms in the design matrix created for the model

patsy.dematrices function takes a formula string along with a dataset, produce design matrices for a linear model

```python
data = pd.DataFrame({
	'x0': [1, 2, 3, 4, 5],
	'x1': [0.01, -0.01, 0.25, -4.1, 0],
	'y': [-1.5, 0., 3.6, 1.3, -2.]
})

data
y, X = patsy.dmatrices('y ~ x0 + x1', data)

y
```

Patsy DesignMatrix instance are NumPy ndarrays with additional metadata

`y = b + mx0 + mx1` is similar to `y ~ x0 + x1`, the difference is in `y = b + mx0 + mx1` , the coefficients b, m0 and m1 are exlicitly shown, while the `y ~ x0 + x1`, the coefficients are estimated by the fitting algorithm. 

The Patsy objects can be passed directly in to algorithms like numpy.linalg.lstsq

```python
coef, resid, _, _ = np.linalg.lstsq(X, y)

# The model metadata is retained in the design_info attribute. Can reattach the model column names to the fitted coefficients to obtain a Series
coef = pd.Series(coef.squeeze(), index=X.design_info.column_names)
```

## Data Transformations in Patsy Formulas

Mix Python code into Patsy formulas, the library will try to find the functions in the enclosing scope

```python
y, X = patsy.dmatrices('y ~ x0 + np.log(np.abs(x1) + 1)', data)
```

Commonly used variable transformations include standardising (to mean 0 and variance 1) and Centering (subtracting the mean)

```python
y, X = patsy.dmatrices('y ~ standardize(x0) + center(x1)', data)

X
```

## Categorical Data and Patsy
When you use non-numeric terms in a Patsy formula, they are converted to dummy variables by default. 

```python
data = pd.DataFrame({
	'key1': ['a', 'b', 'b', 'b', 'a', 'b', 'a', 'b'],
	'key2': [0, 1, 0, 1,0, 1, 0, 0],
	'v1' : [1, 2, 3, 4, 5, 6, 7, 8],
	'v2': [-1., 0, 2.5, -0.5, 4.0, -1.2, 0.2, -1.7]
})

y,X = patsy.dmatrices('v2 ~ key1', data)

# If omit the intercept from the model, columns for each category value will be included in the model design matrix
# the +0 part will omit the intercept
y, X = patsy.dmatrices('v2 ~ key1 + 0', data)
X

# Numeric columns can be interpreted as categorical with the C function
y, X = patsy.dmatrices('v2 ~ C(key2)', data)

X

# When using multiple categorical terms in a model 

data['key2'] = data['key2'].map({0: 'zero', 1:'one'})

data

y, X = patsy.dmatrices('v2 ~ key1 + key2', data)

y, X = patsy.dmatrices('v2 ~ key1 + key2 + key1:key2', data)
```

# 12.3 Introduction to statsmodels
#statsmodels

statsmodels is a Python library for fitting many kinds of statistical models, performing statistical tests and data exploration and visualisation.

Some kinds of models found in statsmodels:
- Linear models, generalised linear models and robust linear models
- Linear mixed effects models
- Analysis of variance (ANOVA) methods
- Time series processes and state space models
- Generalised methods of moments

Use the modeling interfaces with Patsy formulas and pandas DataFrame objects

## Estimating Linear Models

```python
import statsmodels.api as sm
import statsmodels.formula.api as smf

```

## Estimating Linear Models

```python
# To make the example reproducible
rng = np.random.default_rng(seed=12345)

# helper function for generating normally distributed data with a particular mean and variance
def dnorm(mean, variance, size= 1):
	if isinstance(size, int):
		size = size,
	return mean + np.sqrt(variance) * rng.standard_normal(*size)

N = 100

X = np.c_[
	dnorm(0, 0.4, size=N),
	dnorm(0, 0.6, size=N),
	dnorm(0, 0.2, size=N)
]

eps = dnorm(0, 0.1, size=N)

# 'true' model
beta = [0.1, 0.3, 0.5]

y = np.dot(X, beta) + eps


# A linear model is generally fitted with an intercept term
# use sm.add_constant function to add an intercept column
X_model = sm.add_constant(X)
X_model[:5]

# fit an ordinary least square linear regression
model = sm.OLS(y, X)
results = model.fit()

# Print a model detailing dignostic output of the model
print(results.summary())

data = pd.DataFrame(X, columns=['col0', 'col1', 'col2'])

data['y'] = y

results = smf.ols('y ~ col0 + col1 + col2', data=data).fit()

results.params

# Compute the predicted values given the estimated model parameters
results.predict(data[:5])
```

## Estimating Time Series Processes

Autoregressive processes, Kalman filtering, other state space models and multivariable autoregressive model. 

```python
# Simulate some time series data with an autoregressive structure and noise
# This data has an AR(2) structure (two lags) with parameters 0.8 and -0.4. 

init_x = 4

values = [init_x, init_x]

N = 1000

b0 = 0.8
b1 = -0.4
noise = dnorm(0, 0.1, N)

for i in range(N):
	new_x = values[-1] * b0 + values[-2] * b1 + noise[i]
	values.append(new_x)

# Fit the model with some larger number of lags
from statsmodels.tsa.ar_model import AutoReg

MAXLAGS = 5
model = AutoReg(values, MAXLAGS)

results = model.fit()

results.params
```

# 12.4 Introduction to scikit-learn
#scikit-learn #cross-validation

General-purpose Python machine learning toolkits. 

```python
train = pd.read_csv('./datasets/titanic/train.csv')
test = pd.read_csv('./datasets/titanic/test.csv')

train.head(4)

# Libraries like statsmodels and scikit-learn generally cannot be fed missing data. 
# Look at the columns to see if there are ny that contain missing data.
train.isna().sum()

test.isna().sum()

# A model is fitted on a training dataset and then evaluated on an out-of-sample testing dataset
# Use the median of the training data set to fill the nulls in both tables

impute_value = train['Age'].median()

train['Age'] = train['Age'].fillna(impute_value)

test['Age'] = test['Age'].fillna(impute_value)


# Add a column IsFemale to encode the 'Sex' column
train['IsFemale'] = (train['Sex'] == 'female').astype(int)
test['IsFemale'] = (test['Sex'] == 'female').astype(int)

predictors = ['Pclass', 'IsFemale', 'Age']

X_train = train[predictors].to_numpy()
X_test = test[predictors].to_numpy()

y_train = train['Survived'].to_numpy()

X_train[:5]

from sklearn.linear_model import LogisticRegression

model = LogisticRegression()

model.fit(X_train, y_train)

y_predict = model.predict(X_test)

y_predict[:10]

# Compute the accuracy percentage 
# (y_true == y_predict).mean()

```

## #cross-validation 

```python
# Cross validation
# Some models have build-in cross-validation

from sklearn.linear_model import LogisticRegressionCV

model_cv = LogisticRegressionCV(Cs=10)
model_cv.fit(X_train, y_train)

y_predict = model_cv.predict(X_test)

y_predict[:10]

# To do cross validation by hand, use cross_val_score helper function

from sklearn.model_selection import cross_val_score

model = LogisticRegression(C=10)

scores = cross_val_score(model, X_train, y_train, cv=4)

scores
```