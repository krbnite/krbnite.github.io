---
title: Signing in with Selenium
layout: post
tags: selenium webscraping python wwe
---

There are many services we use that provide data from their website... You 
just have to sign in!  Usually there is a dashboard, an Excel/CSV file, or 
both.  Scraping a dashboard is incredibly specific to the platform you are
interested in, depending on how that dashboard is coded.  Scraping a file is
something I need to figure out and will cover in a future post.

In this example, I show how to sign into JW Player's analytics dashboard
and get some data. Notice how specific the scrape is on the dashboard.  The major
takeaway here should be an idea about how to sign into an online account.

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.phantomjs.service import Service as PhantomService
import bs4
from datetime import datetime
import pytz
import time
import re
import pandas as pd

def get_jwp_data(
    email, 
    password,
    visual=False
):

    if visual == True:
        driver_path = '/Users/kurban/Applications/chromedriver'
        service = ChromeService(driver_path)
    else:
        driver_path = '/usr/local/bin/phantomjs'
        service = PhantomService(driver_path)

    today = str(datetime.now(pytz.timezone('US/Eastern'))).split()[0]

    service.start()
    driver = webdriver.Remote(service.service_url, {})

    # Login Page
    driver.get('https://dashboard.jwplayer.com')
    time.sleep(3)
    
    ## Extract Form Features
    email_field    = driver.find_element_by_name("email") 
    password_field = driver.find_element_by_name("password") 
    submit_button  = driver.find_elements_by_id("submit_login")[0]
    
    ## Drive Form Features
    email_field.send_keys(email)
    time.sleep(1)
    password_field.send_keys(password)
    time.sleep(1)
    submit_button.click()
    time.sleep(2)

    # Content Analytics Page
    main = "https://dashboard.jwplayer.com/#/analytics/content?"
    pageLength='l' # s: short (10/pg), l: long (50/pg)
    dateRange='custom'
    url = main+\
      'pageLength='+pageLength+\
      '&dateRange='+dateRange+\
      '&dateRangeEnd='+today+\
      '&dateRangeStart='+today+\
      '&page=1'
    driver.get(url)
    time.sleep(2)

    tables = driver.find_elements_by_tag_name('table')
    
    # In experiment, 3 tables were discovered; data was in last table
    rows = tables[-1].text.split('\n')

    # 1. rows[0] should be: 'TITLE TYPE EMBEDS PLAYS COMPLETES COMPLETE RATE TIME WATCHED
    ##   -- change "COMPLETE RATE" to "COMPLETE_RATE"
    ##   -- change "TIME WATCHED" to "TIME_WATCHED"
    ##   -- add "TIME_UNITS" (seems to always be "hour", but just in case)
    columns = re.sub('complete rate', 'complete_rate', rows[0].lower())
    columns = re.sub('time watched', 'time_watched', columns)
    columns = (columns + ' time_units').split()
    
    # 2. In subsequent rows: split at Type, which is usuall "Video", but can be ______
    #   -- Everything starting Type and after can be split by " "
    df = pd.DataFrame(columns=columns)
    for idx,row in enumerate(rows[1:],1):
        split1 = re.split(' Video ', row)
        title = split1[0]
        right_cols = [re.sub(',|%','',col) for col in split1[1].split()]
        right_cols = [int(re.sub(',|%','',col))
                if (col!='hours' and col!='minutes') else col
                for col in split1[1].split()]
        title_splitter = re.sub('\?','\?', title) # ? is special char
        title_splitter = re.sub('\)','\)', title_splitter) # ) is special char
        title_splitter = re.sub('\(','\(', title_splitter) # ( is special char
        content_type = re.split(title_splitter, row)[1].split()[0]
        df.loc[idx] = [title] + [content_type] + right_cols

    return df
```
