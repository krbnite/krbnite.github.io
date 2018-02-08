---
title: Crontab Backups
layout: post
---

Let's say you have a server: it's yours and yours only.  You life's work is on 
it.  Now let's make it interesting: let's say that suddenly other people were given
the same credentials to log in and out. Imagine the scene: Your automations have been working so well on that Linux server,
and those guys using the Windows server want in.  They've tried to get their automations to work,
but for whatever reason there are umteen different security measures that are in place on that
machine, and it the challenge is more than technical: it's social and political!  People are sending emails, holding meetings,
negotiating deals with the gatekeepers!  

People of all skill levels have reports that need automating, and they're suddenly asking questions
about your Linux server.  More importantly, their managers and higher-ups are asking questions.  Things
are changing, and they might change quick: your lovely days of sole propietorship are coming to an end!

One solution might be to make user accounts for them... But how?  I'm not a... \*ahead\* I mean, in this
scenario, \*you\* are not a Linux administrator.  Sure, you hack around and you can probably do a 
quick Google search to figure some things out.  But it still feels icky, and you don't know
how much time you have!

First Thought: good damn thing you've been using Git for version control!

Second Thought: Know what you haven't been version controlling?  Your cron table!  What if those invaders 
start messing around with crontab and screw things up?  These fears may be unfounded,
but they're still fears.  And the conclusion is legitimate no matter what:  Why have you not 
been version controlling or backing up your crontab file?

Third Thought: Wait, I remember seeing that Cron allows you to grant various user permissions, etc. But
remember: in this scenario, the idea is that everyone is using one set of credentials to access the 
Linux server. 

Fourth Thought: Ok, ok -- better learn some Linux administration.

Fifth Thought: No, first better make a bash script that automatically backs up my cron table every
week.  

In text file (crontab\_backup.bash):
```bash
#!/bin/bash
crontab -l > /path/to/crontab/backups/`date +%Y-%m-%d`.txt
```

In bash shell:
```bash
chmod +x crontab_backup.bash
```

In crontab:
```
0 0 * * 0 /path/to/crontab/backups/crontab_backup.bash
```

Extra Credit:  Have this directory in one of your version controlled repositories for extra backup!
