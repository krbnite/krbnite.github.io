---
title: A Refresher: Pitting Pandas vs SQL
layout: post
tags: sql python database postgres pandas easi
---

Oftentimes when working with a database, it is convenient to simply connect to it
via Python's Pandas interface (`read_sql_query`) and a connection established
through SQLAlchemy's `create_engine`.  For small enough data sets, it's often convenient to just query
all the relevant data right into a DataFrame, then fudge around using Pandas lingo from there (e.g.,
`df.groupby('var1')['var2'].sum()`).  However, as I've covered in the past (e.g., in [Running with Redshift](https://krbnite.github.io/Running-with-Redshift/) and [Conditional Aggregation in {dplyr} and Redshift](https://krbnite.github.io/Conditional-Aggregation-in-dplyr-and-Redshift/)), 
it's often not possible to bring very large data sets  onto your laptop -- you must do
as much in-database aggregation and manipulation as possible.  It's generally good
to know how to do both, though obviously since straight-up SQL skills covers both 
scenarios, that's the more important one to master in general.


In this tutorial, I look at a Spotify database, which 
is available online (code to access it below) via a [shared Google CoLab
notebook](https://colab.research.google.com/drive/1XG-TZbwU2oIZfZOIuX82cAqneK7-1ZSZ)... But!  Make sure to **MAKE A COPY** of that notebook before you use it.   (I 
began playing around with the original notebook,
thinking my changes wouldn't be saved...but I was wrong.  Sorry to its original author!)

The questions below all come from the shared notebook.  Just like my solutions below,
the original notebook has its own solutions: if you want some practice, ignore them and try developing the queries 
yourself.  (That's what I did.)

**NOTE**: The data set has 74,200 rows and 6 columns, which amounts to ~3.6MB (`74200*6*8/1e6`) for
64-bit numbers.  This can easily fit into memory, so we can technically do all of our
aggregations and transformations out of the database.  For fun and instructional purposes, 
we'll also look at how we would do the same aggregations and transformations in SQL.



# Connect to the Database





```python
from sqlalchemy import create_engine

# Connect to Database
db = 'db_strata'
host = 'db-strata.stratascratch.com'
user = 'malabdullah'
psswd = 'nf64VlNxO'
conStr='postgresql://'+user+':'+psswd+'@'+host+':5439/'+db
conn = create_engine(conStr)

# Query into DataFrames
from pandas import read_sql_query as qry

# Easier-to-Remember Table Name (use f strings in queries)
tbl = 'spotify_daily_rankings_2017_us'
```

-------------------------------------

# What percentage of total streams come from the top 10 artists?

### Assumption: I can pull entire table into memory


```python
df = qry("SELECT * FROM spotify_daily_rankings_2017_us", conn)
# List table columns
df.columns
  Index(['position', 'trackname', 'artist', 'streams', 'url', 'date'], dtype='object')
result = df.groupby('artist')[['artist','streams']].sum().sort_values(by='streams', ascending=False)
round(100 * result.streams[:10].sum()/result.streams.sum(), 1)
  29.1
```

### Assumption: I cannot pull entire table into memory (in-db query)

```python
# List table columns
qry("SELECT * FROM spotify_daily_rankings_2017_us LIMIT 0",conn)
  position track name artist streams url date

# Query time!
qry("""  
  WITH A AS (    
    SELECT artist, (100 * streams::numeric / SUM(streams) OVER ()) AS perc    
    FROM spotify_daily_rankings_2017_us  ),  
  B AS (    
    SELECT artist, ROUND(SUM(perc), 2) as perc    
    FROM A     
    GROUP BY artist    
    ORDER BY perc DESC    
    LIMIT 10  )  
  SELECT SUM(perc) 
  FROM B
""",conn)
```

----------------------------------------------------

# What percentage of total streams come from the top 10 tracknames?

Note something important here: when aggregating, it's important to group by artist
AND trackname (not just trackname) -- songs often have the same name!

### Assumption:  I can pull entire table into memory
```python
df = qry("select * from spotify_daily_rankings_2017_us", conn)
# Reminder of what columns there are...

# Get streams by artist and trackname 
#   -- important to include artist since tracknames are often duplicated
streams_by_artist_track = df.groupby(['artist','trackname'])[['artist','trackname','streams']].\
  sum().\
  sort_values(by='streams', ascending=False)

# Answer
round(100 * streams_by_artist_track.streams[:10].sum()/streams_by_artist_track.streams.sum(), 1)
  10.0
```

### Assumption:  I cannot pull entire table into memory (in-db query)
```python
qry("""
  WITH A AS (
    SELECT artist,
      trackname,
      SUM(streams) AS streams
    FROM spotify_daily_rankings_2017_us
    GROUP BY artist, trackname
    ORDER BY streams DESC
  ), B AS (
    SELECT artist,
      trackname,
      (100*streams::numeric / SUM(streams) OVER ()) AS pc_streams
    FROM A
    LIMIT 10
  )
  SELECT ROUND(SUM(pc_streams), 1)
  FROM B
""", conn)
  9.9
```

I get `10.0` for first answer and `9.9` for second... This *might* mean something
is wrong with my big SQL query... But it also might be due to a roundoff error.  (If someone
see an obvious error, let me know!)

---------------------------------

# Which top 10 artists had the most streams in 2017? 
First, if you don't remember, look at the column names:
```python
qry(f"SELECT * FROM {tbl} LIMIT 0")
```

Next, figure out what format the date is in:
```python
qry(f"SELECT date FROM {tbl} LIMIT 3")
  1/1/17
  1/1/17
  1/1/17
```

Unfortunately, we cannot simply use Postgres' `SUBSTRING` function to extract
the year for date in this format since the prefix is variable length (e.g., 
`1/3/17` vs `10/12/17`.  Fortunately, there is another Postgres function for this,
called `RIGHT`.  

```python
qry(f"SELECT RIGHT(date, 2) AS yy FROM {tbl} GROUP BY yy")
  17
  18
```

Cool!  Let's proceed.

This is such a straightforward query; no need to really munge around outside
of the DB with it!

```python
qry(f"""
  SELECT artist,
    SUM(streams) as streams
  FROM {tbl}
    WHERE RIGHT(date,2) = '17'
  GROUP BY artist
  ORDER BY streams DESC
  LIMIT 10
""", conn)
```

| | artist |streams|
|--|--------|-----|
|0 | Drake| 1243212918|
|1 | Kendrick Lamar| 1142667504|
|2| Post Malone| 949111981|
|3| Lil Uzi Vert| 756277795|
|4| Ed Sheeran| 711107331|
|5| Migos| 676582502|
|6| Future| 565462534|
|7| The Chainsmokers| 552246036|
|8| 21 Savage| 470721805|
|9| Khalid| 459771711|

# List the artists by the number of track names in the top 200 in descending order

We are given an additional note:  "You'll need to take into account the number of regions the artist 
is in."  This note weirded me out at first since we do not have a "region" column
in the table we've been using, where we only have position, trackname, artist, streams, url, 
date.  Then I saw that the original author of the notebook was using a different
table for this, which brings us to another important lesson:

How do you quickly see what tables are available in your database?

Well, for one, you can do this:

```python
qry("""
  SELECT tablename
  FROM pg_catalog.pg_tables
    WHERE schemaname != 'pg_catalog'
      AND schemaname != 'information_schema'
""",conn)
```

However, for the database we're currently connected to, this brings up
a few hundred tables corresponding to other SQL interview tutorials, so
we have to be a little more specific.

```python
qry("""
  SELECT tablename
  FROM pg_catalog.pg_tables
    WHERE schemaname != 'pg_catalog'
      AND schemaname != 'information_schema'
      AND tablename LIKE '%spotify%';
""",conn)

    tablename
  0	spotify_worldwide_daily_song_ranking
  1	spotify_daily_rankings_2017_us
```

The table we have not been using is `spotify_worldwide_daily_song_ranking`, so
let's check for a region variable:

```python
qry("SELECT * FROM spotify_worldwide_daily_song_ranking LIMIT 1")

    id	position	trackname	artist	streams	url	date	region
  0	0	1	Reggaet√≥n Lento (Bailemos)	CNCO	19272	https://open.spotify.com/track/3AEZUABDXNtecAO...	1/1/17	ec
```

This one DOES have a region variable.  Nice!

One thing though: do we really need this table at all?  The original table we were working with
has both `position` and `date`.  Shouldn't we be able to answer the question directly from
those variables?  

I guess we can check both... But if the answers come up different, then I don't know what to
tell you (without looking much more deeply into where these tables come from, etc).


My first inclination was to issue a `WHERE` statement to make sure we only
count tracks that made it in the top 200...but then I thought to look at what the max
position actually is -- and it's 200.

```python
qry(f"""
  SELECT MAX(position) as max
  FROM {tbl}
""", conn)
  200  
```

That said, in a general rankings list, the rankings might go up into the 1000's, so 
I'll show how to do that below anyway.

Remember, we want to know how many tracks an artist has that made it into the top 200.


### Assumption:  I can pull entire table into memory
This query is actually so simple, it's probably just worth doing the SQL before
bringing anything into Python... But for consistency, here is the Pandas version
of the query.

```python
df = qry(f"SELECT artist, trackname FROM {tbl};", conn)
df.groupby('artist').\
  nunique('trackname').\
  sort_values(by='trackname', ascending=False)[:10].\
  drop('artist',axis=1)
```

| | artist | track_count |
|--|------|--------|
|0|	Future|	53|
|1|	Drake|	38|
|2|	Linkin Park|	28|
|3|	Big Sean|	27|
|4|	Taylor Swift|	25|
|5|	Lil Uzi Vert|	25|
|6|	Ed Sheeran|	25|
|7|	21 Savage|	24|
|8|	Bryson Tiller|	23|
|9|	Eminem|	21|


In a general rankings list, the rankings might go up into the 1000's.  This is not the case
here, so I didn't explicitly reference the `position` variable...but that almost feels like cheating,
so here is the more general query where we want to see the artist who appeared in the Top 10
the most often.

```python
df = qry(f"SELECT artist, trackname, position FROM {tbl};", conn)
df.query('position <= 10').\
  groupby('artist').\
  nunique('trackname').\
  sort_values(by='trackname', ascending=False)[:10].\
   drop(['artist', 'position'],axis=1)
```

| | artist | track_count |
|--|------|--------|
|0|	Drake|	14|
|1|	Kendrick Lamar|	12|
|2|	Huncho Jack|	5|
|3|	Lil Uzi Vert|	5|
|4|	Post Malone|	4|
|5|	Big Sean|	4|
|6|	Ed Sheeran|	4|
|7|	Eminem|	3|
|8|	The Chainsmokers|	3|
|9|	Migos|	3|


### Assumption:  I cannot pull entire table into memory (in-db query)

```python
qry(f"""
  SELECT artist,
    COUNT(DISTINCT trackname) as track_count
  FROM {tbl}
  GROUP BY artist
  ORDER BY track_count DESC
  LIMIT 10
""", conn)
```

And here is the more general form of the query for when the ranking variable is actually 
necessary to consider.

```python
qry(f"""
  SELECT artist,
    COUNT(DISTINCT trackname) as track_count
  FROM {tbl}
    WHERE position <= 10
  GROUP BY artist
  ORDER BY track_count DESC
  LIMIT 10
""", conn)
```

You might find that both these SQL queries result in slightly different tables than shown 
above.  This is because we only order by track_count, so when the artists tie, the
artist ordering is somewhat arbitrary.  To ensure a consistent return, we must also
order by `artist`.  You will notice however that this means some folks who tied for
the last few spots won't make it into the table... So depending on your business use case,
you might have to get more technical, e.g., Top 10 including all ties for the 10th spot (making
the list a bit longer than 10).

---------------------------------------

# How do the number of streams in the top 10 differ than the number of streams in the top 50? top 100? top 200? Find the average number of streams in the top 10, 50, 100, 200.

In Python, this is easy, right?  You can just use a for loop and get some numbers really quick.

But what if you had to do everything in SQL?  This is just for extra credit.  Let's assume
we use someone SQL workbench GUI and don't know Python.  How could we return an answer?

Here's one way using a bunch of CTEs and JOINs:

```python
qry(f"""
  WITH top10 as (
    select 1 as joinvar, 
      avg(streams) as top10avg
    from {tbl}
      where position <= 10
  ), top50 as (
    select 1 as joinvar, 
      avg(streams) as top50avg
    from {tbl}
      where position <= 50
  ), top100 as (
    select 1 as joinvar, 
      avg(streams) as top100avg
    from {tbl}
      where position <= 100
  ), top200 as (
    select 1 as joinvar, 
      avg(streams) as top200avg
    from {tbl}
      where position <= 200
  )
  SELECT top10avg, 
    top50avg,
    top100avg,
    top200avg
  FROM top10 
    JOIN top50 on top10.joinvar = top50.joinvar
    JOIN top100 on top100.joinvar = top50.joinvar
    JOIN top200 on top200.joinvar = top100.joinvar
""",conn)
```

|	|top10avg| top50avg| top100avg| top200avg|
|--|------|--------|----------|---------|
|0| 1.152252e+06| 695913.378329| 506541.295795 | 355588.68128

Another way could be with a bunch of CTEs (again) and a bunch of UNION operations.

```python
qry(f"""
  WITH top10 AS (
    SELECT 'top10'::text AS top_x,
      avg(streams) AS avg_streams
    FROM {tbl}
      WHERE position <= 10
  ), top50 AS (
    SELECT 'top50'::text AS top_x,
      avg(streams) AS avg_streams
    FROM {tbl}
      WHERE position <= 50
  ), top100 AS (
    SELECT 'top100'::text AS top_x,
    avg(streams) AS avg_streams
    FROM {tbl}
      WHERE position <= 100
  ), top200 AS (
    SELECT 'top200'::text AS top_x,
      avg(streams) AS avg_streams
    FROM {tbl}
      WHERE position <= 200
  )
  SELECT * FROM top10
     UNION SELECT * FROM top50
     UNION SELECT * FROM top100
     UNION SELECT * FROM top200
  ORDER BY avg_streams DESC
""",conn)
```

|	|top_x| avg_streams|
|--|----|--------|
|0| top10| 1.152252e+06|
|1| top50| 6.959134e+05|
|2| top100| 5.065413e+05|
|3| top200| 3.555887e+05|


In Python, this all simplifies to a loop.  This can be a more SQL-esque loop, or 
more Pandas-esque.

### SQL-esque Loop

```python
avg_streams = dict()
for num in [10, 50, 100, 200]:
  avg = qry(f"""
    SELECT avg(streams) AS avg_streams
    FROM {tbl}
      WHERE position <= {num}
  """, conn)
  avg_streams[num] = avg.avg_streams
```

### Pandas-esque Loop
Sometimes Pandas feels more efficient and useful... Other times, it feels
like overkill.  To my mind, this is one of those cases.  But whatever
works!

```python
df = qry(f"SELECT * FROM {tbl}", conn)
avg_streams = dict()
for num in [10, 50, 100, 200]:
  avg_streams[num] = df.query(f'position <= {num}').streams.mean()
```

# How many different artists are there in the top 100 vs top 101-200? Compare the number of artists in the top 100 vs the top 101-200.


### Assumption:  I can pull entire table into memory 
```python
df = qry(f"SELECT * FROM {tbl}", conn)

data = {
  'position <= 100': None, 
  'position > 100 & position <= 200': None,
}

for condition in data.keys(): 
  data[condition] = df.query(condition).artist.nunique()
data
  {'position <= 100': 291, 'position > 100 & position <= 200': 478}
```

### Assumption:  I cannot pull entire table into memory (in-db query)
```python
qry(f"""
  WITH A AS (
    SELECT 'Rank < 100'::text AS category,
      COUNT(DISTINCT artist) AS distinct_artists
    FROM {tbl}
      WHERE position <= 100
  ), B AS (
    SELECT '100 < Rank <= 200'::text AS category,
      COUNT(DISTINCT artist) AS distinct_artists
    FROM {tbl}
      WHERE position BETWEEN 101 and 200
  )
  SELECT * FROM A
    UNION SELECT * FROM B
""",conn)

  	category	          distinct_artists
  0	100 < Rank <= 200	  478
  1	Rank < 100	        291
```

The author in the notebook just shows the number of distinct artists in the top 100 
and top 200, which doesn't quite answer the question... But as a sanity check, let's
just see if we get the same number for the top 200:

```python
df.query('position <= 200').artist.nunique()
  487
```

Ok, yes we do.  Great!

-------------------------------

# Which artists should the marketing team invest in? Why? Support your answer with your analytical research.




# References
https://www.stratascratch.com/spotify-case-study.html
https://colab.research.google.com/drive/1XG-TZbwU2oIZfZOIuX82cAqneK7-1ZSZ#scrollTo=l74CxV8UF4Mt

