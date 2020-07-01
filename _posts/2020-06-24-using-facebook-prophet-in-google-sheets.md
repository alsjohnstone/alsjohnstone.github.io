---
title: Build forecasts in Google Sheets using Facebook Prophet
date: 2020-06-24 13:45:00 Z
permalink: how-to-use-facebook-prophet-in-google-sheets-function
categories:
- Facebook Prophet
- Forecasting
- Google Sheets
layout: post
---

Here’s how you can use Facebook’s open-source Prophet library in Google Sheets to create accurate time-series predictions using simple and intuitive parameters.

<amp-img src="/assets/images/sheets-prophet.png" width="1280" height="720" layout="responsive"></amp-img>

This is a proof-of-concept solution for Prophet forecasting in Google Sheets. You might want to build on this to include additional parameters that will give you greater control over the forecast model.

## What is Facebook Prophet?

[Prophet](https://facebook.github.io/prophet/) is open source software released by Facebook’s Core Data Science team. It is a procedure for forecasting time series data and works best with time series that have strong seasonal effects and several seasons of historical data. It is available for download on CRAN and PyPI.

## Overview

Here’s an overview of the data flow for this solution:

<amp-img src="/assets/images/sheets-prophet-data-flow.png" width="1403" height="693" layout="responsive"></amp-img>

The user fills in the custom function and those variables are sent off to our Python script for forecasting and the results returned to Google Sheets.

## Get started by building your own API with Python

We’ll be building our own API using [FastAPI](https://fastapi.tiangolo.com/), which is a modern, fast (high-performance), web framework for building APIs with Python. This will take parameters from the Google Sheets function and do all the forecasting in the background with Facebook Prophet. I won't go in to how to use FastAPI but you can read all you need to know [here](https://fastapi.tiangolo.com/).

Here we're using some of the more intuitive parameters for Prophet, you can read more about the additional parameters that are available to you in the [Prophet documentation](https://facebook.github.io/prophet/docs/quick_start.html#python-api). I also found this [Implementing Facebook Prophet efficiently](https://towardsdatascience.com/implementing-facebook-prophet-efficiently-c241305405a3) post on Towards Data Science really useful.

{% highlight python %}
from fastapi import FastAPI
from pydantic import BaseModel
import pandas as pd
from fbprophet import Prophet

class Data(BaseModel):
    length: int
    ds: list
    y: list
    model: str
    daily: bool = False
    weekly: bool = False
    annual: bool = False
    upper: float = None
    lower: float = 0.0
    national_holidays: str = None

app = FastAPI()

@app.post("/prophecise/")
async def create_item(data: Data):

    # Create df from base model
    df = pd.DataFrame(list(zip(data.ds, data.y)), columns =['ds', 'y'])

    # Add the cap and floor to df for logistic model
    if data.model == "logistic":
        df['y'] = 10 - df['y']
        df['cap'] = data.upper
        df['floor'] = data.lower

    # make basic prediction
    m = Prophet(growth=data.model,
                weekly_seasonality=data.weekly,
                daily_seasonality=data.daily,
                yearly_seasonality=data.annual
                )
    
    # Add national holidays
    if data.national_holidays is not None:
        m.add_country_holidays(country_name=data.national_holidays)
    
    # Fit data frame
    m.fit(df)

    # Create data frame for future
    future = m.make_future_dataframe(periods=data.length)

    # Add the cap and floor to future for logistic model
    if data.model == "logistic":
        future['cap'] = data.upper
        future['floor'] = data.lower

    # Prophecise!
    forecast = m.predict(future)

    # Print values
    print(list(forecast[['ds']].values))

    # Return results
    return [forecast[['ds']], forecast[['yhat']], forecast[['yhat_lower']], forecast[['yhat_upper']]]
{% endhighlight %}

## Upload your API to Google Cloud Platform
We’ll be running our API on Google Cloud Platform using [Cloud Run](https://cloud.google.com/run), which is a fully managed compute platform for deploying and scaling containerized applications quickly and securely. You'll need to add a billing account to GCP if you haven't already, but there is a generous free tier.

To deploy our API on Cloud Run we first need to containerize our app using Docker. If you’re not familiar with Docker, I recommend taking a look at their [documentation](https://docs.docker.com/get-started/). You’ll need to create a Dockerfile and deploy on Cloud Run which should look something like this:

{% highlight nginx %}
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.7

COPY ./app /app

WORKDIR /app

RUN apt-get -y update  && apt-get install -y \
  python3-dev \
  apt-utils \
  python-dev \
  build-essential \
&& rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade setuptools
RUN pip install cython
RUN pip install numpy
RUN pip install pandas
RUN pip install matplotlib
RUN pip install pystan
RUN pip install fbprophet
RUN pip install fastapi
RUN pip install pydantic
{% endhighlight %}

Once you’ve containerized your API you can [deploy to Cloud Run](https://cloud.google.com/run/docs/quickstarts/build-and-deploy). Once complete, you’ll be given a URL like the one shown below, which will be used as the API endpoint in our Apps Script.

<amp-img src="/assets/images/cloud-run.png" width="861" height="297" layout="responsive"></amp-img>

## Finally, we use Apps Script to call our API
Now we're ready to set up our custom function for Sheets in Apps Script. We’ll take the user inputs from our custom function and send these on to our API for forecasting. The script below sets up the custom function, calls the API and returns the forecast to the user. You'll need to update the `url` variable to your own Cloud Run URL.

{% highlight javascript %}
/**
 * Forecast data using Facebook Prophet. Upper and Lower bounds are only required when using the logistic regression model. More information can be found at https://facebook.github.io/prophet/
 *
 * @param {A2:A150} dates - Select the pre-forecast date range as a STRING
 * @param {B2:B150} values - Select the pre-forecast metric values as a STRING
 * @param {14} forecastLength - How many days would you like to forecast?
 * @param {"linear"} model - "linear" or "logistic"
 * @param {"True"} annual - Does data contain annual seasonality? true or false
 * @param {"False"} weekly - Does data contain weekly seasonality? true or false
 * @param {"False"} daily - Does data contain daily seasonality? true or false
 * @param {""} upper - Select the pre-forecast date range (Only required for logistic regression model)
 * @param {""} lower - Select the pre-forecast date range (Only required for logistic regression model)
 * @param {"UK"} nationalHolidays - ISO of country you'd like to add national holidays for e.g. "UK" 
 * @return Facebook Prophet Forecast.
 * @customfunction
 */

function prophecise(dates, values, forecastLength, model, annual, weekly, daily, nationalHolidays, upper, lower) {
  
	//  API endpoint
	var url = 'YOUR_CLOUD_RUN_URL';
  
	// Format the date and values  
	var dates = SpreadsheetApp.getActiveSpreadsheet().getRange(dates).getDisplayValues().join().split(','),
        dates = dates.filter(String),
        values = SpreadsheetApp.getActiveSpreadsheet().getRange(values).getDisplayValues().join().split(','),
        values = values.filter(String),
        formattedDates = dates.join().split(','),
        formattedMetrics = values.join().split(','),
        preforecastData = {
		"length": forecastLength,
		"ds": formattedDates,
		"y": formattedMetrics,
		"model": model.toLowerCase(),
		"annual": annual.toString().toLowerCase(),
		"weekly": weekly.toString().toLowerCase(),
		"daily": daily.toString().toLowerCase(),
		"upper": upper,
		"lower": lower,
		"national_holidays": nationalHolidays
	};
  
	// Add the pre-forecast data to the payload
	var options = {
		'method': 'post',
		'contentType': 'json',
		'payload': JSON.stringify(preforecastData)
	};
  
	// Call prophecise API
	var response = UrlFetchApp.fetch(url, options);
  
	// Return forecast result
	var json = JSON.parse(response)
	var dates = Object.values(json[0].ds);
	var forecast = Object.values(json[1].yhat);
	var forecastresult = [];
	dates.forEach(function(date) {
		forecastresult.push([date]);
	});
	forecast.forEach(function(yhat, index) {
		forecastresult[index].push(yhat);
	})
	forecastresult.splice(0, 0, ["Date", "Forecast"]);
	return forecastresult;
  
}
{% endhighlight %}

## Using our new formula in Google Sheets
I’ve been using the formula to forecast Google Analytics data in Google Sheets using the Google Analytics add-on and our custom function. So for example, the following function would take the dates from column A and the metrics in column B and return a 30 day forecast that takes in to account national holidays in the UK:

`=prophecise("A2:A","B2:B",30,"linear",true,false,false,"UK")`

<amp-img src="/assets/images/prophet-function.png" width="933" height="466" layout="responsive"></amp-img>

In this example I then used the Prophet forecasts in a Google Data Studio dashboard to show predicted trends for KPIs.

Hopefully you found this post useful and it has inspired you to start using your own Python/R functions in Google Sheets, just think of all the cool stuff you could do!