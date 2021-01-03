---
layout: post
title:  "Weather app"
categories: misc
---

## Do I need to wear that rain jacket?
Recently, I've been having the following issue with the weather app on my phone: it only displays the _current_ Berlin weather. Often that by itself is very handy information, but there are many times where I will want to know more e.g. what the weather will be like in the next couple of hours so I can dress accordingly as I leave the house. While the weather forecast for e.g. 3 hours and 6 hours out is available after one or two clicks on my phone, this solution struck me as very much suboptimal. So rather than looking for a better weather app, I used this as an opportunity to see if I could come up with a better solution myself and display on my newly acquired raspberry pi plus fitting mini monitor.   

What I ended up creating is a __simple application that calls a free-to-use weather forecast API and displays the forecasted temperature and weather for the next 48 hours__.

## Implementation details
The implementation consists of two parts:

1. A collection of python functions that call the [openweathermap API](https://openweathermap.org/api) to obtain the hourly weather forecast for a given location (specified via latitude and longitude coordinates) 

2. A dash app that displays the forecast in a simple graph.

The full code can be found [here](https://github.com/matsmaiwald/weather_app).

## All weather forecast info in one graph
This is what the app looks like with exemplary forecasts for Berlin and New York City on Jan 03, 2021.

### Berlin
![Example](/assets/screenshots/weather_app_berlin.png)


### New York City
![Example](/assets/screenshots/weather_app_nyc.png)

