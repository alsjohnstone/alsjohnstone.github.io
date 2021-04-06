---
title: 'Prophecise: a tool for forecasting GA data using Facebook Prophet'
date: 2020-06-21 13:45:00 Z
permalink: prophecise-forecast-ga-data-with-facebook-prophet
categories:
- Facebook Prophet
- Google Analytics API
- Forecastr
- Prophecise
layout: post
---

I built a [web app](https://prophecise.com) that leverages Facebook Prophet to create fast and robust time-series forecasts from your Google Analytics data, all wrapped up in a simple user interface.

<amp-img src="/assets/images/screely-1592742794169.png" width="929" height="510" layout="responsive"></amp-img>

I wanted a create a fast and simple way for marketers/analysts/data scientists to generate forecasts from their Google Analytics data using Facebook Prophet. When I was researching how to do this I came across Gareth Cull’s amazing open-source [Forecastr](https://github.com/garethcull/forecastr) app, which has been an amazing starting point for [Prophecise](https://prophecise.com).

## What is Facebook Prophet?

[Prophet](https://facebook.github.io/prophet/) is open source software released by Facebook’s Core Data Science team. It is a procedure for forecasting time series data and works best with time series that have strong seasonal effects and several seasons of historical data. It is available for download on [CRAN](https://cran.r-project.org/package=prophet) and [PyPI](https://pypi.python.org/pypi/fbprophet/).

## What is Prophecise?

Prophecise is web app that I built using Python and JavaScript to automatically forecast your GA data. It allows users to log in with their Analytics account and pull their data directly from GA into the guided UI to preview the data before generating a forecast.

Here's a high-level overview of the data flow:

<amp-img src="/assets/images/prophecise-data-flow.jpg" width="1280" height="582" layout="responsive"></amp-img>

The first step is to select the *account*, *property* and *view* you’d like to pull data from, and which metric you’d like to forecast. There are also additional options to enter your own metric (if the one you need is not available on the dropdown) and to select a specific segment. To see a list of all available metrics see Google’s [Dimensions and Metrics Explorer](https://ga-dev-tools.appspot.com/dimensions-metrics-explorer/).

<amp-img src="/assets/images/prophecise-ga-data.jpg" width="963" height="608" layout="responsive"></amp-img>

This data is then sent back to the server so we can do some forecasting with Facebook Prophet! In most cases you'll find Facebook Prophet generates pretty good forecasts out of the box, but in addition to this there's a set of tunable parameters.

<amp-img src="/assets/images/prophecise-tunable.jpg" width="896" height="481" layout="responsive"></amp-img>

For more information on hyper parameter tuning, I'd recommend reading the [Facebook Prophet documentation](https://facebook.github.io/prophet/) and this [great article](https://towardsdatascience.com/implementing-facebook-prophet-efficiently-c241305405a3) on Towards Data Science by Ruan van der Merwe.

Start building your forecasts at [prophecise.com](https://prophecise.com?utm_source=prophecise-post&utm_medium=my-blog)
