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
- Fine any categorical attribute
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
