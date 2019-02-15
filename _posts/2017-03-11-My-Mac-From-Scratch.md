---
layout: post
title: My Mac From Scratch
tags: macbook misc
---

I've spent nearly a decade honing my .bash\_profile, .vimrc, and other
startup files on my personal laptop. But can't say that I think about them too much. 
It's set-and-forget for long periods of time. To be clear, taking things for granted goes far beyond
startup files: XCode, HomeBrew and hordes of brew-installed software, 
R and Python libraries, general preferences and customizations all around!

In this post, I attempt to lay out how to build my old MacBook Pro from scratch. 
It's really for me, but hey--it could be for you too! Also, be warned: I make
no claims that any of this represents the best way to do anything!  So if you 
know a better way, please let me know in the comments.


## Starter Moves
--------------------------------------
1.  Update OS X 
2.  Get XCode
    ```bash
    sudo xcode-select --install 
    sudo xcodebuild -license
    ```
3.  Get HomeBrew
    ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    # Also get Brew Cask (manage software other than CLI stuff)
    brew tap caskroom/cask
    ```
4.  Set up Git (brush up on your Git w/ this [tutorial](http://kbroman.org/github_tutorial/),
    or this [no-deep-git guide](http://rogerdudler.github.io/git-guide/))
    ```bash
    brew install git
    # Optional Stuff: Not necessary if you already have a 
    #   ~/.gitconfig file on your old Mac (see next section 
    #   on startup files)
    git config --global user.name "Your Name"
    git config --global user.email "yourEmail"
    git config --global color.ui true  # Colored Output
    git config --global core.editor vim # For cool people
    ```
5. Bash Completions!
    ```bash
    brew install bash-completions 
    brew tap homebrew/completions
    #  Make sure this is in your .bash_profile (see next section)
    [ -f /usr/local/etc/bash_completion ] && .  /usr/local/etc/bash_completion
    ```
<br/>
 
## Startup Files
--------------------------------------

1.  Round up all your startup files on your old Mac into new directory:
    ```bash
    cd ~
    mkdir .startupfiles
    cp .bash_profile .startupfiles/
    cp .vimrc .startupfiles/
    cp .tmux.conf .startupfiles/
    cp .gitconfig .startupfiles/
    # ...and so on. 
    ```
2.  Make ~/.startupfiles a Git repository: 
    ```bash
    cd .startupfiles/
    git init
    git add .
    git commit . -m "Startup files on my old Mac as of yyyy-mm-dd."
    ```
3.  Create a new GitHub repository called .startupfiles 
4.  Push ~/.startupfiles/ to GitHub's .startupfiles:
    ```bash
    git remote add origin https://github.com/userName/.startupfiles
    git push -u origin master
    ```
5.  Clone .startupfiles onto new Mac
    ```bash
    git clone https://github.com/userName/.startupfiles
    ```
6.  Optional/Convenient: Create .update bash function in .bash\_profile 
    to easily update startup files in home directory with latest versions in .startupfiles, 
    e.g., something like:
    ```bash
    .update() {
      cp `ls -lrtF -d -1 .startupfiles/{,.*} | grep "[a-z]$"` ~
    }
    ```
    
7.  Optional/Helpful: design your .bash\_profile such that each computer can
    have customizations.
    ```bash
    if [ `hostname` = 'yourHostNameForComp1' ]; then
      # custom stuff;
    fi
    ```

Beware:  On my work computer, the hostname is different when 
I'm actually at work vs when I'm remotely logged in through a VPN.  
<br/>

## R
--------------------------------------

1.  First, we want to brew install R, and related software that R depends on.  
    ```bash 
    brew install Caskroom/cask/xquarts 
    brew cask install java 
    brew tap homebrew/science 
    brew install R 
    brew cask install mactex 
    ```

2. If you have an R environment on the old computer that you already like, 
then take it with you. First, make sure R and Java are friends:
    ```bash
    R CMD javareconf
    ```
This is important! Otherwise, the installation of the rJava package will fail, followed by all
other that depend on it, e.g., RPostgreSQL.  Now let's go ahead and get a record of your beloved
R environment on your old Mac:
    ```r
    r.pkgs <- 
      installed.packages()[is.na(installed.packages()[ , "Priority"]), 1]
    save(r.pkgs, file="r-pkgs.Rdata")
    ```
Then get r-pkgs.Rdata on your new Mac, start an R session, and create your 
library:
    ```r
    load("r-pkgs.Rdata")
    install.packages(r.pkgs)
    ```

3. Get RStudio
    ```bash
    brew install Caskroom/cask/rstudio 
    ```

4. RStudio Settings:  
I wanted to figure out a cool command line way of doing this, but couldn't 
figure it out. Two important directories on my Mac seemed important, but amounted in
no discovery of the "preference file" I was looking for: 
    ```bash
    ~/.rstudio-desktop 
    /Applications/RStudio.app/Contents
    ```
Ultimately, I just had to set my preferences manually:
- RStudio > Preferences > Code > Keybindings > Vim
- RStudio > Preferences > Appearance > Editor Theme > Material


<br/>


##  Python 
--------------------------------------
1. Get Anaconda
    ```bash
    brew cask install Caskroom/cask/anaconda
    ```
Homebrew will advise you to put this in .bash\_profile:
    ```bash
    export PATH=~/anaconda3/bin:"$PATH"
    ```
You might already have that taken care of though since we 
transferred over all the startup files from the old Mac.

2. Make conda environment files from your environments on the old Mac. 
Use them to create those environments on the new Mac.  At the very least,
make sure to have matplotlib, pandas, numpy, sklearn, tensorflow, etc.

<br/>
 


##  PostgreSQL / Amazon Redshift
---------------------------------------
1. TO ACCESS FROM TERMINAL:
    ```bash
    # Make sure you have psql
    brew install postgres
    # If first time, create your user db
    brew services start postgres
    createdb `whoami`
    psql  # log in w/ user account
    brew services stop postgres
    # Head to Redshift!
    psql \
      --host=<DB instance endpoint> \
      --port=<port> \
      --username <master user name> \
      --password \
      --dbname=<database name> 
    ```

2. TO ACCESS IN R:
    ```r
    library(RPostgreSQL)
    drv=dbDriver("PostgreSQL")
    host='yourRedShiftInstance.redshift.amazonaws.com'
    con = dbConnect(drv, host=host_production, 
           port='5439',
           dbname='dbName', 
           user='yourUserName', 
           password='yourPassword')
    dbExecute(con,query_to_affect_something)
    dbGetQuery(con,query_something_to_return_results)
    ```

3. TO ACCESS IN PYTHON:
    ```python
    from sqlalchemy import create_engine
    import pandas as pd
    from pandas import read_sql_query as query
    db='dbName'
    host='yourRedShiftInstance.redshift.amazonaws.com'
    user='yourUserName'
    psswd='yourPassword'
    conStr='postgresql://'+user+':'+psswd+'@'+host+':5439/'+db
    con = create_engine(conStr)
    df = query(query_whatever,con)
    ```

<br/>

## VIM:
-------------------------------------------
1. Install Tim Pope's Pathogen (basically a Vim package manager)
    ```bash
    mkdir -p ~/.vim/autoload ~/.vim/bundle && \
    curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
    echo "execute pathogen#infect()" >> ~/.vimrc
    ```
 Go to the bundle directory and start git cloning the not-so-bare essentials!
    ```bash
    cd ~/.vim/bundle
    ```
2. SENSIBLE: If you don't have a .vimrc file already, you can kick off with
   Tim Pope's  (note2self: I should go through his file and see if I've
   missed anyting in my .vimrc)
    ```bash
    git clone git://github.com/tpope/vim-sensible.git
    ```
Alternatively, you might go with [The Ultimate Vim Configuration](https://github.com/kensodev/dotvim)

3. NERDTree: Make Vim more IDE-Like w/ Directory Tree sidebar
    ```bash
    git clone https://github.com/scrooloose/nerdtree.git ~/.vim/bundle/nerdtree
    ```
4. TEX-9: Simple, vimmy LaTeX functionality
    ```bash
    git clone https://github.com/vim-scripts/TeX-9
    ```
5. And so on: Surround, Syntastic, &lt;whatever&gt;...

6.  Include VimPlugIns help in Vim environment:  
    ```bash
    :helptags ~/.vim/bundle/vim-surround
    :helptags ~/.vim/bundle/nerdtree
    :helptags ~/.vim/bundle/syntastic
    ```
<br/>

## Jekyll (GitHub Blog)
-------------------------------------------
1. Ruby: OS X already has it, but for some reason it sucks. Brew it!
    ```bash
    brew install ruby # neccessary, despite OS X having ruby.. otherwise, errors
    ```
2. Get Github Pages and Jekyll
    ```bash
    sudo gem install github-pages
    sudo gem install jekyll
    ```

3. How to Use It:
    ```bash
    cd path/to/local/copy/of/youUserName.github.io
    jekyll serve
    open http://0.0.0.0:4000/
    ```
<br/>

##  Misc HomeBrew Installations
---------------------------------------
Other stuff I use from time to time.

    brew install imagemagick
    brew install ffmpeg
    brew install tmux
    brew install wget
    brew install pandoc
    brew install Caskroom/cask/rodeo

<br/>


## Default printer
---------------------------------------------
I noticed this file on my work computer, so I'm assuming
if you wanted to set a default printer, this is how you do it. 

    mkdir .cups
    echo "Default printer_name" > .cups/lpopoptions

<br/>


## Make Keyboard Less Annoying
---------------------------------------------------
1. When I hold on j in RStudio vim mode, I just want the cursor to 
   move left and keep on doing so... Unfortunately, by default, Mac
   has a special key palette come up.  
   [This is how you fix that](Source: http://osxdaily.com/2011/08/04/enable-key-repeat-mac-os-x-lion/):
    ```bash
    defaults write -g ApplePressAndHoldEnabled -bool false
    ```
If you ever wanted to undo this (don't know why)
    ```bash
    defaults write -g ApplePressAndHoldEnabled -bool true
    ```

2. SPEED
- Apple > System Preferences > Keyboard > Key Repeat (Fast)  
- Apple > System Preferences > Keyboard > Delay Until Repeat (Short)

<br/>


