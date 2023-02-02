githubTo extract meaning from the raw data.

# 13.1 Bitly Data from 1.USA.gov

```python
path = './datasets/bitly_usagov/example.txt'

import json

with open(path) as f:
	records = [json.loads(line) for line in f]

records[0]
```

## Counting Time Zones in Pure Python
#python/counting 
Finding the time zones occur most often in the dataset (tz field). 

```python
# Extract timezones using list comprehension

time_zones = [rec['tz'] for rec in records if 'tz' in rec]

time_zones[:10]

# Counts the timezone 
def get_counts(sequence):
	counts = {}
	for x in sequence:
		if x in counts:
			counts[x] += 1
		else:
			counts[x] = 1
	return counts

# Using more advanced python standard library
from collections import defaultdict

def get_counts2(sequence):
	counts = defaultdict(int) # Values will initialized to 0
	for x in sequence:
		counts[x] += 1
	return counts

counts = get_counts(time_zones)
```

#python/standard-library/counter
```python
# Return top n counts 
def top_counts(count_dict, n=10):
	value_key_pairs = [(count, tz) for tz, count, in count_dict.items()]
	value_key_pairs.sort()
	return value_key_pairs[-n:]

top_counts(get_counts(time_zones), 5)
```

```python
# use Python standard library
from collections import Counter

counts = Counter(time_zones)
counts.most_common(10)
```

## Counting Time Zones with pandas

### Count time zones and display with bar chart
```python
frame = pd.DataFrame(records)
frame['tz'].head()

tz_counts = frame['tz'].value_counts()
tz_counts.head()

# visualize the data using matplotlib

# make the plots looks nicer by filling a substitute value for unknown value
clean_tz = frame['tz'].fillna('Missing')
clean_tz[clean_tz == ""] = "Unknown"
tz_counts = clean_tz.value_counts()

import seaborn as sns

subset = tz_counts.head()
sns.barplot(y = subset.index, x = subset.to_numpy())
```

### Count time zones by device and display grouped bar chart
```python
# Split off the first token in the string and make another summary of the user behavior

results = pd.Series([x.split()[0] for x in frame['a'].dropna()])

results.value_counts().head(8)

# Decompose the top timezones into Windows and non-Window users

# Exclude missing agents from the data
cframe = frame[frame['a'].notna()].copy()

cframe['os'] = np.where(cframe['a'].str.contains('Windows'), 'Windows', 'Not Windows')

cframe['os'].head()

# Group the data by timezone column and list of os
by_tz_os = cframe.groupby(['tz', 'os'])

agg_counts = by_tz_os.size().unstack().fillna(0)

# select the top overall timezones

# Construct an indirect index array from the row counts in agg_counts
# After computing row counts with agg_counts.sum('columns'), use argosort() to obtain and index array
indexer = agg_counts.sum('columns').argsort()

# use `take` to select the rows in the order. Slice off the last 10 rows (largest values)
count_subset = agg_counts.take(indexer[-10:])

count_subset

# Use n-largest to achieve the same result
agg_counts.sum(axis='columns').nlargest(10)
```

### Display normalized data
```python
# To plot in a grouped bar plot

# First stack and reset the index to rearrange the data for better compatibility 
count_subset = count_subset.unstack()

count_subset.name = 'total'

count_subset = count_subset.reset_index()

# sns.barplot(x = 'total', y = 'tz', hue = 'os', data=count_subset)

# Normalize the group percentage sum to 1

def normal_total(group):
	group['normed_total'] = group['total'] / group['total'].sum()
	return group

results = count_subset.groupby('tz').apply(normal_total)

sns.barplot(x = 'normed_total', y='tz', hue='os', data=results)
```

```python
# Compute normalized sum more efficiently using trans from groupby method

g = count_subset.groupby('tz')
results2 = count_subset['total'] / g['total'].transform['sum']
```

# 13.2 MovieLens 1M Dataset

```python
unames = ["user_id", "gender", "age", "occupation", "zip"]

users = pd.read_table(
    "datasets/movielens/users.dat", sep="::", header=None, names=unames, engine="python"
)

rnames = ["user_id", "movie_id", "rating", "timestamp"]

ratings = pd.read_table(
    "datasets/movielens/ratings.dat",
    sep="::",
    header=None,
    names=rnames,
    engine="python",
)

mnames = ["movie_id", "title", "genres"]
movies = pd.read_table(
    "datasets/movielens/movies.dat",
    sep="::",
    header=None,
    names=mnames,
    engine="python",
)

```

### Merge 3 DataFrames in to one
pd.merge() method will automatically look for the column with same name and use as joint key.
```python
# Merge 'ratings' with 'users' and then merge that result with 'movies' data
data = pd.merge(pd.merge(ratings, users), movies)
```

### Get mean movie ratings grouped by gender

```python
# To get mean movie ratings for each film grouped by gender

mean_ratings = data.pivot_table('rating', index='title', columns='gender', aggfunc='mean')
mean_ratings.head()

# Filter down to movies that received at least 250 ratings

ratings_by_title = data.groupby('title').size()

active_titles = ratings_by_title.index[ratings_by_title >= 250]

# Select rows from mean_ratings

mean_ratings = mean_ratings.loc[active_titles]
```

### Find top rated films among female viewers
```python
# To see the top films among female viewers, sort F column in descending order

top_female_ratings = mean_ratings.sort_values('F', ascending=False)

top_female_ratings.head()
```

## Measure Rating Disagreement
Find movies that are most divisive between male and female, add a column to mean_ratings contains the differences in means

```python
mean_ratings['diff'] = mean_ratings['M'] - mean_ratings['F']

sorted_by_diff = mean_ratings.sort_values('diff')

# Find out movies that preferred by men that women didn't rated as highly
sorted_by_diff[::-1].head()

# get the movies that elicited the most disagreement among viewers

# Disagreement can be measured by the variance of standard deviation

rating_std_by_title = data.groupby('title')['rating'].std()
rating_std_by_title = rating_std_by_title.loc[active_titles]

# Sort in descrending order and select the first 10 rows
rating_std_by_title.sort_values(ascending=False)[:10]
```

### Split the formatted strings in to lists, then explode
#pandas/explode #pandas/string/split
```python
# Split the genres string into a list of genres 
movies['genre'] = movies.pop('genres').str.split('|')

# Calling `explode` method to generate a new DataFrame with one row for each 'inner' element
movies_exploded = movies.explode('genre')
movies_exploded[:10]
```

```python
# Merge all three tables together and group by genre

rating_with_genre = pd.merge(pd.merge(movies_exploded, ratings),users)

genre_ratings = (rating_with_genre.groupby(['genre', 'age'])['rating'].mean().unstack('age'))
genre_ratings
```

# 13.3 US Baby Names 1880 - 2010

```python
# Use Unix syntax to loot at first 10 lines of a file
!head -n 10 'datasets/babynames/yob1880.txt'
```

```python
names1880 = pd.read_csv('datasets/babynames/yob1880.txt', names = ['name', 'sex', 'births'])

# calculate total number of both each year
names1880.groupby('sex').sum()

# Assemble all of the data into a single DataFrame 

pieces = []

for year in range(1880, 2011):
	path = f'datasets/babynames/yob{year}.txt'
	frame = pd.read_csv(path, names=['name', 'sex', 'births'])

	# Add a column of year
	frame['year'] = year
	pieces.append(frame)

# Concatenate everything into a single DataFrame
names = pd.concat(pieces, ignore_index=True)

total_births = names.pivot_table('births', index='year', columns='sex', aggfunc=sum)

total_births.tail()

total_births.plot(title='Total births by sex and year')

# Insert a column prop with the fraction of babies given each name relative to the total number of birth
# How popular the name is

def add_prop(group):
	group['prop'] = group['births'] / group['births'].sum()
	return group

names = names.groupby(['year', 'sex']).apply(add_prop)

# Sanity check to verify the prop will sum to 1
names.groupby(['year', 'sex'])['prop'].sum()

# Extract a subset of the data to facilitate further analysis
# Top 1000 names for each sex/year combination

def get_top1000(group):
	return group.sort_values('births', ascending=False)[:1000]

grouped = names.groupby(['year', 'sex'])

top1000 = grouped.apply(get_top1000)

top1000.head()

# drop the group index 
top1000 = top1000.reset_index(drop=True)


```

## Analyzing Naming Trends

```python
boys = top1000[top1000['sex']=='M']

girls = top1000[top1000['sex']=='F']

total_births = top1000.pivot_table('births', index='year', columns='name', aggfunc=sum)

total_births.info()

# Plot names trends with DataFrame's plot method

subset = total_births[['John', 'Harry', 'Mary', 'Marilyn']]

subset.plot(subplots=True, figsize = (12, 10), title = 'Number of births per year')

```

## Measuring the increase in naming diversity

```python

# Proportion of births represented by top 1000 most popular names

table = top1000.pivot_table("prop", index="year", columns="sex", aggfunc=sum)

table.plot(title='Sum of table 1000.prop by year & sex', yticks=np.linspace(0, 1,2, 13))

# Number of distinct name taken in order of popularity from highest ot lowest]
# boy names from 2010

df = boys[boys['year'] == 2010]

df

# Calculate how many most popular name it takes to reach 50% 
prop_cumsum = df['prop'].sort_values(ascending=False).cumsum()

# searchsorted method return the cumulative sum at provided value
prop_cumsum.searchsorted(0.5)

df = boys[boys['year'] == 1900]

in1900 = df.sort_values('prop', ascending=False).prop.cumsum()

in1900.searchsorted(0.5) + 1

# Apply this operation to each year/sex combination

def get_quantile_count(group, q=0.5):
	group = group.sort_values('prop', ascending=False)
	return group.prop.cumsum().searchsorted(q) + 1

diversity = top1000.groupby(['year', 'sex']).apply(get_quantile_count)

diversity.unstack()

diversity.unstack().plot.line(title='Number of popular names in top 50%')
```

## The 'last letter' revolution

To see how the distribution of boy names' final letter has changed.

```python
def get_last_letter(x):
    return x[-1]


last_letters = names["name"].map(get_last_letter)

last_letters.name = "last_letter"

table = names.pivot_table(
    "births", index=last_letters, columns=["sex", "year"], aggfunc=sum
)

subtable = table.reindex(columns=[1910, 1960, 2010], level='year')

subtable.head()

letter_prop = subtable / subtable.sum()

import matplotlib.pyplot as plt

fig, axes = plt.subplots(2, 1, figsize=(10, 8))

letter_prop["M"].plot(kind='bar', rot=0, ax=axes[0], title='Male')
letter_prop["F"].plot(kind="bar", rot=0, ax=axes[1], title='Female', legend=False)

# Normalize by year and sex and select a subset of letter for the boy names
# transposing to make each column a time series

letter_prop = table / table.sum()

dny_ts = letter_prop.loc[['d', 'n', 'y'], 'M'].T

dny_ts.head()

dny_ts.plot()
```

### Boy names that become girl names

```python
all_names = pd.Series(top1000['name'].unique())

lesly_like = all_names[all_names.str.contains('Lesl')]

filtered = top1000[top1000['name'].isin(lesly_like)]

filtered.groupby('name')['births'].sum()

# aggregate by sex and year, normalize within year

table = filtered.pivot_table("births", index="year", columns="sex", aggfunc="sum")

table = table.div(table.sum(axis='columns'), axis='index')

table.tail()

table.plot(style={'M':'k-', 'F':'k--'})

```

# 13.4 USDA Food Database

```python
# load the file into python
import json

db = json.load(open("./datasets/usda_food/database.json"))

len(db)

db[0].keys()

db[0]['nutrients'][0]

nutrients = pd.DataFrame(db[0]['nutrients'])

nutrients.head()

# Converting a list of dictionaries to a DataFrame

info_keys = ['description', 'group', 'id', 'manufacturer']

info = pd.DataFrame(db, columns=info_keys)

info.head()

info.info()

pd.value_counts(info['group'])[:10]

# analyse on all of the nutrient data
# Assemble the nutrients for each food into a single large table

# Convert each list of food nutrients to a dataFrame
# add a column for food it
# concatenate the data

nutrients = []

for rec in db:
	fnuts = pd.DataFrame(rec['nutrients'])
	fnuts['id'] = rec['id']
	nutrients.append(fnuts)

nutrients = pd.concat(nutrients, ignore_index=True)

nutrients.head()

# Check and drop the duplicates
nutrients.duplicated().sum()

nutrients = nutrients.drop_duplicates()

col_mapping = {"description": "food", "group": "fgroup"}

info = info.rename(columns=col_mapping, copy=False)

info.info()

col_mapping = {'description':'nutrient', 'group':'nutgroup'}

nutrients = nutrients.rename(columns=col_mapping, copy=False)

nutrients

# Merge info into nutrients
ndata = pd.merge(nutrients, info, on='id')
ndata.info()

result = ndata.groupby(['nutrient', 'fgroup'])['value'].quantile(0.5)
result['Zinc, Zn'].sort_values().plot(kind='barh')

result['Zinc, Zn'].sort_values()

# Use idxmax or argmax Series method to find which food is most dense in each nutrient

by_nutrient = ndata.groupby(['nutgroup', 'nutrient'])

def get_maximum(x):
	return x.loc[x.value.idxmax()]

max_foods = by_nutrient.apply(get_maximum)[['value', 'food']]
max_foods.loc['Amino Acids']['food']
```