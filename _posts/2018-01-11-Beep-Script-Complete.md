---
title: Beep! Script Complete.
layout: post
tags: unix-tools wwe
---

Sometimes you need to run a script that takes long enough to warrant web surfing, but 
not long enough to really get too involved in another task.  Problem is, sometimes
the script completes and you're still reading about whether or not Harry Potter 
should have ended up with Hermione instead of Ron.  (Good arguments on both sides,
I must say!)
 
Anyway, point is, it would be nice to have some kind of notification that the script
completed its task.  It's actually pretty simple!
 
```
python myScript.py && echo -e "\a"
```
 
On this [StackExchange page](https://unix.stackexchange.com/questions/1974/how-do-i-make-my-pc-speaker-beep),
some people said they had issues with this when remotely logged in via SSH. I did not have this
problem when remotely logged into my EC2 instance on AWS, but in case you do, the recommendation is
to "redirect the output to any of the TTY devices (ideally one that is unused)."
 
```
./someOtherScript && echo -en "\a" > /dev/tty5
```
