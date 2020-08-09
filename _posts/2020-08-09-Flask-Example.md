---
layout: post
title:  "Minimal Flask Example"
categories: misc
---
![Example](/assets/logos/flask-logo.png)

In this post I'm going to share a minimal example of a flask web app.

The example I chose is an app that calculates exponential expressions. It can handle __GET__ and __POST__ requests. 

## Example of a POST request

For __POST__ requests, it expects the _base_ and _exponent_ in json format and returns the result.

{% highlight python %}

url = "http://127.0.0.1:5000/"

input_1 = {"base": "5", "exponent": "2"}

response_post = requests.post(url, json=input_1).text

{% endhighlight %}
## Example of a GET request

For __GET__ requests, it just returns instructions for a __POST__ request.

{% highlight python %}
response_get = requests.get(url).text
{% endhighlight %}

I also included a "client" script which sends a __GET__ requests as well as two __POST__ requests to showcase the functionality of the app.

## Source code
The complete source code can be found at [https://github.com/matsmaiwald/flask_example](https://github.com/matsmaiwald/flask_example).
