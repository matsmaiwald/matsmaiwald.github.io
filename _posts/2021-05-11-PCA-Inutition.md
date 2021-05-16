---
layout: post
title:  "PCA from scratch"
categories: misc
use_math: true
---

# Goal and motivation
Want to find a linear transformation which, once applied to our original data, maximises variance and minimises redudancy in our data. Explain why this is the same.

# Generate test data

# Variance-Covariance matrix
Standardise data (not needed here) and calculate variance covariance matrix.

# Find a linear transformation that solves our problem.
A matrix is just a linear transformation, so we'll want to find 
$$P$$ in $$Y = PX$$. 

# Spectral theorem and matrix diagonalisation: identifying the right linear transformation
If our transformed data is to have minimal redundancy, then its covariances should be zero and the covariance variance matrix should be a diagonal matrix.
From the spectral theorem, we also know that we can diagonalise with the use of its eigenvectors.
Specifically, for any (symmetric) matrix $$A$$, we have that  $$ E^{-1}AE = D$$, where E is a matrix that has the eigenvectors of A as its columns and D is a diagonal matrix with the associated eigenvalues as its entries.

Now comes the key insight: By choosing the matrix of eigenvectors associated with the variance covariance matrix of our data, i.e. by setting $$ P=E $$, we'll end up with with a transformed dataset the covariance variance matrix of which will be diagonal, indicating that the transformed features are all independent of each other. Additionally, if order E and D by the size of the eigenvalues, the transformed features $$PX$$ will be ordered by the amount of variance they capture from the original, untransformed data.

Let's look at the math to see why this is true.

First, note that the transformation matrix P will show twice in the variance covariance matrix of the transformed data $$Y$$:

$$ C_Y = \frac{1}{N} YY^T =  \frac{1}{N} (PX)(PX)^T = P (\frac{1}{N} XX^T) P^T = PC_XP^T$$.

If we now substitute E containting the eigenvectors of $$C_X$$ for P, and use the fact that the inverse of an orthogonal matrix (in our case the matrix of eigenvectors) equals its transpose, we can see that $$C_Y$$, the variance covariance of the transformed data will be diagonal:

$$ C_Y = PC_XP^T = E^TC_XE = E^{(-1)}C_XE = D $$, where the last equality follws straight from the definition of an eigenvector.

This then means, that all we have to do to carry out PCA is find the eigenvectors of the variance covariance matrix of our data to get matrix $$E$$ and then post-multiply our data matrix by it. If we want to only keep those principal components with the largest explanatory power (as far as variance in our original data is concerned) then we can discard $$ x $$ unwanted eigenvectors, making E a non-square matrix with dimentions $$k$$ times $$k-x$$ transforming our data into $$k-x$$ principal components thereby reducing the dimensionality of our data. 

# Interpreting PCA
There are two ways that I like to interprete what's going on in PCA. You can think of it either as a transformation of the data to its eigenbasis or as a projection of the data onto its axes of largest variance. Let's look at both in turn.

If we think of matrices as describing linear transformations, then you can think of PCA as just moving around our data points in such a way that the transformed data has the properties that we desire. As an example in the 2D case, look at the original and transformed data on the left and right below. 

![Graph1](/assets/graphs/orig_data_w_eig_vectors.png)
![Graph2](/assets/graphs/transformed_data.png)

Explain how i and j basis vectors are moved.


In terms of projections, what's happening when we post-multiply our data by $$E$$ is that we get the _scalar projections_ of our data on each eigenvector. This corresponds to the length of the projection of each data point onto a given eigenvector. Note that this will map each data point to a single number (the length of the projection) for each eigenvector and we get our transformed data set by collecting all scalars for one eigenvector and considering them as a new feature and doing the same for all other eigenvectors. 

To visualise this process, we can post-multiply the transformed features once more by $$E$$ to arrive at the _vector projections_ of the data points and be able visualise their relationship to the original data better. Below I've done this for the principal components associated with each eigenvector. It is easy to see that the projections associated with the larger of the two eigenvalues (i.e. the first principal component) capture a larger variance in the original data.

![Graph1](/assets/graphs/pc1.png)
![Graph2](/assets/graphs/pc2.png)
