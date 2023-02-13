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
- Reinforcement Learning

## Online vs batch learning 
Learn incrementally or on the fly

## Instance based vs model-based learning 
Compare new data points with known data points or detecting patterns in the training data and building a predictive model



