---
layout: post
title:  "DeepAR Overview"
categories: misc
use_math: true
---

In this post, I wanna give a brief overview of the DeepAR forecasting model.

## Objective

Given a panel data set, we want a probability distribution over future realisations of each time series. 
So given the historical realisations of the time series 
$$\{\boldsymbol{z}_{i,1:t_0-1}\}_{i=1, ..., N}$$, 
where $$[z_{i, 1}, z_{i, 2}, ... , z_{i,t_0-1}] := \boldsymbol{z}_{i, 1:t_0-1} $$, as well as a past and future observations of a (potentially time series specific) vector of covariates $$ \boldsymbol{x}_{i,t} $$,
we want to obtain $$P(\boldsymbol{z}_{i,t_0:T}|\boldsymbol{z}_{i,1:t_{0-1}}, x_{i,1:T}) $$ i.e. a probability distribution for the value of each time series for each of the future periods from $$t_0$$ up to $$T$$.
Note that is assumed that we know the values of the covariate vector for all future periods which puts an important
limitation on the kinds of variables we can use as covariates.

## High-level modelling approach

Assuming that $$ z_{i,t} $$ is real-valued, the high-level modelling approach will be to estimate the following mapping

Data $$\rightarrow $$ Neural Network $$ \rightarrow $$ parameters of pdf for $$ z_{i,t} $$.

For the parametric form of the pdf we will assume a normal distribution. The more challenging part will be wrapping our head around the structure of the Neural Netowrk (NN) which is where most of the magic happens.

## NN high-level overview
The neural network that maps the data to the probability distribution of the target variable is _autoregressive_ (as it takes the previous periods target variable as input), _recurrent_ (as it takes it own hidden state from the previous period as input) neural network. It is made up of stacked LSTM layers (to allow signal efficiently travel through the unrolled RNN). The following graphic shows the data flow through the network at training (left) and prediction time (right).

![Graph1](/assets/graphs/DeepARNN.jpeg)
*Graph 1*

## Features and parameters of the model

#### Features
Other than the target variable and hidden state from the previous period, the NN takes a _covariate vector_ $$x_{i,t}$$ as input. This vector will typically contain the following features:

- lagged values of the target variable
- day of week, day of month and day of year
- time-variant or invariant real-valued variables associated with 
- embeddings of any time-invariant categorical variables associated with the time series

#### Parameters

The two main groups of parameters that need to be trained are

1. Parameters of the NN, including those governing the embedding of categorical variables
2. Parameters of the function that maps the output of the NN to the parameters of the target variable's likelihood function

## Training

Given a prediction and context length (the latter is a hyperparamter and refers to the encoding length i.e. the number of periods which the NN gets rolled out for, before making its first prediction),

during training, we repeatedly pick subsamples from the N time series with a window length equal to $$ prediction length + context length $$ from training data.  EXPLAIN HOW FIT AND UPDATE MODEL

## Prediction

## Understanding the NN