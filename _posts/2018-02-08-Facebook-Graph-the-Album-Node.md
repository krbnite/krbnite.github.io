---
title: Facebook Graph&#58; The Album Node
layout: post
---

In the [previous post](https://krbnite.github.io/Facebook-Graph-The-Page-Album-Edge/), the 
[Page/Albums Edge](https://developers.facebook.com/docs/graph-api/reference/page/albums/) allowed us to 
get a list of all Album nodes associated with a given 
[Page Node](https://developers.facebook.com/docs/graph-api/reference/page/).  From this, we 
created a simple page\_id/album\_id mapping table. Now, if your only
working with a single [Facebook Page](https://www.facebook.com/7175346442), then this table might 
seem kind of silly.  But! The second you begin working on a [second Facebook Page](https://www.facebook.com/791635687618100), 
it starts to take on some meaning.  If you work for something like a record label, publishing company, or sports empire, 
then it's likely you're tracking tens to hundreds of Facebook Pages, easy.  No question here: the mapping
table becomes pretty dang important!

We also found that it was possible to return each Album's fields from the Page/Albums Edge, e.g.: 

```python
# Assume token object has been created that allows such behavior
fields = ','.join([
  'id', 'name', 'type', 'cover_photo',
  'description', 'event', 'link', 'place', 
  'privacy', 'created_time', 'updated_time'
])
token.get('me/albums?fields='+fields)
```

This is great!  In other words, one needn't return a list of album IDs in order to access each 
[Album Node](https://developers.facebook.com/docs/graph-api/reference/v2.12/album/) separately.  How
laborious!

```python
# Laborious Way
page_albums = token.get('me/albums', paginate=True)
album_id = [album['id'] for album in page_albums['data']]
album_data = []
fields = ','.join([
  'id', 'name', 'type', 'cover_photo',
  'description', 'event', 'link', 'place', 
  'privacy', 'created_time', 'updated_time'
])
for ID in album_id:
  album_data += token.get(ID+'?fields='+fields)
```



and so we created an album fields 
table.  The two tables were kept separate to avoid


# The Album Engagement Table

The album node's engagement edges (/comments, /likes, and /reaction) allow one to get 
a total\_count by setting the summary parameter, `summary=true`. By default, these edges
return a list of the engaged user nodes.  If you want to cut down on the noise and just return 
the summary, then set the limit parameter to zero.

```
token.get(album_id+'/likes?limit=0&summary=true')
```

