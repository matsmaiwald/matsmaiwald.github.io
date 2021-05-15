
---
layout: post
title:  "PCA from scratch"
categories: misc
---

# Goal and motivation
Want to find a linear transformation which, once applied to our original data, maximises variance and minimises redudancy in our data. Explain why this is the same.

# Generate test data

# Variance-Covariance matrix
Standardise data (not needed here) and calculate variance covariance matrix.

# Spectral theorem and matrix diagonalisation: identifying the right linear transformation
If our transformed data is to have minimal redundancy, then its covariances should be zero and the covariance variance matrix should be a diagonal matrix.
From the spectral theorem, we also know that we can diagonalise with the use of its eigenvectors.

# Transforming our original data 
1. Get scalar projections of data on each eigenvector.
2. To visualise, get vector projection by multiplying by vector.

Typically, will only want to those dimensions with highest eigenvalues.