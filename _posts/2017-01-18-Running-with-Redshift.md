---
title: Running with Redshift
layout: post
tags: redshift aws r aws
---

While working with some tables in Redshift, I was getting frustrated: "I wish I could just 
drag all this into R on my laptop, but I probably don't have enough memory!" A devlish grin: "Or do I?"

I had crashed R before trying to pull in too much data, so I was in a phase trying to learn to
do anything and everything in pure Redshift/SQL.  But, ugh, I longed for my R.

I had 89 data windows, each with just under 260k rows.  That's an upper bound of 23M row (for
simplicity and to be cautious). I then multiplied that by the number of columns (15), and the number
of bytes (8) per 64-bit entry (assuming this was true for all entries):

```r
rows = 23e6
cols = 15
bytes = 8
rows*cols*bytes # ~= 2.8 GB
```

I had 6GB of RAM, so this might be an ok pull.

But first I did a sanity check using R's {pryr} library to get the size of 
one of the data windows. 

```r
dw_size = pryr::object_size(one_data_window)  # ~32.8 MB
```

Multiply that by 89 data windows and you get ~2.9 GB.  So my estimate was not far off!

Should be good to go, right?!

Wrong. Turns out I didn't account for ... something or another! Though I could get the data
onto my computer, when I tried to do something with it, the computer came to a crawl for about
10 minutess or so before I finally halted the process b/c memory was capped.  

Had to suck it up and figure out the Redshift way, and I'm glad I did.  The equivalent computation
in Redshift cut down the processing time to less than a minute!
