---
title: Selenium Instabilities
layout: post
---

Last year I developed a lot Selenium scripts in Python to scrape various social media
and data collection platforms.  It was a lot of fun, and certainly impressive (try showing
someone how your program opens up a web browser, signs in to an account, navigates to various pages, clicks on buttons,
and scrolls aroun without impressing them!).  

Over time, I ran into various problems with these scripts.  Most problems need a `time.sleep(x)`, but
not all.  Some problems seem to be outside of my control altogether.

One such problem is on YouTube: recently, they changed the class name of the anchor tags I was
scraping.  This meant that BeatifulSoup's `select` method began churning out empty lists, instead
of the expected data elements.  

For reasons like this, I've changed a lot of automated scripts, preferring available APIs over
Selenium.  Don't get me wrong: Selenium is a great tool, and excellent way to supplement the various
APIs I now use.  But the "stability" of your script is so dependent on little tweaks in the HTML, CSS,
and JavaScript...  

Now that I'm writing this, I'm thinking: "Yea, but APIs are known for abruptly changing as well."  So,
maybe there is no rest for the automated!  

Example:

```python
## Formerly...
vlist = page.select('a[class="style-scope ytd-playlist-video-renderer"]')

## Now...
vlist = page.select('a[class="yt-simple-endpoint style-scope ytd-playlist-video-renderer"]')
```

As you can see, YouTube added an additional class name ("yt-simple-endpoint") to the anchor
tag.  This is likely to happen again!
