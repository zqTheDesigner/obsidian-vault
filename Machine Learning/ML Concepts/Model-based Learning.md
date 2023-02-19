Related to [[Instance-based Learning]]

> Model based learning search for an optimal value for the [[Model Parameters]] such that the model will generalize well to new instances

- The system uses a model
- Build (train) a model - It learns when the model is trained with data (predictors, examples)
- The model performance is evaluated
- Use the model to make prediction

## Process
- Study the data
- Select a model
- Train the model on the training data (predictors, examples)
	- Train by minimizing the [[Model-based Learning#Cost function - Measure how bad the model performs|Cost function]] 
- Apply the model to make predictions 

## Pros & Corns
- Model can be stored / saved
- No need to use all data
- Takes less space than all data
- The model is faster making predictions
- Needs to train and evaluate a model

## Model selection


## Define the parameter value with a performance measure

#### Utility function - Measure how good the model performs

#### Cost function - Measure how bad the model performs
- For [[Linear Regression]] use a cost function that measures the distance between the linear model's prediction and the training examples (to minimize the distance)
- 
