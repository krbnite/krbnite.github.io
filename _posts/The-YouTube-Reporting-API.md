---
layout: post
title: The YouTube Reporting API
---

Scraping is great for views, likes, dislikes, and sometimes a few other quantities.  But in my experience, 
there is only so much you can get without using YouTube analytics.  If selenium is in your arsenal, you
can create your own API to do things like sign into analytics.youtube.com and scrape around.  

But is that the best way?

For example, YouTube offers the [Analytics API](https://developers.google.com/youtube/analytics/v1/data_model) for 
targeted queries against viewership and ad performance data.  If you can sign into to the CMS and do some point-and-click
to find what you seek, then you can probably use the Analytics API.  That said, if you want slice-and-dice the data 
in multiple ways, you might find yourself making programs with all kinds of for-loops to account for different dimensions,
metrics, and filters. If you have only one YouTube channel, this might still seem ok... But not if you are a content
owner with hordes YouTube channels. 

As a content owner (or data scientist working for one), wouldn't it be nice to just have all of the data in
your own database, which you can query however you want and bring into your favorite programming environment?

Well, that's basically what the [YouTube Reporting API](https://developers.google.com/youtube/reporting/v1/reports/) 
is for, at least in terms of your channels' viewership and ad-performance data.  For other types of data, like
comments, there exist other APIs (e.g., the Data API and the Content ID API).  For now, forget about that shit, lest
your head explode.  Fact is, figuring out how to use these APIs can be tricky at first, but once you figure out 
one, learning the others is simple and straightforward.

