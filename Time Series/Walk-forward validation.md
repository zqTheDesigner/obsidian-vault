---
to-read: https://machinelearningmastery.com/backtest-machine-learning-models-time-series-forecasting/
---

- Initialize the train array with train_test_split. 
- For each round of iteration, train array will be used to predict only 1 item in the test data. 
- Once the test data been predicted, we removed that data from test data set, and add the actual data to train data set
- Repeat above steps till run out of test data. 

Reason we do this is because the random forest have to know the latest step and do the prediction, if we just train the model once and try to use the model predict all the rest of test data, the error rate will gets higher and higher. 

