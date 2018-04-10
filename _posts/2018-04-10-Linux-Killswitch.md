---
title: Linux Killswitch
layout: post
---

Recently, I've been adminstering a Linux server for some folks on the Content
Analytics and Digital Analytics teams.  With this great power comes--you guessed it--great
responsibility.  And one such responsibility is ensuring that the system's resources
are available and in good use.  A few of our team members are developing Selenium
scripts, which employ a Chrome webdriver and Xvfb virtual display, both of which seem
to have a problem of hanging around long after they've stopped being used.  This seems
to happen when a function or script someone wrote crashes, or if they don't properly
close things (e.g., `display.stop(); driver.quit()`).  I mean, that's somewhat of a
hypothesis on my part -- the main point is that these processes build up into the 100's
and just hang around.

```
ps -ef | grep chrome | wc -l  # Whoa, way too many!
ps -ef | grep Xvfb | wc -l    # Not as much, but still too many!
```

# Killing Things, Slow and Stupid
If you just type `ps` into the command line, you won't really see any processes besides
those that are directly related to your current shell and username.  At one point, I learned
to use the 'e' and 'f' flags to get a much more complete list:

```bash
ps -ef
```

If your system has hundreds of processes running, you might be like, "Whoa, that's a lot,
but is it 'a lot' a lot or just a lot?"  To know this, you have to (i) count, and (ii) build
an intuition for what's normal.  For counting, just do this:

```bash
ps -ef | wc -l
```

To build an intuition, just do that a lot -- throughout the day and throughout the week. On my
system, it seems like 250 - 350 is normal.  When it gets to 700, I know something's up, and from
this happening several times now, I know that it's probably related to the Chrome webdriver (though
Xvfb is also showing up quite a bit these days since we figured out the benefit of using virtual
displays in our Selenium scripts).

To see all the processes related to Chrome, simply grep it:
```bash
ps -ef | grep chrome
```

At this point, you can look for some old, long-running Chrome procesess and kill them:
```bash
kill PID
```

# Killing Things, Faster and Smarter
The problem is that there are so many Chrome-related processes, and many of them depend on each other. If you kill the right one, 
you might kill like 20 at once. Alternatively, you could end up having to kill all 20 individual processes individually... It would
be useful to know which processes to kill first!


One approach is to kill the processes that take up the largest %CPU or %MEM, but using "-ef" does not provide
that info.  Fortunately, I learned about using the 'aux' argument today:

```bash
ps aux  # gives a lot of info w/ easy-to-interpret cols  like %CPU, %MEM
```

I also learned about ps's tree view today:
```bash
ps axjf
```
Both approaches allow you to make smarter decisions about which processes to kill first so that
you kill as many processes as possible with the least amount of work.  That said, JEEZ! Wouldn't it be
nice to just kill all processes related to some command or program all at once?

The answer, of course, is YES!

# Killing Things with Wild Abandon
This is where `pgrep` comes in: it returns all process IDs (PIDs) associated with a command.  Try it!

```bash
pgrep cat
pgrep chrome
pgrep Xvfb
```

If you're sure you can safely kill all processes at once, just do this:

```bash
kill `pgrep chrome`  # Kill 'em all in one stroke
```

This is a powerful command!  Use it wisely.  For example, we actually have hourly Selenium bots
that go out and do some useful work -- wouldn't want to kill 'em!  But knowing their schedule
allowed me to use this effectively, and the efficiency was so incredible I just had to share
the good news :-)

--------------------------------------------

Related: 
I also played around with `htop` today, which is like `top`, but more human friendly:
```bash
top  # look what's going on
htop  # like top, but for humans 
```

------------------------------------------

## Some References

* https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux
