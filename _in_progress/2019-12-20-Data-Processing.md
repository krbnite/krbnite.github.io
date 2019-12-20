https://www.spotifyjobs.com/job/senior-data-scientist-ads-analytics-machine-learning/



Oftentimes when working with a database, it is convenient to simply connect to it
via Python's Pandas interface (`read_sql_query`) and a connection established
through SQLAlchemy's `create_engine`.  For small enough data sets, I often just query
the data right into a DataFrame, then fudge around using Pandas lingo from there (e.g.,
`df.groupby('var1')['var2'].sum()`).  However, as I've covered in the past, for large
data sets, it's often not possible to bring it fully onto your laptop -- you must do
as much in-database aggregation and manipulation as possible.  It's generally good
to know how to do both, though obviously since straight-up SQL skills covers both 
scenarios, that's the more important one to master in general.


In this tutorial, I look at a Spotify database, which can be queried in various ways...
It is available online (code to access it below).  In fact, you can use the Google CoLab
notebook... The questions all come from that notebook, which also has solutions.  I wanted 
to brush up on my own skills and test my wits, so I just used the notebook for its
data and questions.

Make sure to MAKE A COPY of that notebook before you use it.  I began playing around with it,
thinking my changes wouldn't be saved...but I was wrong.  (Sorry original author.)


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
result.streams[:10].sum()/result.streams.sum()
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


### With Original Table

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
let's assume we have to use a `WHERE` statement anyway.

Remember, we want to know how many tracks an artist has that made it into the top 200.

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


# References
https://www.stratascratch.com/spotify-case-study.html
https://colab.research.google.com/drive/1XG-TZbwU2oIZfZOIuX82cAqneK7-1ZSZ#scrollTo=l74CxV8UF4Mt

