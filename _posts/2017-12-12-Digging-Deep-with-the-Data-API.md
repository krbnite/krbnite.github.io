---
title: Digging Deep with the Data API
layout: post
---

If you are a content creator/owner on YouTube, the Reporting API will provide you with a horde of data.  You just 
have to wait about two days for it.

"Ain't nobody got time for that!"

Oh, and don't forget that it only comes at a daily cadence.

<figure>
<img src="./images/wtf-clint.jpg" width="500vw">
</figure>

What if you work for a media company with 1000s of video assets, and you want to keep a damn near real-time pulse 
on all of them?  

### Attempt #1: Selenium
I first approached this problem with Selenium.  The script was set to run at a regular cadence.  It was good enough,
but not great.   Sporadically, it simply did not scrape.  Over several months, I patched and duct-taped the code together with 
`try/except` and `time.sleep()` statements, creating my own little Frankenstein.  

Still, it would occasionally fail, causing problems for other projects down the pipeline.

During this time I was doing some work with YouTube APIs as well, and gleaned that the Data API is likely able to 
replace Selenium altogether for this task.  It was just a hunch.  But it required implementation.

### Attempt #2: Data API (Search)
The Data API has a search.list method that you can restrict to a specified channel (by providing the channel ID) and
object type (which I set to 'video'). For any API call, the maximum results returned is 50.  Clearly, pagination would be
necessary to obtain tens of thousands of videos. I did everything right...except that only 400-500 videos were being
returned.  

```python
#code
```

Reason for Failure: I found that limiting the results to ~500 is a built-in feature of Search 
([https://issuetracker.google.com/issues/35171641](https://issuetracker.google.com/issues/35171641)).

Pro Tip: A YouTube representative also told me that Search should be avoided whenever possible because it incurs expensive 
quota costs.  

### Attempt #3: Data API (Playlists + PlaylistItems)


