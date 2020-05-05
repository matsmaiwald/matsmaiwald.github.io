---
layout: post
title:  "Bayesian vs Frequentist AB testing with Dirk and Shaq"
categories: misc
use_math: true
---
## Why this post
Given how easy it is to carry out bayesian statistical analysis, I am often struck by how many people, both in academia and industry, still rely on frequentist methods. In this post, I'll go through a cooked-up example, involving the comparison of two basketball free-throw percentages, two show that a) both, frequentist and bayesian approaches are very easy to carry out (at least for this example) and that the bayesian analysis provides a much richer and easier to interpret result.

## Dirk vs Shaq
To make the examples more fun, imagine the following scenario

- You have been watching a basketball player, call him Shaq, take 100 free-throws with mixed success 
- Now a second player, call him Dirk, joins Shaq in shooting free-throws
- After only a couple of Dirk's free-throws, Shaq and Dirk come over to ask you who you think the better free-throw shooter is
- How do you decide, and how confident can you be?

## Bayesian vs Frequentist AB test: brief recap of the different approaches

### Frequentist approach
The frequentist approach essentially consists of the two following steps:
1. Get a point estimate of the difference between the two players' shooting ability. In our case it is simply the difference in the observed shooting percentage: $\overline{X}_d - \overline{X}_s$ 

2. Figure out whether the difference in measured shooting percentage is large enough to dismiss the possibility that both shooting percentages are actually equal (our null hypothesis). More formally this would be written as
- $H_0: \mu_d = \mu_s$
- using CLT, we know that under $H_0: \overline{X}_d - \overline{X}_s \sim \mathcal{N}(0 ,\sigma)$

- with $$ \sigma = \sqrt{ \frac{s^{2}_{d}}{N_d} + \frac{s^{2}_{s}}{N_s} }$$

- Calculate t-statistic to see whether the probability that the observed difference was actually drawn from $\mathcal{N}(0 ,\sigma)$ is below a pre-specified percerntage cutoff such that we feel comfortable rejecting the null hypothesis.
### Bayesian approach

In Bayesian inference we will directly deal with the posterior probability distribution of the difference in the two players' shooting percentage. We will approximate the posterior distribution of each player's shooting percentage first and then look at the distribution of the difference of the two. The posterior probability distribution of e.g. Dirk's true shooting percentag is defined $Pr(\mu_s,\mu_d\|data)$ and according to _Bayes' rule_ can be calculated as $$Pr(\mu_d\|data) = \frac{ Pr(data\|\mu_d) * Pr(\mu_d) }{Pr(data)}$$. $Pr(data\|\mu_d)$ we get directly from the data and the likelihood. In this case, the obvious choice for likelihood model is binomial.

As regards the prior, we know that the true shooting percentage needs to lie between 0 and 1 for either player, so choose the beta distribution for the prior. 

With this we can calculate the numerator of the posterior, and since the denominator is just a constant (which is easy to calculate in this toy example, but can often not be solved by analytical methods) we can approximate $Pr(\mu_d\|data)$ numerically by drawing samples from it via  MCMC sampling. 

Once we have approximated the posterior distribution of both shooting percentages, we also have the distribution of the difference of their shooting percentage, with which we can directly make probabilistic statements such as _there is an x-percent probability that Dirk is a better free-throw shooter than Shaq_.

## Both approaches in action
### Preliminaries
To follow the code, you need to have a Python virtual with the following packages installed: `pymc3`, `scipy`, `numpy` and `matplotlib`.
Let's start by importing all of them:

{% highlight python %}

import numpy as np
from scipy import stats
from matplotlib import pyplot as plt
import pymc3 as pm

{% endhighlight %}
### Generating some data
First, let's generate some data. Assuming that Shaq's and Dirk's true shooting percentages are 50% and 90% respectively, let's draw a 100 shot sample for Shaq and a 5 shot sample for Dirk.
Below we have the python code as well as the generated samples. 
{% highlight python %}
np.random.seed(111)
shots_shaq = np.random.binomial(n=1,p=0.5, size=100)
shots_dirk = np.random.binomial(n=1,p=0.9, size=5)
{% endhighlight %}

| Player | Total shots | Success | Miss | Shooting percentage | Variance |
|--------|-------------|---------|------|---------------------|----------|
| Shaq   | 100         | 44      | 56   | 0.44                | 0.246    |
| Dirk   | 5           | 3       | 2    | 0.6                 | 0.24     |


### Frequentist
The frequentist point estimate is simply $\overline{X}_d - \overline{X}_s = 0.16$.
To see whether this difference is statistically significant, let's run a Welch test:
{% highlight python %}
stats.ttest_ind(shots_dirk, shots_shaq, equal_var = False)
{% endhighlight %}

This gives a t-statistic of 0.64 with an associated p-value 0.554 i.e. a statisticially _insignificant_ result.
Let's see if the Bayesian approach can do any better.


### Bayesian
#### Choosing a prior
The shooting percentage of a player is bound between 0 and 1. This makes the beta distribution the ideal candidate for our prior. If you watch a little bit of basketball, you also know that extreme values i.e. a shooting percentages close to 0 or 1 are rare. With this in mind, let's parameterise our beta distribution with $\alpha = 2$ and $\beta = 2$. This gives us the following prior distribution, which is relative uninformed (i.e. makes few assumptions) apart from the fact that extremes are less likely that values in the center of the distribution's support.


{% highlight python %}
alpha_prior, beta_prior = 2, 2

fig, ax = plt.subplots()

x = np.linspace(0, 1)
prior = stats.beta(alpha_prior, beta_prior).pdf(x)
plt.plot(x, prior, label="prior")
plt.legend()
ax.set(title="Prior distribution for Dirk and Shaq's true shooting percentage")
{% endhighlight %}

![Prior](/assets/plots/prior.png)

Of course, there are different ways we could have chosen the prior. First of all, if we do have prior information regarding Dirk and Shaq, we could have chosen different priors for the two (in this example we are assuming no prior knowledge specific to the players). Similarly, we could have used data on other NBA players to fit our prior (this is called empirical Bayes and you can read more about it in Robinsons great book here[link]).

#### Specifying the likelihood and approximating the posterior
Next we only need to specify our likelihood model as binomial as well as the number of samples we would like to draw from the posterior (we'll go with 10k for now), link the prior and the likelihood and we're ready to go. For convenience, we will also define the difference between the Dirk's and Shaq's posterior as its own deterministic distribution.

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

posterior_samples = pm.trace_to_dataframe(trace)
{% endhighlight %}

#### Evaluating the result

Once `pymc3` is done sampling the posterior distributions, we can get a pretty good idea of what's going on by just plotting the two posterior distributions as well as their difference:
{% highlight python %}
fig, (ax1, ax2) = plt.subplots(figsize=(12, 6), nrows=2)
ax1.hist(posterior_samples.loc[1:1000,"shooting_pct_shaq"], alpha=0.5, label="Shaq")
ax1.hist(posterior_samples.loc[1:1000,"shooting_pct_dirk"], alpha=0.5, label="Dirk")
ax1.set(title="Dirk's and Shaq's posterior distributions")
ax1.legend()

ax2.hist(posterior_samples.loc[1:1000,"shooting_pct_diff"], alpha=0.5, label="difference")
ax2.set(title="Difference between Dirk's and Shaq's posterior")
ax2.legend()
fig.tight_layout()

{% endhighlight %}

![Posteriors](/assets/plots/posteriors.png)

First of all, note that the point estimates for the shooting percentage difference from both mean (0.114) and median (0.11) of the distribution are lower than the frequentist point estimate of 0.16. This is because our prior gives higher probability to both shooting percentages being close to 0.5.
But going beyond point estimates, we can now also answer questions like: What's the probability that Dirk is a better shooter (answer: 73.6%)?

# Conclusion
In this post, I worked through the frequentist and the bayesian approach of AB testing. Whereas the frequentist approach requires fewer lines of code, the bayesian approach gives a much richer result and is easier to interpret while still being a relative low-effort exercise.