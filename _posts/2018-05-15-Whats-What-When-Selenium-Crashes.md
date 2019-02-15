---
title: What's What When Selenium Crashes
layout: post
tags: selenium python
---

Recently, the Selenium component of my Facebook Graph code broke.  This is important b/c we need Facebook data for during 
and after live events streamed on Facebook, as well as collecting insights data from 400+ Facebook Pages at a regular, 
daily cadence. 

To be clear, when it comes to my Facebook data collection strategy, Selenium is only necessary for one thing: logging into 
a privileged user’s developer account (@ developers.facebook.com) to obtain a user token (e.g., my personal account has managerial/admin 
permissions on hundreds of Facebook Pages).  Once the user token is obtained, Selenium’s job is done and all other data 
collection is done with simple HTTP requests to Facebook’s Graph.  However, that user token is essential... Without it, you 
cannot do much! E.g., the user token is used to obtain Page tokens:

```python
# Method 1
wwe = token.get('wwe?fields=access_token')

# Method 2
data = token.get('me/accounts')
```

I figured I would write up a little bit on Selenium at one point and the issues we have encountered, so that anyone 
maintaining the scripts down the line (or writing new ones!) knows a few things to look out for.  Given this new bug 
(distinct from previous bugs), I figured there is no better time than now.

When you are new to Selenium, diagnosing sudden crashes/catastrophes can be difficult.  Don’t worry though: you will surely 
gain experience!  I’ve not had a Selenium script yet that has remained completely stable.  And with experience, debugging a 
Selenium script becomes ... familiar, but at times still difficult!

So why do Selenium components break?  

### Website Evolution
One thing we know for sure is that Selenium scripts are sensitive to changes in a website’s design (HTML/JS/CSS).  In the 
past, this has usually amounted to an HTML element’s CSS class changing name, or a button’s text changing, etc (simple 
things).  

### Outdated Chromedriver
Another reason scripts suddenly stop working is when the server’s chromedriver becomes outdated (I keep the Chromedriver 
located @ /usr/local/share/chromedriver for some unlucky fool working on my old code, reading this in the future).  

### Headless Chrome
Though you may develop your Selenium scripts while working w/ a regular browser, you cannot use Selenium to drive a 
regular browser on a server that has no display.  My original solution to this was using a headless browser called 
PhantomJS --- until I found out about Headless Chrome.  Either solution works, but since we usually develop our scripts 
in Chrome, it just seemed to make more sense to use Headless Chrome on the server.  

Though headless Chrome generally allows us to run Selenium scripts our our displayless server, unfortunately headless mode 
has its limitations.  For example, you cannot download files when using Headless Chrome (ironically, Google considers this a 
feature, not a bug).  Don't get me wrong: sometimes you can obtain the file's URL, then use a non-Selenium/non-browser 
method of downloading the file (e.g., this can be done on Twitter's Periscope website).  However, there are some websites 
that will give you issues with those other approaches as well!  (For example, when you must interact with a dialog box, 
you might run into trouble not being able to use Selenium.)   

For a while, I wondered how to trick Headless Chrome into downloading these reports... 

Not saying that it can't be done, but I solved it another way before figuring it out!  I realized there might be a 
simpler solution: virtual displays.  Simply googling "selenium virtual display" brought up the necessary confirmation and 
code snippet.  

```python
from pyvirtualdisplay import Display
vdisplay = Display(visible=0, size=(800,600))
```

Just had to make sure we had Xvfb available to all users on the server:
```bash
sudo apt-get install Xvfb
```

Using a virtual display obviates the need for Headless Chrome, and most of the newer Selenium scripts we have on the server 
reflect this (as it has become the preferred method).  The virtual display solves the same problem that Headless Chrome 
did without the limitations: you can use Chrome in visible mode on the virtual display (even though to you it's still 
invisible!), thus you can download files. 

### Anything else?
Ok, ok... Changes in website code, outdated chromedriver, limitations of headless Chrome... 

Have we covered everything?!

Nope.

### Bigger (Virtual Screen) is Better
My most recent bug was of its own kind...  The solution was simple but not immediately obvious.  

So what was the problem?  In short, a Selenium script that once ran free like a beautiful, wild stallion suddenly
did not work: it was no longer able to find a certain login button.  Surely this was a simple case of a CSS class name 
changing, or something similiar, right?

On a displayless server, you cannot see what is going on, so as usual I first tested the code on my laptop in visual 
mode, line by line.  Everything ran fine!  Ok, just to be sure: does it still run fine if I call on the Selenium code 
from another script?  Yes -- no issues!  The script works in visual mode...and so this tells us that the login 
button's code did not change (otherwise it wouldn't have worked).

Since this Facebook script was a bit older, it was still using headless Chrome.  "Maybe Facebook has started blocking 
headless Chrome bots," I thought. So the next step was to run the script line by line in headless mode. It sure seemed 
like this was the issue: while using headless Chrome, the script failed on my laptop.  

I figured the solution would be to simply swap the Headless Chrome code w/ a virtual display like I'd done so many times
before... And, yes!  It worked 

...at least on my laptop... 

I patched in the fix on our EC2 instance, tested, and… WTF?  It was still broken!  Surely I must’ve made a typo or 
something... Right? 

Nope.  

Ok, try updating the chrome driver, and — 

Nope.  Still broken.  Seriously: wtf?

At this point, there was no other choice: debug line by line in the virtual display on the server.  For each line, 
it is helpful to save a screenshot and to download the file locally for viewing:

```python
# In python on Server
browser.save_screenshot('debugging.png')
```

```bash
# In SFTP shell to Server
get debugging.png

# On local terminal
open debugging.png
```

Interesting.  The Selenium script couldn't find a login button because there was no login button to be found...  But I 
noticed that the screenshot did not seem to fit in the entire webpage.  To confirm, I tried using visual mode again on 
my computer.  Yes... It's true.  The screenshot from our server was cutting off about a 1/3 of the screen on the right 
hand side.  

A crazy idea occurred to me: does Facebook only send the code necessary to fill the screen?  Put another way: if the 
virtual display's screen size was too small to fit the entire Facebook page, you might image that the out-of-sight portion 
of the screen is still "there in code," just not currently visible on the screen... But then again: what if it wasn't 
"there"?  What if Facebook only sent enough code to fill out the in-view portion of the screen.  It struck me that in 
the code I use for the virtual display, I select a screen size:

```
vdisplay = Display(visible=0, size=(800,600))
```

I looked at the documentation and found that the default screen size is actually much bigger than this: 1024 x 768.  Remember, 
the code snippet was originally copied and pasted... Not sure why so-and-so chose 800 x 600 and didn't really question it up 
to this point.  

Bam!  It worked.  The Selenium script is now able to find the login button in the code.  The screen grab shows that it is 
now on the screen... So that crazy idea wasn't so crazy: the portion of the screen that is "out of view, but you know is 
there" is actually "out of code" too: it's not there!  It's not actually "there" unless your screen is big enough (or 
possibly you scroll right).  I don't know about you, but that certainly defied my assumption. 

Don't get me wrong: this type of thing isn't news to me.  I know when you scroll vertically on many pages (e.g., Twitter) 
the site's content is dynamically generated (that is, the code isn't there until you scroll there). But it was a surprise 
that this is true for sideway scrolling too (or, at least it's true for this Facebook page).

Wait, but why wasn't it working in headless mode?  I went back in to take some screenshots: coincidentally, the default 
screen size used in headless mode was also too small, and so was failing for the same reason.  

You might also wonder: "But wait, why did the virtual display method originally work on your computer if the designated 
screen size was too small?"  To this I say: don't know.  I just know that on the Linux server, it was an issue -- and that 
issue was fixed by using a larger virtual screen size.  

Anyway, moral of the story: make sure your virtual display is wide enough!  

You'll probably find this code snippet lurking in some other scripts / blog posts b/c I've used it quite a 
bit:

```python
Display(visible=0, size=(800,600))
```

If your script has that snippet and is failing, one thing you can immediately do is try changing that it to
the default screen size since it is bigger:

```python
Display(visible=0) # or manually specify huge screen size
``` 

This bug was the result of Facebook changing how their page is laid out on a device, so fits in the category of 
"website evolution," but it was the bug was an indirect result, whereas usually we've uncovered bugs directly due a 
change in an HTML element's code.

Thanks for reading!
