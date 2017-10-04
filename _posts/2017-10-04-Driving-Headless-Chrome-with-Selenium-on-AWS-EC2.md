---
title: Driving Headless Chrome with Selenium on AWS EC2
layout: post
---

[Yesterday](https://krbnite.github.io/Which-Pip-is-Right-for-You-on-Corporate-EC2/),
I had issues getting the selenium python package installed on my corporate EC2 instance.
It turned out that the system's default pip was not conda's pip. After figuring this out,
and using conda's pip to install selenium, everything was cool...

...or was it?!

No, of course not.  Now my chromedriver was acting all funny -- funny as in, "What the frack's 
with all these error messages? Why the frack is this frackin' thing not frackin' working?!"

<figure>
<img src="/images/battlestar-frack-meme.jpg" width="400vw" align="center">
</figure>

## Trigger Warning: Stupid Ahead!
I found several blogs talking about apt-getting this and that -- xvfb, gconf2, and so on. 
These were supposed to be the dependencies I needed that would fix my problem.

Yet the bug persisted!

This morning, I figured things out in under a minute or two just by glancing at Chris Le's old
[blog post](http://www.chrisle.me/2013/08/running-headless-selenium-with-chrome/) about 
running headless Selenium with Chrome.  To be clear, all the stuff about VirtualBox and Vagrant
seems cool, but had nothing to do with my problem.  By reading over his code, it struck me
that I was using a copy of ChromeDriver from my 
MacBook Pro...  [Derrrr!](https://chromedriver.storage.googleapis.com/index.html?path=2.33/)  ChromeDriver has a 
separate build for Linux.

## Solution
The mileage may vary here, but ultimately I got my selenium scripts running this morning, and it 
had something to do with this code, which is a slight modification of some of 
[Chris Le's code](http://www.chrisle.me/2013/08/running-headless-selenium-with-chrome/).

```
# Add Google public key to apt
wget -q -O - "https://dl-ssl.google.com/linux/linux_signing_key.pub" | sudo apt-key add -

# Add Google to the apt-get source list
# echo 'deb http://dl.google.com/linux/chrome/deb/ stable main' >> /etc/apt/sources.list
#   -- didn't have permission for this, even w/ sudo; but could do it w/ vim
sudo vim /etc/apt/sources.list
# Add in the echo line above to bottom of file

# Update apt-get
sudo apt-get update

# Get stable chrome
sudo apt-get -y install google-chrome-stable

# get xvfb
sudo apt-get -y install xvfb

# get unzip
sudo apt-get -y install unzip

# Get the CORRECT version of chromedriver
#   -- as stupid as it might be, I kept trying to figure out bugs that had to do w/
#      using the OS X build that I copied from my laptop to my EC2 instance
wget https://chromedriver.storage.googleapis.com/2.32/chromedriver_linux64.zip
unzip chromedriver_linux64.zip
mv chromedriver to/where/ever/you/want
```

## Make Sure Python's Happy
Just remember where you put ChromeDriver so you can access it from Python.  I usually just 
install a copy right in my project folder.

```python
from selenium import webdriver
service = webdriver.chrome.service.Service('./chromedriver')
service.start()  
options = webdriver.ChromeOptions()                
options.add_argument('--headless')
options = options.to_capabilities()
driver = webdriver.Remote(service.service_url, options)
driver.get("https://www.youtube.com")
```

