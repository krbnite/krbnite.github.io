---
title: Updating ChromeDriver on Linux Ubuntu EC2 Instance
layout: post
---

About 2 weeks ago I returned to an old Selenium scraping project on my MacBook Pro, only to find errors strewn across
the screen like the dead bodies of a recent war my script did not win...  The solution turned out to be updating the
chromedriver.  Last week, the same thing happened to my coworker's Selenium scripts on Windows.  He independently
arrived at the same conclusion and solution, which he brought up this morning, wondering aloud if we need to check 
the chromedriver on our Linux server.  I had already been monitoring the situation: for whatever reason, the few daily automated
scripts had not crashed, and in fact are still working as I write this.  However! (Dramatic suspense: see next paragraph.)

However, when I just went to work on a project I am currently developing, I once again had to bear witness to
the error-ridden brutality of an aging chromedriver...which brings me to this post.  Swapping out the chromedriver
on OS X or windows was easy, but what are the steps for doing it on a Linux server from the command line?

For sanity's sake, here is what worked for me!

```bash
# What is the latest release
wget https://chromedriver.storage.googleapis.com/LATEST_RELEASE
LATEST_RELEASE = `cat LATEST_RELEASE`
rm LATEST_RELEASE

# Get latest release
wget https://chromedriver.storage.googleapis.com/${LATEST_RELEASE}/chromedriver_linux64.zip
unzip chromedriver_linux64.zip
rm chromedriver_linux64.zip

# Move new driver to folder you usually keep it in
sudo mv chromedriver /usr/local/share/chromedriver
```
