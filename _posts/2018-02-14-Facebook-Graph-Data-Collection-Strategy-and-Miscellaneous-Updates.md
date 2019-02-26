---
title: Facebook Graph&#58; Data Collection Strategy (and Miscellaneous Updates)
layout: post
tags: facebook-graph python automation etl wwe
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
Edges, e.g., /albums, /bc\_sponsored\_posts, /events, /insights, /instant\_articles, /instant\_articles\_insights,
/likes, /photos, /posts, /ratings, /tagged, /video\_broadcasts, /videos, and /visitor\_posts, among
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


# The Album Node Summaries

The album node's engagement edges (/comments, /likes, and /reaction) allow one to get 
a total\_count by setting the summary parameter, `summary=true`. By default, these edges
return a list of the engaged user nodes.  If you want to cut down on the noise and just return 
the summary, then set the limit parameter to zero.

```
token.get(album_id+'/likes?limit=0&summary=true')
```


# Side Note About Page Likes
I've looked into this a few times now... Whereas an album node has a likes edge, which ostensibly 
shows you all users (User Nodes) who have liked the album, the Page node does not seem to have this feature.  At first,
I thought I didn't know what I was doing, but I'm not so sure I'm a dummy anymore!

I find that the fan\_count field of a Page Node gives you the number of Facebook users who have liked your page, which
(btw!) is different than the number of folks "following" the page.  From what I can tell, "followers" are "likers who
have not unfollowed the page."  On Facebook, you can unfollow a page (not receive updates on your feed), while still maintaining
that you "like" the page.  

<figure>
  <img src="/images/facebook_fan_count.png" width="300">
</figure>

The "likes" edge of a Page Node returns what other Facebook Pages have been "liked" by the Page Node, which departs
from the behavior of the "likes" edge of most other nodes (albums, photos, posts, videos).

So, basically, if you want to keep tabs on Facebook users who are likely fans of your page, then you can collate
Facebook users who have liked (or reacted to) any of your page's posts, photos, videos, etc.  This can include
users who have not liked your page, and it might also not include all users who have liked your page.  But what
can you do?  (Seriously, if you know -- leave a comment! (Yes, I know I'm likely only speaking to Future Kevin. Hey, Future Kev: 
leave a comment, ya bum!))

One possibility: if you really need to know whether or not the user has liked your page, you may be able to
look at `/{user-id}/likes` to see if your page is in there.  (Haven't checked myself, so I'm not sure if you
will run into a privacy barrier or not.)

Funnily enough, I've read that Facebook does this to "protect the privacy of its users."  But if that were the case,
then why can I get the names and Facebook IDs of users who liked anything else that has to do with my Facebook
Page?  Basically, if you like the a Facebook Page, you're quasi-anonymous to that Page's owner -- but the second you
like anything on that page, you are not.  And let's be clear: you can get some likers directly by manually going
to your Facebook Page > Insights > People, which shows fans, followers, and more.  However, this is apparently only
a small subset if you have a large following.

Relevant: https://developers.facebook.com/bugs/147185208750426

# Bugs in the Graph
Can't believe I haven't perused the [Bugs Page](https://developers.facebook.com/bugs/) before!  So many interesting
and relevant questions/answers (e.g., when I search "graph api" w/ no filter tags).

[Here is a relevant bug](https://developers.facebook.com/bugs/169200003704868/) that is open at the time of this writing:  
> I've been debugging an issue where the numbers that we get from the insights API sometimes change between different runs. It 
> seems that the results of async jobs that we do are sometimes missing some fields and some numbers are 0 instead of the 
> correct number. We've seen differences in spend and impressions previously, but currently it seems to be mainly some other 
> fields, such as reach or inline\_link\_clicks, etc. that are sometimes 0.

[This bug here](https://developers.facebook.com/bugs/1576213349131150/) also seems squeamishly relevant:
> We're trying to query post reactions, likes, and shares on specific page post (not a final link). However, 
> Insights API are returning strange numbers. Also, on analysis window through Business UI, numbers do 
> not match as well.

And [another creep crawler](https://developers.facebook.com/bugs/366800437118057/) that makes my skin crawl a bit: the
developer says, that `post_stories_by_action_type` and `post_story_adds_by_action_type` return the same value, which
doesn't seem to make sense: "I think post\_stories\_by\_action\_type should return actions made only in the page, and post\_story\_adds\_by\_action\_type in the page and in user shares." A Facebook rep responds:
> I enquired internally with the engineering team responsible for this and it looks like this is a valid 
> bug that's already been brought up. Unfortunately, the underlying issue is inherent of the current system 
> design. We are working on a long term solution that would address this issue but due to the complexity of the issue, 
> however, I don't have an ETA at this time.

Well... That's comforting, huh?  Comforting or not, these things are good to know!  I can imagine this bugs page saving
me a lot of grief in the future, trying to explain why things aren't as they seem or should be.

For example, [this bug](https://developers.facebook.com/bugs/166242917345550/) discusses why a certain insights
metric isn't populated everyday. The `page_fans_online_per_day` metric is not populated unless the number meets a
certain minimum threshold. What is that threshold? "Due to a policy issue, we cannot tell you what the threshold value is."

Crazy!

# Some Code Misc

## Recap of SQL Tables Thus Far
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
```

## Some Python
```python
# This uses a fb_token object I define elsewhere
def get_page_fields(
  token,
  me = None,
):
  if me is None:
    me = token.me
  data = token.get('me?fields=id,name,fan_count,talking_about_count')
  return data


get_page_albums(
  token,
  me = None,
  limit = 50,
):
  if me is None:
    me = token.me
  data = []
  next_url = 'me/albums?limit='+str(limit)
  while next_url:
    response = token.get(next_url, me)
    data += response['data']
    try:
      next_url = response['paging']['next'].split(token.fbg)[1]
    except:
      next_url = None
  df = pd.DataFrame(colums=['page_name', 'page_id', 'album_id', 'album_name'])
  for idx in range(len(data)):
    itme = data[idx]
    df.loc[idx, ['album_id','album_name']] = [item['id'],item['name']]
  page = token.get('me', me)
  df.page_id = page['id']
  df.name    = page['name']
  return df

def get_page_albums_for_all_accounts(
  token,
  limit = 50,
):
  pages = token.page_to_token.copy()
  _discard = pages.pop('user')
  df = pd.DataFrame(colums=['page_name', 'page_id', 'album_id', 'album_name'])
  for page in pages.keys():
    temp = get_page_albums(token, me=page)
    df = df.append(temp, ignore_index=True)
  return df
```
