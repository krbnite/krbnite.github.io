---
title: Scraping Playlist IDs from a YouTube Channel
layout: post
tags: selenium webscraping python youtube wwe
---

Yesterday, I covered how to scrape videos from a YouTube playlist. Today, imagine you are interested in
a channel that has multiple playlists, and you want to scrape 'em all!

One way to do this is to manually construct a list of the playlist IDs you are interested in, and run
the function from yesterday on each of them.  

That's ok...but what if a new playlist is added?  Or what if you have to do this on 100s of playlists,
and don't have a list of their playlist IDs in front of you?  The possibilities go on: what if you
are in want to do this for a bunch of YouTube channels?  Or simply just want the possibility of
quickly doing it for any YouTube channel?

Point is: there is a better way!

The better way is to use Selenium to go scrape what playlists a given channel has right here,
right now.  This code is solid: just pass a channel ID.  From there, the code from yesterday
should get you the rest of the way.


```python
import pandas as pd
import time
import bs4
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.phantomjs.service import Service as PhantomService

def get_playlists(
  channel_id, 
  visual = False, 
  new_only = True
):
    #-------------------------------------------
    # Browser Set Up
    #-------------------------------------------
    if visual == True:
        driver_path = '/usr/local/share/chromedriver'
        service = ChromeService(driver_path)
    else:
        driver_path = '/usr/local/bin/phantomjs'
        service = PhantomService(driver_path)
    service.start()
    options = {}
    driver = webdriver.Remote(service.service_url, options)
    time.sleep(1)
    
    #-------------------------------------------
    # Scrape Channel Playlists
    #-------------------------------------------
    youtube = 'https://www.youtube.com'
    channel_url = youtube+'/channel/'+channel_id+'/playlists'
    driver.get(channel_url)
    time.sleep(1)
    page_source = driver.page_source # Or: requests.get(channel_url).text
    
    #-------------------------------------------
    # Extract with BeautifulSoup
    #-------------------------------------------
    page = bs4.BeautifulSoup(page_source,'lxml')
    playlist_html = page.select('h3[class="yt-lockup-title "]')
    channel_name = page.title.text.strip().split("\n")[0]
    playlist = pd.DataFrame(columns=[
        'list_name','list_id','list_url',
        'channel_name','channel_id','channel_url'])
    for idx,row in enumerate(playlist_html):
        attrs = row.select('a')[0].attrs
        list_name = attrs['title']
        list_id = attrs['href'].split('=')[1]
        list_url = youtube + attrs['href']
        playlist.loc[idx] = [
                list_name, list_id, list_url,
                channel_name, channel_id, channel_url]
    driver.close()
    return playlist
```
