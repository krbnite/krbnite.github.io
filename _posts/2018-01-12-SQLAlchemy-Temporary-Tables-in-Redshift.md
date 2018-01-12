---
title: SQLAlchemy&#58; Temporary Tables in Redshift
layout: post
---

In many environments (e.g., DBVisualizer, RPostgreSQL), I would just create a temporary table
using the hash method:
```sql
CREATE TABLE #temp as (SELECT someStuff FROM someTable);
```

However, when using SQLAlchemy, this can be an issue\*:
```python
ex("CREATE TABLE #temp as (SELECT someStuff FROM someTable);")

# This will throw an error saying that no such table exists...
qry("SELECT * from #temp LIMIT 10;")
```

The easiest work around is to just switch to using the "temporary table" method:
```python
ex("CREATE TEMPORARY TABLE temp1 as (SELECT someStuff FROM someTable);")
qry("SELECT * from temp1 LIMIT 10;")
```

This works great! For some reason it annoys me though: I like my temporary tables
to be clearly marked with those dang hashtags.

Crazily enough, there is a little hack using a fstring that I figured out works. It's
interesting, but maybe not optimal.  For documentation's sake, here it is.

```python
ex("CREATE {hash}temp2 as (SELECT someStuff FROM someTable);".format(hash='#')
qry("SELECT * from #temp2 LIMIT 10;")
```

The pro here is that whenever I use `qry` post creation, the temporary table is
clearly marked with '#' in the rest of the code.  However, the con is that
the CREATE statement itself is made slightly awkward in that the fstring's
brackets make it un-SQL-y looking.  Oh well!  

---------------------------
\* Note my two "shortcut functions":
```python
# qry
from pandas import read_sql_query as query

# ex
con = connect_to_redshift() # however you do it
ex = con.execute
```
