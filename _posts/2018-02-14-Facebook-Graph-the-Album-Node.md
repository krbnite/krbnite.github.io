---
title: Facebook Graph&#58; the Album and Photo Nodes
layout: post
---

Our ultimate goal is to treat the Page Node as the root node of a graph, and to strategize
how to traverse that graph and what to collect along the way.  

We have found that fields often hold managerial info, which can be
uninteresting, yet is still important for housekeeping purposes (e.g., it's good to know
the privacy setting of a video if/when people are wondering why its generated no views; e.g., 
the engagement of a video or post is important by itself, but can be better understood in
context of the object's publication date).  Sometimes, fields also contain high level metrics,
like fan count.  

A Facebook Page Node has many types of
Edges, e.g., /albums, /bc_sponsored_posts, /events, /insights, /instant_articles, /instant_articles_insights,
/likes, /photos, /posts, /ratings, /tagged, /video_broadcasts, /videos, and /visitor_posts, among
many others.  Each edge contains related nodes, which also have fields and edges.  Using field
expansion and nested requests, you can start to envisage collecting everything and anything all at once...  But
this approach can actually get unwieldy if your goal is to "collect everything."  

So, importantly, we have already developed some strategy: do not collect everything at once!

Instead, I think it's better to piece together the Page's graph little by little.  For each edge like
(page\_id, x\_id), simply create a mapping table.  Then for each x\_id, create a high level ("managerial")
fields table, several (x\_id, y\_id) mapping tables for important edges of x\_id, and the corresponding
high level y\_id tables. 

There are some caveats, e.g., you have to choose where to stop this process along each graph path:
- say you create the (page\_id, album\_id) mapping table
- then you might create the high level `album_fields` table and the (album\_id, photo\_id) mapping table
- STOP: you might next decide to create a high level `album_photo_fields` table, but this would only result in a subtable of 
  the `photo_fields` table you would create in conjunction with the (page\_id, photo\_id) mapping table




---------------------------------------


The album node's engagement edges (/comments, /likes, and /reaction) allow one to get 
a total\_count by setting the summary parameter, `summary=true`. By default, these edges
return a list of the engaged user nodes.  If you want to cut down on the noise and just return 
the summary, then set the limit parameter to zero.

```
token.get(album_id+'/likes?limit=0&summary=true')
```



# Recap of Table Thus Far
```
CREATE TABLE page_high_level (
  page_id varchar,
  page_name varchar,
  fan_count int,
  talking_about_count int,
  as_on_date datetime
);

CREATE TABLE page_album_map (
  page_id varchar,
  album_id varchar
);

CREATE TABLE album_fields (
  album_id varchar,
  album_name varchar,
  album_type varchar,
  cover_photo_id varchar,
  description varchar,
  event varchar,
  link varchar,
  place_id varchar,
  privacy_setting varchar,
  created_time datetime,
  updated_time datetime
);

CREATE TABLE facebook_places (
  place_id varchar,
  name varchar,
  street varchar,
  city varchar,
  zip varchar,
  state varchar(2),
  country varchar, 
  latitude float,
  longitude float
);

CREATE TABLE album_photo_map (
  album_id varchar,
  photo_id varchar
);

CREATE table album_sharedpost_mapping (
  album_id,
  sharedpost_id
);

CREATE table album_likes_mapping (
  album_id,
  user_id
);

CREATE TABLE page_photo_map (
  page_id,
  photo_id
);

CREATE TABLE photo_fields (
  photo_id,
  back_dated_time,
  back_dated_time_granularity,
  created_time,
  event,
  link,
  page_story_id,
  place,
  height,
  width,
  created_time datetime,
  updated_time datetime
);
