---
title: Track button clicks as Google Analytics events using Google Tag Manager
date: 2016-03-08 00:00:00 Z
permalink: /blog/track-button-clicks-using-google-tag-manager
layout: post
---

In this post I’ll show you how to track button clicks by using Google Tag Manager to send events to Google Analytics.

<amp-img src="/assets/images/click-me.gif" height="349" width="660" layout="responsive" alt="Track button clicks with GTM"></amp-img>

#### 1) Set up your click trigger

First set up a generic click trigger. We’ll be updating this later.

1. Click _Trigger_
1. Create a _new_ Trigger
1. Choose _All Elements_ under Click
1. Fire the trigger on _All Clicks_
1. _Save_

<amp-img src="/assets/images/all-clicks-trigger.jpg" height="189" width="660" layout="responsive" alt="GTM trigger configuration"></amp-img>

Open up the GTM preview panel on your website and click the _variables_ tab. 

Click the button you would like to track. You can now see the variables that define the specific button click. We’ll use these variables to update our generic click trigger so that it that only tracks your specific button clicks.

<amp-img src="/assets/images/variables.jpg" height="266" width="660" layout="responsive" alt="GTM variables"></amp-img>

<hr>

#### 2) Update your generic click trigger

1. Give your generic click trigger a suitable name
1. Update to Fire on _Some Clicks_.
1. Choose _Click ID_ > **Containing** > **[Your button ID]** eg `my-button`

<amp-img src="/assets/images/trigger-connfig.jpg" height="245" width="660" layout="responsive" alt="GTM trigger configuration with some clicks"></amp-img>

<hr>

#### 3) Create a tag that sends event data to Google Analytics

1. Go to _Tags_
1. _Create_ New tag
1. Choose the _Universal Google Analytics_ tag type
1. Enter your Google analytics tracking ID
1. Choose the _Event_ Track Type
1. Enter the _Category_,  _Action_ and _Label_
1. Under the Triggering section select your Click Trigger
1. _Save_

<amp-img src="/assets/images/button-click-event.jpg" height="308" width="660" layout="responsive" alt="GTM event"></amp-img>

I've already saved my Google Analytics ID in a constant variable and recommend you do the same. This way if you ever need to update the ID you only have to update one variable.

<hr>

#### 4) Check your tag is working

Open the preview and debug window and make sure that the tag is firing correctly.

<amp-img src="/assets/images/check-button-click-tag.jpg" height="309" width="660" layout="responsive" alt="GTM tag firing"></amp-img>

Open Google Analytics and go to _Real-time_ > _Events_ where you will be able to see your button clicks in real time.

<amp-img src="/assets/images/real-time-button-click.jpg" height="243" width="660" layout="responsive" alt="GA real-time events"></amp-img>
