---
title: How to use Google Analytics with Jekyll
date: 2016-03-13 00:00:00 Z
permalink: how-to-add-google-analytics-to-your-jekyll-bogl
layout: post
---

Now that you have your Jekyll blog up and running, you'll probably want to know if anyone is reading it. Here's how to add Google Analytics to your Jekyll site in a few simple steps.

<amp-img layout="responsive" width="800" height="517" src="/assets/images/jekyll.jpg"></amp-img>

## Get your Google Analytics tracking code

Create your [Google Analytics account](https://analytics.google.com/analytics/web/?authuser=0#provision/SignUp/ "Google Analytics") if you haven’t already got one.

Navigate to _Admin > Property > Tracking Info > Tracking Code_ and make a note of your Google Analytics tracking number and tracking code, you’ll need these later.

<hr>

## Add your tracking code to Jekyll

Thanks to Jekyll’s templating system, you only have to add your tracking code once, and Jekyll will include the code on all of your blog pages.

Create a new file called `analytics.html` in the `_includes` folder inside your Jekyll project and paste in your Google Analytics tracking code. It should look something like this:

``` html
<script>
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
  ga('create', 'UA-XXXXXXX-X', 'auto');
  ga('send', 'pageview');
  test
</script>
```

Remember to replace `UA-XXXXXXX-X` with `{{ "{{ site.google_analytics " }}}}`.

<hr>

## Add Google Analytics Tracking ID to Jekyll’s _config.yml file

Under 'Google service', add your tracking ID and save.

``` yml
Google services
google_analytics: UA—XXXXXXXX-X
```

Replace `UA—XXXXXXXX-X` with your own tracking ID.

The last step is to open `_includes.head.html` and add this code before the end of the `</head>` tag. 

``` html
{% raw %}{% if site.google_analytics and jekyll.environment == 'production' % "}
{% include analytics.html %}
{% endif %}{% endraw %}
```

This ensures that only page views in the production environment are registered with Google Analytics. Now when you run `jekyll serve` your Google Analytics code will not load.

GitHub Pages is set to `jekyll.environment == 'production'` automatically, so you're ready to start using Google Analytics!
