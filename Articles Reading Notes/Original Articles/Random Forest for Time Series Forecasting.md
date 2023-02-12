---
tag: random-forest time-series  
---

**Random Forest** is a popular and effective ensemble machine learning algorithm.

It is widely used for #classification and #regression predictive modeling problems with structured (tabular) data sets, e.g. data as it looks in a spreadsheet or database table.

Random Forest can also be used for **time series forecasting**, although it requires that the time series dataset be transformed into a supervised learning problem first. It also requires the use of a specialized technique for evaluating the model called walk-forward validation, as evaluating the model using k-fold cross validation would result in optimistically biased results.

In this tutorial, you will discover how to develop a Random Forest model for time series forecasting.

After completing this tutorial, you will know:

-   Random Forest is an ensemble of decision trees algorithms that can be used for classification and regression predictive modeling.
-   Time series datasets can be transformed into supervised learning using a sliding-window representation.
-   How to fit, evaluate, and make predictions with an Random Forest regression model for time series forecasting.

Let’s get started.

![Random Forest for Time Series Forecasting](media/Random_Forest_for_Time_Series_Forecasting.jpg)

Random Forest for Time Series Forecasting  
Photo by [IvyMike](https://www.flickr.com/photos/ivymike/3894514618/), some rights reserved.

## Tutorial Overview

This tutorial is divided into three parts; they are:

1.  Random Forest Ensemble
2.  Time Series Data Preparation
3.  Random Forest for Time Series

## Random Forest Ensemble

[Random forest](https://machinelearningmastery.com/random-forest-ensemble-in-python/) is an ensemble of decision tree algorithms.

It is an extension of [bootstrap aggregation (bagging)](https://machinelearningmastery.com/bagging-ensemble-with-python/) of decision trees and can be used for classification and regression problems.

In bagging, a number of decision trees are made where each tree is created from a different bootstrap sample of the training dataset. A [bootstrap sample](https://machinelearningmastery.com/a-gentle-introduction-to-the-bootstrap-method/) is a sample of the training dataset where an example may appear more than once in the sample. This is referred to as “_sampling with replacement_”.

Bagging is an effective ensemble algorithm as each decision tree is fit on a slightly different training dataset, and in turn, has a slightly different performance. Unlike normal decision tree models, such as classification and regression trees (CART), trees used in the ensemble are unpruned, making them slightly overfit to the training dataset. This is desirable as it helps to make each tree more different and have less correlated predictions or prediction errors.

Predictions from the trees are averaged across all decision trees, resulting in better performance than any single tree in the model.

A prediction on a regression problem is the average of the prediction across the trees in the ensemble. A prediction on a classification problem is the majority vote for the class label across the trees in the ensemble.

-   **Regression**: Prediction is the average prediction across the decision trees.
-   **Classification**: Prediction is the majority vote class label predicted across the decision trees.

Random forest involves constructing a large number of decision trees from bootstrap samples from the training dataset, like bagging.

Unlike bagging, random forest also involves selecting a subset of input features (columns or variables) at each split point in the construction of the trees. Typically, constructing a decision tree involves evaluating the value for each input variable in the data in order to select a split point. By reducing the features to a random subset that may be considered at each split point, it forces each decision tree in the ensemble to be more different.

The effect is that the predictions, and in turn, prediction errors, made by each tree in the ensemble are more different or less correlated. When the predictions from these less correlated trees are averaged to make a prediction, it often results in better performance than bagged decision trees.

For more on the Random Forest algorithm, see the tutorial:

-   [How to Develop a Random Forest Ensemble in Python](https://machinelearningmastery.com/random-forest-ensemble-in-python/)

## Time Series Data Preparation

Time series data can be phrased as supervised learning.

Given a sequence of numbers for a time series dataset, we can restructure the data to look like a supervised learning problem. We can do this by using previous time steps as input variables and use the next time step as the output variable.

Let’s make this concrete with an example. Imagine we have a time series as follows:

<table><tbody><tr><td data-settings="show"></td><td><div><p>time, measure</p><p>1, 100</p><p>2, 110</p><p>3, 108</p><p>4, 115</p><p>5, 120</p></div></td></tr></tbody></table>

We can restructure this time series dataset as a supervised learning problem by using the value at the previous time step to predict the value at the next time-step.

Reorganizing the time series dataset this way, the data would look as follows:

<table><tbody><tr><td data-settings="show"></td><td><div><p>X, y</p><p>?, 100</p><p>100, 110</p><p>110, 108</p><p>108, 115</p><p>115, 120</p><p>120, ?</p></div></td></tr></tbody></table>

Note that the time column is dropped and some rows of data are unusable for training a model, such as the first and the last.

This representation is called a sliding window, as the window of inputs and expected outputs is shifted forward through time to create new “_samples_” for a supervised learning model.

For more on the sliding window approach to preparing time series forecasting data, see the tutorial:

-   [Time Series Forecasting as Supervised Learning](https://machinelearningmastery.com/time-series-forecasting-supervised-learning/)

We can use the [shift() function](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.shift.html) in Pandas to automatically create new framings of time series problems given the desired length of input and output sequences.

This would be a useful tool as it would allow us to explore different framings of a time series problem with machine learning algorithms to see which might result in better-performing models.

The function below will take a time series as a NumPy array time series with one or more columns and transform it into a supervised learning problem with the specified number of inputs and outputs.

<table><tbody><tr><td data-settings="show"><div><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p><p>9</p><p>10</p><p>11</p><p>12</p><p>13</p><p>14</p><p>15</p><p>16</p><p>17</p></div></td><td><div><p><span># transform a time series dataset into a supervised learning dataset</span></p><p><span>def </span><span>series_to_supervised</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_in</span><span>=</span><span>1</span><span>,</span><span> </span><span>n_out</span><span>=</span><span>1</span><span>,</span><span> </span><span>dropnan</span><span>=</span><span>True</span><span>)</span><span>:</span></p><p><span></span><span>n_vars</span><span> </span><span>=</span><span> </span><span>1</span><span> </span><span>if</span><span> </span><span>type</span><span>(</span><span>data</span><span>)</span><span> </span><span>is</span><span> </span><span>list </span><span>else</span><span> </span><span>data</span><span>.</span><span>shape</span><span>[</span><span>1</span><span>]</span></p><p><span></span><span>df</span><span> </span><span>=</span><span> </span><span>DataFrame</span><span>(</span><span>data</span><span>)</span></p><p><span></span><span>cols</span><span> </span><span>=</span><span> </span><span>list</span><span>(</span><span>)</span></p><p><span></span><span># input sequence (t-n, ... t-1)</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>n_in</span><span>,</span><span> </span><span>0</span><span>,</span><span> </span><span>-</span><span>1</span><span>)</span><span>:</span></p><p><span></span><span>cols</span><span>.</span><span>append</span><span>(</span><span>df</span><span>.</span><span>shift</span><span>(</span><span>i</span><span>)</span><span>)</span></p><p><span></span><span># forecast sequence (t, t+1, ... t+n)</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>0</span><span>,</span><span> </span><span>n_out</span><span>)</span><span>:</span></p><p><span></span><span>cols</span><span>.</span><span>append</span><span>(</span><span>df</span><span>.</span><span>shift</span><span>(</span><span>-</span><span>i</span><span>)</span><span>)</span></p><p><span></span><span># put it all together</span></p><p><span></span><span>agg</span><span> </span><span>=</span><span> </span><span>concat</span><span>(</span><span>cols</span><span>,</span><span> </span><span>axis</span><span>=</span><span>1</span><span>)</span></p><p><span></span><span># drop rows with NaN values</span></p><p><span></span><span>if</span><span> </span><span>dropnan</span><span>:</span></p><p><span></span><span>agg</span><span>.</span><span>dropna</span><span>(</span><span>inplace</span><span>=</span><span>True</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>agg</span><span>.</span><span>values</span></p></div></td></tr></tbody></table>

We can use this function to prepare a time series dataset for Random Forest.

For more on the step-by-step development of this function, see the tutorial:

-   [How to Convert a Time Series to a Supervised Learning Problem in Python](https://machinelearningmastery.com/convert-time-series-supervised-learning-problem-python/)

Once the dataset is prepared, we must be careful in how it is used to fit and evaluate a model.

For example, it would not be valid to fit the model on data from the future and have it predict the past. The model must be trained on the past and predict the future.

This means that methods that randomize the dataset during evaluation, like [k-fold cross-validation](https://machinelearningmastery.com/k-fold-cross-validation/), cannot be used. Instead, we must use a technique called walk-forward validation.

In walk-forward validation, the dataset is first split into train and test sets by selecting a cut point, e.g. all data except the last 12 months is used for training and the last 12 months is used for testing.

If we are interested in making a one-step forecast, e.g. one month, then we can evaluate the model by training on the training dataset and predicting the first step in the test dataset. We can then add the real observation from the test set to the training dataset, refit the model, then have the model predict the second step in the test dataset.

Repeating this process for the entire test dataset will give a one-step prediction for the entire test dataset from which an error measure can be calculated to evaluate the skill of the model.

For more on walk-forward validation, see the tutorial:

-   [How to Backtest Machine Learning Models for Time Series Forecasting](https://machinelearningmastery.com/backtest-machine-learning-models-time-series-forecasting/)

The function below performs walk-forward validation.

It takes the entire supervised learning version of the time series dataset and the number of rows to use as the test set as arguments.

It then steps through the test set, calling the _random\_forest\_forecast()_ function to make a one-step forecast. An error measure is calculated and the details are returned for analysis.

<table><tbody><tr><td data-settings="show"><div><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p><p>9</p><p>10</p><p>11</p><p>12</p><p>13</p><p>14</p><p>15</p><p>16</p><p>17</p><p>18</p><p>19</p><p>20</p><p>21</p><p>22</p></div></td><td><div><p><span># walk-forward validation for univariate data</span></p><p><span>def </span><span>walk_forward_validation</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_test</span><span>)</span><span>:</span></p><p><span></span><span>predictions</span><span> </span><span>=</span><span> </span><span>list</span><span>(</span><span>)</span></p><p><span></span><span># split dataset</span></p><p><span></span><span>train</span><span>,</span><span> </span><span>test</span><span> </span><span>=</span><span> </span><span>train_test_split</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_test</span><span>)</span></p><p><span></span><span># seed history with training dataset</span></p><p><span></span><span>history</span><span> </span><span>=</span><span> </span><span>[</span><span>x</span><span> </span><span>for</span><span> </span><span>x</span><span> </span><span>in</span><span> </span><span>train</span><span>]</span></p><p><span></span><span># step over each time-step in the test set</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>len</span><span>(</span><span>test</span><span>)</span><span>)</span><span>:</span></p><p><span></span><span># split test row into input and output columns</span></p><p><span></span><span>testX</span><span>,</span><span> </span><span>testy</span><span> </span><span>=</span><span> </span><span>test</span><span>[</span><span>i</span><span>,</span><span> </span><span>:</span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>test</span><span>[</span><span>i</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span></p><p><span></span><span># fit model on history and make a prediction</span></p><p><span></span><span>yhat</span><span> </span><span>=</span><span> </span><span>random_forest_forecast</span><span>(</span><span>history</span><span>,</span><span> </span><span>testX</span><span>)</span></p><p><span></span><span># store forecast in list of predictions</span></p><p><span></span><span>predictions</span><span>.</span><span>append</span><span>(</span><span>yhat</span><span>)</span></p><p><span></span><span># add actual observation to history for the next loop</span></p><p><span></span><span>history</span><span>.</span><span>append</span><span>(</span><span>test</span><span>[</span><span>i</span><span>]</span><span>)</span></p><p><span></span><span># summarize progress</span></p><p><span></span><span>print</span><span>(</span><span>'&gt;expected=%.1f, predicted=%.1f'</span><span> </span><span>%</span><span> </span><span>(</span><span>testy</span><span>,</span><span> </span><span>yhat</span><span>)</span><span>)</span></p><p><span></span><span># estimate prediction error</span></p><p><span></span><span>error</span><span> </span><span>=</span><span> </span><span>mean_absolute_error</span><span>(</span><span>test</span><span>[</span><span>:</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>predictions</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>error</span><span>,</span><span> </span><span>test</span><span>[</span><span>:</span><span>,</span><span> </span><span>1</span><span>]</span><span>,</span><span> </span><span>predictions</span></p></div></td></tr></tbody></table>

The _train\_test\_split()_ function is called to split the dataset into train and test sets.

We can define this function below.

<table><tbody><tr><td data-settings="show"></td><td><div><p><span># split a univariate dataset into train/test sets</span></p><p><span>def </span><span>train_test_split</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_test</span><span>)</span><span>:</span></p><p><span></span><span>return</span><span> </span><span>data</span><span>[</span><span>:</span><span>-</span><span>n_test</span><span>,</span><span> </span><span>:</span><span>]</span><span>,</span><span> </span><span>data</span><span>[</span><span>-</span><span>n_test</span><span>:</span><span>,</span><span> </span><span>:</span><span>]</span></p></div></td></tr></tbody></table>

We can use the [RandomForestRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html) class to make a one-step forecast.

The _random\_forest\_forecast()_ function below implements this, taking the training dataset and test input row as input, fitting a model and making a one-step prediction.

<table><tbody><tr><td data-settings="show"></td><td><div><p><span># fit an random forest model and make a one step prediction</span></p><p><span>def </span><span>random_forest_forecast</span><span>(</span><span>train</span><span>,</span><span> </span><span>testX</span><span>)</span><span>:</span></p><p><span></span><span># transform list into array</span></p><p><span></span><span>train</span><span> </span><span>=</span><span> </span><span>asarray</span><span>(</span><span>train</span><span>)</span></p><p><span></span><span># split into input and output columns</span></p><p><span></span><span>trainX</span><span>,</span><span> </span><span>trainy</span><span> </span><span>=</span><span> </span><span>train</span><span>[</span><span>:</span><span>,</span><span> </span><span>:</span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>train</span><span>[</span><span>:</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span></p><p><span></span><span># fit model</span></p><p><span></span><span>model</span><span> </span><span>=</span><span> </span><span>RandomForestRegressor</span><span>(</span><span>n_estimators</span><span>=</span><span>1000</span><span>)</span></p><p><span></span><span>model</span><span>.</span><span>fit</span><span>(</span><span>trainX</span><span>,</span><span> </span><span>trainy</span><span>)</span></p><p><span></span><span># make a one-step prediction</span></p><p><span></span><span>yhat</span><span> </span><span>=</span><span> </span><span>model</span><span>.</span><span>predict</span><span>(</span><span>[</span><span>testX</span><span>]</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>yhat</span><span>[</span><span>0</span><span>]</span></p></div></td></tr></tbody></table>

Now that we know how to prepare time series data for forecasting and evaluate a Random Forest model, next we can look at using Random Forest on a real dataset.

## Random Forest for Time Series

In this section, we will explore how to use the Random Forest regressor for time series forecasting.

We will use a standard univariate time series dataset with the intent of using the model to make a one-step forecast.

You can use the code in this section as the starting point in your own project and easily adapt it for multivariate inputs, multivariate forecasts, and multi-step forecasts.

We will use the daily female births dataset, that is the monthly births across three years.

You can download the dataset from here, place it in your current working directory with the filename “_daily-total-female-births.csv_“.

-   [Dataset (daily-total-female-births.csv)](https://raw.githubusercontent.com/jbrownlee/Datasets/master/daily-total-female-births.csv)
-   [Description (daily-total-female-births.names)](https://raw.githubusercontent.com/jbrownlee/Datasets/master/daily-total-female-births.names)

The first few lines of the dataset look as follows:

<table><tbody><tr><td data-settings="show"></td><td><div><p>"Date","Births"</p><p>"1959-01-01",35</p><p>"1959-01-02",32</p><p>"1959-01-03",30</p><p>"1959-01-04",31</p><p>"1959-01-05",44</p><p>...</p></div></td></tr></tbody></table>

First, let’s load and plot the dataset.

The complete example is listed below.

<table><tbody><tr><td data-settings="show"></td><td><div><p><span># load and plot the time series dataset</span></p><p><span>from </span><span>pandas </span><span>import </span><span>read_csv</span></p><p><span>from </span><span>matplotlib </span><span>import </span><span>pyplot</span></p><p><span># load dataset</span></p><p><span>series</span><span> </span><span>=</span><span> </span><span>read_csv</span><span>(</span><span>'daily-total-female-births.csv'</span><span>,</span><span> </span><span>header</span><span>=</span><span>0</span><span>,</span><span> </span><span>index_col</span><span>=</span><span>0</span><span>)</span></p><p><span>values</span><span> </span><span>=</span><span> </span><span>series</span><span>.</span><span>values</span></p><p><span># plot dataset</span></p><p><span>pyplot</span><span>.</span><span>plot</span><span>(</span><span>values</span><span>)</span></p><p><span>pyplot</span><span>.</span><span>show</span><span>(</span><span>)</span></p></div></td></tr></tbody></table>

Running the example creates a line plot of the dataset.

We can see there is no obvious trend or seasonality.

![Line Plot of Monthly Births Time Series Dataset](media/Line_Plot_of_Monthly_Births_Time_Series_Dataset.png)

Line Plot of Monthly Births Time Series Dataset

A persistence model can achieve a MAE of about 6.7 births when predicting the last 12 months. This provides a baseline in performance above which a model may be considered skillful.

Next, we can evaluate the Random Forest model on the dataset when making one-step forecasts for the last 12 months of data.

We will use only the previous six time steps as input to the model and default model hyperparameters, except we will use 1,000 trees in the ensemble (to avoid underlearning).

The complete example is listed below.

<table><tbody><tr><td data-settings="show"><div><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p><p>9</p><p>10</p><p>11</p><p>12</p><p>13</p><p>14</p><p>15</p><p>16</p><p>17</p><p>18</p><p>19</p><p>20</p><p>21</p><p>22</p><p>23</p><p>24</p><p>25</p><p>26</p><p>27</p><p>28</p><p>29</p><p>30</p><p>31</p><p>32</p><p>33</p><p>34</p><p>35</p><p>36</p><p>37</p><p>38</p><p>39</p><p>40</p><p>41</p><p>42</p><p>43</p><p>44</p><p>45</p><p>46</p><p>47</p><p>48</p><p>49</p><p>50</p><p>51</p><p>52</p><p>53</p><p>54</p><p>55</p><p>56</p><p>57</p><p>58</p><p>59</p><p>60</p><p>61</p><p>62</p><p>63</p><p>64</p><p>65</p><p>66</p><p>67</p><p>68</p><p>69</p><p>70</p><p>71</p><p>72</p><p>73</p><p>74</p><p>75</p><p>76</p><p>77</p><p>78</p><p>79</p><p>80</p></div></td><td><div><p><span># forecast monthly births with random forest</span></p><p><span>from </span><span>numpy </span><span>import </span><span>asarray</span></p><p><span>from </span><span>pandas </span><span>import </span><span>read_csv</span></p><p><span>from </span><span>pandas </span><span>import </span><span>DataFrame</span></p><p><span>from </span><span>pandas </span><span>import </span><span>concat</span></p><p><span>from </span><span>sklearn</span><span>.</span><span>metrics </span><span>import </span><span>mean_absolute_error</span></p><p><span>from </span><span>sklearn</span><span>.</span><span>ensemble </span><span>import </span><span>RandomForestRegressor</span></p><p><span>from </span><span>matplotlib </span><span>import </span><span>pyplot</span></p><p><span># transform a time series dataset into a supervised learning dataset</span></p><p><span>def </span><span>series_to_supervised</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_in</span><span>=</span><span>1</span><span>,</span><span> </span><span>n_out</span><span>=</span><span>1</span><span>,</span><span> </span><span>dropnan</span><span>=</span><span>True</span><span>)</span><span>:</span></p><p><span></span><span>n_vars</span><span> </span><span>=</span><span> </span><span>1</span><span> </span><span>if</span><span> </span><span>type</span><span>(</span><span>data</span><span>)</span><span> </span><span>is</span><span> </span><span>list </span><span>else</span><span> </span><span>data</span><span>.</span><span>shape</span><span>[</span><span>1</span><span>]</span></p><p><span></span><span>df</span><span> </span><span>=</span><span> </span><span>DataFrame</span><span>(</span><span>data</span><span>)</span></p><p><span></span><span>cols</span><span> </span><span>=</span><span> </span><span>list</span><span>(</span><span>)</span></p><p><span></span><span># input sequence (t-n, ... t-1)</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>n_in</span><span>,</span><span> </span><span>0</span><span>,</span><span> </span><span>-</span><span>1</span><span>)</span><span>:</span></p><p><span></span><span>cols</span><span>.</span><span>append</span><span>(</span><span>df</span><span>.</span><span>shift</span><span>(</span><span>i</span><span>)</span><span>)</span></p><p><span></span><span># forecast sequence (t, t+1, ... t+n)</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>0</span><span>,</span><span> </span><span>n_out</span><span>)</span><span>:</span></p><p><span></span><span>cols</span><span>.</span><span>append</span><span>(</span><span>df</span><span>.</span><span>shift</span><span>(</span><span>-</span><span>i</span><span>)</span><span>)</span></p><p><span></span><span># put it all together</span></p><p><span></span><span>agg</span><span> </span><span>=</span><span> </span><span>concat</span><span>(</span><span>cols</span><span>,</span><span> </span><span>axis</span><span>=</span><span>1</span><span>)</span></p><p><span></span><span># drop rows with NaN values</span></p><p><span></span><span>if</span><span> </span><span>dropnan</span><span>:</span></p><p><span></span><span>agg</span><span>.</span><span>dropna</span><span>(</span><span>inplace</span><span>=</span><span>True</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>agg</span><span>.</span><span>values</span></p><p><span># split a univariate dataset into train/test sets</span></p><p><span>def </span><span>train_test_split</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_test</span><span>)</span><span>:</span></p><p><span></span><span>return</span><span> </span><span>data</span><span>[</span><span>:</span><span>-</span><span>n_test</span><span>,</span><span> </span><span>:</span><span>]</span><span>,</span><span> </span><span>data</span><span>[</span><span>-</span><span>n_test</span><span>:</span><span>,</span><span> </span><span>:</span><span>]</span></p><p><span># fit an random forest model and make a one step prediction</span></p><p><span>def </span><span>random_forest_forecast</span><span>(</span><span>train</span><span>,</span><span> </span><span>testX</span><span>)</span><span>:</span></p><p><span></span><span># transform list into array</span></p><p><span></span><span>train</span><span> </span><span>=</span><span> </span><span>asarray</span><span>(</span><span>train</span><span>)</span></p><p><span></span><span># split into input and output columns</span></p><p><span></span><span>trainX</span><span>,</span><span> </span><span>trainy</span><span> </span><span>=</span><span> </span><span>train</span><span>[</span><span>:</span><span>,</span><span> </span><span>:</span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>train</span><span>[</span><span>:</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span></p><p><span></span><span># fit model</span></p><p><span></span><span>model</span><span> </span><span>=</span><span> </span><span>RandomForestRegressor</span><span>(</span><span>n_estimators</span><span>=</span><span>1000</span><span>)</span></p><p><span></span><span>model</span><span>.</span><span>fit</span><span>(</span><span>trainX</span><span>,</span><span> </span><span>trainy</span><span>)</span></p><p><span></span><span># make a one-step prediction</span></p><p><span></span><span>yhat</span><span> </span><span>=</span><span> </span><span>model</span><span>.</span><span>predict</span><span>(</span><span>[</span><span>testX</span><span>]</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>yhat</span><span>[</span><span>0</span><span>]</span></p><p><span># walk-forward validation for univariate data</span></p><p><span>def </span><span>walk_forward_validation</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_test</span><span>)</span><span>:</span></p><p><span></span><span>predictions</span><span> </span><span>=</span><span> </span><span>list</span><span>(</span><span>)</span></p><p><span></span><span># split dataset</span></p><p><span></span><span>train</span><span>,</span><span> </span><span>test</span><span> </span><span>=</span><span> </span><span>train_test_split</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_test</span><span>)</span></p><p><span></span><span># seed history with training dataset</span></p><p><span></span><span>history</span><span> </span><span>=</span><span> </span><span>[</span><span>x</span><span> </span><span>for</span><span> </span><span>x</span><span> </span><span>in</span><span> </span><span>train</span><span>]</span></p><p><span></span><span># step over each time-step in the test set</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>len</span><span>(</span><span>test</span><span>)</span><span>)</span><span>:</span></p><p><span></span><span># split test row into input and output columns</span></p><p><span></span><span>testX</span><span>,</span><span> </span><span>testy</span><span> </span><span>=</span><span> </span><span>test</span><span>[</span><span>i</span><span>,</span><span> </span><span>:</span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>test</span><span>[</span><span>i</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span></p><p><span></span><span># fit model on history and make a prediction</span></p><p><span></span><span>yhat</span><span> </span><span>=</span><span> </span><span>random_forest_forecast</span><span>(</span><span>history</span><span>,</span><span> </span><span>testX</span><span>)</span></p><p><span></span><span># store forecast in list of predictions</span></p><p><span></span><span>predictions</span><span>.</span><span>append</span><span>(</span><span>yhat</span><span>)</span></p><p><span></span><span># add actual observation to history for the next loop</span></p><p><span></span><span>history</span><span>.</span><span>append</span><span>(</span><span>test</span><span>[</span><span>i</span><span>]</span><span>)</span></p><p><span></span><span># summarize progress</span></p><p><span></span><span>print</span><span>(</span><span>'&gt;expected=%.1f, predicted=%.1f'</span><span> </span><span>%</span><span> </span><span>(</span><span>testy</span><span>,</span><span> </span><span>yhat</span><span>)</span><span>)</span></p><p><span></span><span># estimate prediction error</span></p><p><span></span><span>error</span><span> </span><span>=</span><span> </span><span>mean_absolute_error</span><span>(</span><span>test</span><span>[</span><span>:</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>predictions</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>error</span><span>,</span><span> </span><span>test</span><span>[</span><span>:</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>predictions</span></p><p><span># load the dataset</span></p><p><span>series</span><span> </span><span>=</span><span> </span><span>read_csv</span><span>(</span><span>'daily-total-female-births.csv'</span><span>,</span><span> </span><span>header</span><span>=</span><span>0</span><span>,</span><span> </span><span>index_col</span><span>=</span><span>0</span><span>)</span></p><p><span>values</span><span> </span><span>=</span><span> </span><span>series</span><span>.</span><span>values</span></p><p><span># transform the time series data into supervised learning</span></p><p><span>data</span><span> </span><span>=</span><span> </span><span>series_to_supervised</span><span>(</span><span>values</span><span>,</span><span> </span><span>n_in</span><span>=</span><span>6</span><span>)</span></p><p><span># evaluate</span></p><p><span>mae</span><span>,</span><span> </span><span>y</span><span>,</span><span> </span><span>yhat</span><span> </span><span>=</span><span> </span><span>walk_forward_validation</span><span>(</span><span>data</span><span>,</span><span> </span><span>12</span><span>)</span></p><p><span>print</span><span>(</span><span>'MAE: %.3f'</span><span> </span><span>%</span><span> </span><span>mae</span><span>)</span></p><p><span># plot expected vs predicted</span></p><p><span>pyplot</span><span>.</span><span>plot</span><span>(</span><span>y</span><span>,</span><span> </span><span>label</span><span>=</span><span>'Expected'</span><span>)</span></p><p><span>pyplot</span><span>.</span><span>plot</span><span>(</span><span>yhat</span><span>,</span><span> </span><span>label</span><span>=</span><span>'Predicted'</span><span>)</span></p><p><span>pyplot</span><span>.</span><span>legend</span><span>(</span><span>)</span></p><p><span>pyplot</span><span>.</span><span>show</span><span>(</span><span>)</span></p></div></td></tr></tbody></table>

Running the example reports the expected and predicted values for each step in the test set, then the MAE for all predicted values.

**Note**: Your [results may vary](https://machinelearningmastery.com/different-results-each-time-in-machine-learning/) given the stochastic nature of the algorithm or evaluation procedure, or differences in numerical precision. Consider running the example a few times and compare the average outcome.

We can see that the model performs better than a persistence model, achieving a MAE of about 5.9 births, compared to 6.7 births.

**Can you do better?**

You can test different Random Forest hyperparameters and numbers of time steps as input to see if you can achieve better performance. Share your results in the comments below.

<table><tbody><tr><td data-settings="show"></td><td><div><p>&gt;expected=42.0, predicted=45.0</p><p>&gt;expected=53.0, predicted=43.7</p><p>&gt;expected=39.0, predicted=41.4</p><p>&gt;expected=40.0, predicted=38.1</p><p>&gt;expected=38.0, predicted=42.6</p><p>&gt;expected=44.0, predicted=48.7</p><p>&gt;expected=34.0, predicted=42.7</p><p>&gt;expected=37.0, predicted=37.0</p><p>&gt;expected=52.0, predicted=38.4</p><p>&gt;expected=48.0, predicted=41.4</p><p>&gt;expected=55.0, predicted=43.7</p><p>&gt;expected=50.0, predicted=45.3</p><p>MAE: 5.905</p></div></td></tr></tbody></table>

A line plot is created comparing the series of expected values and predicted values for the last 12 months of the dataset.

This gives a geometric interpretation of how well the model performed on the test set.

![Line Plot of Expected vs. Births Predicted Using Random Forest](media/Line_Plot_of_Expected_vs._Births_Predicted_Using_Random_Forest.png)

Line Plot of Expected vs. Births Predicted Using Random Forest

Once a final Random Forest model configuration is chosen, a model can be finalized and used to make a prediction on new data.

This is called an out-of-sample forecast, e.g. predicting beyond the training dataset. This is identical to making a prediction during the evaluation of the model, as we always want to evaluate a model using the same procedure that we expect to use when the model is used to make predictions on new data.

The example below demonstrates fitting a final Random Forest model on all available data and making a one-step prediction beyond the end of the dataset.

<table><tbody><tr><td data-settings="show"><div><p>1</p><p>2</p><p>3</p><p>4</p><p>5</p><p>6</p><p>7</p><p>8</p><p>9</p><p>10</p><p>11</p><p>12</p><p>13</p><p>14</p><p>15</p><p>16</p><p>17</p><p>18</p><p>19</p><p>20</p><p>21</p><p>22</p><p>23</p><p>24</p><p>25</p><p>26</p><p>27</p><p>28</p><p>29</p><p>30</p><p>31</p><p>32</p><p>33</p><p>34</p><p>35</p><p>36</p><p>37</p><p>38</p><p>39</p><p>40</p></div></td><td><div><p><span># finalize model and make a prediction for monthly births with random forest</span></p><p><span>from </span><span>numpy </span><span>import </span><span>asarray</span></p><p><span>from </span><span>pandas </span><span>import </span><span>read_csv</span></p><p><span>from </span><span>pandas </span><span>import </span><span>DataFrame</span></p><p><span>from </span><span>pandas </span><span>import </span><span>concat</span></p><p><span>from </span><span>sklearn</span><span>.</span><span>ensemble </span><span>import </span><span>RandomForestRegressor</span></p><p><span># transform a time series dataset into a supervised learning dataset</span></p><p><span>def </span><span>series_to_supervised</span><span>(</span><span>data</span><span>,</span><span> </span><span>n_in</span><span>=</span><span>1</span><span>,</span><span> </span><span>n_out</span><span>=</span><span>1</span><span>,</span><span> </span><span>dropnan</span><span>=</span><span>True</span><span>)</span><span>:</span></p><p><span></span><span>n_vars</span><span> </span><span>=</span><span> </span><span>1</span><span> </span><span>if</span><span> </span><span>type</span><span>(</span><span>data</span><span>)</span><span> </span><span>is</span><span> </span><span>list </span><span>else</span><span> </span><span>data</span><span>.</span><span>shape</span><span>[</span><span>1</span><span>]</span></p><p><span></span><span>df</span><span> </span><span>=</span><span> </span><span>DataFrame</span><span>(</span><span>data</span><span>)</span></p><p><span></span><span>cols</span><span> </span><span>=</span><span> </span><span>list</span><span>(</span><span>)</span></p><p><span></span><span># input sequence (t-n, ... t-1)</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>n_in</span><span>,</span><span> </span><span>0</span><span>,</span><span> </span><span>-</span><span>1</span><span>)</span><span>:</span></p><p><span></span><span>cols</span><span>.</span><span>append</span><span>(</span><span>df</span><span>.</span><span>shift</span><span>(</span><span>i</span><span>)</span><span>)</span></p><p><span></span><span># forecast sequence (t, t+1, ... t+n)</span></p><p><span></span><span>for</span><span> </span><span>i</span><span> </span><span>in</span><span> </span><span>range</span><span>(</span><span>0</span><span>,</span><span> </span><span>n_out</span><span>)</span><span>:</span></p><p><span></span><span>cols</span><span>.</span><span>append</span><span>(</span><span>df</span><span>.</span><span>shift</span><span>(</span><span>-</span><span>i</span><span>)</span><span>)</span></p><p><span></span><span># put it all together</span></p><p><span></span><span>agg</span><span> </span><span>=</span><span> </span><span>concat</span><span>(</span><span>cols</span><span>,</span><span> </span><span>axis</span><span>=</span><span>1</span><span>)</span></p><p><span></span><span># drop rows with NaN values</span></p><p><span></span><span>if</span><span> </span><span>dropnan</span><span>:</span></p><p><span></span><span>agg</span><span>.</span><span>dropna</span><span>(</span><span>inplace</span><span>=</span><span>True</span><span>)</span></p><p><span></span><span>return</span><span> </span><span>agg</span><span>.</span><span>values</span></p><p><span># load the dataset</span></p><p><span>series</span><span> </span><span>=</span><span> </span><span>read_csv</span><span>(</span><span>'daily-total-female-births.csv'</span><span>,</span><span> </span><span>header</span><span>=</span><span>0</span><span>,</span><span> </span><span>index_col</span><span>=</span><span>0</span><span>)</span></p><p><span>values</span><span> </span><span>=</span><span> </span><span>series</span><span>.</span><span>values</span></p><p><span># transform the time series data into supervised learning</span></p><p><span>train</span><span> </span><span>=</span><span> </span><span>series_to_supervised</span><span>(</span><span>values</span><span>,</span><span> </span><span>n_in</span><span>=</span><span>6</span><span>)</span></p><p><span># split into input and output columns</span></p><p><span>trainX</span><span>,</span><span> </span><span>trainy</span><span> </span><span>=</span><span> </span><span>train</span><span>[</span><span>:</span><span>,</span><span> </span><span>:</span><span>-</span><span>1</span><span>]</span><span>,</span><span> </span><span>train</span><span>[</span><span>:</span><span>,</span><span> </span><span>-</span><span>1</span><span>]</span></p><p><span># fit model</span></p><p><span>model</span><span> </span><span>=</span><span> </span><span>RandomForestRegressor</span><span>(</span><span>n_estimators</span><span>=</span><span>1000</span><span>)</span></p><p><span>model</span><span>.</span><span>fit</span><span>(</span><span>trainX</span><span>,</span><span> </span><span>trainy</span><span>)</span></p><p><span># construct an input for a new prediction</span></p><p><span>row</span><span> </span><span>=</span><span> </span><span>values</span><span>[</span><span>-</span><span>6</span><span>:</span><span>]</span><span>.</span><span>flatten</span><span>(</span><span>)</span></p><p><span># make a one-step prediction</span></p><p><span>yhat</span><span> </span><span>=</span><span> </span><span>model</span><span>.</span><span>predict</span><span>(</span><span>asarray</span><span>(</span><span>[</span><span>row</span><span>]</span><span>)</span><span>)</span></p><p><span>print</span><span>(</span><span>'Input: %s, Predicted: %.3f'</span><span> </span><span>%</span><span> </span><span>(</span><span>row</span><span>,</span><span> </span><span>yhat</span><span>[</span><span>0</span><span>]</span><span>)</span><span>)</span></p></div></td></tr></tbody></table>

Running the example fits an Random Forest model on all available data.

A new row of input is prepared using the last six months of known data and the next month beyond the end of the dataset is predicted.

<table><tbody><tr><td data-settings="show"></td><td><div><p>Input: [34 37 52 48 55 50], Predicted: 43.053</p></div></td></tr></tbody></table>

## Further Reading

This section provides more resources on the topic if you are looking to go deeper.

### Tutorials

-   [How to Develop a Random Forest Ensemble in Python](https://machinelearningmastery.com/random-forest-ensemble-in-python/)
-   [Time Series Forecasting as Supervised Learning](https://machinelearningmastery.com/time-series-forecasting-supervised-learning/)
-   [How to Convert a Time Series to a Supervised Learning Problem in Python](https://machinelearningmastery.com/convert-time-series-supervised-learning-problem-python/)
-   [How To Backtest Machine Learning Models for Time Series Forecasting](https://machinelearningmastery.com/backtest-machine-learning-models-time-series-forecasting/)

### APIs

-   [sklearn.ensemble.RandomForestRegressor API](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html).

## Summary

In this tutorial, you discovered how to develop a Random Forest model for time series forecasting.

Specifically, you learned:

-   Random Forest is an ensemble of decision trees algorithms that can be used for classification and regression predictive modeling.
-   Time series datasets can be transformed into supervised learning using a sliding-window representation.
-   How to fit, evaluate, and make predictions with an Random Forest regression model for time series forecasting.

**Do you have any questions?**  
Ask your questions in the comments below and I will do my best to answer.

## Want to Develop Time Series Forecasts with Python?

[![Introduction to Time Series Forecasting With Python](media/Introduction_to_Time_Series_Forecasting_With_Python.png)](https://machinelearningmastery.com/wp-content/uploads/2022/11/TSFP-220.png)

#### Develop Your Own Forecasts in Minutes

...with just a few lines of python code

Discover how in my new Ebook:  
[Introduction to Time Series Forecasting With Python](https://machinelearningmastery.com/introduction-to-time-series-forecasting-with-python/)

It covers **self-study tutorials** and **end-to-end projects** on topics like: _Loading data, visualization, modeling, algorithm tuning,_ and much more...

#### Finally Bring Time Series Forecasting to  
Your Own Projects

Skip the Academics. Just Results.

[See What's Inside](https://machinelearningmastery.com/introduction-to-time-series-forecasting-with-python/)