cd## What's KNN imputation? 

KNN imputation will find the K amount of nearest neighbours from the dataset based on the observed features (the missing feature will be ignored first) and fill up the missing feature by calculating the mean value of the nearest K neighbours.

Here, we talking about "nearest" distance by calculating the [[Euclidean Distance]] between the data points. (Other ways of distance calculation may be used, but not as popular as Euclidean Distance)

**For example**: we have feature A, B, C in the dataset, and one of the row missed feature C,
We'll first find the K neighbours based on observed feature A and B, then use the mean value of the observed C to calculated the missed C. 

KNN imputation and KNN classification are different. 
- KNN classification uses the features to predict the labels. 
- KNN imputation uses the observed features to compute the missed feature

Because KNN is based on the distance between data points, outliers will affect the result a lot, data normalisation is important. (I think sklearn don't normalise the data automatically, the normalisation is a necessary step found from a few tutorials.)

## What is K-Means imputation?

What's the difference between K-means and KNN?

# 11.4 Time Zone Handling
#pandas/time-series/time-zone
#time-zone
Time zones are expressed as offsets from UTC

Python time zone information comes from pytz library. 

```python
import pytz

pytz.common_timezones[-5:]

# to get a time zone object from pytz, use pytz.timezone
tz = pytz.timezone("America/New_York")

tz
```


## Time Zone Localization and Conversion
By default, time series in pandas are time zone naive. 

```python
dates = pd.date_range("2012-03-09 09:30", periods=6)

ts = pd.Series(np.random.standard_normal(len(dates)), index=dates)

print(ts.index.tz)  # None by default

# To set a date range with a timezone
pd.date_range("2012-03-09 09:30", periods=10, tz="UTC")

# Use `tz_localize` method to convert naive to localized
ts_utc = ts.tz_localize('UTC')

# Once a time series has been localized to a particular timezone
# it can be converted to another timezone with tz_convert
ts_utc.tz_convert("America/New_York")
```

## Operations with Time Zone-Aware Timestamp Objects

Individual Timestamp object can be localized from naive to time zone-aware
and converted from one time zone to another. 

```python
stamp = pd.Timestamp("2011-03-12 04:00")

stamp_utc = stamp.tz_localize('utc')

stamp_utc.tz_convert('Asia/Shanghai')

# pass time zone argument when create the TimeStamp
stamp_moscow = pd.Timestamp("2011-03-12 04:00", tz="Europe/Moscow")

# change time zone doesn't alter the internal UTC value
stamp_utc.value == stamp_utc.tz_convert('Asia/Singapore').value # True
```

## Operations Between Different Time Zones

If two time series with different time zone are combined
The result will be UTC.

```python
dates = pd.date_range("2012-03-07 09:30", periods=10, freq='B')

ts = pd.Series(np.random.standard_normal(len(dates)), index=dates)

ts1 = ts[:7].tz_localize("Europe/London")
ts2 = ts1[2:].tz_convert("Europe/Moscow")

result = ts1 + ts2

result

```
Operations between time zone naive and time zone aware data are not supported and will raise exception.

# 11.5 Periods and Period Arithmetic
#pandas/time-series/periods

Periods represent time spans (days, months, quarters)

pandas.Period class represents Periods data type, requiring a string or integer and a supported frequency

```python
# A Period object represents the full time span from Jan 1 2011 to dec 31 2011, entire year
p = pd.Period('2011', freq="A-DEC")

# Adding and subtracting integers from periods will shift the period frequency
p + 5

# If two periods have the same frequency, their difference is the number of units between then as a date offset
pd.Period('2014', freq="A-DEC") - p

# Regular ranges of periods can be constructed with the period_range function
periods = pd.period_range("2000-01-01", "2000-06-30", freq="M")
periods

# PeriodIndex class stores a sequence of periods and can serve as an axis index in any pandas data structure. 
pd.Series(np.random.standard_normal(6), index=periods)
```