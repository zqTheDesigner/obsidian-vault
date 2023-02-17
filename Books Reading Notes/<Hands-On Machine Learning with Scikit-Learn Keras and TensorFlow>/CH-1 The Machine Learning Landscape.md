First machine learning application - spam filter (1990s)


A computer program is said to learn from experience E with respect to some task T and some performance measure P. If it's performance on task T, as measured by P improves with experience E.

[[Data mining]] - dig into large amount of data and discover patterns that were not immediately apparent. 

Machine Learning is great for
- Problems for which existing solutions require a lot of fine-tuning or long lists of rules. (Machine Learning algorithm can often simplify the code and perform better than the traditional approach)
- Complex problems for which using a traditional approach yields no good solutions, Machine Learning technique can perhaps find a solution.
- Fluctuating environments: a Machine Learning system can adapt to new data.
- Getting insights about complex problems and large amounts of data. 

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

## Non-representative Training Data
