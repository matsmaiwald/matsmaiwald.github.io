---
layout: post
title:  "PCA under the hood"
categories: misc
use_math: true
---

In this post, I want to look at the mathematics behind one of the most popular methods for dimensionality reduction: principal component analysis aka PCA.

# Goal and motivation
At a high level, PCA finds a linear transformation of our original data that produces new, transformed variables. These variables are independent of each other (in contrast to our original data, which will typically contain variables that are correlated with each other) and try to capture as much of the original variance in as few independent variables as possible. 

# Linear transformation = matrix multiplication
One key idea to grasp in the context of PCA is that matrix multiplication can be interpreted as a linear transformation (check out [this](https://www.youtube.com/watch?v=kYB8IZa5AuE) video for a great visual explanation). With this in mind, we can restate our problem as finding a matrix $$P$$ in $$Y = PX$$ such that $$Y$$ displays the desired properties.

# Set up
Imagine we have a data set which contains observations of two explanatory variables as row vectors (note that this is the transpose of the format in which you waould typically have the data in e.g. a pandas or R dataframe, where a variable corresponds to a column rather than a row). Because we want to find principal components that explain a lot of the variance in our data, we need to make sure that we don't focus unduly on any one specific variable, just because it is measured on a different scale. So unless the scale on which a variable is measured has inherent information, we'll standardise each variable by subtracting its mean and dividing by its standard deviation. Let $$X$$ denote the standardised data set.
Having removed any influences of measurement scales, we can go on to calculate the variance covariance matrix of our data as $$C_X = \frac{1}{N-1}  XX^T $$, where $$N$$ is the number of observations.


# Spectral theorem and matrix diagonalisation: identifying the right linear transformation
If our transformed data is to have minimal redundancy, then its covariances should be zero and the covariance variance matrix should be a diagonal matrix.
From the spectral theorem, we also know that we can diagonalise a matrix via its eigenvectors.
Specifically, for any (symmetric) matrix $$A$$, we have that  $$ E^{-1}AE = D$$, where E is a matrix that has the eigenvectors of A as its columns and D is a diagonal matrix with the associated eigenvalues as its entries.

Now comes the __key insight: By choosing as our transformation matrix (transpose of) the matrix of eigenvectors associated with the variance covariance matrix of our data, i.e. by setting $$ P=E^T $$, we'll end up with with a transformed dataset of whicht the covariance variance matrix is diagonal, indicating that the columns i.e. the transformed features are linearly independent of each other or in other words uncorrelated__. Additionally, if we order the diagonal entries of $$D$$ by the size of the eigenvalues and if we sort the columns of $$E$$ in the same order (such that the eigenvector in the $$ith$$ column of E corresponds to the entry of $$D$$ in the $$ith$$ row and $$ith$$ column) the transformed features $$PX$$ will be ordered by the amount of variance they capture from the original, untransformed data.

Let's look at the math to see why this is true.

First, note that the transformation matrix $$P$$ will show up twice in the variance covariance matrix of the transformed data $$Y$$:

$$ C_Y = \frac{1}{N} YY^T =  \frac{1}{N} (PX)(PX)^T = P (\frac{1}{N} XX^T) P^T = PC_XP^T$$.

If we now substitute in $$E^T$$ for $$P$$, and use the fact that the transpose of an orthogonal matrix (in our case the matrix of eigenvectors) equals its inverse, we can see that $$C_Y$$, the variance covariance of the transformed data will be diagonal:

$$ C_Y = PC_XP^T = E^TC_XE = E^{-1}C_XE = D $$, where the last equality follws straight from the definition of an eigenvector.

This then means, that __all we have to do to carry out PCA is find the eigenvectors of the variance covariance matrix of our data to get matrix $$E$$ and then pre-multiply our data matrix by its transpose__. If we want to only keep those principal components with the largest explanatory power (as far as variance in our original data is concerned) then we can discard $$ j $$ unwanted eigenvectors, making E a non-square matrix with dimentions $$k$$ times $$k-j$$. Pre-multiplying our data by the transpose of this reduced version of $$ E $$ will transform our data into $$k-j$$ principal components thereby reducing the dimensionality of our data. 

# Interpreting PCA
There are __two ways that I like to interprete what's going on in PCA__. You can think of it either

1.) as __a transformation of the data to its eigenbasis__ or 
2.) as __a projection of the data onto its axes of largest variance__. 

Let's look at each in turn.

### PCA as transformation of data to eigenbasis

If we think of matrices as describing linear transformations, then you can think of PCA as just moving around our data points in such a way that the transformed data has the properties that we desire. As an example in the 2D case, look at the original (figure 1) and transformed (figure 2) data below. 

##### Figure 1: Original data
![Graph1](/assets/graphs/orig_data_w_eig_vectors.png)
##### Figure 2: Transformed data
![Graph2](/assets/graphs/transformed_data.png)

More formally, what happens in the transformation is that we carry out a change of basis i.e. we move from using the typical basis vectors $$i$$ and $$j$$ (corresponding to the x and y axis in the 2d case) to using the eigenvectors in $$E$$ as our basis vectors. This special basis that we switch to is also called _eigenbasis_.


### PCA as projection of data onto its axes of largest variance

In terms of projections, what's happening when we multiply our data by $$E^T$$ is that we get the _scalar projections_ of each of our $$n$$ observations on each of our $$k$$ eigenvectors. These scalar projections are just (scaled) dot products of an eigenvector and a $$k$$-dimensional column vector corresponding to an individual observation in our data (where the scaling factor is the norm of the eigenvector). The numerical value of the scalar projections correspond to the length of the projection. Note, that this will map each data point to a single number for each eigenvector.

To visualise this process, we can multiply the transformed features once more by $$E^T$$ to arrive at the _vector projections_ of the data points and be able visualise their relationship to the original data better. Below, in figures 3 and 4, I've done this for the principal components associated with each eigenvector. To make it easier to verify visually that this works, I have highlighted three exemplary points in orange. It is easy to see that the projections associated with the larger of the two eigenvalues (i.e. the first principal component) capture a larger variance in the original data.

##### Figure 3
![Graph1](/assets/graphs/pc1.png)
##### Figure 4
![Graph2](/assets/graphs/pc2.png)

# References
- _Stephen Marslsnd: Machine Learning - An Algorithmic Perspective (Second Edition), Chapter 6.2_
- [3Blue1Brown video on eigenvectors and eigenvalues](https://www.youtube.com/watch?v=PFDu9oVAE-g)
- [Jonathon Shlens - A Tutorial on Principal Component Analysis[(https://arxiv.org/abs/1404.1100v1)]