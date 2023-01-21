---
tags: timeseries, python/datetime, python/timedelta
---
Anything that is recorded repeatedly at many points in time forms a time series. 

Many time series are fixed frequency - data points occur at regular intervals according to some rules. 

How to mark or refer time series data:

- Timestamps: specific instants in time
- Fixed period: Such as whole month or whole year
- Intervals of time: Indicated by a start and end timestamp. Periods can be thought of as special cases of intervals.
- Experiment or elapsed time: Each timestamp is a measure if time related to a particular start time. (The index may be an integer or floating-point number indicating elapsed time from the start of the experiment)


# 11.1 Date and Time Data Types and Tools

```python
from datetime import datetime, timedelta

now = datetime.now()
now
now.year, now.month, now.day

# datetime stores both the date and time down to the microsecond. 
# datetime.timedelta represents the temporal difference between two datetime objects

delta = datetime(2011, 1, 7) - datetime(2008, 6, 24, 8, 15)
delta
delta.days, delta.seconds

# Add or substract a timedelta or multipole to a datetime object to yield a new shifted object
start = datetime(2011, 1, 7)
start + timedelta(12)
start - 2 * timedelta(12)
```

```python
# Format datetime objects and pandas Timestamp objects as strings using str or strftime method

stamp = datetime(2011, 1, 3)
str(stamp)
stamp.strftime("%Y-%m-%d")

# Use same format code convert strings to date
value = "2011-01-03"

# use datetime.strptime to parse a date with a known format
datetime.strptime(value, "%Y-%m-%d")
datestrs = ['7/6/2011', '8/6/2011']
[datetime.strptime(x, "%m/%d/%Y") for x in datestrs]
```

## Pandas datetime object
#pandas/to_datetime

use to_datetime to parse string to date time.
datetime objects also have a number of local-specific formatting options for systems in other countries or languages. 

```python
# pandas.to_datetime method parses different kinds of date representations, e.g. ISO8601 formats
datestrs = ["2011-07-06 12:00:00", "2011-08-06 00:00:00"]

pd.to_datetime(datestrs)


# NaT(Not a Time) is pandas's null value for timestamp data 
idx = pd.to_datetime(datestrs + [None])
idx
```

# 11.2 Time Series Basics
A basic kind of time series object in pandas is a Series indexed by timestamps, which is often represented outside of pandas as Python strings or datetime objects

```python
dates = [
    datetime(2011, 1, 2),
    datetime(2011, 1, 5),
    datetime(2011, 1, 7),
    datetime(2011, 1, 8),
    datetime(2011, 1, 10),
    datetime(2011, 1, 12),
]

ts = pd.Series(np.random.standard_normal(6), index=dates)

ts

# perform arithmetic operation between differently indexed time series. 
# [::2] selects every second elements in ts
ts + ts[::2]

# Find the index data type
ts.index.dtype

# scalar values from a datetimeIndex are pandas timestamp objects

stamp = ts.index[0]
stamp
```
A pandas.Timestamp can be substituted most places where your would use a datetime object. But reverse it not true.
pandas.Timestamp can store nano seconds and frequency information, where datetime object cannot. 

## Indexing, Selection, Subsetting
#pandas/time-series/indexing

Time series behaves like any other Series when you are indexing and selecting data based on the label.

```python
# Select by index
stamp = ts.index[2]

ts[stamp]

# Select by string that is interpretable as a date
# Both of these 3 works
ts["2011/01/10"]
ts["20110110"]
ts["2011-01-10"]

# Pace a year or a month to select slices of data 
longer_ts = pd.Series(np.random.standard_normal(1000), index=pd.date_range('2011-01-01', periods=1000))

longer_ts['2012']

# Select the month
longer_ts['2012-02']

# Select by datetime object
ts[datetime(2011,1,7)]

# If time stamps not contained in a time series, the range still can be selected
ts['2011-01-06':'2011-01-11']

# use truncate slice a series between two dates
ts.truncate(after="2011-01-09")


times = ts.truncate(after="2011-01-09")
times[:] = np.nan
```

Indexing with DataFrame
```python
#  Indexing is same for DataFrame

dates = pd.date_range("2000-01-01", periods=100, freq="W-WED")

long_df = pd.DataFrame(
    np.random.standard_normal((100, 4)),
    index=dates,
    columns=["Coloradi", "Texas", "New York", "Ohio"],
)

long_df.loc['2001-05']
```

## Time Series with Duplicated Indices

When there are multiple observations failing on a particular timestamp. 

```python
dates = pd.DatetimeIndex(['2000-01-01','2000-01-02','2000-01-02', '2000-01-02', '2000-01-03'])

dup_ts = pd.Series(np.arange(5), index=dates)

# Check if there are duplicated index
dup_ts.index.is_unique

# To aggregate the data having nonunique timestamps

grouped = dup_ts.groupby(level=0)

# To get the dataframe without duplications
grouped.mean()
```

# 11.3 Date Ranges, Frequencies and Shifting
#pandas/time-series/resample
Generic time series in pandas are assumed to be irregular - no fixed frequencies.

Convert the sample time series to fixed daily frequency buy calling resample.

```python
resampler = ts.resample('D')

# use resampler.mean() to convert the resampler object to DataFrame
resampler.mean()

```

## Generating Date Ranges
`pandas.date_range` is responsible for generating a DatetimeIndex with an indicated length according to a particular frequency. 

```python
# By default, pandas.date_range generates daily timestamps.
index = pd.date_range("2012-04-01", "2012-06-01")

# Pass only a start or end date, must pass a number of periods to generate
# generate 20 days timestamps starts from 2012-04-01
pd.date_range(start="2012-04-01", periods=20)

# frequency "BM" stands to BusinessMonthEnd
pd.date_range("2000-01-01", "2000-12-01", freq="BM")

# date_range preserves the time of start and end timestamp
pd.date_range("2012-05-02 12:56:31", periods=5)

# Normalize the timestamp by passing the normalize option
pd.date_range("2012-05-02 12:56:31", periods=5, normalize=True)
```

Anchored offsets - Frequencies describe points in time that are not evenly spaces, "M" and "BM" depend on the number of days in a month. "BM" depends on whether the month ends on a weekend or not.

## Week of month dates

Frequency class starting with WOM enables you to get dates like the third Friday of each month

```python
# get every 3rd Friday of each week from start to end
monthly_dates = pd.date_range("2012-01-01", "2012-09-01", freq="WOM-3FRI")

monthly_dates
```

## Shifting (Leading and Lagging) Data
#pandas/time-series/shifting
Shifting refers to moving data backward and forward through time. 

Both Series and DataFrame have a shift method for doing naive shifts forward or backward, leaving the index unmodified. 

```python
ts = pd.Series(
    np.random.standard_normal(4), index=pd.date_range("2000-01-01", periods=4, freq="M")
)

ts.shift(2)
ts.shift(-2)

# use shift to computing consecutive percent changes in a time series or multiple time series as DataFrame columns
ts / ts.shift(1) - 1

# Naive shifts leave the index unmodified
# If frequency is known, the shift is moving the timestamps instead of the data
ts.shift(2, freq="M")

ts.shift(3, freq="D")

# T stands for minutes. 
# the freq parameter indicates the offset to apply to the timestamps, it does not change the underlaying frequency of the data
ts.shift(1, freq="90T")

```

