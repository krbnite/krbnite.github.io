---
title: Facebook Graph&#58; The Page/Album Edge
layout: post
---

So -- what are some tables I can create based on the user engagement with a 
[Page Node](https://developers.facebook.com/docs/graph-api/reference/page/) that I can collect on 
the daily?  

* Question: Why not just leave the data in JSON files housed in S3, then create a Hive table and
access through a Hive or Presto connection?  
* Answer: Even in that case, one still creates a JSON SerDe file 
to map the JSON into a tabular structure.  More importantly, most of the analysts and stakeholders interested
in using the collected data expect that it will be tabular, not wonky.  In this post, just assume that the data is somehow mapped
into a table, whether or not the raw data lives in S3, Redshift, or whatever.

Moving along!  

Clearly, a simple table could just be based on the "non-managerial" fields that I identified as 
potentially interesting in the [previous article](https://krbnite.github.io/Facebook-Graph-The-Page-Node/):

```sql
CREATE TABLE page_high_level (
  page_id varchar,
  page_name varchar,
  fan_count int,
  talking_about_count int,
  as_on_date datetime
);
```

However, for more interesting data on how Facebook users interact with the Page, you have to
create tables using the information from the Page Node's Edges.  

In this post, I brainstorm some ideas for the [Page/Album edge](https://developers.facebook.com/docs/graph-api/reference/v2.12/album).  

# The Page/Album Map
If your end goal is to apply the data collection process to tens or hundreds of Facebook Pages, then
an important table to maintain will be the page-album mapping table.

```sql
CREATE TABLE page_album_map (
  page_id varchar,
  album_id varchar
);
```

# Album Fields
The next obvious table is one that maintains information about the album.  Who knows, some of the
album fields might be useful in some machine learning model down the line, or in mapping resources
between social media platforms.  Hell, the link field can be used in a Tableau dashboard to assist
a user in quickly looking at the album.  At worst, it's good to have an inventory that analysts have easy
access to!  We can't all be pinging the Graph API every time we need the info real quick.

```sql
CREATE TABLE album_fields (
  album_id varchar,
  album_name varchar,
  album_type varchar,
  cover_photo_id varchar,
  description varchar,
  event varchar,
  link varchar,
  page_story_id varchar,
  place_id varchar,
  height float,
  width float,
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
```

If you're keeping track of the available fields, you'll noticed I left several out:
* count
  - Definition: approximate number of photos
  - Reason for Rejection: I can just imagine being asked, "Why approximate? Can you just get us the exact number?"
* can\_upload
  - Definition: whether the viewer can upload photos to this album
  - Reason for Rejection: doesn't really matter if my current access\_token has this permission or not
* from
  - Definition: the profile that created the album
  - Reason for Rejection: almost always created by the page itself; doesn't seem important (could be wrong)
* location
  - Reason for Rejection: the "location" already comes back in the place list, and can be found by mapping the place\_id to the facebook\_places table
  
Note to Self: Doesn't really seem like the event, page\_story\_id, height and width fields are being populated in most of what I've seen.

### Some Strategy
The facebook\_places table is not too important. We could just as simply ingest latitude and longitude into the 
album\_fields table and leave it at that.  What is important is keeping track of updates, but not overwriting the
original row.  The analyst can do as they please when using the table (e.g., use row with more recent updated\_time).  The
reason I included the privacy field is that it is likely that some albums are set to private for a while before they
are released, and the analyst will need to differentiate between a private inventory item, and a live asset.

### Facebook Query
```python
# Assume object has been created that allows such behavior
fields = ','.join([
  'id', 'name', 'type', 'cover_photo',
  'description', 'event', 'link', 'page_story_id',
  'place', 'height', 'width', 'privacy', 
  'created_time', 'updated_time'
])
token.get('me/albums?fields='+fields)
```

### The Flattening
