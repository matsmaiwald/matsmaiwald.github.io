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
- under $H_0: \overline{X}_d - \overline{X}_s \sim \mathcal{N}(0 ,\sigma)$

- with $$ \sigma = \sqrt{ \frac{s^{2}_{d}}{N_d} + \frac{s^{2}_{s}}{N_s} }$$

### Bayesian solution

Brief reminder: in Bayesian inference we are interested in the posterior probability, which is $Pr(\mu_s,\mu_d\|data)$ and e.g. for Dirk's true shooting percentage is calculated as $Pr(\mu_d\|data) = \frac{ Pr(data\|\mu_d) * Pr(\mu_d) }{Pr(data)}$

We know that the true shooting percentage needs to lie between 0 and 1 for either player, so choose the beta distribution for both, the prior and the likelihood. In the case of a beta distribution as prior, we know that the resulting posterior distribution is also a beta distribution and can actually be computed analytically. In general though, this is not the case, and one has to approximate the posterior numerically via MCMC sampling. 

Once we have approximated the posterior distribution of both shooting percentages, we also have the distribution of their difference, which will help us make additional statistical statement over the difference between the two players' shooting ability.

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
