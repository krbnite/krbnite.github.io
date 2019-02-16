---
title: Customing R with .RProfile
layout: post
tags: r
---

Is there a custom function you always use that is too specific to really create a library around, but that you 
use so frequently that doing something like `source(path_to_file)` gets annoying? For me, its one I call `rsConnect()`, 
which allows me to connect to Amazon Redshift without having to remember the specifics every single time.  

By creating an R Profile, conveniently called ".RProfile", you can make sure this function is automatically
loaded each time you begin R. You can also do fun things, like change the prompt and create "hello" and
"goodbye" messages to be issued when beginning and ending an R session, respectively.

Here is something simple to get you started:

```r
options(prompt="aRgh>")

# Let R know which Python distro to use
Sys.setenv(PATH = paste("/Users/kurban/anaconda3/bin", Sys.getenv("PATH"), sep=":"))

# On start-up
.First = function() {
    cat("\nHey there, Hot Shot!")
    cat("\nConnect to RedShift:  con = rsConnect()\n")
}

# On restart/termination
.Last = function() {
    cat("\nLeaving so soon, Big Boy?!\n")
}

# Make it easier to connect to most frequently-used database
rsConnect = function() {
    host= <default_redshift_url>
    drv = DBI::dbDriver("PostgreSQL")
    con = DBI::dbConnect(drv, 
        host = host, 
        port ='5439',
        dbname = <Database Name>, 
        user = <User name>, 
        password = <Password>
    )
}

# Frequent Projects...
go2proj1 = function() {setwd(<path_to_proj1); getwd()}
```


