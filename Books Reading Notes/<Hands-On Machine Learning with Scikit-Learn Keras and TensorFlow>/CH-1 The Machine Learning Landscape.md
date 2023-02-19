---
tags: machine-learning
---

First machine learning application - spam filter (1990s)


A computer program is said to learn from experience E with respect to some task T and some performance measure P. If it's performance on task T, as measured by P improves with experience E.

[[Data mining]] - dig into large amount of data and discover patterns that were not immediately apparent. 

Machine Learning is great for
- Problems for which existing solutions require a lot of fine-tuning or long lists of rules. (Machine Learning algorithm can often simplify the code and perform better than the traditional approach)
- Complex problems for which using a traditional approach yields no good solutions, Machine Learning technique can perhaps find a solution.
- Fluctuating environments: a Machine Learning system can adapt to new data.
- Getting insights about complex problems and large amounts of data. 

# Machine Learning
- Making machines get better at some task by learning from data instead of code rules
- Types pf ML systems: Supervised or not, bath or online, instance based or model based
- Gather data -> Feed training data to learning algorithms
- Simple model -> underfit
- Complex model -> overfit 

# Types of Machine Learning Systems

## [[Supervised Learning|Supervised]] vs [[Unsupervised Learning|unsupervised]] learning
whether they are trained with human supervision
- [[Supervised Learning]]
	- The training set you feed to the algorithm includes the desired solution (Labels)
	- Typical supervised learning task is [[Classification]]
	- To predict a target numeric value - [[Regression]]
	- [[KNN|k-Nearest Neighbors]]
	- [[Linear Regression]]
	- Logistic Regression
	- Support Vector Machines (SVMs)
	- Decision Trees and Random Forests
	- Neural Networks
- [[Unsupervised Learning]]
	- The training data is unlabeled, they system tries to learn without a teacher. 
	- Clustering
		- K-Means
		- DBSCAN
		- Hierarchical Cluster Analysis (HCA)
	- [[Anomaly detection]] and [[Novelty detection]]
		- One-class SVM
		- Isolation Forest
	- Visualization and [[Dimensionality Reduction]]
		- Principal Component Analysis (PCA)
		- Kernel PCA
		- Locally Linear Embedding (LLE)
		- t-Distributed Stochastic Neighbor Embedding (t-SNE)
	- [[Association rule learning]]
		- Apriori
		- Eclat
- [[Semi-supervised learning]]
	- Deal with partially labeled data. 
	- Most semi-supervised learning algorithms are combination of unsupervised and supervised algorithms.
	- Deep belief networks (DBNs) are based on Restricted Boltzmann Machines (RBMs, unsupervised learning) stacked on top of each other. 
- [[Reinforcement Learning]]

## Online vs batch learning 
- Learn incrementally or on the fly.
- Whether or not the system can learn incrementally from a stream of incoming data. 
#### [[Batch learning]]
- System is not capable for learning incrementally. It must be trained using all the available data. 
- If you want a batch learning system to know about new data, a new system (with both new data and old data) have to be trained together from scratch then stop the old system and replace it with the new one.  
- 
#### [[Online Learning]] (Incremental learning)
- Train the system incrementally by feeding it data instances sequentially (either individually or in small groups - mini-batches).
- Online learning is great for systems that receive data as a continuous flow. 
- Can also be used to train systems on huge datasets that cannot fit in one machine's main memory. (out-of-core learning)
- Learning-rate - how fast they should adapt to changing data.
- Higher learning rate make least new data faster and also forget older data faster. 
- Bad data fed to system will decline the performance from time to time.

## Instance based vs model-based learning 
- Compare new data points with known data points or detecting patterns in the training data and building a predictive model
- How they generalize (make predictions for examples it has never seen before)
#### [[Instance-based Learning]]
- Requires a measure of similarity.
- The system learns the examples by heart(??)
#### [[Model-based Learning]]
- Training a model means running an algorithm to find the model parameters that will make it best fit the training data. 
- 

# Main Challenges of Machine Learning

## Insufficient Quality of Training Data (Bad data)
- Very different machine learning algorithms performed almost identically well on a complex problem of natural language disambiguation once they they were given enough data. 
- The unreasonable effectiveness of data (More data always provide better quality predictions)

## Non-representative Training Data
- Training data must be representative of the new cases you want to generalize to. 
- Use a training set that is representative of the cases you want to generalize 
- Smaller samples - [[Sampling Noise]]
- Very large simple but non-representative, or simple method is flowed - [[Sampling Bias]] 

## Poor-Quality Data
- Discard the clearly outliers (or fix the errors manually)
- For missing data - ignore attribute/ Ignore instance/ Filling the missing values/ Train one model with and one without the feature

## Irrelevant Features
- Training data have to contain enough relevant features and not too many irrelevant ones. 
- To come up with a good set of features to train on - [[Feature Engineering]]
	- Feature selection (Select the most useful features to train on among existing features)
	- Feature extraction (Combining existing features to product a more useful one ([[Dimensionality Reduction]]))
	- Creating new features by gathering new data

## [[Overfitting|Over fitting]] the Training Data
- Overgeneralizing 
- When the model performs well on the training data, but doesn't generalize well. 
- Simplify the mode by selecting fewer parameters can reduce the overfitting
- Constraining a model to make it simpler and reduce the risk of overfitting - #regularization 
- The amount of regularization to apply during learning can be controlled by a hyper-parameter
- A [[Hyperparameter Tuning | Hyperparameter]] is a parameter of a learning algorithm (not of the model)

## Under-fitting the Training Data 
- Occurs when the model is too simple to learn the underlying structure of the data
- To fix the under-fitting problem:
	- Select a more powerful model. with more parameters
	- Feed better features to the learning algorithm 
	- Reduce the constrains on the model

# Testing and [[Validating]]
- To know how well a model will generalize to new cases
- Split data in to training set and testing set
- The error rate of new cases is called generalization error (out-of-sample error / test error)
- Evaluate how model will perform on instances it has never seen before
- If the training error is low, but the generalization error (test error) is high, it means the model is [[Overfitting|overfitting]] the training data. 
- Training error : calculating the error of a model on the same data the model was trained on

## [[Hyperparameter]] Tuning and Model Selection
- To choose between multiple models - train multiple models and choose the model with lower generalization error
- When apply regularization to avoid overfitting, the regularization applied may just suitable for the particular set (unlikely perform as well on new data)
- Holdout validation: Hold out part of the training set to evaluate several candidate models and select the best one. 

## Data Mismatch
- Testing and validation data must be as representative as possible of the data in production
- Train-dev set: holds some training data to validate of the model is over fitting. 
- If performs well on train-dev set - not overfitting. If performs poorly on validation set - data mismatch 