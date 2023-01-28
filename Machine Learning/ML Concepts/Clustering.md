---
tags: k-means, clustering 
---

[[K-Means Clustering]]

Clustering, or Cluster analysis is a technique that allows us to find groups of similar objects. 

# What is clustering

Clustering is the process of dividing the entire data into groups (also known as clusters) based on the patterns in the data.

Club similar observations and form different groups. 

Clustering is an [[Unsupervised Learning]] problem.
 
# Properties of Clusters
- All data points in a cluster should be similar to each other. 
- The data points from different clusters should be as different as possible.

# Evaluation Metrics for Clustering
## Interia
#interia
How far the points within a cluster are.

Interia calculated the sum of distances of all the points within a cluster from the centroid of that cluster - Intracluster distance.

We want the points within the same cluster to be similar to each other, so the lesser the interia value, the better the cluster are. 

## Dunn Index

**Inter-cluster distance** - the distance between the centroids of tow different clusters.

Dunn index is the ratio of the minimum of **inter-cluster distance** and maximum of **intracluster distance**.

We want to maximise the Dunn Index


# Prototype-based clustering

Each cluster is represented by a prototype, which can either be the [[Clustering#Centroid|centroid]] (average) of similar points or [[Clustering#Medoid|medoid]] (the most representative or most frequently occurring point).

## Centroid

## Medoid



