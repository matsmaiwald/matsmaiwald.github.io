---
layout: post
title: "Understanding the opportunities and risks of ETF-index portfolios"
categories: misc
---

## We have little historical data on the performance of index-ETF portfolios
Low-cost portfolios based on a combination of stock and bond index-ETFs are very popular nowadays. However, we have very little historical data on the performance of these portfolios, since most of them were created after the last recession in 2008/2009.

## Approximating index-ETF portfolio returns by looking at historical data
In the absence of historical on the portfolios themselves, we can instead look at historical data for their underlying components to get a feel for how an index-ETF portfolio would have performend in the past.

Specifically, thanks to an economist from NYU, we have historical data for the yearly returns of the S&P500 and treasuries (3-months and 10-years maturity) going back to 1928.

## On average, stocks have higher returns but also higher volatility
As is typical for stocks vs bonds, the S&P500 has a better average return than the two treasuries, though there are also many years with negative returns. This is less so the case for 10 year bonds. 3-months treasuries have never had negative returns, though their upside is also very limited.
![Stock returns](/assets/plots/return_histogramm_S&P500.png)

![3 months treasuries](/assets/plots/return_histogramm_3-months_treasuries.png)

![10 year treasuries](/assets/plots/return_histogramm_10-year_treasuries.png)
## Bonds show decent returns in years which the stock market tanks
Below, we see that, as stock markets tank, investors move their funds into safer assets such as treasuries. As a result, the returns to treasuries typically balance those of stocks in a portfolio containing both asset classes.

![Returns during recessions](/assets/plots/returns_during_recessions.png)
## ETF-Index portfolios' historic return profiles over a five-year horizon
Lastly, let's explore how different types of simple index-ETF portfolios would have done
historically. We'll look at portfolios with 30%, 60% and 90% of their assets in stocks.
We then ask the question: how would these portfolios have done historically. In fact, we
ask, how these portfolios would have done over any consecutive 5-year period between
1928 and 2018. We then
order the outcomes for every year -- that is year 1, 2, 3, 4 and 5 of holding the portfolio. In the plots below, the light-green and light-red area capture the returns that one would have received in 90% of the cases. The top area (dark-green) and the bottom area (dark-red) show the value of the portfolio in the top 10 and bottom 10 percent of cases.
### 30% stocks
![Portfolio returns](/assets/plots/portfolio_returns_30.png)
While this is the most conservative of all the three portfolios, one would still end up
with a return of between 18% and 70% percent in 90 percent of the cases. The chance of
cumulative losses after 5 years is less than 5 percent.
### 60% stocks
![Portfolio returns](/assets/plots/portfolio_returns_60.png)
As expected, upside and downside both increase as the equity share is doubled. In the
case of 60% initial investment in the S&P500, the chance of losses is only at 10 percent
after 4 years. Half the time, the cumulative returns exceed 50 percent.
### 90% stocks
![Portfolio returns](/assets/plots/portfolio_returns_90.png)
The inherent risk of the 90 percent stock portfolio is immediately obvious from the
plot. For each year, the risk of having a portfolio that is worth less what was
originally invested is 10 percent or more. At the same time, there is a 50 percent
chance of exceeding cumulative returns of 70 percent.
