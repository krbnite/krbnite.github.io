---
title: Facebook Instant Articles
layout: post
---

Over the past several months, I've been on several reconnaissance missions to uncover
what data is available from Facebook, how one might get it, and whether it can be 
automated.  

Much of this has been pure information gathering---snaking in and out of what
may be immediately useful to my company, and what may not be entirely relevant.  For example,
the Graph API has been incredibly useful and I'm still learning how to leverage it (nowhere near
to tapping its full potential).  On the other hand, learning about the Open Graph Protocol 
was more eye-opening than immediately useful (e.g., it seems our website and app use OGP, and maybe
in the future we can think of clever types of 'actions' that we want to collect data on; of course, 
this would have to then be worked out with the product team and so on).  The list of things goes on,
from the super-userful Graph Explorer, to the Page/Video Insights GUIs, GraphQL, and more!

Recently, I was asked about [Instant Articles](https://developers.facebook.com/docs/instant-articles/). 

My first thought before knowing anything was, "Is there an API?"  One thing I've learned about Facebook
is that they make it awfully hard to inspect their page sources and take advantage of Selenium 
automations.  Fortunately, there are several APIs:
* [Instant Articles API](https://developers.facebook.com/docs/instant-articles/api)
* [Instant Article Insights API](https://developers.facebook.com/docs/graph-api/reference/v2.8/instant-article-insights)
* [Aggregated Instant Article Insights API](https://developers.facebook.com/docs/graph-api/reference/v2.8/instant-articles-insights-aggregated)

For automated data collection, the [Instant Articles API] itself is not really useful.  That's something
for some product engineer or social media manager to leverage.  The two insights APIs are ultimately
more useful for my purposes.  There are some 
[GUI features](https://developers.facebook.com/docs/instant-articles/analytics#dashboard) for visual 
inspection and sanity as well, if needed, which can be found in the "Publisher Tools" section of a
Facebook Page's admin page (see [Instant Article Analytics](https://developers.facebook.com/docs/instant-articles/analytics)
for a more complete overview).  If one is interested in website traffic and similar things, then one 
must use whatever tool they regularly use, e.g., Google Analytics.  However, what about likes and reactions? That's
something that was in the request I received...  But not immediately available through the APIs or GUI.

## What is an Instant Article anyway?



