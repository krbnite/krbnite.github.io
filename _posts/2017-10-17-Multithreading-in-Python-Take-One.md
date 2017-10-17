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





However, the script is used to load "infinitely long", dynamically-generated webpages, and can drain the machine's memory quite a bit, so 
