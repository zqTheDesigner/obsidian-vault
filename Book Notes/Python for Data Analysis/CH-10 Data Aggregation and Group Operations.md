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

Count: Will count how many data in each columns belongs to the key group. Count is more to get the relationship between key group and other groups 

Size: How many rows belongs to each key group, size is to get the information about the key grout itself. 

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

