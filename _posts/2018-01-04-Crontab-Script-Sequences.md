---
title: Crontab Script Sequences
layout: post
---

This is a quickie, but a goodie!  Let's say during an ETL process, you only want to run a second 
script if a first one completes successfully.  For example, say the first script uses an API to
extract some cumulative data from a social media site (e.g., views,  likes, whatever), transforms it into some 
tabular form, and loads it into S3 on a daily basis; and say the second script computes deltas on this
data to derive a daily top 10 table to be used by some interested end user in Redshift (e.g., daily top 10 viewed
YouTube videos, daily top 10 most-liked posts, daily top 10 whatever).  

The second script depends
on the successful completion of the first, so putting each a cron job can result in redundant email 
error notifications:

* Head's up, Kev!  Script 1 failed at 3:00 A.M.
* Head's up, Kev!  Script 2 failed at 3:01 A.M.

Alternatively, if one doesn't space out the cron jobs enough, it's possible that Script 2
could begin before Script 1 ended:

* Head's up, Kev!  Script 2 failed at 3:01 A.M.
* Success!  Script 1 completed at 3:03 A.M.

A simple solution is to use bash's '&&' operator to link the two scripts together in a 
single crontab row:

```
0 3 * * * /path/to/Script1.py && /path/to/Script2.py
```

In fact, you can do this with any number of scripts if you really want, though it seems to
become a less elegant solution as the number chain-linked scripts rises.  (But who knows,
inelegant isn't always bad!)

```
0 3 * * * /path/to/Script1.py && /path/to/Script2.py && ... && /path/to/Script3.py
```

To be fair, an alternative way to do this is to link the scripts together in bash file...

```vim
#!/bin/bash
/path/to/Script1.py
/path/to/Script2.py
```


...and just call that file from your crontab job.

Is one better than the other?  I don't know -- you tell me!  
