---
title: Keeping the Pulse on Facebook Live with the Graph API
layout: post
---

In this post, we cover how to measure the performance of a video posted to a Facebook Page.  We explore how to automatically 
identify any live broadcasts, obtain their video ID, and track their performance.

## How to Obtain a Page Token
1. Go to business.facebook.com and sign in.
2. Go to the Graph API Explorer
3. Get Token: Click on one of the pages you have page-token rights to

## List the Video Broadcasts for a Given Page ID
```python
import requests
import json
fb_graph = 'https://graph.facebook.com'
response = requests.get(fb_graph + '/me/video_broadcasts?access_token=' + token)
video_broadcasts = json.loads(response.text)
```

## Extract Current Live Streams
```python
live_videos = [item for item in video_broadcasts['data'] 
    if item['status'].lower()=='live']
```

## Use the LiveVideo ID to Obtain the Corresponding Video ID
```python
live_ID_0 = live_videos[0]['id']
response = requests.get('https://graph.facebook.com/v2.10/' +\ 
    live_ID_0 + '?fields=video&access_token=' + token)
video_id = json.loads(response.text)['video']['id']
```

## Get Video Insights
```python
response = requests.get(fb_graph + '/v2.10/' +\
    video_id+'/video_insights?access_token=' + token)
video_insights = json.loads(response.text)
```

## List the Description of Each Metric in Video Insights
```python
[item['description'] for item in video_insights['data']]
```
