---
layout: post
title:  "Categorical Embeddings for Tabular Data"
categories: misc
use_math: true
---


In this post, I am going to review how, in the context of neural networks for tabular data, categorical data can be encoded using embeddings. 

## What are embeddings for categorical variables and why are they useful?
Similar to word embeddings, embeddings of categorical variables are encodings that map each unique value which a given categorical variable can take, to a vector of fixed size. E.g. the values "apple" and "orange" of a variable "food" might be mapped to the vectors [1, 0.5] and [0, 2], respectively. Because the _embedding size_ (i.e. the size of the vectors that we map categorical inputs to) is typically lower than the _cardinality_ (i.e. the number of unique values) of the categorical variable, encoding variables via embeddings rather than as e.g. one-hot variables reduces the sparsity of the input data and reduces the overall number of parameters of the neural network (particularly if the layer following the input layer is large). Once obtained, categorical embeddings can also be re-used in different settings if the categorical variable is thought to affect the outcome variable in a similar manner to the setting it was trained in.


## Obtaining categorical embeddings as part of training a neural network
Embeddings are generally obtained as part of training a neural network on a supervised learning task. The embedding layer that encodes a categorical variable is situated right at the beginning of the neural network, directly between the "raw" categorical variable and the rest of the network. __The main component of the embedding layer is the embedding matrix $$E$$__ with dimensions $$e \times c$$, where $$e$$ is the number of dimensions which we want the embedding to have and $$c$$ is the cardinality of the categorical variable. __To map categorical inputs to their embeddings, we first construct a one-hot encoding matrix $$O$$__ of dimension $$n \times c$$, where $$n$$ is the size of our data sample and $c$ is again the cardinality of the categorical variable. Each row of the one-hot encoding matrix correspsonds to a data point and each column to one of the unique values that the categorical variable can take. The matrix is filled with ones and zeros only and a one indicates that for the data point corresponding to the row, the categorical variable equals the value corresponding to the column. Otherwise, the entry is zero. 

__By multiplying the one-hot encoding matrix by the transpose of the embedding matrix, we obtain a matrix of embedded inputs__ whose rows equal the embedded value corresponding to the rows of the one-hot encoding matrix. This matrix of embedded inputs is then consumed by the following layers of the network.

$$ \text{Embedded Inputs} =  OE^T $$

During training, we randomly initialise the embeddings matrix and update its entries together with the rest of the weights of the network via gradient descent.

## Choosing the size of the embedding

The optimal embedding size will depend on the problem at hand, but a good rule of thumb that tends to work well empirically is to set a variable's embedding size equal to $$\min(50, (cardinality/2))$$.


## Reference
- A blog post by Rachel Thomas of fastai on using categorical variables in neural networks for tabular data: [https://www.fast.ai/2018/04/29/categorical-embeddings/](https://www.fast.ai/2018/04/29/categorical-embeddings/)

- A paper that explains the approach of Kaggle contestants who used categorical embeddings to finish 3rd in the Kaggle Rossmann competition: [https://arxiv.org/pdf/1604.06737v1.pdf](https://arxiv.org/pdf/1604.06737v1.pdf)