---
title: The Selenium Webdriver
layout: post
---

## Get selenium package
```
pip install selenium
```

## Make sure you have Chrome Driver installed
1. Find Chrome Driver at: https://sites.google.com/a/chromium.org/chromedriver/home
2. Download the Desktop driver (at time of this writing): 
  - click on [Getting started with ChromeDriver on Desktop](https://sites.google.com/a/chromium.org/chromedriver/getting-started)
  - Click on [downloads](https://sites.google.com/a/chromium.org/chromedriver/downloads)
  - Click on latest release (currently 2.31)
  - Choose zip file for computer script will run on (you might have to do this first for your development computer, e.g., a mac or windows, then again for a production computer, e.g., windows or linux)
  - Download zip file to known location (e.g., /usr/local/share/)


## Bare Bones Example In Python
# Example
```python
import time
from selenium import webdriver

# Option 1
options = {}
driver = webdriver.Chrome('/path/to/chromedriver', desired_capabilities=options)
driver.get('http://www.google.com/');
driver.quit()

# Option 2
import selenium.webdriver.chrome.service as service
service = service.Service('/path/to/chromedriver')
service.start()
options = {}
driver = webdriver.Remote(service.service_url, {})
driver.get('http://www.google.com/');
driver.quit()
```

For Option 2, I found that `capabilities` NEEDS to be passed as a 2nd argument to 
`webdriver.Remote`, but it can actually be an empty dictionary `{}`. For Option 1, 
the `desired_capabilities` argument is optional, but I show it to sync up better with
Option 2. Note that in Option 1, there is also a `chrome_options` argument, but it
seems like another way of establishing the `capabilities` argument (try using it with
an empty dictionary and you'll be told that it has no attribute `to_capabilities`).


IMPORTANT:  Using this webdriver allows the automated control of a 
web browser, however if you do not know exactly what IDs or tags to 
look for, it will not be extremely helpful (at first).  I would
recommend exploring the contents of each page you want to navigate
first by using:
* the Developer Tools in Chrome (on MacBook Pro, cmd+alt+i)
  - on MacBook Pro, to locate the code of a specific part of the page, 2-finger click > "Inspect"
* Selenium & BeautifulSoup 
  - after locating the code of a page element in Developer Tools, try using Selenium and/or BeautifulSoup to find it in the code
  - e.g., driver.find_element_by_name('login')
  - e.g., page = bs4.BeautifulSoup(driver.page_source, 'lxml'); page.select('button')

## Miscellaneous Selenium Commands
### Ignore Content-Blocking Ads / Notifications
When I log into one particular website, a warning message appears, blocking the page content.  This is how to "x it out":
```python
driver.switch_to_active_element().click()
```

### Login
Say you want to log in to your Facebook or YouTube account.  This is usually fairly simple: you just need to
use Chrome's Developer Tools to figure out some identifying features of the username and password fields, as
well as the login button.

It should look something like:
```python
# Get control of the page elements
email = driver.find_element_by_name('email')
pwrd  = driver.find_element_by_name('password')
login = driver.find_element_by_name('Log In')
# Sign in
email.send_keys('you_email_address')
pwrd.send_keys('your_password')
login.click()
```

### Do it the easy way!
Pro Tip: Once you sign into a platform, you needn't figure out how to get to the page of interest (e.g.,
the analytics dashboard) by finding links and buttons on the page to click. Just like signing in manually
with a browser, you can just use the specific URL of the page you are interested in.  For example, 
we want to scrape some data from JW Player:
```python
# After signing in...
driver.get("https://dashboard.jwplayer.com/#/analytics/content")
```


### Refresh browser
```python
driver.refresh()
```

### Get page source
Ultimately, you want to collect data from the page. Great way to do it is to pass the page source code
to Beautiful Soup:
```python
page = bs4.BeautifulSoup(driver.page_source, 'lxml')
```


### Take a ScreenShot
```python
driver.get_screenshot_as_file('/Users/kurban/Desktop/checkThisOut.png')
```

## Last Few Notes (for Now)
1. For some reason exiting python is a chore after using webdriver... But just wait: it will eventually exit.
2. When you put your Selenium code into a function, suddenly `time.sleep(x)` will become really important; place after any Selenium command that might take a while.  This becomes even more important if the code is scheduled/automated. 
