---
title: Selenium&#58; Swapping Out PhantomJS for Headless Chrome
layout: post
tags: selenium webscraping python wwe
---

To run headless browser scripts, I've been using PhantomJS. But all along, all I could think was, "Would
be nice if Chrome just had its own headless version."  Then as the story usually goes, I finally googled this.

2 seconds later:  Oh! Ok, Chrome does have its own headless version.  Nice!

## Advantages of Headless Chrome over PhantomJS 
From [Why PhantomJS When You Can Chrome](https://www.gizra.com/content/phantomjs-chrome-docker-selenium-standalone/):
> PhantomJS is a great tool - a headless browser that can run in the terminal. It has lots of different uses, but when it 
> comes to testing your sites, I’d argue that it makes more sense to run the tests on real browsers. A second problem of 
> PhantomJS is debugging. When a test fails, it’s pretty hard to know why. Developers need to start saving screenshots or 
> worse - print the HTML of the page, in order to “see” what PhantomJS sees. With the Docker solution, a developer can 
> simply login via the provided VNC, see and even interact with the browser, so troubleshooting becomes much easier.

## Notes from StackOverflow & Elsewhere
* If we're using Chrome visually, then we should def use headless Chrome for nonvisual executions
* Basically, PhantomJS used to run on the same rendering engine as Chrome, but no longer does Chrome since Blink was released (Chrome's new rendering engine), so running tests from PhantomJS will not provide an accurate representation of how things will perform on Chrome
* Chrome's cross-platform, headless support is relatively new (early 2017): https://news.ycombinator.com/item?id=14101233
* The guy who maintains PhantomJS, Vitaly Slobodin, basically said "fuzz it" when headless chrome was released:
  - > I think people will switch to it, eventually. Chrome is faster and more stable than PhantomJS. And it doesn't eat memory like crazy.
  - > I don't see any future in developing PhantomJS. Developing PhantomJS 2 and 2.5 as a single developer is bloody hell. ... We have no support. From now, I am stepping down as maintainer.

## Some Code
```python
service = webdriver.chrome.service.Service(self.driver_path)
service.start()
options = webdriver.ChromeOptions()
options.add_argument('--headless')
options = options.to_capabilities()
driver = webdriver.Remote(service.service_url, options)
```

## Some References
* [Running Selenium with Headless Chrome]( https://intoli.com/blog/running-selenium-with-headless-chrome/)
* [Driving Headless Chrome with Python](https://duo.com/blog/driving-headless-chrome-with-python)
