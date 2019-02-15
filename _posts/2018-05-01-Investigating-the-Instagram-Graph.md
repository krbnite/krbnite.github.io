---
title: Investigating the Instagram Graph
layout: post
tags: facebook-graph wwe
---

If you working for a media juggernaut, the Facebook Graph API is helpful in collecting engagement/consumption data on 
Facebook pages, posts, videos, and so on.  Of course, its utility is based on how well you can automate the process of 
obtaining a user token and swapping that user token for a page token (or 100's of page tokens!).  With a page token,
obtaining data is as simple (strategizing how to best collect, clean up, and store all the data is another story).  Just
issue a GET request: this can be done in the browser, using a Python package like requests, and so on.  

What's great is if, like me, you've spent a lot of time figuring out the Graph API for Facebook, then you 
can fairly easily apply lessons learned to your media giant's Instagram accounts, which are also on the Graph 
API.  However, there are some differences -- e.g., instead of swapping a user token for a page token, you must
obtain the Instagram business ID that is associated with the Facebook Page that you have sufficient permissions
to manage/administer:

```python
insta_id = token.get('fb_page_id?fields=instagram_business_account')
```

If you have the right permissions, you're all set.  If you don't: well, I'm sorry -- go figure it out!  For
those of you still with me, here's your first "Instagraph" query:

```python
token.get(f'{insta_id}?fields=followers_count,media_count,media{media_type,caption}')
```

In this request, we asked for the number of followers we currently have, how much media we've posted (or
whatever the slang term might be for Instagram), and a list of our media posts and their corresponding type (e.g.,
IMAGE or CAROUSEL_ALBUM) and captions.  Cool... But what else can we get?

Luckily, Instagraph nodes also have that monk-like introspective quality:

```python
# Return fields and edges associated with Instagram account
token.get(f'{insta_id}?metadata=1')
```

We find that an Instagram Business Account (IBA) has the following fields:
* id
* biography
* business_discovery
* followers_count
* ig_id
* media_count
* mentioned_comment
* mentioned_media
* name
* profile_picture_url
* username
* website

And the IBA has the following edges:
* insights
* media
* stories
* tags

## Keeping Tabs on the Competition
The `business_discovery` field was interesting: it's actually more of a function.  But not just any
function: it serves as a looking glass into the souls of your competitors!

```python
token.get(f'{insta_id}?fields=business_discovery.\
    username(myCompetitor){
        followers_count,
        media_count,
        media{media_type, caption}
    }
)
```

## Ephemeral Stories
Just like a Facebook Page has Insights, an Instagram Business Account has insights, so it should be no surprise
that Instagram posts/stories have insights as well. However, a major difference from Facebook is the 
possibility of story expiration -- and, along with it, expiration of its insights:

> Stories insights are only available for 24 hours, even if the stories are archived or highlighted. If
> you want to get the latest insights for a story before it expires, set up a Webhook for the Instagram
> topic and subscribe to the story_insights field.

To continue doing this as I currently do them, I'd at least have to ping the Instagraph every hour, but 
ultimatley -- it really seems like I'm going to have to figure out this whole "webhook" thing!

To look at a list of stories, simply access the stories edge of the IBA:

```python
token.get(f'{insta_id}?fields=stories')
```

And to look at what fields and edges are available to a story, select one of the story IDs and
perform some node introspection:

```python
token.get(f'{story_id}?metadata=1')
```

An Instagram story has the following fields:
* id
* caption
* comments_count
* ig_id
* is_comment_enabled
* like_count
* media_type
* media_url
* owner
* permalink
* shortcode
* thumbnail_url
* timestamp
* username

And an Instagram story has the following edges:
* children
* comments
* insights

## Longer-Lasting Media
Nothing much more to add: seems to have the same fields and edges as a story.

## Tags?
You can get a list of tags pretty easily:
```python
token.get(f'{insta_id}?fields=tags')
```

However, if I try to do a request on a tag, I get an error message:
```python
token.get(f'{tag_id}')
```

ERROR:
```
{
  "error": {
    "message": "Unsupported get request. Object with ID '17843611327269293' does not exist, cannot be loaded due to missing permissions, or does not support this operation. Please read the Graph API documentation at https://developers.facebook.com/docs/graph-api",
    "type": "GraphMethodException",
    "code": 100,
    "error_subcode": 33,
    "fbtrace_id": "HiCCkSHZyeB"
  }
}
```

I don't know... Maybe I have insufficient permissions? Will have to figure it out another day!
