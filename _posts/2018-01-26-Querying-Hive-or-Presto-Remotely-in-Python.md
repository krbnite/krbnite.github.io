---
title: Querying Hive or Presto Remotely in Python
layout: post
tags: python aws hive presto databases wwe
---

Using the YouTube Reporting API several months ago, I "turned on" any and every daily
data report available.  That's a lot of damn data.  Putting it into Redshift would be a headache,
so our team decided to keep it in S3 and finally give Hive and/or Presto a shot.

Here, I show how to connect to remote instances of Hive and Presto (we're on AWS, but this will work for
whatever I would think).  We have all the daily reports in Hive database, call it hiveYtReps.  These 
reports are just CSV files in S3 buckets, but through some magic (serialization, I believe it's called), our
DE team makes 'em appear as tables if accessed through Hive or Presto.

## PyHive
I first installed PyHive and various dependencies... [Write more on this (find the notes where I had to pip install sasl and all that)]


## Hive
```python
from SQLAlchemy import create_engine
con = create_engine(
    'hive://'+username+'@'+IP+':10000/'+hiveDbName,
     connect_args = {'configuration': {'hive.exec.reducers.max': '123'}},
)
```

## Presto
```python
from SQLAlchemy import create_engine
create_engine(
    'presto://'+username+'@'+IP+'/hive/'+hiveDbName,
    connect_args={'session_props': {'query_max_run_time': '1234m'}}
)
```

On the PyHive webpage, you will see that `connect_args` has another key word:
```python
connect_args={'protocol': 'https', 'session_props': {'query_max_run_time': '1234m'}}
```

In my case, this caused an error.  By removing the protocol keyword, everything worked fine! (We have
other security measures in place...but still kind of feels weird getting something to work by making
it less secure looking.)
