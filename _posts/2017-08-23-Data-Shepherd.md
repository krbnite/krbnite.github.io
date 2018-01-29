---
layout: post
title: Data Shepherd
---

Data doesn't always come neatly packaged in a table or streaming through some API.  
Oftentimes it's just out there --- free range flocks of it on the Wild, Wild Web, just waiting
for a cowboy to come by and herd it to the meat factory for slaughter. 

I'm that cowboy.  

I spend a bunch of time shepherding wild, untamed data sets --- or more accurately, 
automating webscrapers to perform such tasks on a regular schedule.  This started pretty simply
when someone said, "There's no way we can get that data -- at least not at the scale we need."  

The way I look at it is, if somebody can slowly...humanly...extract data from 
a website by point-and-click, copy-and-paste, manually downloading, or any other
means, then I should be able to make a computer do it --- faster, at higher volume, and at consistent
intervals, if necessary.

The trick is figuring it out for the computer, which is nothing like a goold ol' point-and-click or 
copy-and-paste.  However, figure it out once, and the job is automated ad infinitum (or until it chokes 
on a bug or unforeseen edge case).

For some sites, this is easy -- you just need the page's source code, possibly some regex, and
time to munge through the HTML.  For simple projects, this might only require Python's 
[requests](http://requests.readthedocs.io/en/latest/api/) library, 
though it seems one gains a bit more power tinkering with 
[BeautifulSoup](https://beautiful-soup-4.readthedocs.io/en/latest/) as well. 
However, some data is hidden behind a point-and-click action, or a button push. Both of these tools are 
rendered impotent when it comes to such JavaScript-heavy webpages.

If only you could just automate a browser, right? 

Your wish is Selenium's command.  Of course, things get a little more complex (e.g., you need to properly
install selenium and a browser's webdriver), but it's worth it.  The better I get at using selenium,
the more I realize is possible and the less I find myself using requests or BeautifulSoup (though honestly,
I often still rely on BeautifulSoup; also, selenium is overkill for simple tasks).

In several upcoming (and previous) installments, I go over some up-and-running instructions and code chunks. 


* [Install Selenium](http://selenium-python.readthedocs.io/installation.html)
* [Getting Started w/ Selenium](http://selenium-python.readthedocs.io/getting-started.html)


