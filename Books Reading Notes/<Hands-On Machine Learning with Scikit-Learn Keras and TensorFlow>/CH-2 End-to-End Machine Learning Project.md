- Look at the big picture
- Get the data
- Discover and visualize the data to gain insights
- Prepare the data for Machine Learning algorithms
- Select a model and train it
- Fine-tune your model
- Present your solution
- Launch, monitor, and maintain the system

# Working with Real Data
Places to get data:
- UC Irvine Machine Learning Repository
- Kaggle datasets
- Amazon's AWS datasets
- Data Portals (dataportals.org)
- OpenDataMonitor
- Quandl
- Wikipedia's list of Machine Learning datasets
- Quora.com
- Datasets subreddit

# Look at the Big Picture
[[Machine Learning Project Checklist]]

## Frame the Problem
- What exactly the business objective is? 
- How does the company use and benefit from this model?
- [[Data Pipeline]]

## Select a performance measure
- [[Root Mean Square Error]] (RMSE)
- [[Mean Absolute Error]] (MAE)
- The higher the norm index, the more it focus on large vales and neglect small ones
- RMSE is more sensitive to outliers than MAE (To test)

## Check Assumptions
- List and verify the assumptions that have been made
- E.g. after the price been calculated, will it be feed in to another algorithm as classes or real numbers?


# Get the Data

## Download the data

```python
DOWNLOAD_ROOT = "https://raw.githubusercontent.com/ageron/handson-ml2/master/"
HOUSING_PATH = os.path.join('datasets', 'housing')
HOUSING_URL =  DOWNLOAD_ROOT + 'datasets/housing/housing.tgz'

def fetch_housing_data(housing_url=HOUSING_URL, housing_path=HOUSING_PATH):
	os.makedirs(housing_path, exist_ok=True)
	tgz_path = os.path.join(housing_path, 'housing.tgz')
	urllib.request.urlretrieve(housing_url, tgz_path)
	housing_tgz = tarfile.open(tgz_path)
	housing_tgz.extractall(path=housing_path)
	housing_tgz.close()

def load_housing_data(housing_path=HOUSING_PATH):
	csv_path = os.path.join(housing_path, "housing.csv")
	return pd.read_csv(csv_path)
```

## Take a quick look at the Data Structure

```python
housing = load_housing_data()

housing.head()

housing.info()
```

- Check the amount of entries (rows), columns, and memory usage
- Check the amount of missing values
- Check data types
- Object data type can be any python object, but if it is from csv, it will be text
- Find any categorical attribute
	- `housing['ocean_proximity'].value_counts()`
- Use `describe()` to check the summary of other numerical attributes
	- count, mean, min, max, [[Standard deviation]], [[Percentiles]]
- Plot a [[Histogram]] for each numerical attribute. 
	- `housing.hist(bin=50, figsize=(20, 15))`
	- Check the unit of each feature
	- Check if any feature may be capped (appeared as a very long bar at end of the histogram)
	- Check the scales of the feature 
	- Many histograms are tail-heavy (extended much farther to the right of the median than to the left), transforming to a more bell-shaped distributions will be better for Machine Learning algorithms

## Create a Test Set
- Use crc32 to make sure the same set of test value will be split out even run the function multiple times. 
- Use `train_test_split` from `sklearn.model_selection` with a fixed value of random state (e.g. 42), this will make sure each time run the function the  same test set been split out
#### [[Stratified Sampling]]
- The population is divided into homogeneous subgroups - strata, and the right number of instances are sampled from each stratum to guarantee that the test set is representative of the overall population.
- Make sure the test set is representative of the most important feature
- Then do stratified sampling based on the important category
``` Python
from sklearn.model_selection import StratifiedShuffleSplit

split = StratifiedShuffleSplit(n_splits=1, test_size=0.2, random_state=42)

for train_index, test_index in split.split(housing, housing['income_cat']):
    strat_train_set = housing.loc[train_index]
    strat_test_set = housing.loc[test_index]
    
strat_test_set['income_cat'].value_counts() / len(strat_test_set)
```

- Remove any additional attribute in the data to keep its original state
```python
for set_ in (strat_test_set, strat_train_set):
    set_.drop("income_cat", axis=1, inplace=True)
```

# Discover and Visualize the Data
- To gain insights
- Make sure you have put the test set aside and you are only exploring the training set

## Visualizing Geographical data
```python
housing.plot(kind='scatter', x='longitude', y='latitude', alpha=0.1)
```

``` python
housing.plot(kind='scatter', x='longitude', y='latitude', alpha=0.4, s=housing['population']/100, label='population', figsize=(10, 7), c='median_house_value', cmap=plt.get_cmap('jet'), colorbar=True)
```
- c is the color axis
- cmap - color map

- If the data have a clear density related to the features, a cluster algorithm can be used

## Looking for [[Feature Correlations|Correlations]]

```python
corr_matrix = housing.corr(numeric_only=True)
corr_matrix['median_house_value'].sort_values(ascending=False)
```

- The [[Feature Correlations|correlation coefficient]] only measures linear correlations, it may completely miss out on nonlinear relationships

- Use `scatter_matrix()` to check the correlations between attributes (Plot every numerical value against every other numerical value)

```python
from pandas.plotting import scatter_matrix

attributes = ["median_house_value", "median_income", "total_rooms", "housing_median_age"]

scatter_matrix(housing[attributes], figsize=(12, 8))
```

![[Pasted image 20230226102229.png]]
- To read the above scatter_matrix
	- Identify the trend (median_income and median_house_value has a very strong correlation, because the linear trends)
	- Identify some data quirks (e.g. the horizontal like on 450, 000 and 350, 000), these may need to be removed later to prevent algorithms learning form these.
	- Identify the cap of 500, 000
	- Identify the tail heavy trends, may want to transform them by computing their logarithm
```python
housing.plot(kind='scatter', x='median_income', y='median_house_value', alpha=0.06)
```

## Experimenting with Attribute Combinations
- E.g. the total number of rooms in a district is not very useful if you don't know how many households there are. What you really want is the number of rooms per household. 
- e.g. population per house or bedrooms compare with total rooms
```python
# Experimenting with attribute combinations
housing['rooms_per_household'] = housing['total_rooms']/housing['households']
housing['bedrooms_per_room'] = housing['total_bedrooms'] / housing['total_rooms']
housing['population_per_household'] = housing['population']/housing['households']
```

# Prepare the Data for Machine Learning Algorithms
- Write functions for this step
	- To reproduce the same transformation on any other dataset
	- Build a library of transformation functions that can be reused in future projects
	- Use these functions in live system to transform the new data before feeding in to algorithms

## Data Cleaning
- Handling missing value
	- Get rid of the corresponding districts `df.dropna(suibset=['feature_name'])`
	- Get rid of the whole attribute `df.drop('feature_column', axis=1)`
	- Set the value to some value
```python
median = housing['total_bedrooms'].median()
housing['total_bedrooms'].fillna(median, inplace=True)

# Use scikit learn Simple imputer
from sklearn.impute import SimpleImputer
imputer = SimpleImputer(strategy='median')

housing_num = housing.drop('ocean_proximity', axis=1)
imputer.fit(housing_num)
```
[[Scikit-Learn| Scikit-Learn Design]]

## Handing Text and Categorical Attributes
- To convert categorical value to numbers , use Scikit-Learn's OrdinalEncoder class
```python
from sklearn.preprocessing import OrdinalEncoder
ordinal_encoder = OrdinalEncoder()

housing_cat_encoded = ordinal_encoder.fit_transform(housing_cat)

housing_cat_encoded[:10]
```
- To get a list of categories using the categories_ instance
```python
ordinal_encoder.categories_
```

- One-hot encoding
```python
from sklearn.preprocessing import OneHotEncoder

cat_encoder = OneHotEncoder()

housing_cat_1hot = cat_encoder.fit_transform(housing_cat)
housing_cat_1hot
```
- `OneHotEncoder`'s categories can be found using (categories_)
```python
cat_encoder.categories_
```
- Output of OneHotEncoder is SciPy sparse matrix instead of a NumPy array.
```python
# To convert SciPy sparse to list
housing_cat_1hot.toarray()
```

## Custom Transformers 
#scikit-learn 

[[Scikit-Learn#Custom Transformers]]

- Make the transformer work seamlessly with Scikit-Learn functions
- Create a class and implement fit (retruning self), transform() 
- Adding TransformerMixin as base class to enable fit_transform method
- Add BaseEstimater to avoid `*args` and `**kwargs`
- BaseEstimater will provide two extra methods - `get_params()`, `set_params()`, useful for automatic hyperparameter tuning
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

## Feature Scaling
#feature-scaling

- One of the most important transformations you need to apply to your data
- Machine Learning Algorithms don't perform well when the input numerical attributes have very different scales (with exceptions)
- Scaling the target values is generally not required

- Fit the scales to the training data only. not the full dataset
- Then use them to transform the training set and the test set

#### [[Min-max scaling (Normalization)]]
- Values are shifted and re-scaled so that they end up ranging from 0 to 1
- Subtracting the min value and dividing by the max minus min
- Use Scikit-Learn's `MinMaxScaler` for this

#### [[Standardization]]
- Subtracts the mean value
- Divided by the standard deviation so that the resulting distribution has unit variance
- Does not bound values to specific range.
- Less affected by outliers
- Use Scikit-Learn's `StandardScaler` for standardization

## Transformation Pipelines
- Use Scikit-Learn's Pipeline class for sequences of transformations
```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

num_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='median')),
    ('attribs_adder', CombineAttributesAdder()),
    ('std_scaler', StandardScaler())
])

housing_num_tr = num_pipeline.fit_transform(housing_num)
```

- Pipeline constructor takes a list of name/estimator pairs defining a sequence of steps
- Except the last estimator, all must be transformers (they must have a fit_transform() method)
- When call `Pipeline.fit()`, it calls fit_transform sequentially on all transformers till reach the final estimator, will call the fit() method of the final estimator. 
- Use `ColumnTransformer` class to handle categorical columns and numerical columns separaterly.
```python
from sklearn.compose import ColumnTransformer

num_attribs = list(housing_num)
cat_attribs = ['ocean_proximity']

full_pipeline = ColumnTransformer([
    ('num', num_pipeline, num_attribs),
    ('cat', OneHotEncoder(), cat_attribs)
])

housing_prepared = full_pipeline.fit_transform(housing)
```
- `OneHotEncoder` returns a [[sparse matrix]]
- `num_pipeline` returns a [[dense matrix]]
- ColumnTransformer estimates the density of the final matrix (e.g. the ratio of none-zero cells), if the density is lower than a given threshold (default 0.3) it returns a dense matrix

# Select and Train a model

## Training and evaluating on the Training set
