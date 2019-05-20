From Wikipedia:
> The problem is computationally difficult (NP-hard); however, efficient heuristic algorithms converge quickly to a local optimum.

Lloyd's algorithm,
==================

Input: N datapoints in D dimensions
Computes: K points that are the centers of K clusters in the data

Algorithm:

1. Choose K points at random to be the initial "centroids", i.e., guesses at cluster centers.
2. Assign each data point to its closest centroid (use *squared* Euclidian distance for some reason).
3. You now have K clusters, and each point belongs to a cluster. Calculae the *mean* of these points (just sum all the points coordinate-wise and divide by number of points in the cluster.)
4. Repeat from 1.

Keep looping until convergence, i.e., until the centers/assignments don't change.
