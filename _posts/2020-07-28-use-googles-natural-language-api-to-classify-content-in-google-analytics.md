---
title: Use machine learning to automatically categorise content in Google Analytics
date: 2020-07-28 13:45:00 Z
permalink: use-googles-natural-language-api-to-classify-content-in-google-analytics
categories:
- Google Analytics
- NLP
- Machine Learning
layout: post
---

Automatically categorise all of the content on your website and upload the results to Google Analytics using Google Cloud Platform. This process can be scheduled so all new content is classified automatically.

<amp-img src="/assets/images/nlp-ga.jpg" width="1280" height="720" layout="responsive"></amp-img>

Here's how you can scrape content from websites using an [open-source web crawler](https://github.com/yujiosaka/headless-chrome-crawler), run the content through Google's pre-trained content classification model and then uploads the results to Google Analytics using the Google Analytics [Management API](https://developers.google.com/analytics/devguides/config/mgmt/v3). 

## Why?

Classifying your content allows content creators to see patterns and insights based on categories assigned by Google and enables segmentation of users by reading habits. Categorising your content automatically also improves speed and accuracy while reducing manual labor.

## What is Google's NLP API?

Google's [Natural Language API](https://cloud.google.com/natural-language) is one of Google's machine leaning and AI products. It allows us to derive insights from unstructured text using the same deep machine learning technology that powers both Google Search and the language-understanding system behind Google Assistant.

## How it works

We'll use a Google Compute Engine VM instance to run the crawler script. Running the script in the cloud means you can scale up the instance to have more power than your local machine and the crawl will complete faster.

<amp-img src="/assets/images/scraper-vm.png" width="2386" height="654" layout="responsive"></amp-img>

The web crawler scrapes the relevant text content on the pages of your website and runs the result through Google's Natural Language API which has the ability to classify documents in more than 700 predefined categories.

The results are then uploaded to Google Analytics via the Management API, you'll need to set up some custom dimensions and the data import in GA for this.

## How to set it up

First you'll need to set up a Google Cloud Platform project and enable billing.

1) Create three custom dimensions in GA for `Primary Category`, `Subcategory` and `Secondary Subcategory`

2) Set up a Data Import in GA with the type "Content". For the data set schema: "Key" should be set to `Page` and "Imported Data" should be set to the three new custom dimensions

3) Clone this repo `git clone https://github.com/alsjohnstone/scraper-classification.git`

4) Enable NLP API and Analytics API in your GCP project

5) Create service worker account and download the credentials.json file

6) Update the config.json file

7) Create a new GCP storage bucket and add your config.json and credentials.json files

8) Update the bucket referenced in the install.sh file with your bucket

9) Run the command below to spin up a new GCP Compute Instance that will automatically shutdown after the crawl is complete.

```
gcloud compute instances create scraper \
    --machine-type=n1-standard-16 \
    --metadata-from-file=startup-script=./install.sh \
    --scopes=cloud-platform \
    --zone=europe-west2-c
```

Restarting the GCP Compute Instance will automatically scrape, classify and upload the results to GA. You can schedule crawls using Cloud Scheduler.

