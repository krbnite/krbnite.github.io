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

## Instant Articles
Recently, I was asked about [Instant Articles](https://developers.facebook.com/docs/instant-articles/). 

My first thought before knowing anything was, "Is there an API?"  One thing I've learned about Facebook
is that they make it awfully hard to inspect their page sources and take advantage of Selenium 
automations.  Fortunately, there are several APIs:
* [Instant Articles API](https://developers.facebook.com/docs/instant-articles/api)
* [Instant Article Insights API](https://developers.facebook.com/docs/graph-api/reference/v2.8/instant-article-insights)
* [Aggregated Instant Article Insights API](https://developers.facebook.com/docs/graph-api/reference/v2.8/instant-articles-insights-aggregated)

For automated data collection, the [Instant Articles API](https://developers.facebook.com/docs/instant-articles/api)
itself is not really useful.  That's something for some product engineer or social media manager to leverage.  
The two insights APIs are ultimately more useful for my purposes.  There are some 
[GUI features](https://developers.facebook.com/docs/instant-articles/analytics#dashboard) for visual 
inspection and sanity as well, if needed, which can be found in the "Publisher Tools" section of a
Facebook Page's admin page (see [Instant Article Analytics](https://developers.facebook.com/docs/instant-articles/analytics)
for a more complete overview).  If you're interested in website traffic and similar things, then you 
must use whatever tool you would regularly use, e.g., Google Analytics.  However, what about likes and reactions? That's
something that was in the request I received...  But not immediately available through the APIs or GUI.

## What is an Instant Article anyway?
As Facebook puts is:
> "An Instant Article is an HTML document optimized for fast mobile performance, rich storytelling capabilities,
> branded design and customized visual display."

Keyword is mobile. Instant Articles are not exclusive Facebook content, but optimized HTML documents associated with
pre-existing content on a brand's website. The idea is that a webpage generally takes too long to load for a mobile
user on the Facebook app (e.g., 7-10 seconds), which tests the user's patience and generally reduces engagement. By
creating an Instant Article version of a webpage, which is stored on Facebook's servers, the mobile user can quickly
browser your brand's content since Instant Articles are optimized such that the page often takes less than 1 second to 
load.

As Facebook answer in the FAQs:
> * Instant Articles provides a faster, Facebook-native way to distribute the content publishers already produce for their own websites.
> * Instant Articles are only available on Facebook's mobile app.
> * Building an Instant Article does not automatically create a corresponding Facebook post. It is a separate tool meant to enhance your article once someone shares it on Facebook. It simply means that any time a reader on a mobile device is directed to the article's URL on Facebook, the link will be displayed as an Instant Article.
> * Every article published as an Instant Article must be published on a news publisher's website as well. ...  Having a standard web URL that links to a web-based version of the content ensures that shared links to Instant Articles discovered on Facebook remain accessible to any reader on any platform.

The point is, an Instant Article is not something in and of itself: it's an optimized representation of a thing
already out there on the web, and only expressed to mobile users on the Facebook app.  The same shared content
in the same Facebook post links to the web URL for all other users (e.g., desktop users, 
off-Facebook mobile users, etc). As far as my request is concerned, this is important: the likes, shares, and
reactions to a post that is associated with an Instant Article are not necessarily likes, shares, and reactions
to the Instant Article. Some of 'em, sure, but mixed in with desktop users and the bunch, and also mobile Facebook
users that didn't even bother to click on the article. 

There might be a way to de-aggregate. Not sure yet.

What I have found is that there do appear to be some Instant Article-specific metrics, available through one
of the associated APIs or the GUI.

## Instant Article Data


## Some More Noise on Instant Articles
If you read through the FAQs, Facebook goes to great lengths to suggest that Instant Articles would
not be favored in the News Feed ("Instant Articles are ranked in the News Feed by the same criteria
that we use to rank standard articles on the mobile web") or impact organic reach ("Instant Articles are treated like any other
stories publishers post to Facebook"). However, they also state that the "News Feed ranks stories based
on a number of factors, including the amount of people that interact with them and how much time people spend 
reading them."

As stated on their webpage @ [instantarticles.fb.com](https://instantarticles.fb.com/):
* 20% more Instant Articles read on average
* 70% less likely to abandon the article
* The fast, immersive reading experience inspires people to share Instant Articles 30% more often than mobile web articles on average, amplifying the reach of your stories in News Feed.

<figure>
  <img src="/images/instant-articles-are-fast-and-responsive.png" width="400">
</figure>

Also, in the "Performance Tool" section at the bottom of the Instant Article Analytics page @ 
[developers.facebook.com/docs/instant-articles/analytics](https://developers.facebook.com/docs/instant-articles/analytics):
* In News Feed we rank stories based on how relevant each post is to each individual person; we do not show links higher in people's News Feed because they are Instant Articles. If, based on engagement signals, someone might find an Instant Article more relevant, we may show that link higher in their feed.

So Instant Articles don't impact organic reach or get higher in the News Feed, but then again, Instant Articles
definitely impact organic reach and get higher in the News Feed.  Like, not, but not not-not. Gnomesayin'?

<figure>
  <img src="/images/gnomesayin.gif" width="400">
</figure>


## How to Create an Instant Article
In the simplest sense, an Instant Article is just the content from your webpage with different HTML and XML 
tags. Similar to the meta tags defined for the Open Graph Protocol, when creating an Instant Articles, there
are standardized mark-up features that one must use to apply style and interactive functionality to the 
document. If a brand has a quickly-evolving, high-volume content feed, such mark-up and restructuring of a 
brand's content can be made automatic, but for smaller operations one might prefer to do it manually.



