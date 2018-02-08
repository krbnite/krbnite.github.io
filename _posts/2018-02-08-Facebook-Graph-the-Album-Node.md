---
title: Facebook Graph&#58; The Album Node
layout: post
---

In the [previous post](https://krbnite.github.io/Facebook-Graph-The-Page-Album-Edge/), the 
[Page/Albums Edge](https://developers.facebook.com/docs/graph-api/reference/page/albums/) allowed us to 
get a list of all Album nodes associated with a given 
[Page Node](https://developers.facebook.com/docs/graph-api/reference/page/).  From this, we 
created a simple page\_id/album\_id mapping table (called `page_album_map`). Now, if your only
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
for aid in album_id:
  album_data += token.get(aid+'?fields='+fields)
```

And so we created a table for album fields, imaginatively called `album_fields`.  Now, if you have tens 
to hundreds of Facebook Pages, then you likely
have hundred to thousands of Facebook Albums. Good thing we have that page/album mapping table!  This way,
we can easily map album field records to their parent pages:

That's why we have the page\_id/album\_id mapping table:
```sql
SELECT *
FROM album_fields A
  JOIN page_album_map B
    ON A.album_id = B.album_id
  WHERE B.page_id = '9899376497'
```

The question might arise: Why not just include the page\_id in this table instead of requiring a separate
mapping table. 

Short Answer: The page\_album\_map and album\_fields tables were kept separate because at one point in my life I heard 
about normalization in relational databases.  

Slightly Longer Answer: In the `page\_album\_map` table, each (page\_id, album\_id)
pair is written only once.  However, in the `album\_fields` table, an album\_id may be written multiple
times (one row per album update).  If we included the page\_id field, we would waste space and allow for the
possibility of error.  This might not seem like a big deal here, but below we will create album engagement
tables that get updated daily or hourly -- at which point it becomes a slightly bigger deal.

Not gonna lie: we have tables in Redshift that rebel like crazy against normalization, and quite frankly, 
sometimes it's just simpler that way.  I'm not a database guru, so judge me not if I depart from such
norms anywhere in here or the annals of this blog!


# The Album Engagement Table

The album node's engagement edges (/comments, /likes, and /reaction) allow one to get 
a total\_count by setting the summary parameter, `summary=true`. By default, these edges
return a list of the engaged user nodes.  If you want to cut down on the noise and just return 
the summary, then set the limit parameter to zero.

```
token.get(album_id+'/likes?limit=0&summary=true')
```

