---
title: Selenium Still Hangin' Round!
layout: post
tags: unix-tools python selenium automation etl wwe
---

At work, I have a particular python Selenium script that is scheduled in Crontab
to scrape some data every two hours.  Little did I know that somehow, at one point,
the code broke... Assuming everything was working, I wondered:
> Why the hell are there so many chromedriver processes hanging around in the process list?

I was cued into this when some other scripts began failing because the system was lacking
resources.  I began investigating:

```
ps -ef  
```

Whoa. That's a lot of processes... Lot more than the other day.  How many?

```
ps -ef  | wc -l
```

Holy cow!  Over 800 processes in the list... It looked like a lot of them had to do
with chrome or chromedriver... How many?

```
ps -ef  | grep chrome | wc -l
```

Yea -- over 75%. Wow.  

The potential cause was obvious: I only have one script that uses chromedriver.  It had run
for months and months with no issue, but -- Murphy's Law + Occam's Razor.  

The first thing I did was reboot the remote Linux system when no automations were 
running.  The multitude of chrome processes disappered and my other automations resumed
their normal health.  Then I began inspecting the script in question.

My basic browser start-up code was this:
```python
#--------------------------
# Start Browser Service
#--------------------------
service = webdriver.chrome.service.Service(self.driver_path)
service.start()
if visual == True:
    options = {}
else:
     options = webdriver.ChromeOptions()
     options.add_argument('--headless')
     options = options.to_capabilities()
     driver = webdriver.Remote(service.service_url, options)
time.sleep(3)
```

End when a particular segment of scraping was complete, the script would abandon
the browser like so:
```python
driver.close()
```

After interactively playing around with the code for a while, my first guess at a solution 
was that I was not explicitly stopp the Chrome Webdriver service, so I added a line to the code
to remedy this.
```python
driver.close()
service.stop()
```

About a week later, I check and --- Dang it! I have a few hundred Chrome processes again. What
is going on?  Time to go back to the Google/StackOverflow drawing board!

I was led into three new directions:
* use time.sleep(3) before attempting to close the browser 
* use driver.quit() instead of or in addition to driver.close() 
* look into [zombie processes](https://www.howtogeek.com/119815/htg-explains-what-is-a-zombie-process-on-linux/)

So I began with the first idea:
```python
time.sleep(3)
driver.close()
service.stop()
```

But instead of waiting around this time, I rebooted the remote Linux system and manually began 
running the script... Oh, it crashed.  I must've done something wrong.  Tweaked my command a bit and
tried again... Crash!  Maybe I'm using the wrong python interpreter: tweak, crash!  

It became clear: at some point, something I did broke this code. It had nothing to do with `driver.close()`,
but more to do with not making it to `driver.close()` before the crash.  I checked: yup! There were now
15 chrome processes lingering for no good reason... So for a script scheduled to run
every 2 hours, I was accruing **at least** 12 chrome processes a day, but more likely quite a few more
given my experimentation and observations.  

Some interesting asides: 
* Python vs iPython: the script usually uses the iPython interpreter, which I found crashes with 7 lingering processes; however, I also tested with a regular python3 interpreter and found that there were no lingering processes afterwards.  There's a lesson in this somewhere! (Likely shouldn't be using the iPython interpreter for an autoamted script, unless necessary.)
* Zombies Nowhere: If you use the Linux `top` command, the number of zombie processes is listed at the top of the output.  As far as Linux is concerned, the crashes were not generating any zombie processes -- just real, live ones!


## Epilogue: Reason for Crashing
One of the assets being tracked stopped generating new content, which is an obvious edge case in hindsight,
but was not accounted for in the original code.  Now that it is, the program no longer crashes and (I suspect)
there will no longer be an issue of too many Chrome processes hangin' round!

------------------------------

### References
* https://stackoverflow.com/questions/21320837/release-selenium-chromedriver-exe-from-memory
* https://www.howtogeek.com/119815/htg-explains-what-is-a-zombie-process-on-linux/
