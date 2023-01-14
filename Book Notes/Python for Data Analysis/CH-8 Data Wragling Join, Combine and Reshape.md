# 8.2 Combine and Merging Datasets

- The whole concept of merge is to merge the data of 2 data sets by defining and finding the row index label, either from columns, or by the default index.

## Merging on column
`pd.merge(left, right, on=['key1', 'key2'], how='outer')`

## Merging on index
Pass `left_index=True` or `right_index=True` to indicate that the index should be used as the merge key. 

The code below will combine index value of right1 and map with left1, do the many-to-one type of merging. For all the "a" rows in left1, will have an additional column "3.5"
```python
left1 = pd.DataFrame(
    {"key": ["a", "b", "a", "a", "b", "c"], "value": pd.Series(range(6), dtype="int64")}
)

right1 = pd.DataFrame({"group_val": [3.5, 7]}, index=["a", "b"])

pd.merge(left1, right1, left_on='key', right_index=True, how='outer')
```

Joining with hierarchical indexed data, the left side column keys have to map the right side hierarchical indexes
```python
# Joining with hierarchically indexed data
lefth = pd.DataFrame(
    {
        "key1": ["Ohio", "Ohio", "Ohio", "Nevada", "Nevada"],
        "key2": [2000, 2001, 2002, 2001, 2002],
        "data": pd.Series(range(5), dtype="Int64"),
    }
)

righth_index = pd.MultiIndex.from_arrays(
    [
        ["Nevada", "Nevada", "Ohio", "Ohio", "Ohio", "Ohio"],
        [2001, 2000, 2000, 2000, 2001, 2002],
    ]
)

righth = pd.DataFrame(
    {
        "event1": pd.Series([0, 2, 4, 6, 8, 10], dtype="Int64", index=righth_index),
        "event2": pd.Series([1, 3, 5, 7, 9, 11], dtype="Int64", index=righth_index),
    }
)

pd.merge(lefth, righth, left_on=["key1", "key2"], right_index=True, how="outer")
```

Use index for both side, use either `pd.merge()` with left_index and right_index specified. Or use `df.join()` method.
```python
left2 = pd.DataFrame(
    [[1.0, 2.0], [3.0, 4.0], [5.0, 6.0]],
    index=["a", "c", "e"],
    columns=["Ohio", "Necada"],
).astype("Int64")

right2 = pd.DataFrame(
    [[7.0, 8.0], [9.0, 10.0], [11.0, 12.0], [13.0, 14.0]],
    index=["b", "c", "d", "e"],
    columns=["Mussouri", "Alabama"],
).astype("Int64")

pd.merge(left2, right2, how="outer", left_index=True, right_index=True)
# Same as using DataFramels join method
left2.join(right2, how="outer")
```

## Concatenating Along an Axis
Concatenation or stacking.

```python
arr = np.arange(12).reshape((3, 4))
# axis 0 will concatenate on rows (stack)
# axis 1 will concatenate on columns (horizontally attach)
np.concatenate([arr, arr], axis=0)
```

```python
s1 = pd.Series([0, 1], index=["a", "b"], dtype="Int64")
s2 = pd.Series([2, 3, 4], index=["c", "d", "e"], dtype="Int64")
s3 = pd.Series([5, 6], index=["f", "g"], dtype="Int64")

# By default, cerateing another series, vertically stack
pd.concat([s1, s2, s3], axis="index")

# Return a data frame
pd.concat([s1, s2, s3], axis="columns")

s4 = pd.concat([s1, s3])

pd.concat([s1, s4], axis="columns")

# Create a hierarchical index on the concatenation axis
result = pd.concat([s1, s2, s3], keys=['one', 'two', 'three'])

# When use columns as the axis, the keys become headers
result = pd.concat([s1, s2, s3], axis="columns", keys=["one", "two", "three"])
```

Concatenate DataFrame objects
```python
df1 = pd.DataFrame(
    np.arange(6).reshape((3, 2)), index=["a", "b", "c"], columns=["one", "two"]
)


df2 = pd.DataFrame(
    5 + np.arange(4).reshape((2, 2)), index=["a", "c"], columns=["three", "four"]
)

# keys argument is used to create a hierarchical index
# where the first level can be used to identify each of the concatenated DataFrame objects
pd.concat([df1, df2], keys=["level1", "level2"], axis="columns")

# Can also pass in a dictionary, pass in names to name the created axis levels
pd.concat({"level1": df1, "level2": df2}, axis="columns", names=["upper", "lower"])

# And above functions produce same result as :
pd.merge(df1, df2, left_index=True, right_index=True, how="outer")
```

The last consideration concerns FataFrames in which the row index does not contain any relevant data. 
So the row index is to be ignored.
```python
df1 = pd.DataFrame(np.random.standard_normal((3, 4)), columns=["a", "b", "c", "d"])
df2 = pd.DataFrame(np.random.standard_normal((2, 3)), columns=["b", "d", "a"])

# If directly concatenate, the index number will be messed up
pd.concat([df1, df2])

# to keep a clean index number
pd.concat([df1, df2], ignore_index=True)
```

## Combining Data with Overlap

Two datasets with indexes that overlap in full or in part.

An example of NumPy's `where` function, which performs the array-oriented equivalent of an if-else expression.
```python
a = pd.Series([np.nan, 2.5, 0, 3.5, 4.5, np.nan], index=["f", "e", "d", "c", "b", "a"])

b = pd.Series([0, np.nan, 2, np.nan, np.nan, 5], index=["a", "b", "c", "d", "e", "f"])

# Whenever value in a are null, valyes from b are selected
# This doesn't check the input label are aligned or not
np.where(pd.isna(a), b, a)

# to line up values by index, use combine_first method
a.combine_first(b)
```


For DataFrame, this will be done for each column
```python
df1 = pd.DataFrame(
    {"a": [1, np.nan, 5, np.nan], "b": [np.nan, 2, np.nan, 6], "c": range(2, 18, 4)}
)

df2 = pd.DataFrame({"a": [5, 4, np.nan, 3, 7], "b": [np.nan, 3, 4, 6, 8]})

df1.combine_first(df2)
```

# 8.3 Reshaping and Pivoting
Rearranging tabular data

## Reshaping with Hierarchical Indexing
**stack** "Rotates" of pivots from the columns in the data to rows
**unstack** Pivots from the rows into the columns
```python
data = pd.DataFrame(
    np.arange(6).reshape((2, 3)),
    index=pd.Index(["Ohio", "Calorado"], name="state"),
    columns=pd.Index(["one", "two", "three"], name='number'),
)

# Moves the column index to row index
data.stack()
data.unstack()

# Unstack different level by passing a level number or name
data.unstack(level='state')
```
Unstack may introduce missing values
```python
s1 = pd.Series([0, 1, 2, 3], index=['a', 'b', 'c', 'd'], dtype="Int64", name='one')

s2 = pd.Series([4, 5, 6], index=['c', 'd', 'e'], dtype='Int64', name='two')

data2 = pd.concat([s1, s2], keys=['one', 'two'])

# After unstack, it will attach the columns to the right 
data2.unstack()
# Which is same as
pd.merge(s1, s2, left_index=True, right_index=True, how='outer').T
```

When unstack in a DataFrame, the level unstacked becomes the lowest level in result.
```python
result = data.unstack()

df = pd.DataFrame(
    {"left": result, "right": result + 5},
    columns=pd.Index(["left", "right"], name="side"),
)

df.unstack(level='state').stack(level='side')
```

## Pivoting "Long" to "Wide" format
```python
data = pd.read_csv("./datasets/macrodata.csv")

data = data.loc[:, ["year", "quarter", "realgdp", "infl", "unemp"]]

data.head()

# use pandas.PeriodIndex (repersents time intervals)
# df.pop will return the column and delete it from dataframe
periods = pd.PeriodIndex(
    year=data.pop("year"), quarter=data.pop("quarter"), name="date"
)

periods

data.index = periods.to_timestamp("D")

data = data.reindex(columns=['realgdp', 'infl', 'unemp'])
data.columns.name = 'item'

long_data = (data.stack().reset_index().rename(columns={0: 'value'}))

# Pivot long data to wide data
pivoted = long_data.pivot(index='date', columns='item', values='value')

# By omitting the last argument, obtain a DataFrame with hierarchial columns
pivoted = long_data.pivot(index='date', columns='item')

unstacked = long_data.set_index(['date', 'item']).unstack(level='item')
```

## Pivoting "Wide" to "Long" format
In inverse operation of pivot for DataFrames is pandas.melt
Merges multiple columns in to one

```python
df = pd.DataFrame(
    {"key": ["foo", "bar", "baz"], "A": [1, 2, 3], "B": [4, 5, 6], "C": [7, 8, 9]}
)

melted = df.melt(id_vars="key")

# Use pivot to reshape the data back
melted.pivot(index='key', columns='variable', values='value')


# Specify the subset of columns to use as value columns
# id_vars are group identifiers
pd.melt(df, id_vars='key', value_vars=['A', 'B'])
```

