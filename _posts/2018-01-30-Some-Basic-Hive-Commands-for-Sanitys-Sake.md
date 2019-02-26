---
title: Some Basic Hive Commands for Sanity's Sake
layout: post
tags: hive python aws wwe
---

```python
con = connect_to_hive()
ex = con.execute
from pandas import read_sql_query as qry
```

These commands work with either a Hive connection.  Some of them work with
a Presto connection, but not all.  Presto has different syntax, which I'll save
for another day.

```
# List available hive databases 
qry('SHOW DATABASES', con)

# List tables
qry('SHOW TABLES', con)

# Show Table Info (e.g., column data types)
qry('DESCRIBE table_name', con)
```

## Create an External Table from CSV Files in S3
If you collect a lot of data in S3, keep it there!  For a long time, my team was using S3
as a holding site, ultimately waiting to be loaded into Redshift.  But time passed and
we grew smarter.  Not saying Redshift doesn't have its use cases, but then again, you
can just toss CSV files into an S3 directory and treat it as a table in Hive.  Better, 
once the Hive table is created, you can hit it with Presto, which is faster (maybe you can
even create the Hive table while in Presto...not sure).

```hive
CREATE EXTERNAL TABLE table_name (col1 dataType1, …, colN dataTypeN)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION 's3://bucketname/keypath/'
TBLPROPERTIES ('skip.header.line.count'='1')
```

There are some important hiccups I ran into. 
* there is no datetime data type in Hive
* I'm actually not mapping the CSV file to a Hive table successfully (some columns are skipped...?)

## Features I need to figure out
Obviously, the table issue above for starters.  

Also, our file naming scheme has the as\_on\_date in the filename, and I'd love to get this into
a column in the Hive table. Related to this, I found that every Hive table has a "virutal column" that
stores what file name/location a row comes from:
```hive
SELECT INPUT__FILE__NAME FROM tbl_name
```

If you know the filename, you can pluck out the date, like so:
```python
qry("select substr(INPUT__FILE__NAME,-19,8) from tbl_name limit 5",con)
```

But, of course, all this I'm doing *after* the table was already created... Not the biggest deal, but still
kind of annoying.

## Some References
* [Hive: How to create a table from CSV files](http://qpleple.com/hive-how-to-create-a-table-from-csv-files/)
* [HiveQL: Data Definition Language](https://www.safaribooksonline.com/library/view/programming-hive/9781449326944/ch04.html)
* [Hive Data Types](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types)

