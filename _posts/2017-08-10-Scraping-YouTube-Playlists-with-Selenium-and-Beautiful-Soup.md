---
title: Scraping YouTube Playlists with Selenium and Beautiful Soup
layout: post
---

Let's say you want to monitor your YouTube Playlist on the daily or hourly: keep track of which videos
are getting viewed, liked, and so on.  One way to do this is to
employ some Selenium -- all you'll need it the playlist's list ID.

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
import bs4
import pandas as pd
import time
import re

def get_playlist_videos(
    list_id, 
    driver_path='/usr/local/share/chromedriver',
    new_only=None, 
    visual=None,
):
       
    #--------------------------
    # Start Browser Service
    #--------------------------
    service = webdriver.chrome.service.Service(driver_path)
    service.start()
    if visual == True:
        options = {}
    else:
        options = webdriver.ChromeOptions()
        options.add_argument('--headless')
        options = options.to_capabilities()
    driver = webdriver.Remote(service.service_url, options)
    driver.implicitly_wait(10)
    
    #--------------------------
    # Go to Playlist Page
    #--------------------------
    youtube = 'https://www.youtube.com'
    list_url = youtube+'/playlist?list='+str(list_id)
    driver.get(list_url)
    time.sleep(3)
    
    #--------------------------
    # Click to Load Content
    #--------------------------
    there_is_more_to_load = True
    while there_is_more_to_load:
        try:
            load_more = driver.find_element_by_class_name("load-more-text")
            driver.implicitly_wait(10)
            load_more.click()
            time.sleep(3)
        except:
            there_is_more_to_load=False
    
    # WARNING: sometimes page loads w/o load more button and refresh is based on scrolling
    #--------------------------
    # Get Source Code
    #--------------------------
    page = bs4.BeautifulSoup(driver.page_source, 'lxml')
    
    #--------------------------
    # Extract Video Info
    #--------------------------
    list_name = page.select('a[class="yt-simple-endpoint style-scope yt-formatted-string"]')[0].text
    playlist = pd.DataFrame(columns=['video_name','video_id','video_url', 'views'])
    vlist = page.select('a[class="style-scope ytd-playlist-video-renderer"]')
    
    print("\tGetting playlist data from ", list_name)
    for idx,video in enumerate(vlist):
        info1 = video.select('span[id="video-title"]')[0]
        if 'aria-label' in info1.attrs.keys():
            info2 = info1.attrs['aria-label'].\
                    split(info1.attrs['title'])[1].split(self.channel_name)[-1]
            video_name = info1.attrs['title']
            video_id = video.a.attrs['href'].split('/watch?v=')[1].split('&')[0]
            video_url = youtube+video.a.attrs['href'].split('&list')[0]
            try:
                time_since_release = info2.split()[0:2]  # e.g., ['2', 'days']
            except:
                start_here = re.search('[0-9]',info2).start()
                info2 = info2[start_here:]
                time_since_release = info2.split()[0:2]  # e.g., ['2', 'days']
            num_views = int(''.join(info2.split()[-2].split(',')))
            if new_only:
                if time_since_release[1] in ['seconds','minutes','hours','days']:
                    playlist.loc[idx] = [video_name, video_id, video_url, num_views]
            else:
                playlist.loc[idx] = [video_name, video_id, video_url, num_views]
        else:
            if info1.attrs['title']=='[Private video]':
                print('\t\t\t...private video encountered: no data.')
    
    #--------------------------
    # Wrap it Up!
    #--------------------------
    driver.close()
    return playlist

```
