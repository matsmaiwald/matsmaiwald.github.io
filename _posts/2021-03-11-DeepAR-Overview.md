---
layout: post
title:  "DeepAR Overview"
categories: misc
use_math: true
---

In this post, I wanna give a brief overview of the DeepAR forecasting model and focus a little more on some details of training and prediction that receive less attention in the [original paper](https://www.sciencedirect.com/science/article/pii/S0169207019301888).

## Objective

Letting $$ z_{i,t} $$ denote the value of a time series $$ i $$ in period $$ t $$, our aim is to obtain a probabilistic forecast for a set of $$ N $$ time series and for $$ T - t_0 $$ periods ahead, where $$ t_0 $$ and $$ T $$ denote the first and last period for which we want a forecast. 
So given the historical realisations of the time series 
$$\{\boldsymbol{z}_{i,1:t_0-1}\}_{i=1, ..., N}$$, 
where $$[z_{i, 1}, z_{i, 2}, ... , z_{i,t_0-1}] := \boldsymbol{z}_{i, 1:t_0-1} $$, as well as past and future observations of a vector of covariates $$ \boldsymbol{x}_{i,t} $$,
we want to obtain $$P(\boldsymbol{z}_{i,t_0:T}|\boldsymbol{z}_{i,1:t_{0-1}}, x_{i,1:T}) $$ i.e. a probability distribution for the value of each time series for each of the future periods from $$t_0$$ up to $$T$$.


Note that is assumed that we know the values of the covariate vector for all future periods. This puts an important
limitation on the kinds of variables we can use as covariates.

## Modelling approach

Assuming that our target variable $$ z_{i,t} $$ can take any value on the real line (check out the paper for details of the count data case), the high-level modelling approach will be to estimate the following mapping

$$ \text{Data}_{i,t} \overset{Neural Network}{\rightarrow} $$ parameters of pdf for $$ z_{i,t} $$.

For the parametric form of the pdf we can assume e.g. a normal distribution.


## Neural Network architecture
The neural network that maps the data to the probability distribution of the target variable is _autoregressive_ (it takes the target variable from the previous period as input) and _recurrent_ (it takes it own hidden state from the previous period as input) neural network. It is made up of stacked LSTM layers (to allow information to efficiently travel through the unrolled RNN). The following graphic shows the data flow through the network at training (left) and prediction time (right). 

Here $$ \boldsymbol{h}_{i,t} $$ denotes the hidden state of the network and $$ \theta_{i,t} $$ denotes the parameter values of the pdf which are the output of a final layer of the NN.

![Graph1](/assets/graphs/DeepARNN.jpeg)
*Graph 1*

## Features and parameters of the model

#### Features
Other than the target variable and hidden state from the previous period, the NN takes a _covariate vector_ $$x_{i,t}$$ as input. This vector will typically contain the following features:

- lagged values of the target variable
- day of week, day of month and day of year
- time-variant or invariant real-valued variables associated with 
- embeddings (learned as part of training) of any time-invariant categorical variables associated with the time series

#### Parameters

Let $$ \Theta $$ denote the set of parameters to be learned. $$ \Theta $$ is made up of parameters for

1. the LSTMS cells of NN 
2. the embedding layers for categorical variables
3. the final layer that maps the hidden state of the NN to the parameters of the target variable's likelihood function

## Training
To understand how we can train the model and get predictions from it, it will be helpful to define the concept of _context length_. Because the NN is _recurrent_, whenever we want to make a prediction for e.g. $$ t_0$$ we need the hidden state given by the NN for the previous period $$ t_{0-1} $$. The _context length_ determines, how many periods from $$ t_0 $$ we go backwards to "unroll" the NN. Because the NN also takes as input the target variable from the previous period, a context length of $$ k $$ allows the NN to learn a highly non-linear mapping that is dependent on each individual target value in the last $$ k $$ periods. This is in addition to the target values of past periods captured in the vector of covariates $$ \boldsymbol{x} $$ .

Given a context length $$ k $$ and a prediction length $$ T-k $$, we sample batches of times series (each time series will be sampled multiple times overall) and train the model as follows:

Given the $$j$$-th batch of time series
1. 
  - sample a window of length $$ T $$ to obtain $$ \{ \boldsymbol{z}_{i,{1:T}} \} $$ and $$ \{ \boldsymbol{x}_{i,{1:T}} \} $$
  - given the current parameter values $$ \Theta_j $$: 
  obtain the target prediction $$ \widetilde{z}_{i,t} $$ and hidden state $$ \boldsymbol{h}_{i,t} $$ from the NN for $$ t $$ in $$ 1:T $$, using as inputs $$ z_{i, t-1} $$, 
  $$ h_{i, t-1} $$ and $$ x_{i,t} $$, where $$ h_{i,0} $$ and $$ z_{i,0} $$ are initialised to zero.
  - retain $$ \{ \widetilde{z}_{t_0,T} \} $$ i.e. those predictions that fall into the prediction range
3. Compute the loss for this batch as
  $$ \mathcal{L} = \sum\limits_{i=1}^N \sum\limits_{t=t_0}^T \log  p(z_{i,t} | \Theta_j) $$
4. Update parameters: $$ \Theta_{j+1} = \Theta_j - \alpha \frac{ \partial \mathcal{L}(\Theta) }{ \partial \Theta} $$


## Prediction

1. Given a time series which we want to obtain predictions for, as well as its covatiates, filter to the $$ k $$ most recent observations, where $$ k $$ is the context length and denote the result as $$ \{z_{i,t}, x_{i,t} \}_{t=0}^{t_0-1} $$.

2. For $$ t $$ in $$ [0,t_{0-1}] $$:
  Calculate $$ h_{i,t} $$

3. For $$ t $$ in $$ [t_0, T] $$, where $$ T-t_0 $$ equals the prediction length,
  Calculate $$ \widetilde{z}_{i,t} $$

4. Repeat step 4) until as many sample predictions as desired are obtained.


## References

- Original DeepAR paper: [https://www.sciencedirect.com/science/article/pii/S0169207019301888](https://www.sciencedirect.com/science/article/pii/S0169207019301888)
- Blog post with helpful visualisations of the model architecture: [https://www.telesens.co/2019/06/08/time-series-prediction/](https://www.telesens.co/2019/06/08/time-series-prediction/)
- Api docs for the gluonts implementation: [https://ts.gluon.ai/api/gluonts/gluonts.model.deepar.html](https://ts.gluon.ai/api/gluonts/gluonts.model.deepar.html)