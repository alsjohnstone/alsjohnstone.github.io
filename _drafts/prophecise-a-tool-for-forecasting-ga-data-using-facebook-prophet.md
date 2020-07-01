---
title: 'Prophecise: a tool for forecasting GA data using Facebook Prophet'
date: 2020-06-21 20:28:04 Z
permalink: prophecise-automatically-build-forecasts-for-google-analytics
layout: post
---

![](/assets/images/screely-1592742794169.png)I wanted a create a fast and simple way for marketers/analysts/data scientists to generate forecasts from their Google Analytics data using Facebook Prophet. But really what I wanted was an interesting project to work I came across Gareth Cull’s amazing open-source [Forecastr](https://github.com/garethcull/forecastr) app, which provided a great starting point for [Prophecise](https://prophecise.com).

## What is Facebook Prophet?

[Prophet](https://facebook.github.io/prophet/) is open source software released by Facebook’s Core Data Science team. It is a procedure for forecasting time series data and works best with time series that have strong seasonal effects and several seasons of historical data. It is available for download on [CRAN](https://cran.r-project.org/package=prophet) and [PyPI](https://pypi.python.org/pypi/fbprophet/).

## What is Prophecise?

Prophecise is a Flask app that uses Python and JavaScript to automatically forecast your GA data. Using the Google Analytics API I was able to add functionality that lets you log in with your Analytics account and pull data directly from GA into the guided UI and preview the data before generating a forecast.

\[IMAGE OF DROPDOWNS\]

You can select the account/property/view you’d like to pull data from, and which metric you’d like to forecast. There are also additional options to enter your own metric (if the one you need is not available on the dropdown) and to select a specific segment. To see a list of all available metrics see Google’s Dimensions and Metrics Explorer.

This data is then sent back to the server so we can do some forecasting with Facebook Prophet! In most cases you'll find Facebook Prophet generates pretty good forecasts out of the box, but in addition to this there's a set of tunable parameters.

\[TABLE OF PARAMETERS\]

For more information on hyper parameter tuning, I'd recommend reading the Facebook Prophet documentation and this excellent article on Towards Data Science by Ruan van der Merwe.

Start building your forecasts at [prophecise.com](https://prophecise.com)