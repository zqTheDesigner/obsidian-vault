---
tags: #clustering
source: https://towardsdatascience.com/k-means-clustering-with-scikit-learn-6b47a369a83c
---

[[Clustering#Prototype-based clustering]]

K-means algorithm is easy to implement, computationally efficient. 

K-means is very good at identifying clusters with a spherical shape.

In real-world applications of clustering, we do not have any ground truth category information (else it will fall in to the category of supervised learning)

Group the samples based on their feature similarities. 

1. Randomly pick k centroids from the sample points as initial cluster centres
2. Assign each sample to the nearest centroid (???)
3. Move the centroidsto the centre of the samples that were assigned to it.
4. Repeat step 2 and 3 until the cluster assignments to not change or a user-defined tolerance or maximum number of iterations is reached. 

### How do we measure similarity between objects?
Similarity is the opposite of distance. 

An iterative approach for minimising the within-cluster [[Sum of Squared Errors (SSE)]] or [[Sum of Squared Errors (SSE)|cluster intertia]] 


# K-means clustering using scikit-learn


If a cluster is empty, the algorithm will search for the sample that is farthest away from the centroid of the empty cluster.  