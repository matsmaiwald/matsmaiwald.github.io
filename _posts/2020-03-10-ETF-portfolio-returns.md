---
layout: post
title: "Understanding the opportunities and risks of ETF-index portfolios"
categories: misc
---

In this post, we'll look at what historical asset return data allow us to infer about the opportunities and risk of index-ETF portfolios i.e. the distributions of their returns. The accompanying data and code can be found [here](https://github.com/matsmaiwald/historical_asset_returns/blob/master/main.ipynb).

<!-- ## Short- and mid-term risks matter for most investors
The reason for looking at value fluctuations of portfolios is that most (private) investors, apart from being interested in the expected return of their portfolio, also care about  -->

## We have little direct data on the performance of index-ETF portfolios
Low-cost portfolios based on a combination of stock and bond index-ETFs are very popular these days. They offer broad diversification at a fraction of fees charged by traditional portfolio managers. Because inde-ETFs are a relatively new asset class however, we have very little historical data on their performance, or that of any portfolios consisting of index-ETFs. In particular, we have very little data on their behaviour during recessions.


## Historical asset return data
In the absence of data on index-ETF performance, we can instead look at historical data for the underlying indeces that index-ETFs track, to get a better idea for how an index-ETF portfolio would have performend in the past.

Specifically, thanks to Aswath Damodaran of NYU, we have historical data for the yearly returns of the S&P500 and US government bonds (3-months and 10-years maturity) going back to 1928 [(available here)](http://pages.stern.nyu.edu/~adamodar/New_Home_Page/datafile/histretSP.html).

## Some basic facts aboun stock and bond returns

Looking at the yearly return data, we can first confirm the following well-known basic facts, which together are often referred to as the *equity premium* in Economics and Finance.

### 1.) Stocks: high returns and high volatility

![Stock returns](/assets/plots/return_histogramm_S&P500.png)

Plotting a histogramm of the S&P500's returns shows that there is considerable variance around the average 11% return: in some years gains or losses of 40% and above are recorded.

### 2.) Bonds: lower returns and lower volatility
As the two graphs below show, the longer a bond's maturity, the more its returns resemble those of stocks: 

- the 3-months bonds are very stable with returns that are never negative, very limited in their upside, and on average equal to 3%. 
- 10-year bonds have a slightly higher mean return of 5%. There are a few years with returns above 20% but also a couple with negative returns of up to -10%.
  
![3 months treasuries](/assets/plots/return_histogramm_3-months_treasuries.png)

![10 year treasuries](/assets/plots/return_histogramm_10-year_treasuries.png)

### 3.) Combining stocks and bonds reduces portfolio risk
The reason why most portfolios combine stocks with bonds, is that both assets tend perform better at different times in the business cycle, meaning that they balance each other out.

Below, returns are plotted over time, allowing us to see that in years when  stocks give negative returns (shaded in grey), bonds show positive, often higher than normal, returns. This is the mechanism that typically reduces the risk of multi- vs single-asset porftolios.

![Returns during recessions](/assets/plots/returns_during_recessions.png)


## The historical distribution of equity-bond portfolio returns
- combining the data on individual assets, we can calculate the historical returns to a portfolio containing a mix of stocks and bonds.
- if we do this for every year between 1928 and 2018, we get a distribution of returns
- constructing a moving five-year window allows us to capture the distribution of returns during the first five years of holding a portfolio
- For a portfolio that invests 60% in the S&P500 and 20% into 3-months and 10 year treasuries each, the distribution looks as follows: 


![Portfolio returns](/assets/plots/portfolio_returns_60.png)

In this and the two plots below, the light-green and light-red area capture the returns that one would have received in 80% of the cases. The top area (dark-green) and the bottom area (dark-red) show the value of the portfolio in the top 10 and bottom 10 percent of cases.

 In the case of 60% initial investment in the S&P500, the chance of cumulative losses after 4 years, for example, is only at 10 percent. Half the time, the cumulative returns exceed 50 percent.

## Different asset allocations

### 30% stocks
For a more conservative portfolio, the cumulative return after five years lies between 18% and 70% percent in 80 percent of the cases. The chance of
cumulative losses after 5 years is less than 10 percent.
![Portfolio returns](/assets/plots/portfolio_returns_30.png)


<!-- Lastly, let's explore how different types of simple index-ETF portfolios would have done
historically. We'll look at portfolios with 30%, 60% and 90% of their assets in stocks.
We then ask the question: how would these portfolios have done historically. In fact, we
ask, how these portfolios would have done over any consecutive 5-year period between
1928 and 2018. For example, we start in the year 1927 and ask what the value of 100 dollars invested would be after *one year* (fill in number). Then we ask what the value of the same 100 dollars invested in 1927 would be after *two years*, i.e. in 1929. We then do the same for the third, fourth and fifth year. Presumably, the value would increase over time, but this does not have to be the case. We then repeat this exercise by looking at the value of a 100 dollars invested in 1928, i.e. looking at the returns in the year 1929, 1930, 1931, 1932 and 1932. Doing this for every year between 1927 and 2013 (we need data up to 2018 to determine the five-year returns for 100 dollars invested in 2013), sorting the portfolio values into five bins, one for each of the five years, and sorting each bin gives us the following graph.  -->

### 90% stocks

As expected, upside and downside both increase if the equity share is 90%.
![Portfolio returns](/assets/plots/portfolio_returns_90.png)
The inherent risk of the 90 percent stock portfolio is immediately obvious from the
plot. For each year, the risk of having a portfolio that is worth less what was
originally invested is 10 percent or more. At the same time, there is a 50 percent
chance of exceeding cumulative returns of 70 percent.

<!-- ## Appendix 

- To estimate the true distribution of portfolio returns over a five year horizon, we calculate portfolio returns for all five-year windows in the data.
- we start in the year 1927 and ask what the value of 100 dollars invested would be after *one year*. Then we ask what the value of the same 100 dollars invested in 1927 would be after *two years*, i.e. in 1929 and do the same for the third, fourth and fifth year. 
- we then shift the window by one year and look at the value of a 100 dollars invested in 1928, i.e. looking at the returns in the year 1929, 1930, 1931, 1932 and 1932. 
- Doing this for every year between 1927 and 2013 (we need data up to 2018 to determine the five-year returns for 100 dollars invested in 2013), and plotting returns together, depending on their relative position in the time window gives us the estimated distribution -->
