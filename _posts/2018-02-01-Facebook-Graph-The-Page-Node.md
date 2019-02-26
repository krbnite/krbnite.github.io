---
title: Facebook Graph&#58; The Page Node
layout: post
tags: facebook-graph python automation etl wwe
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
      url = fbg + url + '&access_token=' + access_token
    else:
      url = fbg + url + '?access_token=' + access_token
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
of time, then the fields of the Page node by itself might bore you!  Most of its fields represent static, owner-defined bits
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

Question to Self: Do I/we care about any of the Page fields to collect on a periodic basis?  There are over 100 potential
fields listed in the documentation, most of which are either irrelevant to the page category or periodic data collection
in general.

Here is a list of potentially useful fields (for my current purposes):

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
  - that said, the me/ratings edge is filled with ratings... 
* rating\_count
  - definition: Number of ratings for the page
  - came back 0, so not useful to me
  - just like w/ overall\_star\_rating: this is weird since the me/ratings edge is highly populated
* **talking\_about\_count**
  - definition: The number of people talking about this Page
  - seems useful

So, basically, fan\_count and talking\_about\_count might be interesting to monitor at a regular cadence.

### Page Edges
Ok, admittedly at first glance, a lot of edges seem like they can be fields. 

One key distinction is that
a field describes something about the page (literally, it is usually a "text field" description). A field 
provides information about the real thing: Who are the band members? What food style is the
restaurant? Is there nearby public transit available?  Importantly, the values of a field are not Facebook objects, but 
characterizations of a Facebook object.  In contrast, the values of an edge are Facebook objects related to the Page.

Or, as [Facebook puts it](https://developers.facebook.com/docs/graph-api/overview):
> * nodes - basically "things" such as a User, a Photo, a Page, a Comment
> * edges - the connections between those "things", such as a Page's Photos, or a Photo's Comments
> * fields - info about those "things", such as a person's birthday, or the name of a Page

In an more object-oriented vocabulary, you might refer to a node as a Graph Object, and fields
at attributes of that object.  Edges are kind of like passive methods: "Get me all photos from 
this page."   

```python
# Node
get('me', page_token)

# Fields (Attributes)
get('me?fields=id,name', page_token)

# Edge (w/ Edge Method)
get('me/photos?limit=5', page_token)
```

The me/photos endpoint returns a list of photo nodes.  So whereas "me" is a node, "me/photos" is an edge: pathways from 
one node to other, related nodes. (This guy says it all very well: [Optimizing Request Queries to the Facebook Graph API](https://www.sammyk.me/optimizing-request-queries-to-the-facebook-graph-api)).

### Edges as Fields!
Fact is, despite their differences, edges and fields can be treated similarly.  This goes by "nested requests" or
"[field expansion](https://developers.facebook.com/docs/graph-api/using-graph-api#fieldexpansion)," depending on what you're 
reading.  The idea of field expansion is, if we treat edges like fields syntactically, then
we can make requests to multiple edges in a single request to the Graph API!  The related idea of "nested requests" is that,
when using field expansion on edges, you can continue using field expansion on the edge's objects in a nested fashion.

#### Example of Field Expansion
```python
# Old Way: Two Edges = Two Graph Requests
get('me/photos', page_token)
get('me/posts', page_token)

# New Way: Field Expansion
get('/me?fields=photos,posts', page_token)
```

#### Example of Nested Field Expansion
```python
# Old Way
get('/me/albums?fields=name,link&limit=3', page_token)

# Nested Way
get('/me?fields=albums.limit(3){name,link}', page_token)
```

#### Some Words of Warning
Note that the field expansion technique returns a slightly different JSON architecture than the old-school 
technique.  In Python:
```python
old_way = get('me/posts')
new_way = get('me?fields=posts')
old_way['data'] == new_way['posts']['data']
```

Also, not all edges can be treated as fields.  For example, the page 
node already has an edge called "likes", but for whatever reason the "likes" edge cannot be treated
like a field.  Seems like in older versions of the Graph API (until v2.5), `me?fields=likes` would return how many
people liked the page; this field does not exist for v2.6 onward.  


### Edges that don't interest me
The list of Page edges is huge, and not all of them are of immediate interest.  

For example, some of them are fairly "managerial" like many of the Page fields:
* admin\_notes
* blocked (user or page profiles blocked from this page)
* business\_activities (requires VIEW\_BUSINESS\_LOG permission)

Some of the edges go beyond managerial -- these are fieldy-feeling edges! For example,
there is the [screennames edge](https://developers.facebook.com/docs/graph-api/reference/page/screennames/)
that definitely feels like a "field" and also has no real
edge properties (no parameters...no create/update/delete methods).

For many edges, it is difficult to know whether or not it will provide interesting
user data without looking more into it.  In the next section, I look at the edges that
caught my attention, and assess whether they will ultimately be useful for my 
purposes.

### Edges that interest me
Below, I've tried to
gather some edges that are important to my current needs, or ones that appear to have potential. It is
still a big list, and possibly nonexhaustive.

* **me/albums**
  - returns a list of [album nodes](https://developers.facebook.com/docs/graph-api/reference/album/)
  - album nodes have fairly managerial fields: id, can\_upload, count (approximate number of photos in the album), 
  cover\_photo (id of album's cover photo), created\_time, description, event, name, privacy, etc
  - using the album id with info from its edges could be useful, e.g., page\_id, album\_id, num\_likes, num\_reactions
  - album edges: picture (node for cover photo), photos (photo nodes of album's pictures), shared\_posts (stream
  posts that are shares of this album), likes (people who liked the album), reactions (people who have reacted to
  the album), comments
* **me/bc\_sponsored\_posts**
  - def: A list of posts where the page is tagged as sponsor in branded content posts, only posts that are marked as 
  shared with sponsor on creation.
  - e.g., there is a post by NJ Devils announcing a Devils/WWE night on Nov. 9, 2017
* me/call\_to\_actions
  - the call-to-actions (CTAs) created by this Page, returned as [Page CTA nodes](https://developers.facebook.com/docs/graph-api/reference/page-call-to-action/)
  - I thought maybe the CTA nodes returned by this edge would then have interesting info, like click rate; however, they contain
  more "managerial" info (e.g., the type of action, the last time it was updated, etc)
* me/canvases
  - returns a list of canvas nodes, which represent the page's canvas documents
  - a canvas node is very managerial, having the fields: id, background\_color, body\_elements, canvas\_link, is\_hidden, is\_published,
  last\_editor, name, owner, update\_time
  - doesn't seem to directly provide the type of info I'm looking for
* /me/checkin\_posts: 
  - lists [Checkin Posts](https://developers.facebook.com/docs/graph-api/reference/page/checkin_posts/) 
  - "All public posts in which the page has been tagged as the location."

* /me/claimed\_urls: 
  - Claimed URLs for Instant Articles that are associated with this Facebook Page
  - [docs](https://developers.facebook.com/docs/graph-api/reference/page/claimed_urls/)
  - not really too useful: just lists domain names we own that are associated with the Instant Articles we publish

* /me/crosspost\_whitelisted\_pages: Pages whitelisted for crossposting
  - [docs](https://developers.facebook.com/docs/graph-api/reference/page/crosspost_whitelisted_pages/)
  - just gives page nodes for pages granted crosspost permissions

* **/me/events**
  - [page event docs](https://developers.facebook.com/docs/graph-api/reference/page/events/)
  - returns a list of [event nodes](https://developers.facebook.com/docs/graph-api/reference/event/)
  - could be very useful
  - worth digging deep on (will write a post dedicated to page/events)
  - fields include: attending\_count, declined\_count, interested\_count
  - edges include: attending (users attending the event), comments, interested (users interested in the event),
  maybe (users who replied 'maybe'), feed

* /me/featured\_videos\_collection
  - [docs](https://developers.facebook.com/docs/graph-api/reference/page/featured_videos_collection/)
  - could be useful, but seems like we do not populate this feature
  
* **/me/insights**
  - Obviously useful (will write whole post dedicated to it)
  - [docs](https://developers.facebook.com/docs/graph-api/reference/page/insights/)  
* **/me/instant\_articles**
  - [docs](https://developers.facebook.com/docs/graph-api/reference/page/instant_articles/)
  - returns list of InstantArticle nodes (with default fields: id, canonical\_url, and html\_source)
  - useful for returning object ID of individual instant articles, which can then be used to obtain article-level insights
* **/me/instant\_articles\_insights**
  - [docs on page-level insights for Instant Articles](https://developers.facebook.com/docs/graph-api/reference/instant-articles-insights-aggregated)
  - Provides page-aggregated view on Instant Article metrics
  - Obviously useful (will write whole post dedicated to it)
  - this edge requires that the `metric` and period parameters are populated, e.g. `me/instant_articles_insights?metric=all_views&period=day`
  - metrics have specific cadences they are available at: 
    * daily: all\_views
    * weekly: all\_view\_durations, all\_view\_durations\_average, all\_scrolls, all\_scrolls\_average
    
* /me/likes: 
  - [docs](https://developers.facebook.com/docs/graph-api/reference/page/likes/)
  - this is a weird one: 
    * in the documentation, it says that it provides the "number of users who like the Page. For Global Pages this is the count for all Pages across the brand."
    * however, in reality, it lists names and Page IDs of other [Facebook Pages](https://developers.facebook.com/docs/graph-api/reference/page/) liked by the "me" Page 
    * if you treat the edge like a field, it does not return anything (me?fields=likes)
    
* /me/live\_videos
  - Error Code 10: *To use live-video-api on behalf of people who are not admins, developers and testers of your app, your use of this endpoint must be reviewed and approved by Facebook.  To submit this feature for review please read our documentation on reviewable features: https://developers.facebook.com/apps/review.*
  - returns a list of [LiveVideo](https://developers.facebook.com/docs/graph-api/reference/live-video/) nodes
  - this is for people setting up the live streams
  - I can get live video info from the video\_broadcasts edge
  
* /me/media\_fingerprints
  - Error Code 100: *The page is not qualified to use Media Fingerprint API. Please verify that the page has completed Media Fingerprint API onboarding process.*

* /me/photos
  - useful to track photo engagement
* /me/posts  
  - this looks the same as me/feed, but is actually a subset of me/feed
* /me/ratings
  - could be useful: lists user rating and datetime, which can allow us to track rating dynamics over time
* /me/tagged
* /me/video\_broadcasts
  - this is how I get a list of our live content
* /me/videos
  - all videos
* /me/video\_media\_copyrights
* /me/videos\_you\_can\_use  (list of videos page can use for crossposting....)
* /me/visitor\_posts


## Miscellaneous
### The Limit on Limit
One thing you will notice right away when using the Facebook Graph API is that it, by default,
returns only a handful of list items on each page.  You can use `limit` to make this amount
smaller, but importantly: you can use it to make it bigger too!  

The limit was 500 at one point, though more recently it has been 100:
https://stackoverflow.com/questions/14876105/the-limit-of-facebooks-graph-api-limit-parameter

### Feed vs Posts
The Page Feed includes everything you might see on the page's feed, including tagged posts and
checkins... The posts edge is just posts...

https://stackoverflow.com/questions/6214535/what-is-the-difference-between-feed-posts-and-statuses-in-facebook-graph-api


### Order, Order!!
It might be important to return posts or photos or what-have-you in strict `chronological` or
`reverse_chronological` order.  This is done like:
```
/{node}/{edge}?fields=someField.order(reverse_chronological)
```


## Node Introspection: What Page Edges and Fields are there again?
One might not want to go back to the docs every time they want to know what fields and
edges exist for a given node.  For this, once can use a feature called 
[node instrospection](https://developers.facebook.com/docs/graph-api/using-graph-api#introspection):

```python
get(fbg+'me?metadata=1)['metadata']['connections'].keys()
```
Doing this, I found an edge not listed in the docs: agencies.  So issuing this command every once in
a while could be good for your health!  (The docs are not always up to date.)


## References


* https://www.sammyk.me/optimizing-request-queries-to-the-facebook-graph-api
* https://developers.facebook.com/docs/platforminsights/page
* https://developers.facebook.com/docs/pages/access-tokens
* https://stackoverflow.com/questions/6214535/what-is-the-difference-between-feed-posts-and-statuses-in-facebook-graph-api
* https://stackoverflow.com/questions/14876105/the-limit-of-facebooks-graph-api-limit-parameter
