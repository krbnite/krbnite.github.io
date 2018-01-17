---
title: Weighing in on Facebook Watch
layout: post
---

Reader beware: this post isn't about whether or not I like consuming content
on Facebook's new [Watch](https://newsroom.fb.com/news/2017/08/introducing-watch-a-new-platform-for-shows-on-facebook/)
platform. Instead, it's more of a follow-up to my exploration of using the Graph API
to [keep a pulse on live video streams](https://krbnite.github.io/Keeping-the-Pulse-on-Facebook-Live-with-the-Graph-API/).

My employer, who shan't be named, released the first episode of our new Facebook Watch series last 
night.  It was my job to experiment in real time and develop code to keep tabs on some viewership and engagement metrics of
interest.  For various reasons, I had a feeling the approach would be similar to live streaming content on
a regular Facebook Page (for one, the Watch Page itself appeared to be like a regular Facebook Page,
including [Page Insights](https://www.facebook.com/business/a/page/page-insights), etc).  

For the most part, it was.

One difference was in tokens.  For most of our Facebook Pages, I am able to sign into our Facebook business
account, grab the page token I need, and run some graph API scripts.  However, for some reason (at least as of
last night) our Watch asset is not available from the main business account.  Instead, my personal account was 
provided with admin permissions for the Facebook Watch page. It didn't seem like an issue though since I was able to
grab a page token for the Watch Page from the [Graph Explorer](https://developers.facebook.com/tools/explorer).
With some minor tweaks and observations, almost everything I figured out for live streams worked.

Everything up until the live asset went VOD ("video on demand" for those of you not in the biz).  I had extracted the 
correct video ID for the VOD asset, but there appeared to be no video insights data... Wtf?

An idea struck... But, no, that would be too crazy.  Or would it?  I mean, is anything really too crazy
in a corporate quagmire of 3rd party vendors and ever-changing APIs...?  Especially on an opening night or 
product launch?  

The idea: maybe the page token does not have read_insights permission. 

The reality: yes, the page did not have read_insights permission! I resolved this quickly by simply generating
a user token for myself that had this permission.  Video insights obtained.  But at what cost?  It's trivial,
but irksome: why doesn't the page token work?!  (Obviously, I'll look into this a bit...but for now, WHY?!)

## Some lessons learned
In my previous post, I provided some code for extracting live streams from a list of video broadcasts:
```python
live_videos = [item for item in video_broadcasts['data'] 
    if item['status'].lower()=='live']
```

That will certainly get you whatever live stream is currently associated with your page token, but what
if the live stream has not begun yet?  It's easy to see in the "Publishing Tools" section of a Facebook
Page that the team responsible for the live stream has already scheduled it, so clearly it should
be listed in broadcasts too. And it is: as SCHEDULED_UNPUBLISHED.

## Going Forward
In summary, keeping a pulse on the live airing of a Facebook Watch episode was similar to my experience
with doing so for a Facebook Page's live streams.  I was able to get the `live_views` metric as frequently as 
desired.  For some reason, the page token didn't have the necessary `read_insights` permission to access 
the `video_insights` edge of the `video_id` node when the live video went VOD; however, I was able to generate 
a user token with this permission and obtain this data.

A good code strategy for our next episode might be:
* actively watch the video broadcasts list until the status of the live_video node goes from SCHEDULED_UNPUBLISHED to LIVE (e.g., with a while loop)
* ascertain video_id from live_video_id and begin recording live_views at a regular cadence (e.g., one minute)
* once the live_views metric returns as None, the live asset has gone VOD (can double check status going from LIVE to VOD)
* use video_id to then grab video_insights immediately after transition
  - might have to use a user token (unless the read_insights permission is magically bestowed upon the page token)
* if interested, continue grabbing video_insights at a regular cadence for a relevant time interval thereafter (e.g., for next hour, or next 12 hours)

