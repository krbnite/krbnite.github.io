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
navigate to analytics page, scrape the  HTML components containing the relevant, and 
ultimately scrub the HTML and extract the necessary data with some regex/etc.  Then a team mate
in a separate project showed that JW Player was not giving the right numbers, and that another
platform our business used, Conviva, was the better option.  JWP component scrapped!

For a live stream report on YouTube, another Selenium script was necessary as the various APIs
do not seem to give access to the live stream metrics available in the YouTube Analytics console
once the live stream ends.  This was the second component built for the project, and would
go on to sit around for several months as other projects were addressed and unanticipated fires
put out.


...to be continued...
