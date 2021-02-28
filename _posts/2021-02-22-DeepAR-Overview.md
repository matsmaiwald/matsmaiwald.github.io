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


## Data Flow

## Training

## Prediction

## Understanding the NN