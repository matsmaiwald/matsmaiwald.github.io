---
layout: post
title:  "Interactive Forecast Visualisation"
categories: misc
---

I've found that one of the best ways for me to improve my intuition for how models work is to play around with their (hyper-)parameters to see how outcomes are affected. To that end, I built a [streamlit](https://streamlit.io/) app to interactively visualise predictions coming out of different time series forecasting models. The app is interactive in the sense that one can change all the following on the fly in the UI: 
-   choice of time series model
-   choice of some important hyperparameters
-   choice of data set
-   choice of train-test split

Here's what the app looks like:

![Example](/assets/gifs/fc_viz.gif)

To keep the interface of the models with the app relatively simple, I'm using the model(-wrappers) provided by [sktime](https://www.sktime.org/en/latest/).
While I like the ease with which streamlit let's you build interactive apps, one downside is that I have not yet found an easy way to have different UI elements interact with each other directly e.g. to have the options in a dropdown menu for hyperparameters be dependent on the choice of model.

In case you want to run the app yourself or just check out the source code, you can do so [here](https://github.com/matsmaiwald/fc_viz).