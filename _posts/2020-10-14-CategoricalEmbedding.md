---
layout: post
title:  "Categorical Embeddings for Tabular Data"
categories: misc
use_math: true
---


In this post, I am going to look at how categorical data can be encoded using embeddings. 

# What are embeddings for categorical variables and why are they useful
Similar to word embeddings, embeddings of categorical variables are encodings that map each category which a given variable can take, to a vector of fixed size. E.g. the values "apple" and "orange" of a variable "food" might be mapped to the vectors [1, 0.5] and [0, 2]. Categorical embeddings are particularly useful for categorical variables with high _cardinality_ -- i.e. variables that can take on many different categories -- because they reduce the dimension of the input variable and, compared to e.g. one-hot-encoding, can reduce the number of parameters that have to be estimated. Once obtained, categorical embeddings can also be re-used in different settings if the categorical variable is thought to affect the outcome variable in a similar manner to the setting it was trained in.


# How are they obtained?

- show how can train them as part of normal NN

# Example of time series data

- model for toy data
- show how embeddings match underlying model