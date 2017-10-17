---
title: Multithreading in Python (Take One)
layout: post
---

Here's the situation:
* I have a selenium script that scrapes and collates data from a javascript-heavy target source
* There are multiple target sources of interest
* The data collection from each source should happen at about the same time 
  - e.g., more-or-less within same ~20 minute span is good enough
* If each source scrape were quick, one might target them in sequence, however this is not the case!
  - Several of the targets are large and take way too much time to meet the "quasi-simultaneous" requirement
  
Some solutions include:
* create separate python script for each target and use crontab to schedule all at the same time
  - however, any changes in the codebase's naming schemes, etc, could make upkeeping each script a nightmare
  - ultimately want one script
* create single python script that takes parameters from the commandline, and use crontab to schedule all at the same time
  - this is a much better solution than the first
  - however, I'm dealing with 30+ targets and would ideally like to keep my crontab file clean, e.g., can this be done with one row?
* create single python script that takes parameters from the commandline, make a bash script and run multiple iterations of the python script simultaneously by forking (&), and use crontab to schedule bash script
  - this is getting pretty good!
* learn how to use multithreading in python

The python/bash/crontab combo is pretty satisfying, but this post is about multithreading in python!

```python
import threading
from threading import Thread
from myTargets import target1, target2
from myHelperFcns import connect_to_database()
con2db = connect_to_database()

def fcn1():
  scrape = scrapeObj(target1, con2db)
  scrape.get()
  scrape.write()

def fcn2():
  scrape = scrapeObj(target2, con2db)
  scrape.get()
  scrape.write()
  
def fcnMaster():
  Thread(target = fcn1).start()
  Thread(target = fcn2).start()

fcnMaster()
```

This setup is the first bit of advice I found on 
[StackOverflow](https://stackoverflow.com/questions/2957116/make-2-functions-run-at-the-same-time), and I'm
happy to say that it seemed to do exactly what I wanted: it ran two instances of my selenium script side-by-side (as evidenced
by two the Chrome browsers that popped up and did my dirty work for me).

But is it working as good as it could be?

If you read through that StackOverflow page, or this suggested page on 
[deciding among subprocess, multiprocessing, and thread in Python](https://stackoverflow.com/questions/2629680/deciding-among-subprocess-multiprocessing-and-thread-in-python), it seems that threading might not be the best option. In a follow-up post, I'll explore threading and these other options in more detail. 




