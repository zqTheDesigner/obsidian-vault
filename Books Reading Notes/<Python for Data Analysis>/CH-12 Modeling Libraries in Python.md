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

