---
title: How to add Structured Data using Google Tag Manager
date: 2018-05-10 00:00:00 Z
layout: post
---

Google Tag Manager is a great way to add structured data to your page templates. 

<amp-img src="/assets/images/structured-data.jpg" height="343" width="1325" layout="responsive"></amp-img>

### Benefits of implementing with GTM

Adding your structured data using GTM can help you:

- Cut down on development costs
- Deploy quickly and avoid development scheduling
- Structured data managed in one place with all other marketing tags
- Built-in testing tools

Be sure to use the JSON-LD format, as Google can still read this format when injected using GTM.

### Why you should add structured data to your site

Adding structured data to your website makes it eligible for [Google's Search Features](https://developers.google.com/search/docs/guides/search-features "Search Features")

This results in:

- More organic traffic
- Decreased bounce rate due to higher relevancy
- Higher rankings
- Better search experience for users

### How to add your structured data

#### 1) Choose a structured data content type

Pick a page template to add your structured data to. Adding structured data to a page template allows you to add structured data to multiple pages dynamically using one tag. Google have a list of supported content types in their [developer documentation](https://developers.google.com/search/docs/data-types/article).

#### 2) Add the structured data as a Custom HTML tag

Paste the strcutured data template in to a Custom HTML tag in GTM, we'll be replacing the existing variables with dynamic variables.

#### 3) Add your dynamic variables

Create Custom JavaScript Variables in GTM, you can scrape these from the page, or ask your developer to add these to the dataLayer.

All you need to do is add your custom variales to your Custom HTML tag:

<amp-img src="/assets/images/ccustom-variables.jpg" height="308" width="634" layout="responsive"></amp-img>

#### 4) Test and publish

Use GTM's preview and debug panel and Google's [Structured Data Testing Tool](https://search.google.com/structured-data/testing-tool) to make sure your tag is working!
