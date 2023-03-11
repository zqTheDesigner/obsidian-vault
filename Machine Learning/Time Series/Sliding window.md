---
tags: time-series, window-width, sliding-window
---

Using the previous time steps as input variables and use the next step as the output variables.

| time | measure |
|-|-|
| 1 | 100 |
| 2 | 110 |
| 3 | 108 |
| 4 | 115 |
| 5 | 120 |

We can transfer this time series dataset as a supervised learning problem by using the value at the previous time step to predict the value at the next time-step. The missed value will then be ignored. 

| X | y |
|-|-|
| ? | 100 |
| 100 | 110 |
| 110 | 108 |
| 108 | 115 |
| 115 | 120 |
| 120 | ? |

- We can see that the previous time step is the input (X) and the next time step is the output (y) in our [[Supervised Learning|supervised learning problem]]
- The order between the observations is preserved, and must continue to be preserved when using this dataset to train a supervised model.
- For the first value, because we don't have the previous value, so we won't know the first X. 
- For the last value, we also don't know what's the next value, so we won't know the last y.

The number of previous time steps is called the window width, or size of the lag. 

The width of sliding window can be increased to include more previous time steps

Sliding window approach can be used on a time series that has more than one value - [[Multivariate Time Series|multivariate time series]]

## Sliding window for multivariate time series

| time | measure1| measure2 |
|-|-|-|
| 1 | 0.2 | 88 |
| 2 | 0.5 | 89 | 
| 3 | 0.7 | 87 |
| 4 | 0.4 | 88 |
| 5 | 1.0 | 90 |

Use previous time step values of measure 1 and measure 2, we will also have available the next time step value for measure 1. 
- We don't know what caused 0.2 and 88, but in the next time stamp, we know 0.2 and 88 (previously) plus the current measurement of 0.5 together caused 89. 
| X1 | X2| X3 | y |
|-|-|-|-|
| ? | ? | 0.2 | 88 |
| 0.2 | 88 | 0.5 | 89 | 
|0.5 | 89 | 0.7 | 87 |
| 0.7 | 87 | 0.4 | 88 |
| 0.4 | 88 | 1.0 | 90 |
| 1.0 | 90 | ? | ? |

What if we want to predict both measure 1 and measure 1, the process will be same, except X3 now become another label to be predicted instead of measured feature.  

# Sliding window with [[Multi-Step Forecasting]]

[[One-Step Forecasting]] - Where the next step (t + 1) is predicted
[[Multi-Step Forecasting]] - Where two or more future time steps are to be predicted

| time | measure |
|-|-|
| 1 | 100 |
| 2 | 110 |
| 3 | 108 |
| 4 | 115 |
| 5 | 120 |

We can frame this time series as a two step forecasting data sets for supervised learning with a window width os one

| X1 | y1 | y2 |
|-|-|-|
| ? | 100 | 110 |
| 100 | 110 |108 |
| 110 | 108 | 115 |
| 108 | 115 | 120 |
| 115 | 120 | ? 
| 120 | ? | ? |

The first row and last two rows cannot be used to train a supervised model. 
The supervised model only has X1 to work with in order to predict y1 and y2

