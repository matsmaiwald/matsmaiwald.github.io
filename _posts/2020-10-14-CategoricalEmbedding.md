---
layout: post
title:  "Categorical Embeddings for Tabular Data"
categories: misc
use_math: true
---


In this post, I am going to look at how categorical data can be encoded using embeddings. Embeddings are a superior encoding technique to e.g. one-hot-encoding, because they can capture the same information with fewer parameters.

# What are embeddings in tabular data

- just good old Linear/affine transformations that map a category into n-dimensions
- give an example

# How are they obtained?

- show how can train them as part of normal NN

# Example of time series data

- model for toy data
- show how embeddings match underlying model

# Comparison to OHE

- embeddings are particularly cool if you have categorical variables with high cardinality and the variables interact with other features --> dimensionality reduction
- supervised grouping of data
- Once trained, can re-use them in new contexts