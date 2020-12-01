---
layout: post
title:  "Bayesian vs Frequentist AB testing with Dirk and Shaq"
categories: misc
use_math: true
---
## Why this post
Given how easy it is to carry out Bayesian A/B or hypothesis testing, I am often struck by how many people, both in academia and industry, still rely preimarily on frequentist methods. In this post, I'll go through a cooked-up example, involving the comparison of two basketball free-throw percentages and show that a) both, frequentist and Bayesian approaches are easy to carry out and that b) the Bayesian analysis provides a much richer result that is also easier to interpret.

## Dirk vs Shaq
To make the numbers more fun, imagine the following scenario

- You have been watching a basketball player, call him Shaq, take 100 free-throws with mixed success 
- Now a second player, call him Dirk, joins Shaq in shooting free-throws
- After only five of Dirk's free-throws (most of which are succesful), _Shaq and Dirk come over to ask you who you think the better free-throw shooter is_
- How do you decide, and how confident can you be?

## Bayesian vs Frequentist AB test: brief recap of the different approaches

First let's start with a quick recap of the theoretical underpinnings of the two possible approaches.

### Frequentist approach
On an intuitive level, the frequentist approach consists of the two following steps:
1. Get a point estimate of the difference between the two players' shooting ability. In our example this is simply the difference in the observed shooting percentage: $$\overline{X}_d - \overline{X}_s$$ 

2. _Figure out whether the difference in observed shooting percentage is large enough relative to its standard deviation_ to dismiss the possibility that both shooting percentages are actually equal (which is the null hypothesis). 

More formally, the steps are:
- define the null hypothesis as $$H_0: \mu_d = \mu_s$$
- using the _Central Limit Theorem_, we know that under $$H_0$$ the following large sample approximation holds: $$ \overline{X}_d - \overline{X}_s \stackrel{H_0}{\sim} \mathcal{N}(0 ,\sigma)$$, with $$ \sigma = \sqrt{ \frac{s^{2}_{d}}{N_d} + \frac{s^{2}_{s}}{N_s} }$$. In order to not rely on the _Central Limit Theorem's_ asymptotic result (which requires $$N$$ to be large), in practice we use the typical _student-t distribution_, where the degrees of freedom are determined by the _Welch–Satterthwaite equation_. 

- Calculate the t-statistic as $$t = \frac{\overline{X}_d - \overline{X}_s}{\sigma} $$ to see whether the probability that the observed difference was actually drawn from a zero-centered _stutent-t distribution_ is below a pre-specified percerntage cutoff (e.g. 1%) such that we can reject the null hypothesis.


### Bayesian approach

In Bayesian inference we will directly deal with the posterior probability distribution of the difference in the two players' shooting percentage. We will approximate the posterior distribution of each player's shooting percentage first and then look at the distribution of the difference of the two. According to _Bayes' rule_, the posterior probability distribution of e.g. Dirk's true shooting percentag $$Pr(\mu_s,\mu_d\|data)$$ can be calculated as $$Pr(\mu_d\|data) = \frac{ Pr(data\|\mu_d) * Pr(\mu_d) }{Pr(data)}$$. $$Pr(data\|\mu_d)$$ we get directly from the data and the likelihood. In this case, the obvious choice for the likelihood is binomial.

As regards the prior, we know that the true shooting percentage needs to lie between 0 and 1 for either player, so we'll choose the beta distribution for the prior. 

With this we can calculate the numerator of the posterior, and since the denominator is just a constant (which is easy to calculate in this toy example, but often cannot be solved by analytical methods) we can approximate $$Pr(\mu_d\|data)$$ numerically by drawing samples from it via _Markov Chain Monte Carlo_ (MCMC) sampling. 

Once we have approximated the posterior distribution of both shooting percentages, we also have the distribution of the difference of their shooting percentage, with which we can directly make probabilistic statements such as _there is an x-percent probability that Dirk is a better free-throw shooter than Shaq_.

## Both approaches in action
### Preliminaries
To run the code below, you need to have a Python environment with the following packages installed: `pymc3`, `scipy`, `numpy` and `matplotlib`.
Let's start by importing all of them:

{% highlight python %}

from matplotlib import pyplot as plt
import numpy as np
import pymc3 as pm
from scipy import stats

{% endhighlight %}
### Generating some data
First, to generate some data, let's **assume that Shaq's and Dirk's true shooting percentages are 50% and 90% respectively**, and let's draw a 100 shot sample for Shaq and a 5 shot sample for Dirk.

{% highlight python %}
np.random.seed(111)
shots_shaq = np.random.binomial(n=1,p=0.5, size=100)
shots_dirk = np.random.binomial(n=1,p=0.9, size=5)
{% endhighlight %}

The generated samples will look like this:

| Player | Total shots | Success | Miss | Shooting percentage | Variance |
|--------|-------------|---------|------|---------------------|----------|
| Shaq   | 100         | 44      | 56   | 0.44                | 0.246    |
| Dirk   | 5           | 3       | 2    | 0.6                 | 0.24     |


### Frequentist
The frequentist point estimate is simply $$\overline{X}_d - \overline{X}_s = 0.16$$.
To see whether this difference is statistically significant, let's run a **Welch test** (which is a generalised version of a standard t-test):
{% highlight python %}
stats.ttest_ind(shots_dirk, shots_shaq, equal_var = False)
{% endhighlight %}

This gives a t-statistic of 0.64 with an associated p-value 0.554 i.e. a statisticially _insignificant_ result. So our **best guess is that Dirk is a better shooter, but we cannot attach any statistical certainty to this guess.** 

Let's see if the Bayesian approach can do any better.


### Bayesian
#### Choosing a prior
As the shooting percentage of a player is bound between 0 and 1, the beta distribution is an ideal candidate for our prior. If you watch a little bit of basketball, you also know that extreme values i.e. a shooting percentages close to 0 or 1 are rare. With this in mind, let's parameterise our beta distribution with $$\alpha = 2$$ and $$\beta = 2$$. This gives us the following prior distribution, which is relative uninformed (i.e. makes few assumptions) apart from the fact that extremes are less likely that values in the center of the distribution's support.


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

Of course, there are different ways we could have chosen the prior. First of all, if we had prior information regarding Dirk and Shaq, we could have chosen different priors for the two (in this example we are assuming no prior knowledge specific to the players). Similarly, we could have used data on other NBA players to fit our prior (you can read more about in an excellent blog post by David Robinson (see reference at the end).

#### Specifying the likelihood and approximating the posterior
With our prior at hand, we only need to specify our likelihood model as binomial, set the number of samples we would like to draw from the posterior (we'll go with 10k for now), link the prior and the likelihood and `pymc3` will do the rest for us. For convenience, we will also define the difference between the Dirk's and Shaq's posterior as its own deterministic distribution.

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

First of all, note that the point estimates for the shooting percentage difference from both mean (0.114) and median (0.11) of the posterior distribution are lower than the frequentist point estimate of 0.16. This is because our prior gives higher probability to both shooting percentages being close to 0.5.
But going beyond point estimates, we now **have the full distribution of the difference in shooting percentage of Dirk and Shaq**. This allows us to answer question regarding any quantile of the difference but also allows us to assign a probability to the case in which Dirk is a better freethrow shooter than Shaq: 73.6% of the posterior's mass lie to the right of the zero-mark.

## Conclusion
In this post, I worked through the frequentist and the Bayesian approach of AB testing. Whereas the frequentist approach requires fewer lines of code, the Bayesian approach gives a much richer result and is easier to interpret while still being a relative low-effort exercise.


## Further Reading
- David Robinson's [blog post](http://varianceexplained.org/r/empirical_bayes_baseball/) about how to empirically estiamte priors in the context of Baseball data.
- My former colleague Mehrdad Baghery's [blog post](https://mbaghery.github.io/2020/02/28/bayesian-ab-testing.html) with more details on AB testing in the Bayesian framework.
- The general comparison of Bayesian and Frequentist approaches in [this class note](https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading20.pdf) of MIT's intro to probability and statistics class.