---
title: The Beautiful Soup
layout: post
---

I use this package all the time, but only figure it out as I go along...and I often forget what I did the last 
time.  Here are a few refresher commands:
 
```python
import requests
import bs4 

page = requests.get(URL)
page = bs4.BeautifulSoup(page,'lxml')
page.select('div')   #  get list of all div tags and their contents
page.select('input')  # get list of all input tags
page.select('input[type="hidden"]')   #  list of all hidden inputs
page.select('input[type="button"]')   # list of all buttons
page.select('div')[0].get_text()   #  get text on webpage that has to do w/ the first div tag
page.select('div')[1].attrs   #  get attributes of the second div tag
page.select('span')[5].get('id')   # if id is attribute of the 6th span tag, then this will get that id
```
