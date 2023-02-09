# Transform in to supervised learning problem

Restructure the time series dataset as a supervised learning problem by using the value at the previous time step to predict the value at the next time-step. ([[Sliding window]])

```

time	value
0	1.0	NaN
1	2.0	100.0
2	3.0	110.0
3	4.0	108.0
4	5.0	115.0
5	6.0	120.0

be transformed to 

	X	    y
0	NaN	    100.0
1	100.0	110.0
2	110.0	108.0
3	108.0	115.0
4	115.0	120.0
5	120.0	NaN

```

Because in time series, the later value is affected by the previous stat. 
by shifting actual value in -1 interval, what we get the a relationship between X and value (X caused the value)
- We don't know what caused the initial value (so first X is always NaN)
- We don't know what will the last value cause (so the last y is NaN)