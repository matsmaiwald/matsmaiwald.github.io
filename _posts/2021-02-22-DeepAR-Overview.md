---
layout: post
title:  "DeepAR Overview"
categories: misc
use_math: true
---

In this post, I wanna give a brief overview of the DeepAR forecasting model.

## Objective

Letting $$ z_{i,t} $$ denote the value of time series $$ i $$ in period $$ t $$, our aim is to obtain a probabilistic forecast for a set of $$ N $$ time series and for $$ T - t_0 $$ periods ahead, where $$ t_0 $$ and $$ T $$ denote the first and last period for which want a forecast. 
So given the historical realisations of the time series 
$$\{\boldsymbol{z}_{i,1:t_0-1}\}_{i=1, ..., N}$$, 
where $$[z_{i, 1}, z_{i, 2}, ... , z_{i,t_0-1}] := \boldsymbol{z}_{i, 1:t_0-1} $$, as well as a past and future observations of a (potentially time series specific) vector of covariates $$ \boldsymbol{x}_{i,t} $$,
we want to obtain $$P(\boldsymbol{z}_{i,t_0:T}|\boldsymbol{z}_{i,1:t_{0-1}}, x_{i,1:T}) $$ i.e. a probability distribution for the value of each time series for each of the future periods from $$t_0$$ up to $$T$$.
Note that is assumed that we know the values of the covariate vector for all future periods which puts an important
limitation on the kinds of variables we can use as covariates.

## High-level modelling approach

Let's look at the case where our target variable can take the value any real number (check out the original paper for the count data case). Assuming that $$ z_{i,t} $$ is real-valued, the high-level modelling approach will be to estimate the following mapping

_Data_ $$\rightarrow $$ _Neural Network_ $$ \rightarrow $$ _parameters of pdf_ for $$ z_{i,t} $$.

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

we train the model on samples from the training data. Specifically, for a given time series, we uniformly sample a window of a length that equals the sum of prediction and context length. We start by calculating the hidden state of the NN for the first time period in the sampled window (initialising $$ z $$ and $$ x $$ as zero) and then together with the covariates and target values of the rest of the time window, sequentially calculate the NN's hidden state for each period in the sampled window. Using the log-likelihood as the loss function, calculate the gradient of the loss function and use the gradient to update the NN's parameters. Note that we typically create mini-batches of say, size 100, average the gradient and update only once per 100 time series.

In pseudo-code, we can describe the training procedure as:

Given encoder length $$ l $$ and prediction length $$ T-t_0 $$
1. For time series in current batch
    * select time windows of length $$ T-t_0 $$ from training data, this _prediction range_ window will be used for evaluating predictions
    * for each of the windows in the previous step, select the $$ l $$ prior data points as _conditioning range_
      - if some or all of the date prior to the _prediction range_ is missing (i.e. because a window at the beginning of training data was selected), fill up with zeros
    * using the current parameters of the NN and the likelihood function, "unroll" the NN for the $$ l $$ periods in the _conditioning range_ to obtain $$ z_{i, t_{0-1}} $$ and $$ h_{i, t_{0-1}} $$ and combine them with $$ x_{i,t_0} $$ to obtain a pdf for $$ P(z_{i,t_0} conditional) $$.
        - For $$ i $$ in number of eval samples, draw a value of z, and use it to get a pdf for next period, repeat $$ l $$ times
    * Combine obtained forecasts and calculate loss (QQ: do we need probabilistic forecasts during training?)


Questions: 

- use ancestral sampling or real data during training? 
- How do we calculate the first hidden state during any training period? 
- Explain solution to cold start problem

## Prediction
Once the model is trained, we use the most recent available target value as well the covariates for the next _prediction length_ days to obtain forecasts, by sampling from the probility function which the NN outputs. Mention ancestral sampling.