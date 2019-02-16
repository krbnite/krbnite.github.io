---
layout: post
title: Character Encoding Craziness
tags: r
---

All I have to say is, if all your data is in English, then you haven't yet lived!  Don't you want to live?! 

I deal with a lot of Chinese textual data these days... (Turns out I have more to say.)  
Sometimes I import it into R from an Excel file, or from Amazon Redshift.  Other times,
I export it.  (Import, export -- think I covered my bases.)

The problem is, dealing with these Chinese characters will almost always give you a problem.  

For example, you might try to export to a CSV file and find that the Chinese characters were
all replaced with nonsensical symbols.  Worse, you might successfully export the data to a CSV
file, then make another copy of the file and---somehow!---find that it is no longer properly
encoded when you open it in Excel.  WTF?

For my sanity, and maybe yours, here are some lessons learned.

#### 1. In R, the readxl package will automatically read things in using UTF-8 encoding.
```R
library(readxl)
data = read_xl(fileName, sheet=sheetName)
```
This is great, but {readxl} doesn't seem to offer anything in the way of export.

#### 2. To export data from R to a CSV file with the proper encoding, you cannot simply use write.csv(). 
Instead, you must open a file connection, then use write.csv().
```R
con = file(filename, encoding="UTF-8")
write.csv(yourData, file=con)
close(con)
```

#### 3. Even though you properly exported the data from R to Excel (everything looked as it should), you're not guaranteed this luck to persist :-(
I don't know why... But Excel suddenly doesn't always "get it" once you modify the file in some way.  This 
happened to me when I emailed someone the CSV file, then wanted to look at it a couple weeks later and dragged
the attachment back onto my computer.  Excel was like, "WTF is this?"  I was like, "Dude, what do you mean? 
You knew what it was the last time we talked."  And Excel was like, "Nope, never seen it." 

Anyway -- I have Mac's Excel 2011. This is how you slap some sense into Excel.

1. Open new Excel File
2. In topmost icon bar, click on Data > Get External Data > Import Text File...
3. Choose "Delimited"
4. Change file origin (in my case, it is to Unicode (UTF-8), but there are other encodings out there)
5. Click on "Next" and choose the comma as your delimiter
6. Intelligently do a couple more things, and wa-la! 


#### 4. Even though data from Redshift might look right in RStudio, the {dplyr} package might not think so!
This can drive someone nuts: when you print the data to screen, it looks alright, but when you go to
do a join or group-by using some {dplyr} functions, the results will not be correct.  Worse, I had the same
data imported from an Excel file, call it text_data_xl, and from RedShift, text_data_rs, and all the various ways
I checked and compared their entries seemed to show they held identical data.

Likely, RStudio was just smart about it somehow... Or something.  I don't know.  Fortunately, I finally found
a difference: under the hood, they were tagged with different encodings, which you can check like so:
```R
# Get data from Excel file
library(readxl)
text_data_xl = read_xl(fileName, sheet=sheetName)

# Get data from Redshift
library(RPostgreSQL)
drv=dbDriver("PostgreSQL")
host='yourRedShiftInstance.redshift.amazonaws.com'
con = dbConnect(
  drv, 
  host=host_production, 
  port='5439',
  dbname='dbName', 
  user='yourUserName', 
  password='yourPassword')
text_data_rs = dbGetQuery(con, query_that_gets_the_same_data)

# Check to see if they have the same encoding!
# -- Encoding() works on scalar values
Encoding(text_data_xl[i,j])
Encoding(text_data_rs[i,j])

# Convert the RS data to have UTF-8 encoding
text_data_rs = enc2utf8(text_data_rs)
```

The Excel data
