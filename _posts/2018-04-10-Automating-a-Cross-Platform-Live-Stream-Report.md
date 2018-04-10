---
title: Trouble, Tributlation, and Triumph&#58; Automating a Cross-Platform Live Stream Report
layout: post
---

How many views did our live stream get on Facebook? How about YouTube and Twitter, or on our 
mobile app, website, and monthly subscription network?  How do these numbers compare to each other
and to similar events we streamed in the past? And, finally, how do we best distribute this information 
to stakeholders and decision makers in near-real time?

Once upon a time at my place of business, capture of live stream viewership metrics on each 
platform was governed by a host of different individuals and separate processes.  Signing into
a platform-specific analytics console, searching around for the live stream, and eyeballing or
screenshotting viewership metrics was the norm.  A ring leader
was in place to get the info from the various teams, update an Excel file, and send out an
email.  

Shortly after I joined the Content Analytics team, there was a re-org and my new team
was saddled with the entirety of the report.  I had already been researching and documenting the
different use cases for YouTube's reporting, analytics, and data APIs, and setting up
various webscrapers in Selenium.  The automation of this cross-platform report from data capture
to report generation to email seemed quite within reach!  

Of course, there were setbacks and unexpected turns. For example, for website viewership, 
I originally developed a Selenium script to automatically open up a browser, sign into JW Player, 
navigate to analytics page, scrape the  HTML components containing the relevant data, and 
ultimately regex/scrub the HTML to extract the necessary data.  Then a team mate
in a separate project showed that JW Player was not giving the right numbers, and that another
platform our business used, Conviva, was the better option.  Just like that: JWP component scrapped!

For a live stream report on YouTube, I wrote another Selenium script, which had a lot of 
its own troubles.  For example, the data required for the project comes from a CSV report made
available in the YouTube Analytics console once the live stream ends. To make downloading
the CSV as deterministic as possible, whether the script is running on Windows, OS X, or 
Linux Ubuntu, I first needed to figure out how to specify to the ChromeDriver that the
default download directory should be the calling script's directory.  Also, this script
needed to run on a server without a display, so to my mind that meant it needed to run
in headless mode... But wait, wtf?! In Chrome's headless mode, you cannot download files -- apparently
it's a safety feature.  It became overly time consuming trying to figure out how to force it
to do my will!  There had to be another way... And then it occurred to me: what if
we don't run the Chrome browser in headless mode at all, but in its regular mode on a virtual 
display?  Yup!  Within seconds of having this though, a Google search turned up solutions
doing just that. 

```python
from selenium import webdriver
from pyvirtualdisplay import Display

# Create Virtual Display
display = Display(visible=0, size=(800, 600))
display.start()

# Open up a Chrome Browser
prefs = {"download.default_directory" : download_directory}
options = webdriver.ChromeOptions().\
    add_experimental_option("prefs", 
        {"download.default_directory" : download_directory})
driver = webdriver.Chrome(driver_path, 
    desired_capabilities = options.to_capabilities())

# Do Stuff...

# Close Things...
driver.quit()
display.stop()
```

This was the second component built for the project, and would
go on to sit around for several months as other projects were addressed and unanticipated fires
put out.


...to be continued...
