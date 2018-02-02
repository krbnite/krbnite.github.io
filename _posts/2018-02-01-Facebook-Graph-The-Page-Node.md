---
title: Facebook Graph&#58; The Page Node
layout: post
---

First, let's make the code blocks a bit friendlier looking: some definitions! 

```python
import requests
import json

VERSION = '2.11'  # or newer/whatever
fbg = 'https://graph.facebook.com/'+VERSION+/'

def get(url, access_token):
  if access_token:
    if url.find('?') > 0:
      url = url + '&access_token=' + access_token
    else:
      url = url + '?access_token=' + access_token
  return json.loads(requests.get(url).text)
```
I'll be assuming you are using a [page token](https://developers.facebook.com/docs/pages/access-tokens)
throughout the article.  This is a decent assumption since:
> As of February 5, 2018 all metrics require a page access token of the page of which the insights are being queried.

Since the goal here are the insights, this is pretty relevant.


## The Facebook Page Node
The Page Node represents a Facebook Page, e.g., there is a wrestling company called WWE, which has its
own Facebook Page @ [https://www.facebook.com/wwe](https://www.facebook.com/wwe).  You 
can hit the Graph API with a Page name ("wwe"), but if you're managing tens to hundreds of pages, it's
more likely you'll be using page IDs. For example, the URL 
"[https://www.facebook.com/7175346442](https://www.facebook.com/7175346442)" goes to the same webpage. From this,
the Page ID should be obvious.

### Page Fields
If you're someone interested in social, engagement, viewership and/or viewership behavioral data as a function
of time, then this node by itself might bore you!  Most of its fields represent static, owner-defined bits
of information: about, affiliation, awards, bio, birthday, category, contact_address, emails.

That said, if you're scraping around a bunch of local businesses, some of this could be what you're looking for!  

A lot of the the Page Node's fields are super specific to what type of page it is, e.g., if you have a band, then
there a quite a few band-related fields (artists\_we\_like, band\_interests, band\_members, booking\_agent, bio, 
hometown, influences, press\_contact, record\_label).  You can imagine creating an app that grabs data on
local bands like this.  Similarly, a restaurant page has its own string of fields (attire, culinary\_team,
food\_styles, general\_manager, payment\_options, price\_range, public\_transit, restaurant\_services, 
restaurant\_specialties).  This data is useful for an app that helps a user quickly find some food ("I want 
some Thai at an upscale restaurant that accepts credit and is near public transit, damn it!")

Other types of Pages include: Film, Vehicle, Person, Company, Team Org, TV Show, and more. 

Importantly, the Page Node has data about the thing, not things that interact with thing: that's where Page Insights
comes into play!  And that's mostly the type of thing I'm after.  But I'll get to that in another post.

Question to Self: Do I/we care about any of the Page fields to collect on a periodic basis?

* engagement
  - definition: the social sentence and like count information for this Page; this is the same info used for the like button
  - only the count is useful to me, e.g., {'engagement': {'count': 38746716, 'social\_sentence': '38M people like this.'}}
* **fan\_count**
  - definition: the number of users who like the Page; for Global Pages this is the count for all Pages across the brand
  - this obviates any need I had for the engagement field
* new\_like\_count
  - definition: the number of people who have liked the Page, since the last login; only visible to a page admin
  - seemed like a contender, but it is fairly subjective
* featured\_video
  - definition: Video featured by the Page
  - could be useful to track how different featured videos generate engagement
  - that said, for the page I'm currently working on, this field is empty
* instagram\_business\_account
  - definition: Instagram account linked to page during Instagram business conversion flow
  - useful in getting our Instagram account ID, which I might be able to hit with the Graph API
  - [Instagram Graph API](https://developers.facebook.com/docs/instagram-api)
* is\_webhooks\_subscribed
  - definition: Indicates whether the application is subscribed for real time updates from this page
  - I just want to learn more about webhooks in general
* overall\_star\_rating
  - definition: Overall page rating based on rating survey from users on a scale of 1-5; this value is normalized and is not guaranteed to be a strict average of user ratings
  - seemed cool, but the field came back empty
* rating\_count
  - definition: Number of ratings for the page
  - came back 0, so not useful to me
* **talking\_about\_count**
  - definition: The number of people talking about this Page
  - seems useful

So, basically, fan\_count and talking\_about\_count might be interesting to monitor at a regular cadence.

### Page Edges
Ok, admittedly at first glance, a lot of edges seem like they can be fields. One key distinction is that
a field describes something about the page (literally, it is usually a "text field" description), while an
edge is ... A field says something about the real thing: Who are the band members? What food style is the
restaurant? Is there nearby public transit available?  The values of a field are not a Facebook objects, but 
characterizations of a Facebook object (the Page).  

The values of an edge are Facebook objects related to the Page.

Big, but nonexhaustive list (needs editing!): Edges that interest me

Not that the other edges aren't interesting... But I'm trying to be practical and focused. I've listed
some ones that might be immediately useful to me or my team.

* /me/likes: lists names and Page IDs other [Facebook Pages](https://developers.facebook.com/docs/graph-api/reference/page/) liked by this Page 
* /me/blocked: [Users](https://developers.facebook.com/docs/graph-api/reference/user/) or [Pages](https://developers.facebook.com/docs/graph-api/reference/page/) blocked from this Page
* /me/checkin_posts: lists [Checkin Posts](https://developers.facebook.com/docs/graph-api/reference/page/checkin_posts/) ("All public posts in which the page has been tagged as the location.")
* /me/claimed_urls: Claimed URLs for Instant Articles that are associated with this Facebook Page; https://developers.facebook.com/docs/graph-api/reference/page/claimed_urls/
* /me/crosspost_whitelisted_pages: Pages whitelisted for crossposting; https://developers.facebook.com/docs/graph-api/reference/page/crosspost_whitelisted_pages/
* /me/featured_videos_collection: https://developers.facebook.com/docs/graph-api/reference/page/featured_videos_collection/
* /me/insights
* /me/instant_articles
* /me/instant_articles_insights
* /me/live_videos
* /me/media_fingerprints
* /me/photos
* /me/posts  (same as /me/feed?)
* /me/ratings
* /me/tagged
* /me/video_broadcasts
* /me/videos
* /me/video_media_copyrights
* /me/videos_you_can_use  (list of videos page can use for crossposting....)
* /me/visitor_posts

What's weird is that sometimes an edge can be treated like a field...or something... Idk.  
* e.g.,  /me?fields=promotable_posts.limit(5)
* but not: /me?fields=ratings.limit(5)  # Error!!!!!!

Then there is the screennames edge that definitely feels like a "field" and also has no real
edge properties (no parameters...no create/update/delete methods)
https://developers.facebook.com/docs/graph-api/reference/page/screennames/


(FINISH WRITING/EDITING)

## References


Page Insights 
* https://developers.facebook.com/docs/platforminsights/page
* https://developers.facebook.com/docs/pages/access-tokens
