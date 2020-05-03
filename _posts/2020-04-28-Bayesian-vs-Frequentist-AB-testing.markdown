---
layout: post
title:  "Bayesian vs Frequentist AB testing with Dirk and Shaq"
categories: misc
use_math: true
---
## Why this post
- confusion, many people still use frequentist
- go through an example to see how Bayesian supersedes

## Dirk vs Shaq
Following scenario

- don't know Shaq O'Neal nor Dirk Nowitzki
- You have been watching Shaq for a while
- Dirk comes in takes a couple of free throws
- the two do a competition
- Another bystander approaches you to take bets
- Who do you bet on and how confident can you be? 

## Bayesian vs Frequentist AB test: brief recap of theory

### Frequentist solution
Quick recap:
- start with $H_0: \mu_d = \mu_s$
- using CLT, we know that under $H_0: \overline{X}_d - \overline{X}_s \sim \mathcal{N}(0 ,\sigma)$

- with $$ \sigma = \sqrt{ \frac{s^{2}_{d}}{N_d} + \frac{s^{2}_{s}}{N_s} }$$

- Calculate t-statistic to see whether we can reject the null hypothesis
### Bayesian solution

In Bayesian inference we directly deal with the posterior probability, which is $Pr(\mu_s,\mu_d\|data)$ and e.g. for Dirk's true shooting percentage is calculated as $Pr(\mu_d\|data) = \frac{ Pr(data\|\mu_d) * Pr(\mu_d) }{Pr(data)}$. $Pr(data\|\mu_d) we get directly from the data and the likelihood. In this case, the obvious choice for likelihood is binomial.$

As regards the prior, we know that the true shooting percentage needs to lie between 0 and 1 for either player, so choose the beta distribution for the prior. 

With this we can calculate the numerator of the posterior, and since the denominator is just a constant (which is easy to calculate in this toy example, but can often not be solved by analytical methods) we can approximate $Pr(\mu_d\|data)$ numerically by drawing samples from it via  MCMC sampling. 

Once we have approximated the posterior distribution of both shooting percentages, we also have the distribution of the difference of their shooting percentage, with which we can directly make probabilistic statements about our hypothesis.

## Both solutions in action
### Generating some data

{% highlight python %}
import numpy as np
np.random.seed(111)
shots_shaq = np.random.binomial(n=1,p=0.5, size=100)
shots_dirk = np.random.binomial(n=1,p=0.9, size=5)
{% endhighlight %}


### Frequentist
The first thing to note about the frequentist approach is how easy it is to implement, it just takes the following x lines to have `statsmodels` run a Welch test and give us the appropriate p-value.

{% highlight python %}
n_mc_samples = 10000

with pm.Model() as shooting_pct_model:
    # specify parameter of interest and assign priors
    p_shaq = pm.Beta('shooting_pct_shaq', alpha=alpha_prior, beta=beta_prior)
    p_dirk = pm.Beta('shooting_pct_dirk', alpha=alpha_prior, beta=beta_prior)
    
    # specify likelihood model
    likelihood_shaq = pm.Bernoulli('likelihood_shaq', p=p_shaq, observed=shots_shaq)
    likelihood_dirk = pm.Bernoulli('likelihood_dirk', p=p_dirk, observed=shots_dirk)
    

    # convenient way to calculate the difference straight away
    p_diff = pm.Deterministic('shooting_pct_diff', p_dirk - p_shaq)
    
    trace = pm.sample(n_mc_samples)

posterior_samples = pm.trace_to_dataframe(trace)t
{% endhighlight %}
# Conclusion
[(here)](https://github.com/matsmaiwald/cli_tools/blob/master/git-hist)
