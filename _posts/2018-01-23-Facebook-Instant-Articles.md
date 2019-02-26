---
title: Facebook Instant Articles
layout: post
tags: facebook-graph wwe
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
Unfortunately, I was having problems accessing the data using the Graph API: the error suggested that I did
not have the `pages_manage_instant_articles` permission.  With this permission, you can access data on a 
page's instant articles like so (assuming "me" refers to a page token):
```
me/instant_articles
me/instant_articles_insights
```

* [Instant Articles: Insights](https://developers.facebook.com/docs/graph-api/reference/v2.12/instant-article-insights)
* [Instant Articles: Aggregated Insights](https://developers.facebook.com/docs/graph-api/reference/v2.12/instant-articles-insights-aggregated)


### The Instant Article Performance Tool
Read about it [here](https://media.fb.com/2017/07/19/new-instant-articles-analytics-tool/). Unfortunately,
it appears none of the accounts I work on have access to this tool (though I did email Facebook and eagerly
await their reply!).

## How to Create an Instant Article
In the simplest sense, an Instant Article is just the content from your webpage with different HTML and XML 
tags. Similar to the meta tags defined for the Open Graph Protocol, when creating an Instant Articles, there
are standardized mark-up features that one must use to apply style and interactive functionality to the 
document. If a brand has a quickly-evolving, high-volume content feed, such mark-up and restructuring of a 
brand's content can be made automatic, but for smaller operations one might prefer to do it manually.



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

That said, earlier this month Facebook decided it will be 
[Bringing People Closer Together](https://newsroom.fb.com/news/2018/01/news-feed-fyi-bringing-people-closer-together/),
or in other words: deprioritizing publisher content on the newsfeed in favor of personal posts.  As Mark Zuckerberg
puts it: 
> "[R]ecently we've gotten feedback from our community that public content -- posts from businesses, brands and media -- is crowding out the personal moments that lead us to connect more with each other. ... The research shows that when we use social media to connect with people we care about, it can be good for our well-being. We can feel more connected and less lonely, and that correlates with long term measures of happiness and health. On the other hand, passively reading articles or watching videos -- even if they're entertaining or informative -- may not be as good. Based on this, we're making a major change to how we build Facebook. ... The first changes you'll see will be in News Feed, where you can expect to see more from your friends, family and groups. As we roll this out, you'll see less public content like posts from businesses, brands, and media."

Basically, if there was any potential for the indirect prioritization of Instant Articles in the News Feed, it's
now likely offset by the nature of their origin: publishers!  

I mean, wow, this is a huge blow to publisher content -- the article goes on:
>Today we use signals like how many people react to, comment on or share posts to determine how high they appear in News Feed.
>
> With this update, we will also prioritize posts that spark conversations and meaningful interactions between people. To do this, we will predict which posts you might want to interact with your friends about, and show these posts higher in feed. These are posts that inspire back-and-forth discussion in the comments and posts that you might want to share and react to – whether that’s a post from a friend seeking advice, a friend asking for recommendations for a trip, or a news article or video prompting lots of discussion.
>
> We will also prioritize posts from friends and family over public content, consistent with our [News Feed values](https://newsroom.fb.com/news/2016/06/building-a-better-news-feed-for-you/).
>
> Because space in News Feed is limited, showing more posts from friends and family and updates that spark conversation means we’ll show less public content, including videos and other posts from publishers or businesses.
>
> As we make these updates, Pages may see their reach, video watch time and referral traffic decrease. 

And it goes on, making sure publishers make no mistake: GoFY!  

Man, I know this post is getting a bit siderailed, but it's just crazy to read about the history of Instant Articles,
then all of this recent stuff.  




