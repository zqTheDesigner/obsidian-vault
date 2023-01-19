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

