---
title: Metrics on the Instagraph
layout: post
tags: facebook-graph wwe
---

In the last article on the Instagram graph, I covered some fields and edges on the Instagram
Account, Story, and Media nodes.  In this post, I talk a little bit about the available metrics
on the Instagraph.

## Account Insights
Like a Page node, an Instagram Account node has its own Account-level insights... But what metrics
are available?  Easiest way to find out is to just make up a bogus metric name and try it out. The error
message will list the valid metrics (listed below).

```python
token.get(f'{insta_id}/insights?metric=wanker')
```

* impressions (day, week, days_28)
* reach (day, week, days_28)
* follower_count (day)
* email_contacts (day)
* phone_call_clicks (day)
* text_message_clicks (day)
* get_directions_clicks (day)
* website_clicks (day)
* profile_views (day)
* audience_gender_age (lifetime)
* audience_locale (lifetime)
* audience_country (lifetime)
* audience_city (lifetime)
* online_followers (lifetime)

In the [documentation](https://developers.facebook.com/docs/instagram-api/reference/user/insights), we 
are further told that the lifetime period for "audience-related metrics will only use the previous day's data,"
which doesn't feel very lifetime-y.  But whatever.

Here is an example:

```python
# Get impressions number for each day from 2018-05-01 up-to-but-not-including 2018-05-05
token.get(f'{insta_id}?fields=insights.metric(impressions).period(day).since(2018-05-01).until(2018-05-05)')
```

Importantly, from a data collection point of view:
* we really only need to collect they day-level data for the metrics with multiple period types (week and days_28 can be aggregated from there, right?)
* we can request multiple metrics at once, but it seems only at one specified period type, so day-level and lifetime-level metrics must be collected separately

### Day-Level Account/User Insights
```python
day_level_metrics = [
  'impressions', 'reach', 'follower_count', 
  'email_contacts', 'phone_call_clicks', 'text_message_clicks',
  'get_directions_clicks', 'website_clicks', 'profile_views'
]

token.get(f'{insta_id}?fields=insights'
  '.metric({''.join(day_level_metrics)})'
  '.period(day)')
```

### Lifetime-Level Account/User Insights
```python
lifetime_level_metrics = [
'audience_gender_age', 'audience_locale', 'audience_country',
'audience_city', 'online_followers'
]

token.get(f'{insta_id}?fields=insights'
  '.metric({''.join(lifetime_level_metrics)})'
  '.period(lifetime)')
```

## Media Insights
There are 3 major types of Instagram media:

1. IMAGE
2. VIDEO
3. CAROUSEL_ALBUM


### Image & Video Insights
An image has some insights within its fields, such as `comment_count` and `like_count`.



impressions, 
reach, 
taps_forward, 
taps_back, 
exits, 
replies, 
engagement, 
saved, 
video_views

carousel_album_impressions, 
carousel_album_reach, 
carousel_album_engagement, 
carousel_album_saved, 
carousel_album_video_views, 


If you try to use the `exits`, `replies`, `taps_forward`, `taps_back`, or any of the 
carousel-specific metrics on a Video or Image node, you will
receive an error response that looks something like the following:
```
{
  "error": {
    "message": "(#100) Incompatible metrics (metric_name) with single post media",
    "type": "OAuthException",
    "code": 100,
    "fbtrace_id": "EPVy9v0N2ZN"
  }
}
```




### Carousel Insights
There are exclusive metric names for carousels, which exactly mirror the general metric
names for Instagram Media nodes:

* carousel_album_engagement
* carousel_album_impressions
* carousel_album_reach
* carousel_album_saved
* carousel_album_video_views

Oddly, these exclusive names are not necessary: I have found that simply using the regular
media metric names returns the same exact numbers for these objects (i.e., no need to write 
out "carousel_album_").

Try it yourself:
```python
token.get(f'{carousel_id}/insights?metric=engagement,carousel_album_engagement')
token.get(f'{carousel_id}/insights?metric=impressions,carousel_album_impressions')
token.get(f'{carousel_id}/insights?metric=reach,carousel_album_reach')
# etc...
```

### Carousel Children: No Insights!
It's important to note that not all Instagram photos and videos are created equal: if
they are the children of a carousel, they do not have their own insights.

```python
token.get(f'{carousel_id}/children?fields=insights.metric(engagement)')
{
  "error": {
    "message": "(#100) Field is not available for Carousel children media.",
    "type": "OAuthException",
    "code": 100,
    "fbtrace_id": "AQL7CQTiv+s"
  }
}
```

## Story Insights
* exits
* impressions
* reach
* replies
* taps_forward
* taps_back

-----------------------------------------------------------------------

## Next Up: Instagraph Data Collection Strategy
I almost wish I started learning and strategizing Graph API data collection with 
Instagram: so many fewer node types, fields, edges, and insights!  Very simple to really
set up in a reasonable way (except possibly for stories since I still have never 
set up a webhook).

-----------------------------------------------------------------------

## A Few Extra Notes...

As a sanity check, I made sure to perform node introspection (`metadata=1`) on each type
of media node to make sure I was not missing some node-specific fields or edges. It seems that
I was not... The reason I checked is because I'm curious if Video nodes have any info about 
duration/length.

* Image Fields: id, caption, comments_count, ig_id, is_comment_enabled, like_count, media_type, media_url,
  owner, permalink, shortcode, thumbnail_url, timestamp, username
* Image Edges: children, comments, insights

* Video Fields: id, caption, comments_count, ig_id, is_comment_enabled, like_count, media_type, media_url,
  owner, permalink, shortcode, thumbnail_url, timestamp, username
* Video Edges: children, comments, insights

* Carousel Fields: id, caption, comments_count, ig_id, is_comment_enabled, like_count, media_type, media_url,
  owner, permalink, shortcode, thumbnail_url, timestamp, username
* Carousel Edges: children, comments, insights


------------------------------------------------------

## Some References
* https://developers.facebook.com/docs/instagram-api/reference/user/insights
* https://developers.facebook.com/docs/instagram-api/reference/media/insights
