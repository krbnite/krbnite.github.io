---
title: How to Import a Python Module from an Arbitrary Path
layout: post
---

Time and again in my various data collection and reporting automations, I use similar functions
to connect to Redshift or S3, or to make a request from the YouTube Data API or Facebook Graph.  I keep these automations
in a single repository with subdirectories largely based on platform (e.g., youtube, facebook, instagram, etc).  Within
each subdirectory, projects are further organized by cadence (e.g., hourly or daily) or some other defining
characteristic (e.g., majorEvents).  The quickest/dirtiest way to re-use a function I've already developed in another project
is to copy-and-paste the function into a script in the current project's directory... But then something changes:
maybe it's an update to the API, or I figure out a more efficient way of doing something.  Or maybe I just develop a few
extra related functions each time I work on a new project, and eventually start forgetting what I've already created
and which project it's housed under.

You get the point: I need a better organization strategy.

Most of my reusalbe code can be neatly organized into various "helper tools" modules, e.g., redshiftTools, seleniumTools, 
hiveTools, etc.  It would be nice if I could just make a directory at the root of my project tree called "commonLib" and
keep any and all reusable code in neatly organized modules within that directory.  

Importing from a descending directory is fairly straightforward, e.g.:
```python
import subDir.myLib as myLib
```

But how do you import Python modules from a parent directory?  Or more generally: how do 
you import a module from an arbitrary path?  

# Method 1
```python
from import.machinery import SourceFileLoader
myLib1 = SourceFileLoader('myLib1', '/path/to/commonLib/myLib1.py').load_module()
myLib2 = SourceFileLoader('myLib2', '/path/to/commonLib/myLib2.py').load_module()
myLib1.myFcn()
```
Pros:
* It works

Cons: 
* have to issue this the `SourceFileLoader` command for every required module in commonLib
* if the path to commonLib changes, we need to find/fix it in every instance of SourceFileLoader in every project subdirectory
* really annoying when doing some interactive development within a project's directory (much more code remember and type than `import myLib1`)

# Method 2
```
import sys
sys.path.insert(len(sys.path), '/path/to/commonLib')
import myLib1, myLib2
```

Same basic pros and cons.


# Method 3
Permanently put the path to commonLib in the Python PATH variable, e.g., have this happen every time the
bash shell is opened.  

Put this in your .bash\_profile (on Mac) or .bash\_rc (on Ubuntu):
```bash
export PYTHONPATH=$PYTHONPATH:/path/to/commonLib
```

Pros: 
* It works when testing manually (like previous methods)
* Chaning the name of "commonLib" or its path only requires one edit

Cons:
* Seems to fail for crontab-scheduled scripts
* If it did work well with Cron, you have to leave a note for other programmers that "commonLib" must be in the Python PATH variable

(Still need to fully figure this option out; I'm sure the cons are b/c of something I did / didn't do.)

-----------------------------------------

## Some References
* https://stackoverflow.com/questions/67631/how-to-import-a-module-given-the-full-path
* https://askubuntu.com/questions/470982/how-to-add-a-python-module-to-syspath/471168

