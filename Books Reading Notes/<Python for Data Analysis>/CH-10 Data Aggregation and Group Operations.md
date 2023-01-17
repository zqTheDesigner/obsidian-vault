---
tags: data-aggregation data-group-operations
---

Categorizing a dataset and applying a function to each group. 

After lading, merging and preparing a dataset, you may need to compute statistics or possibly picot tables for reproting or visualization purpose. 

Use pandas `groupby` interface to slice, dice and summarize datasets

- Split a pandas object into pieces using one or more keys
- Calculate group summary statistics like count, mean or standard deviation
- Apply within-group transformation or other manipulation like normalization, linear regression, rand or subset selection
- Compute pivot tables and cross-tabulations
- Perform quantile analysis and other statistical group analyses

# 10.1 How to think about Group Operations
#pandas/groupby
"split-apply-combine" - group operations
1. Data containes in a pandas object split into groups based on one or more keys that you provide, the splitting is performed on a particular axis of an object.
2. A function applied to each group producting a new value. 
3. Finally, the results of all those function applications are combined into a result object. 

Each grouping key can take many forms, and they keu do not have to be all the same type. 


```python
df = pd.DataFrame(
    {
        "key1": ["a", "a", None, "b", "b", "a", None],
        "key2": pd.Series([1, 2, 1, 2, 1, None, 1], dtype="Int64"),
        "data1": np.random.standard_normal(7),
        "data2": np.random.standard_normal(7),
    }
)

# Compute the mean of data1 columns using the labels from key1
# Will return the mean value of each group in "key1" (same key1 will be consider as 1 group)
grouped = df['data1'].groupby(df['key1'])
grouped.mean()


```
`GroupBy` object may looks like a DataFrame, but it is already grouped by the provided group key

The data has been aggregated by splitting the data on the group key, producing a new Series that is now indexed by the unique value in the key 1 column. 

To get the means by 2 different keys 
(The na value in both keys will be dropped)
Returns a DataFrame with hierarchical index consisting of the unique pairs of keys observed
```python
means = df['data1'].groupby([df['key1'], df['key2']]).mean()
means.unstack()
```


```python
states = np.array(['OH', "CA", "CA", "OH", "OH", "CA", "OH"])

years = [2005, 2005, 2006, 2005, 2006, 2005, 2006]

# group keys can be any array of the right length.
df['data1'].groupby([states, years]).mean().unstack()
```

By default, all of the numeric columns are aggregated. 

```python
# Pass column names to use the column as the group keys

df.groupby('key1').mean()

df.groupby(['key1', 'key2']).mean().unstack()

```

Use `GroupBy.size` method to return a Series containing group sizes. Any missing values in a group key are excluded from the result by default. This hebavior can be disabled by passing `dropna=False` 

```python
df.groupby(['key1', 'key2'], dropna=False).size().unstack()
```

**Count**: Will count how many data in each columns belongs to the key group. Count is more to get the relationship between key group and other groups 

**Size**: How many rows belongs to each key group, size is to get the information about the key grout itself. 

```python
df.groupby('key1').count()
df.groupby('key1', dropna=False).size()
```

## Iterating over Groups
#pandas/groupby/iterating

The object returned by groupby supposts iteration, generating a sequence of 2-tuples containing the group name along with the chunk of data.

```python
for name, group in df.groupby('key1'):
	print(name)
	print(group)

# In the case of multiple keys, the first element in the tuple will be a tuple of key values
for (k1, k2), group in df.groupby(['key1', 'key2']):
	print((k1, k2))
	print(group)
```


```python

# Computing a dictionary of data pieces as a one-linear
pieces = {name: group for name, group in df.groupby("key1")}

pieces['b']
```

group on any other axes 

Group df by whether they start with 'key' or 'data'

```python
grouped = df.groupby(
    {"key1": "key", "key2": "key", "data1": "data", "data2": "data"}, axis="columns"
)

for group_key, group_val in grouped:
	print(group_key)
	print(group_val)

# Similar as. But the below functions seems returning copy of the df instead of view of the df 
df[['data1','data2']]
df[['key1', 'key2']]
```

## Selecting a Column or Subset of Columns

Indexing a GroupBy object created from a DataFrame with a column name or array of column names

```python
df.groupby('key1')['data1']
# Same as 
df['data1'].groupby(df['key1'])

# To aggregate only a few columns
# To only compute means for the data 2 column
df.groupby(['key1', 'key2'])[['data2']].mean()
```

## Grouping with Dictionaries and Series

Use a dictionary to re-group the DataFrame
```python
people = pd.DataFrame(
    np.random.standard_normal((5, 5)),
    columns=["a", "b", "c", "d", "e"],
    index=["Joe", "Steve", "Wanda", "Jill", "Trey"],
)

people.iloc[2:3, [1,2]] = np.nan
mapping = {"a": "red", "b": "red", "c": "blue", "d": "blue", "e": "red", "f": "orange"}

by_column = people.groupby(mapping, axis="columns")

by_column.sum()

# Group by pandas.Series
map_series = pd.Series(mapping)
people.groupby(map_series, axis="columns").count()
```


## Grouping with Functions
Any function passed as a group key will be called once per index value, with the return values being used as the group names. 

```python
people.groupby(len).sum()
```

## Grouping by Index Levels
Aggregate using one of the levles of an axis index. 


```python
columns = pd.MultiIndex.from_arrays(
    [["US", "US", "US", "JP", "JP"], [1, 3, 5, 1, 3]], names=["city", "tenor"]
)

hier_df = pd.DataFrame(np.random.standard_normal((4, 5)), columns=columns)

hier_df

# To group by level, pass the level number or name using level keyword
hier_df.groupby(level="city", axis='columns').count()
```

# 10.2 Data Aggregation

Aggregation refer to any data transformation that produces scalar values form arrays. 

Optimized groupby methods

| Function Name | Description |
| - | - |
| any, all | return True is any (one or more values) or all none-Na values are "truthy" | 
| count | Number of non-NA values | 
| cumin, cummax | Cumulative minimum and maximum of no-NA values | 
| cumsum | Cumulative sum of non-NA values |
| cumprod | Cumulative product of non-NA values |
| first, last | First and last non-NA values |
| mean | Mean of non-NA values |
| median | Arithemetic median of non-NA values |
| min, max | Minimum and maximum of non-NA values |
| nth | Retrieve value that would appear at position n with the data in sorted order |
| ohlc | Compute four "open-high-low-close" statistics for time series-like data. |
| prod | product of non-NA values | 
| quantile | Compute sample quantile | 
| rand | Ordinal ranks of non-NA values, like calling Series.rank |
| size | Compute group sizes, returning result as a Series | 
| std, var | Sample standard deviation and variance | 
 
To use your own aggregation functions, pass any function that aggregates an array to the aggregate method to its short alias `agg`:

```python
def peak_to_peak(arr):
	return arr.max() - arr.min()

grouped.agg(peak_to_peak)
```


## Column-wise and multiple function application 

```python
tips = pd.read_csv('./datasets/tips.csv')
tips.head()

tips['tip_pct'] = tips['tip'] / tips['total_bill']
tips.head()

grouped = tips.groupby(['day', 'smoker'])
grouped_pct = grouped['tip_pct']

grouped_pct.agg('mean')

# Pass a list of functions or function names to get a DataFrame with column names taken from the functions
grouped_pct.agg(['mean', 'std', peak_to_peak])

# Pass a list of (name, function) tuples, the first element of each tuple will be used as DataFrame column name
grouped_pct.agg([('Average', 'mean'), ('stdev', np.std)])
```

Specify a list of functions to apply to all of the columns or different functions per column in DataFrame

```python
functions = ['count', 'mean', 'max']

result = grouped[['tip_pct', 'total_bill']].agg(functions)

result
```

Apply different functions to one or more of the column, pass a dictionary to agg that contains a mapping of column names to any of the function specifications

```python
grouped.agg({"tip": np.max, "size": "sum"})

# Pass multiple function to one column by using a list
grouped.agg({"tip_pct": ["min", "max", "mean"]})
```

## Return aggregated data without row indexes

By default, the grouped key will be use as the index. 

```python
tips.groupby(['day', 'smoker'], as_index=False).mean()
```

# 10.3 Apply: General split-apply-combine
#pandas/groupby/apply

The most general purpose GroupBy method is apply. 
`apply` splits the object being manipulated into pieces, invokes the passed function on each piece and then attempts to concatenate the pieces. 

```python
# A function that selects the rows with the largest values in a particular column
def top(df, n=5, column="tip_pct"):
	return df.sort_values(column, ascending=False)[:n]

top(tips, n=6)

# The top function will be applied to each smoker group
# The result has a hierarchical index with an inner level that contains index values from the original DataFrame
tips.groupby('smoker').apply(top)

# Pass a function to apply with other arguments
# Below code will return the highest total bill in each day for smoker and non smokers
tips.groupby(['smoker', 'day']).apply(top, n=1, column='total_bill')
```

```python
result = tips.groupby('smoker')['tip_pct'].describe()
result
result.unstack('smoker')
# Inside groupby when invoke a method like describe, it is a sort cut for
def f(group):
	return group.describe()

grouped.apply(f)
```

## Suppress the group keys

```python
tips.groupby('smoker', group_keys=False).apply(top)
```

## Quantile and Bucket Analysis
`pandas.cut` and `pandas.qcut`, slicing data up into buckets with binds of your choosing. 

```python
# Sample random dataset and an equal-length bucket categorization using pandas.cut
frame = pd.DataFrame(
    {"data1": np.random.standard_normal(1000), "data2": np.random.standard_normal(1000)}
)

frame.head()

quartiles = pd.cut(frame['data1'], 4)

# The Categorical object returned by cut can be passed directly to groupby. 
# So we could compute a set of group statistics fro the quartiles.

def get_stats(group):
    return pd.DataFrame(
        {
            "min": group.min(),
            "max": group.max(),
            "count": group.count(),
            "mean": group.mean(),
        }
    )

grouped = frame.groupby(quartiles)

grouped.apply(get_stats)

```

## Example: Filling Missing Values with Group-Specific Values
Suppose you need to fill value to vary by group.
Use apply with function that calls fillna on each data chunk

```python
states = ['Ohio', 'New York', 'Vermont', 'Florida', 'Oregon', 'Nevada', 'California', 'Idaho']

group_key = ['East', 'East', 'East', 'East', 'West', 'West', 'West', 'West']

data = pd.Series(np.random.standard_normal(8), index=states)

# Set some value in the data to be missing
data[['Vermont', 'Nevada', "Idaho"]] = np.nan

data.groupby(group_key).size() # Count the total data
data.groupby(group_key).count() # Count the valid data

def fill_mean(group):
	return group.fillna(group.mean())

# Fill up the missing value by each group's mean
data.groupby(group_key).apply(fill_mean)
```

```python
fill_values = {'East':0.5, 'West': -1}

def fill_func(group):
	return group.fillna(fill_values[group.name])

# groupby.apply() returns a dataframe
data.groupby(group_key).apply(fill_func)

```

## Example : Random Sampling and Permutation

To draw a random sample from a large dataset for Monte Carlo simulation. 

#code-snippet/cards
```python
# Create a set of cards
suits = ["H", "S", "C", "D"]

card_val = (list(range(1, 11)) + [10] * 3) * 4
base_names = ["A"] + list(range(2, 11)) + ["J", "K", "Q"]

cards = []

for suit in suits:
	cards.extend(str(num) + suit for num in base_names)

deck = pd.Series(card_val, index=cards)

# Get 5 random cards
def draw(deck, n = 5):
	return deck.sample(5)

draw(deck)

# Get 2 random cards from each suit. 

def get_suit(card):
	# last letter is suit
	return card[-1]

deck.groupby(get_suit).apply(draw, n=2)
```

## Example: Group Weighted Average and Correlation
#weighted-average

```python
df = pd.DataFrame(
    {
        "category": ["a", "a", "a", "a", "b", "b", "b", "b"],
        "data": np.random.standard_normal(8),
        "weights": np.random.uniform(size=8),
    }
)
df

grouped = df.groupby("category")

# Weighted average by category
def get_wavg(group):
    return np.average(group["data"], weights=group["weights"])


grouped.apply(get_wavg)
```

Compute the correlations between groups and columns
#pandas/correlations

```python
close_px = pd.read_csv("./datasets/stock_px.csv", parse_dates=True, index_col=0)

close_px.info()

close_px.tail(4)

# Create a function that computes the pair-wise correlation or each column with SPX column

def spx_corr(group):
	return group.corrwith(group['SPX'])

# Compute percent change on the close_px using pct_change
rets = close_px.pct_change().dropna()

# Group these percent changes by year
def get_year(x):
	return x.year

by_year = rets.groupby(get_year)

by_year.apply(spx_corr)

# Compute intercolumn correlations

def corr_appl_msft(group):
	return group['AAPL'].corr(group["MSFT"])

by_year.apply(corr_appl_msft)
```