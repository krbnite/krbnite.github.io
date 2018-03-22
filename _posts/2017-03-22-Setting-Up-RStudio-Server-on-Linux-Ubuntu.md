---
title: Setting Up RStudio Server on Linux Ubuntu
layout: post
---

First things first: update and upgrade your system software.

```bash
sudo apt-get update
sudo apt-get upgrade
```

# Base R
And, of course, second things second: use `apt-get` to install `base-r` and `base-r-dev` as root.

```bash
sudo apt-get install r-base r-base-dev
```

This basic install of R  include packages like: boot, class, cluster, codetools, foreign, kernsmooth, lattice, mess, matrix, mgcv, 
nlme, nnet, rpart, spatial, and survival. 

It is important that you install R like this as the root user (using `sudo`) on your Ubuntu server so that these packages and resources 
are available to all users on the server.  If you're setting up RStudio server for you and your team members at work, this is essential!  


# Fleshing R Out with Apt-Get
Individual users will be able to install their own packages locally to their user accounts, but these packages will
only be available on their user account.  To avoid redundacny on your machine and to ensure that your team has a great
RStudio experience from git-go, you should likely go ahead and install a few more packages and dependencies as root!
 
As a start, install the following Ubuntu-managed R packages and their dependencies as root 

```bash
sudo apt-get install reshape2
sudo apt-get install rodbc
```

This will include the following R packages: abind, crayon, digest, domc, foreach, iterators, libodbc1, magrittr, memoise, multicore, pkgkitten, 
plyr, rcpp, stringi, stringr, reshape2, dbi, rmysql, rodbc, rpostgresql .
 
These packages (r-base, r-base-dev, reshape2, rodbc, and all their dependencies) are updated using routine system maintenance:

```bash
sudo apt-get update
sudo apt-get upgrade
```

# Further Fleshing of R
Not all R libraries/packages can be installed with apt-get.  To install other important packages (and their dependencies)
into your server's default R environment for all users, run R as a root user and install in the usual `install.packages()` way:
 
```bash
# Run R as root
sudo R
```

```r
# Now inside R
install.packages(c(‘dplyr’, ‘ggplot2’, ‘tidyr’, ‘lubridate’))
```
 
# Install RStudio-Server

As of 2017-03-22, this is how you do it for Ubuntu Xenial:

```bash
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/rstudio-server-1.1.442-amd64.deb
sudo gdebi rstudio-server-1.1.442-amd64.deb
```

But don't just blindly copy-and-paste from me!  Rather, blindly copy-and-paste from the RStudio
website itself:  https://www.rstudio.com/products/rstudio/download-server/

Believe it or not, that's it: you've got RStudio-Server up and running!  Now you just have to sign in... But how?

# RStudio in the Browser
This is the best part: just open up your browser of choice and type:

```bash
yourUbuntuIpAddress:8787
```

A sign-in screen will appear: just put in your regular SSH credentials that you would use if SSH'ing in 
through Terminal.  And you're in!

I cannot overestimate how great this product is!  

[1] This set-up was primarily 
for 3 other members of my team who prefer R, but didn't know enough Vim/Tmux/Bash to use our Linux server 
in any kind of useful way.  This set-up has provided them with all the power of our Linux server
right out-of-the-box.  They went from 0 to 100 in under a minute!  Prior to this, I was teaching them
as much as possible about SSH, SFTP, cron, Vim, Tmux, and a host of Bash commands so that they could
use the server how I do...  I'm simultaneously happy and sad to say that my esoteric expertise are no 
longer as necessary as they once were: RStudio Server is an absolute game changer for my team.

[2] Though I primarily develop my models and automations in Python, RStudio Server is still useful. For one, it comes stock
with my usual Terminal set up: I can have a Vim session up side-by-side with iPython.  I may or may not switch to
just signing into our Linux system using this RStudio Server approach.  Not sure, but it seems promising.




# References
* https://cran.r-project.org/bin/linux/ubuntu/README.html
* https://www.rstudio.com/products/rstudio/download-server/
* https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started
