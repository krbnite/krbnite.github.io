---
title: Pandas Remote Crash Prevention
layout: post
---



The Issue: When I SSH into my AWS/EC2 instance at work and romp around in iPython, the command line completion
functionality works just fine... That is, until I import pandas and dare hit the tab key!  Then it pukes
its guts out and dies.

<figure>
<img src="/images/pandas-makes-ipython-puke-and-die" width="600vw">
</figure>

Mileage may vary here, so to be clear: this issue occurs when I SSH into a Linux Ubuntu (Xenial) 
AWS/EC2 instance from the Terminal on my MacBook Pro and play around in an iPython interactive instance, which
stems from an Anaconda distro of Python3.  (Could probably get more specific, but I'm sure this
problem is likely more generalized than that.)

Gotta admit: the solution came within 10 seconds of a Google search, however, for at least a month,
my mad method was to avoid pressing tab instead of Googling... You know, to not waste time... Except
that avoiding tab completion remotely when you use it all the time locally is damn-nigh impossible!

Anyway, got sick of it and finally figured it out, thanks to Anaconda issue #1215 
([matplotlib raises error](https://github.com/ContinuumIO/anaconda-issues/issues/1215)).

The gist is, if you're going to use pandas, you have to make sure matplotlib is using something
called 'Agg':
```
import pandas as pd
import matplotlib as mpl
mpl.use('Agg')
```

That's it!  No more gut puke.
<figure>
<img src="/images/pandas-no-more-gut-puke.png" width="600vw">
</figure>

The issue itself that helped me adds one more tidbit that has to do with `mpl.pyplot`.  Looks as if
the user had to also issue the command `mpl.pyplot.ioff()` to turn interactive mode.  This was based on
info in a StackOverflow page on related issues (worth reading over!):
* [Problem running python/matplotlib in background after ending ssh session
](https://stackoverflow.com/questions/2443702/problem-running-python-matplotlib-in-background-after-ending-ssh-session)
